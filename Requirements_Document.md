# Requirements Document

**OMID AI-Based Farm Disease, Pest, Yield, and Frost Detection System**\
Assignment 9 · Version 1.0 · 2026-11-11\
**Team:** Andrea Dall'Ara, Bridget Trout, Dang Luu, Cyrus Speicher

## 1. Version and change log

| Version | Date | Change | Author |
|---|---|---|---|
| 1.0 | 2026-11-11 | First numbered baseline. Mapped Week 4 stories to stable requirements, recorded measurable quality targets and Week 3 constraints, and identified open design gaps. Advisor verification pending. | Andrea Dall'Ara |

REQ, NFR, and CON identifiers are permanent. If an item is dropped, retain its row with a **Withdrawn** label and record the ID, reason, author, and date in the next log entry. Never recycle its ID. ChatGPT assisted with drafting and formatting from the team's source documents.

## 2. Purpose and scope

OMID helps specialty-crop growers make timely decisions using stationary sensor readings and crop imagery. The first field deployment targets a Cincinnati vineyard, with a greenhouse available for testing. The system supports human decisions about frost and crop health, estimates bunch counts and yield, and prepares reports for buyers and researchers.

The scope includes authorized access, data ingestion, frost warnings, image-based disease and pest screening, yield analysis, and controlled exports. Disease and pest screening evaluates the observed image; future-event forecasting is a separate requirement with a known design gap. Growers make and carry out all treatment decisions. The [technical specification](Technical_Specification.md) links the existing designs and records which parts are still planned.

## 3. Stakeholders and user goals

| Stakeholder | Week 4 goal |
|---|---|
| Farm owner (primary; US-01) | Know when frost, pests, and disease may affect a field so treatment can be targeted. |
| Farm worker (primary; US-02) | Obtain automated crop counts and yield estimates without manually counting the crop. |
| App administrator (secondary; US-03) | Enable accounts and manage access to fields and functions. |
| Crop buyer (downstream; US-04) | Use a readable yield forecast to plan purchases and distribution. |
| Agricultural researcher (downstream; US-05) | Compare long-term crop, yield, disease, pest, and frost records without receiving farm or account identifiers. |

