# Technical Specification

**OMID AI-Based Farm Disease, Pest, Yield, and Frost Detection System**\
Assignment 9 · Version 1.0 · 2026-11-11\
**Team:** Andrea Dall'Ara, Bridget Trout, Dang Luu, Cyrus Speicher

## 1. Version and change log

| Version | Date | Change | Author |
|---|---|---|---|
| 1.0 | 2026-11-11 | Consolidated D0–D2 interfaces, mapped the requirements baseline, and recorded design gaps and planning review evidence. | Andrea Dall'Ara |

**Requirements baseline:** [Requirements Document v1.0, 2026-11-11](Requirements_Document.md#1-version-and-change-log). The matrix in §6 traces to that exact version. Update this citation and recheck every affected row when requirements change. ChatGPT assisted with drafting and formatting from the team's source documents.

## 2. Architecture summary

The [D0 block diagram](Design_Diagrams/D0_High_Level_Design.md#block-diagram-the-d0-diagram) defines the system boundary and connections I1–I6; the [D0 data-flow diagram](Design_Diagrams/D0_High_Level_Design.md#data-flow-diagram) shows the two data pipelines. The browser runs the Flutter dashboard, Firebase Authentication provides identity, and the Node.js/Firebase gateway authorizes requests and coordinates ingestion, analysis, and retrieval. Python image services run separately from the web client, and yield counting runs as a batch worker; its API wrapper is planned. Firestore holds operational records, BigQuery holds sensor history, and Cloud Storage holds capture files and analysis outputs.

Field hardware submits stationary readings through I2 and capture images through I1. The gateway validates the organization and field boundary, saves records, and loads recent readings and zone thresholds for the I3 frost engine. The engine returns a risk state and input quality. Multispectral TIFFs go to disease analysis (I4); single-photo disease and pest screening use I4 and I5. Side-view RGB photos go to I6 for bunch detection, filtering, aggregation, and calibrated yield. I6 accepts a capture reference and returns a run identifier so the client can poll without keeping a long-running request open.

The dashboard reads authorized records, historical chart series, and an in-app notification feed. Configuration history and request-correlated logs support investigation of results. Planned export handlers produce buyer reports and de-identified research CSVs from authorized records. The [D1 design](Design_Diagrams/D1_Detailed_Design.md) documents existing algorithms and prototype contracts. The [D2 design](Design_Diagrams/D2_Detailed_Design.md) supersedes conflicting D0/D1 interface details for the target release. A target contract or planned extension does not imply it is deployed.

## 3. Interfaces and data formats

### 3.1 Gateway endpoints

Routes use HTTPS; requests and responses use JSON unless a multipart upload or file response is specified. Bearer tokens protect user routes, and I2 uses a per-sensor key. For D2 target errors, use `{success:false, code:string, error:string}` where the design defines a body. The inherited D1 history error body omits `code`. Identifiers are strings unless typed otherwise; timestamps are exchanged with an offset and normalized to UTC for storage and range comparison.

| Method and route | Request | Success | Errors / recovery |
|---|---|---|---|
| POST `/api/v1/captures` (I1) | Multipart `files[]`, `zone_id`; bearer token. Derive the organization from the authorized zone. | 201 `{capture_id}`; TIFF to I4; JPEG/PNG to I6. | 401 unauthenticated; 403 out of scope; 413 FILE_TOO_LARGE; 415 UNSUPPORTED_FORMAT. |
| POST `/api/v1/readings` (I2) | JSON `{sensor_id,timestamp,zone_id,sensors,battery_level}`; `X-Sensor-Key`. | 200 `{ack:true,duplicate:boolean}`. Upsert by sensor and timestamp. | 400 SCHEMA_INVALID; 401 invalid key; 422 OUT_OF_RANGE; 503 STORE_UNAVAILABLE. Buffer and retry transient failures. |
| POST `/getHistoricalSeries` | Bearer; `HistoryRequest` below. | 200 `HistoryResponse`; empty `graphs` if none. OPTIONS returns 204. | 400 invalid fields/range; 401 invalid token; 403 membership or zone/site mismatch; 405 method; 413 TOO_MANY_POINTS; 500 processing failure (D1). |
| POST `/api/v1/auth/verify` | Bearer; organization to check in the request context. | 200 `{valid,uid,orgId,permissions}`. | 401 UNAUTHORIZED; 403 FORBIDDEN; 503 AUTH_UNAVAILABLE (fail closed). |
| PUT `/api/v1/zones/{zone_id}/config` | Bearer; `{growth_stage,critical_temp,min_exposure_duration,updated_at}`. | 200 `ZoneConfig`; preserve previous settings in history. | 400 INVALID_CONFIG; 403 permission; 404 ZONE_NOT_FOUND; 409 CONFIG_CONFLICT; reload on conflict. |

**I2 telemetry.** D0's example uses `sensors:{temperature_c,dew_point_c,leaf_wetness_pct,soil_moisture}`, plus a battery reading and timestamp. D2 adds `sensor_id`, validates identity and ranges, and converts the air temperature to °F before storage. Required frost channels such as relative humidity and wind are modeled in D2 F-1; coverage and remaining device field mappings must be confirmed with the hardware team. If a required channel is missing or stale, the gateway withholds a current forecast.

**History payload.** `HistoryRequest` requires `organizationId`, `siteId`, nonempty `zoneIds[]` and `readings[]`, `start`, and `end`; optional `interval`, `sensorId`, `timezone`, and `aggregation`. The response contains `success`, `interval`, `aggregation`, and `graphs[]`. Each graph has `field`, nullable `unit`, and `series[]` of `{zoneId,points:[{t,v}]}`, where `t` is the bucket timestamp and `v` is numeric. Use D1's time buckets and AVG/MIN/MAX. D2 adds ordered-bound, zone-parentage and point-limit checks; the numerical point limit awaits AQ-04.

### 3.2 Analysis interfaces and endpoints

**I3 · Frost engine.** In-process call `evaluate(zone_id, readings, zone_config, forecast?)`. The gateway supplies the last six hours of readings on a 15-minute cadence (at least two valid readings), the zone thresholds in °F, and an optional weather forecast. The engine does not query BigQuery. Returns `{zone_id,alert_level,level_name,frost_type,injury_likelihood,quality,last_reading_at}`; quality is ok, degraded, or stale. `INSUFFICIENT_DATA` holds the previous alert and `INVALID_CONFIG` rejects the evaluation. History failures are retried three times by the gateway before it shows a delayed-data state. See [D2 I3 and F-2](Design_Diagrams/D2_Detailed_Design.md#21-frost-prediction-engine-i3).

I4–I6 use internal HTTPS. The gateway sets `X-Internal-Service-Key`, `X-Request-User`, and `X-Request-Id`; browser-supplied versions are untrusted. D1's prototype image endpoints lack application-level authorization. Put protected requests behind the D2 gateway boundary.

| Method and route | Request format | Success | Errors |
|---|---|---|---|
| POST `/predict` (I4) | Multipart `file` (single image). | 200 `{disease,confidence,top3,status}`; OK or MANUAL_REVIEW. | 400 INVALID_IMAGE; 503 MODEL_UNAVAILABLE. |
| POST `/process-capture` (I4) | Multipart at least five ordered TIFF `files`, `capture_id`, `zone_id`; organization from trusted context. | 200 `CaptureResponse`. | 400 invalid image/TOO_FEW_BANDS; 409 CAPTURE_EXISTS; 413 FILE_TOO_LARGE; 422 ALIGNMENT_FAILED; 502 UPLOAD_FAILED; 503 MODEL_UNAVAILABLE. |
| POST `/pests_predict` (I5) | Multipart `file` (image at least 64×64 px). | 200 `{success:true,prediction,confidence,status}`; OK or UNCERTAIN. | 400 INVALID_IMAGE; 422 IMAGE_TOO_SMALL; 503 MODEL_UNAVAILABLE. |
| POST `/yield/runs` (I6, planned) | JSON `{capture_id,level?,force?}`; level defaults to row (row/section/image); force defaults to false. | 202 new `RunStatus`; 200 existing matching run when force is false. | 400 INVALID_LEVEL; 401; 404 CAPTURE_NOT_FOUND; 422 CAPTURE_NAME_INVALID or NO_SUPPORTED_IMAGES; 503 MODEL_UNAVAILABLE. |
| GET `/yield/runs/{run_id}` (I6, planned) | Run ID in path; organization checked. | 200 `RunStatus`. | 401; 404 RUN_NOT_FOUND. |
| GET `/yield/runs/{run_id}/results` (I6, planned) | Run ID in path; organization checked. | 200 `RunResult` for completed runs. | 401; 404; 409 RUN_NOT_FINISHED or RUN_FAILED. |
| PUT `/yield/calibrations/{zone}/{season}` (I6, planned) | Path zone and season; JSON `{mean_bunch_mass_kg,sample_size,measured_on}`. | 200 saved calibration. | 400 INVALID_MASS (must be >0 and ≤2 kg); 401; gateway denies unauthorized scope. |
| GET `/health`, GET `/healthz` (D1 I4 probes) | No body; separate photo and multispectral services. | 200 `{status:"ok"}` or `{ok:true}`. | D1 defines no custom failure body. |

**Result shapes.** `CaptureResponse` contains `capture_id`, `status`, `inference_summary`, and `firebase_upload`; return file references as URLs rather than server paths. Summary statuses are OK, NO_VINE_TILES, and MANUAL_REVIEW. `RunStatus` holds run/capture IDs, state, image totals and failures, model version, and a nullable failure code. `RunResult` adds zone, capture date, `groups[]`, `issues[]`, and `limitations[]`. Each group carries `group_key`, image counts, total/near/countable counts, nullable `estimated_yield_kg`, and nullable `unavailable_reason` (NO_CALIBRATION or INCOMPLETE_GROUP). Issues contain nullable `rel_path`, severity, code, and message. Every result declares OVERLAP_NOT_CHECKED. An unreadable photo is logged and skipped; a run with no readable photo fails and exposes no results.

### 3.3 Service and storage boundaries

| Boundary and protocol | Inputs, outputs, and formats | Error behavior |
|---|---|---|
| Dashboard → Firebase Auth / gateway (HTTPS SDK and JSON) | Credentials return a bearer token. Load `organizations/{orgId}/members/{userId}` per request: role, `canManageMembers`, `canManageSites`, `canManageSensors`. `RoleUpdater(orgId,uid,newRole)` saves changes. | 401 invalid identity; 403 denied; 503 provider outage. Refresh an expired token once. Transaction protects the last owner. |
| Gateway → BigQuery / Firestore (SDK) | `SENSOR_READING` keyed by `(sensor_id,tstamp)` holds org/site/zone, sensor fields, units and quality. `sensors/{sensor_id}` holds zone, last_seen, ACTIVE/STALE and key hash. History returns aggregated points. | Invalid readings rejected; duplicate upsert is safe. On store outage return 503; device retains readings and retries with backoff. |
| Gateway / analysis → Cloud Storage and Firestore (SDK) | Image bytes and outputs in object storage. `captures/{capture_id}`: `org_id`, `zone_id`, timestamp, analysis references. Validate site via zone parentage; stream authorized records. | 502 upload/write failed; keep local outputs. Replace D2 public URLs with protected retrieval for NFR-02. |
| Gateway → weather source (provider interface open) | Zone/time in; optional transient forecast to I3. Provider and wire format unselected. | Use local rules if forecast unavailable. Missing or stale local readings still hold the warning. |
| Yield → run store / Firestore (files and SDK) | Per-image and group CSVs, run metadata/issues, totals by `(run_id,group_key)`, calibration by `(zone,season)`, summary at `yield_runs/{run_id}`. | Interrupted run fails. If publication fails, keep results in the run store; poll with backoff. |
| Gateway → configuration, notifications, logging (internal calls) | `ZoneConfig`: zone, org, growth stage, threshold, exposure, update stamp and history. `notify(event)`: event/org/zone IDs, type, severity, message, time, dedup key → status/suppression. `log(level,event,**fields)` emits JSON lines with request ID, service, user, code, time. | 409 config conflict; cache fallback degrades quality. Reject INVALID_EVENT; retry feed writes three times, then mark FAILED. Logging never blocks; errors fall back to stderr. |

### 3.4 Planned public contracts for story gaps

Apply §3.1 access checks to these proposed contracts. Route protected images through a permission-checked download backed by private objects (route and migration open, AQ-04). Crop/harvest metadata, vision event retention and export aliases remain open.

| Proposed endpoint | JSON input | Success / errors |
|---|---|---|
| POST `/api/v1/predictions` | `{zone_id,event_type}` | 200 frost `{zone_id,likelihood,window_start,window_end,last_reading_at,quality}`. Missing data: 409 with missing/stale sensor IDs, no current result. Unsupported forecast type: 422. |
| POST `/api/v1/exports/buyer` | `{zone_id,crop,harvest_start,harvest_end}` | 200 downloadable buyer report with selection, generation date, and yield in kg or unavailable. Match all selection keys. Invalid range: 400; denied access: 403. |
| POST `/api/v1/exports/research` | `{zone_ids,start,end}` | 200 CSV with `date,crop,field_alias,record_type,value,unit`, inclusive bounds and only authorized records. Empty: 200 JSON with the REQ-10 message, no file. Invalid range: 400; denied access: 403. |

Build research CSVs from an explicit column allowlist. Downstream recipients receive only the generated file.

## 4. Technology stack

| Layer | Choice | Requirement or constraint justification |
|---|---|---|
| Browser application | Flutter / Dart | Shared UI for field views, yield results and text alert states (REQ-01, REQ-03, NFR-03). |
| Gateway and identity | Node.js Firebase HTTPS Functions; Firebase Authentication; Firestore membership | Validate current permissions per request and support revocation without re-login (REQ-05, REQ-06, CON-02). |
| Operational / historical data | Firestore, BigQuery, Cloud Storage | Separate live state, durable keyed telemetry, and image files; keep analysis and history scoped (REQ-02, REQ-09, NFR-04). |
| Field communication | HTTPS REST JSON and multipart; existing sensors, drone and camera | Explicit acknowledgments, retries and format checks while reusing field equipment (REQ-04, CON-01, CON-03). |
| Frost analytics | Six-level rules and Magnus dew point, CPU | Uses local readings, configurable thresholds, and a recovery guard without a trained frost model (REQ-01, REQ-14, CON-04). |
| Vision screening | Python, OpenCV, PyTorch; ViT and ResNet-18 | Reuses separate photo and spectral models with explicit uncertainty states (REQ-12, CON-01). |
| Yield counting | Python, YOLO11, SAHI, Depth Anything V2 Small; CPU fallback; CSV/JSON/YAML | Tiling preserves small bunch detail; depth and quality stages separate foreground counts. Batch execution limits cloud cost (REQ-03, REQ-13, NFR-01, CON-01). |
| Verification | pytest, synthetic sensor replay, labeled vineyard photos, GitHub Actions | Exercises stale inputs, unreadable images, and model decisions before field deployment (REQ-02, REQ-04, REQ-14, NFR-04). |

## 5. Known constraints

| ID | Inherited constraint | What it rules out or forces |
|---|---|---|
| CON-01 | $2,000 combined project budget; reuse current drone/camera and favor open/free tooling. | Rules out budgeting for a replacement aircraft or permanent paid GPU fleet in the baseline. Use batch inference on owned hardware where practical. Review the YOLO11 licensing constraint before closed deployment; D1 identifies an RT-DETR alternative. |
| CON-02 | Authorized access, retained configuration/model versions, logging and quality flags. | Rules out client-only permissions and unrestricted artifact links. Verify membership and zone scope per request, preserve versions, and correlate results with inputs. Alert-to-config version linkage is still open. |
| CON-03 | Outdoor vineyard and greenhouse deployment, weatherproofing, wireless reliability, efficient field power, repeatable capture. | Forces device buffering/replay and field validation of sensor power and connection quality. Keep REST unless the field test triggers the D0 MQTT alternative. |
| CON-04 | Affordable and adaptable to changing region, crop, and climate. | Requires configurable zone rules and seasonal calibration. New crops require local data, model validation and potentially retraining; no zero-shot promise. |

## 6. Traceability matrix

**Status:** Designed = documented target behavior; In progress = a documented prototype covers part of the requirement, with integration or validation remaining; Planned = a required capability or acceptance target is pending. These statuses are not test results. The [planning milestone A10](Planning/Plan.md#2-milestones-list-assignment-6) lists a test-ID range but no case assignments. **TBD** means the Week 10 ID is not yet verified.

| Req ID | Description | Design Component (D0/D1/D2) | Status | Test Case |
|---|---|---|---|---|
| REQ-01 | Field, likelihood, window and reading time | D2 §2.1 I3 / F-2; dashboard composition in §3.4 | Designed | TBD |
| REQ-02 | Identify stale sensors; withhold current result | D2 §2.4 SENSOR_STATUS, EC-W2; F-2 | Designed | TBD |
| REQ-03 | Automated count and calibrated kg | D1 alg. 5; D2 Y-1, I6 GroupResult and calibration | In progress | TBD |
| REQ-04 | No output when all photos are unreadable | D2 Y-2, I6 FAILED, EC-Y1/Y9 | Designed | TBD |
| REQ-05 | Sign-in and role restrictions | D1 history/auth prototype; D2 §2.6 auth, EC-A1/A4 | In progress | TBD |
| REQ-06 | Deny next request after revocation | D2 §2.6 RoleUpdater, EC-A3 | Designed | TBD |
| REQ-07 | Buyer report for matching forecast | D2 Y-1 and I6; crop/harvest metadata open, §3.4 | Planned | TBD |
| REQ-08 | Missing buyer forecast shown as unavailable | D2 I6 nullable yield, EC-Y7; buyer report §3.4 | Planned | TBD |
| REQ-09 | Exact de-identified historical CSV | D0 DL-4; D2 Y-1, F-1; history/alias gaps, §3.4 | Planned | TBD |
| REQ-10 | Empty export message; no file | D1 empty history response; D2 trace table AC-05.2; §3.4 | Planned | TBD |
| REQ-11 | Future pest/disease risk by window | **ORPHANED:** D2 I4/I5 classify images; no forecast design | Planned | TBD |
| REQ-12 | Screen images and flag uncertainty | D1 alg. 1–3; D2 I4/I5 statuses, EC-D1/P1 | In progress | TBD |
| REQ-13 | Ordered counts by row and quality | D1 alg. 5–6; D2 Y-2 and I6 GroupResult | In progress | TBD |
| REQ-14 | Hold frost warning; gate all-clear | D2 F-2, L5/L6 and EC-F1/F2 | Designed | TBD |
| REQ-15 | Deduplicate and batch in-app alerts | D2 §2.6 notification feed, EC-N1–N4 | Designed | TBD |
| NFR-01 | Yield results in 60 minutes | D2 I6 queue/polling, EC-Y25; benchmark pending | Planned | TBD |
| NFR-02 | Zero unauthorized successful operations | D2 §2.6, EC-A3/A4; protect I4 links, §3.4 | Planned | TBD |
| NFR-03 | Alert text contrast and non-color cues | D2 F-2 states and quality labels; contrast pending | Planned | TBD |
| NFR-04 | No loss or duplicates after replay | D2 I2 upsert, EC-W3/W5 | Designed | TBD |
| NFR-05 | Correlate inputs and versions with results | D2 F-1/Y-1, §2.6 logs; frost version link open | Planned | TBD |
| CON-01 | Budget and reuse | D0 DL-6; D1 yield stack; D2 EC-Y25 | Designed | TBD |
| CON-02 | Access and audit trail | D0 DL-1/DL-4; D2 §2.6; gaps in §5 | Designed | TBD |
| CON-03 | Reliable field collection | D0 I1/I2, DL-5; D2 EC-W5; field validation pending | In progress | TBD |
| CON-04 | Affordable, adaptable design | D2 F-1 zone config and Y-1 mass calibration | Designed | TBD |

**Open trace:** REQ-11 requires dated event labels and a validated forecast model for each event. Bridget owns the feasibility decision (AQ-03). Until that decision, I4/I5 confidence stays a screening result. Partial export, protected media, accessibility, and provenance designs are tracked in §3 and §5, with owners and dates in the requirements document.

## 7. Advisor review record

**Informal review source:** [Planning/Plan.md, milestones and schedule](Planning/Plan.md#2-milestones-list-assignment-6) and its [risk register](Planning/Plan.md#6-risk-register). The plan names the project advisor as an A9 verifier and lists an advisor meeting as a dependency for A12/13.

| Record | Evidence and effect on this specification |
|---|---|
| Date | **2026-10-28 to 2026-10-30**, the documented A9 planning window. Oct 30 is the milestone due date. The plan does not record a separate meeting date. |
| Topics documented | A9 deliverable and verification scope; stale sensor readings and warning retention (R3); large-image processing and timeout risk (R4); pest image resolution (R1); local validation and model overfitting (R5). |
| Changes reflected in v1.0 | Added explicit stale-data and replay requirements (REQ-02, REQ-14, NFR-04); specified async yield submission and polling with a benchmark target (I6, NFR-01); distinguished image screening from unsupported forecast and localization (REQ-11–12); assigned local-validation follow-ups (AQ-01–03). |
| Review status | These are changes derived from the planning record. Advisor comments, requested changes, and approval are not separately recorded; confirm them at the next documented review. |

The [D2 peer design review notes](Design_Diagrams/Peer_Review_Notes.md) separately document PR-01–04 and their frost, notification, image-readability, and disease-confidence changes.