Sources: [Week 4 stories and acceptance criteria](User_Stories.md), [Week 3 constraints](constraints_essay.md), and the [D2 gap and traceability notes](Design_Diagrams/D2_Detailed_Design.md#requirements-traceability-table).

## 4. Glossary

| Term | Definition |
|---|---|
| OMID USA | Project sponsor focused on affordable tools for smallholder farmers. |
| Smallholder farm / specialty crop | A small-scale farm; a specialty crop is a cultivated crop such as grapes, the crop used for this deployment. |
| Farm owner / farm worker | The grower responsible for field decisions and exports; a person authorized to collect data or run field analysis. These are stakeholder roles. |
| App administrator | A person authorized to enable accounts and manage permissions for an organization. |
| Crop buyer / agricultural researcher | A downstream recipient of a buyer forecast or a historical research export, respectively. Neither needs an application account to read the file. |
| Organization / site / field (zone) | The access boundary for a farm or farm group, a managed location within it, and a monitored subdivision of that location. A field is represented as a zone in the application. |
| Owner / admin / member / viewer | Application membership roles. The owner can govern ownership; admin can manage members, sites, and sensors; member can manage sites and sensors; viewer has no management flags. Each remains restricted to permitted organizations, fields, and functions. |
| Sensor reading / freshness limit | A timestamped measurement from a stationary sensor box; freshness is the maximum age allowed for that measurement to be used as current. A reading beyond that age is stale. |
| Bud-zone temperature / growth stage | Air temperature measured at bud or shoot height; the crop's stage of development, which informs frost sensitivity. |
| Critical temperature / exposure duration | The configured temperature threshold for a frost event and the minimum time at or below it to qualify as sustained exposure. |
| Forecast / forecast window | An estimate of a future event or harvest outcome; for a risk forecast, the time interval the estimate covers. |
| Frost alert / all-clear | A level from the frost engine describing current risk or event state. All-clear is its recovery state. A degraded alert retains prior warning information when new inputs cannot support a current estimate. |
| Capture / multispectral image | A set of images collected together for a field and date. Multispectral images record multiple wavelength bands, including bands beyond visible color. |
| RGB / JPEG / PNG / TIFF | RGB is visible red, green, and blue imagery. JPEG and PNG are supported yield image formats; TIFF files carry the multispectral bands used for disease analysis. |
| Screening / confidence / manual review | Analysis of what appears in an image. Confidence is a model score from 0 to 1, not severity or future likelihood. Manual review is a result requiring human interpretation. |
| Bunch / countable bunch / near row | A cluster of grapes; a bunch accepted by the row and quality rules; the surveyed row in the foreground rather than a background row. |
| Yield / mass calibration / harvest period | Estimated crop mass in kilograms, derived from count and the measured mean bunch mass for a zone and season. The harvest period is the date span to which a buyer forecast applies. |
| Anonymized field identifier / CSV | A stable export alias that omits the original field ID and precise location; a comma-separated values file that can be opened outside the app. |

## 5. Functional requirements

Source IDs refer to the [Week 4 stories](User_Stories.md). Entries derived from D2 or its proposals are identified in the source column.

| ID | Requirement | MoSCoW | Source story | Verification method |
|---|---|---|---|---|
| REQ-01 | The system shall show the affected field, risk likelihood, two-hour forecast window, and sensor timestamp for an authorized frost forecast request with fresh inputs and valid configuration. | Must | US-01 / AC-01.1 | Replay valid readings and inspect all four displayed fields. |
| REQ-02 | The system shall list every required sensor with missing or stale data and label the current prediction unavailable when any required sensor exceeds the configured freshness limit. | Must | US-01 / AC-01.2 | Age or remove two required sensor feeds; verify both appear and no old prediction is marked current. |
| REQ-03 | The system shall show a nonnegative integer crop count, field, capture date, and calibrated yield in kg for a successful analysis of readable supported photos with valid mass calibration. | Must | US-02 / AC-02.1 | Run a labeled capture with a known calibration; check the count type, metadata, and count × mean mass. |
| REQ-04 | The system shall explain the failure and prompt for supported images when zero submitted images can be read, and shall display no count or yield for that analysis. | Must | US-02 / AC-02.2 | Submit unsupported and corrupt-only sets and inspect the error and result view. |
| REQ-05 | The system shall authenticate enabled accounts with valid credentials and limit each account to the fields and functions granted by its assigned role. | Must | US-03 / AC-03.1 | Exercise each role against permitted and prohibited fields and actions. |
| REQ-06 | The system shall persist a saved permission revocation and deny the very next request for that function from the affected account, including an active session or a direct URL. | Must | US-03 / AC-03.2 | Revoke access without ending the session; repeat the restricted request and expect denial. |
| REQ-07 | The system shall let an authorized farm owner export a buyer report with crop, field, expected harvest period, estimated yield and unit, and generation date for a matching forecast, readable without an app account. | Should | US-04 / AC-04.1 | Export one known forecast and check the five fields and offline readability. |
| REQ-08 | The system shall label yield unavailable in a buyer report when no forecast matches the selected crop, field, and harvest period, without substituting another forecast or a zero value. | Should | US-04 / AC-04.2 | Export a missing selection and a matching zero-yield fixture; verify distinct outputs. |
| REQ-09 | The system shall export exactly the stored yield, pest, disease, and frost records in the authorized inclusive date range as a CSV with dates, crops, stable field aliases, record types, and values with applicable units, excluding names, contacts, precise locations, and account IDs. | Should | US-05 / AC-05.1 | Compare CSV rows with a four-type fixture across both date boundaries; inspect the allowlist and open offline. |
| REQ-10 | The system shall show “No records found for the selected date range” and generate zero files when the research query is empty, while allowing a different date range. | Should | US-05 / AC-05.2 | Request an empty range, verify the message and no download, then change the range. |
| REQ-11 | The system shall show likelihood, forecast window, field, and sensor timestamp for an authorized pest or disease forecast request when that event has a configured forecast model and fresh data. | Could | US-01 / AC-01.1 (forecast gap) | Use a time-window forecast fixture for each event; check output and distinguish it from photo confidence. |
| REQ-12 | The system shall label a supported image screening result with class and confidence when a class is scored, flag single-photo disease scores below 0.5 at the default cutoff for manual review, flag pest scores below 0.5 as uncertain, and distinguish zero qualifying vine tiles from a healthy finding. | Should | US-01 / D2 I4–I5 | Check scores on either side of each cutoff and a TIFF capture without vine tiles. |
| REQ-13 | The system shall report total, near-row, and countable bunch counts per requested group, with countable ≤ near ≤ total and each retained bunch satisfying the configured row and quality rules. | Should | US-02 / D2 proposed AC-02.3 | Compare stage totals against annotated near/far and full/sparse fixtures; record proxy errors. |
| REQ-14 | The system shall hold an active frost warning during stale or invalid input and permit an event-in-progress all-clear only after at least 30 minutes of stable or rising temperature at least 2 °F above the critical value and no decline in an available forecast. | Must | US-01 / D2 R-F3 | Replay active-event, sensor-loss, near-threshold, and recovery sequences; inspect transitions. |
| REQ-15 | The system shall send critical in-app alerts immediately for qualifying events, group other qualifying alerts into at most one digest per recipient per 15 minutes, and suppress repeats at the same severity. | Should | US-01 / D2 §2.6 | Use a controlled clock and repeated or escalating events; inspect the feed and batch count. |

## 6. Non-functional requirements

The numeric values below are acceptance targets, not measurements. NFR-01 formalizes the proposed D2 performance target. The test scenarios and thresholds added here require team and advisor confirmation.

| ID | Requirement | MoSCoW | Source story | Verification method |
|---|---|---|---|---|
| NFR-01 | The system shall make yield results available within 60 minutes of accepting an isolated run of up to 2,200 supported photos on the designated GPU worker, with no competing runs or fallback. | Should | US-02 / D2 NFR-PERF | Measure three 2,200-photo runs from acceptance to results available; all three must meet the target. |
| NFR-02 | The system shall allow zero unauthorized successful operations in a two-organization test covering API requests, direct asset URLs, and revoked active sessions. | Must | US-03 / AC-03.1–02; CON-02 | Test both tenants, missing credentials, and revocation; verify no protected data or changes are returned. |
| NFR-03 | The system shall render field alert text with a contrast ratio of at least 4.5:1 and provide at least one text label identifying each severity or unavailable state independently of color. | Should | US-01 / stakeholder map | Measure foreground/background pairs and inspect all alert states with color disabled. |
| NFR-04 | The system shall retain exactly one record for each of 10 distinct sensor readings replayed after a 60-second simulated store outage, even when each reading is retried twice. | Must | US-01 / AC-01.2; CON-03 | Interrupt the store, replay the buffered set, then compare stored composite keys and count. |
| NFR-05 | The system shall let an authorized operator trace each evaluated frost alert and yield run in the validation set to one request or cycle identifier, its input identifiers, and the configuration and model or rules version used. | Should | US-01, US-02 / CON-02 | Select three of each result type and recover the matching inputs and versions from records and logs. |

## 7. MoSCoW summary

- **Must:** REQ-01–06 and REQ-14; NFR-02 and NFR-04. These cover the core warning, counting, and access flows and their failure paths.
- **Should:** REQ-07–10, REQ-12–13, and REQ-15; NFR-01, NFR-03, and NFR-05. These cover reporting, interpretable screening, and quality targets.
- **Could:** REQ-11, if suitable forecast models and validation data become available. Its inclusion preserves the Week 4 goal without treating current photo classification as a forecast.
- **Won't for this release:** Automatic treatment or equipment control; offline dashboard/maps; SMS and email alert delivery; pest bounding-box localization; cross-photo bunch tracking; and zero-shot support for new crops. These items are outside the baseline even when listed as future planning work.

## 8. Inherited constraints

| ID | Week 3 constraint | Effect on the release |
|---|---|---|
| CON-01 | **Economic.** OMID USA's combined hardware/software budget is at most $2,000. Reuse the current drone and multispectral camera where practical and favor open or freely available tools. | Reserve funds for security, calibration, weatherproofing, and reliable collection before optional hardware or UI features. |
| CON-02 | **Security.** Restrict app access to authorized members and retain configuration and model versions, logs, error handling, and data-quality indicators. | Enforce access in the backend and make degraded or uncertain results identifiable and traceable. |
| CON-03 | **Environmental.** The Cincinnati vineyard and greenhouse require weatherproof sensor boxes, reliable wireless communication, efficient battery or solar power, and repeatable drone acquisition. | Verify collection outside the lab and tolerate intermittent connectivity; record capture conditions. |
| CON-04 | **Social.** Keep the system affordable for smallholder farmers and adaptable to new regions, crops, and climates. | Use configurable thresholds and calibration, and plan local validation and model adaptation when conditions change. |

## 9. Assumptions and open questions

Dates are proposed checkpoints, not records of approval. Review updates belong in the version log.

| ID | Assumption or open question | Owner | Checkpoint |
|---|---|---|---|
| AQ-01 | Confirm frost thresholds, reporting cadence and freshness by alert level, and the rule for mixed frost. Use the documented D2 rule set for initial replay. | Cyrus Speicher | 2026-11-18 |
| AQ-02 | Assume non-overlapping side-view photos. Confirm who weighs bunches, the sample size and season, the reference GPU, and the evaluation protocol. Report uncalibrated yield as unavailable. | Dang Luu | 2026-11-18 |
| AQ-03 | Confirm whether pest/disease forecast data and models are feasible. Decide how to retain event records for research and validate photo confidence thresholds. | Bridget Trout | 2026-11-18 |
| AQ-04 | Decide the source of crop and harvest metadata, research alias policy, and protected image access. Validate organization/site/zone checks and the history query limit. | Andrea Dall'Ara | 2026-11-18 |
| AQ-05 | Measure sensor power and network reliability in the vineyard; revisit the D0 MQTT fallback if HTTP fails field criteria. Confirm the numerical NFR targets and Week 10 mappings. | Andrea Dall'Ara and Cyrus Speicher | 2026-11-18 |
