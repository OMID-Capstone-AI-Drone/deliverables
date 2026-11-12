# Ethics and Responsible-Use Statement

**Project:** OMID AI-Based Farm Disease, Pest, Yield, and Frost Detection System
**Team:** Agri-AI. **Members:** Andrea Dall'Ara, Bridget Trout, Dang Luu, Cyrus Speicher
**Sponsor:** OMID USA. **Date:** November 11, 2026 (Assignment 11)

**Owners:**
- Section checkers:
  - Privacy and Security: Andrea (web app and gateway).
  - Licensing and Attribution: each component owner for their own rows.
  - AI-use disclosure: each member for their own rows.

**Cross-references:**
- Data items and interfaces come from:
  - D0: I1–I6 and the data-flow diagram;
  - D1: the yield data model, the API contract, and the build-versus-reuse table;
  - D2: Figures F-1, D-1, and Y-1, and §2.4 and §2.6.
- Week 9:
  - the storage schema in [Technical_Specification.md](Technical_Specification.md) §3.3, "Service and storage boundaries";
  - the stack table in Technical_Specification.md §4, "Technology stack";
  - requirement IDs (REQ, NFR, CON, AQ) from [Requirements_Document.md](Requirements_Document.md) v1.0.
- Constraints essay: [constraints_essay.md](constraints_essay.md).

**Code covered:**
- Team repository `OMID-Capstone-AI-Drone/deliverables` (design documents).
- Yield module `grapevision/` in `GrapeBunchDetection` (`dang-branch`, commit `c528e00`).
- The Flutter app, Firebase functions, disease service, and pest service are covered through their design documents (D1, D2, Tech Spec) and their owners' slides. They were not part of the git-history audit.

## 1. Privacy summary

**What we collect, and from whom.** The system is about vines, not people. Even so, four kinds of personal or sensitive data pass through it:
- account data of farm staff;
- user IDs attached to actions;
- people, faces, or location metadata that can appear in field photos;
- zone names and locations, which identify the farm and are commercially sensitive.

**How consent is obtained.** Using the app is not treated as consent. The mechanism is built in task T1, and the team approved its wording.
- **At account creation:** the user sees a notice before the account exists. The notice names every data item below, who can read it, and how long it is kept.
- **Explicit agreement:** the user must tick an unticked "I agree" box. The tick is stored as `consent_version` and `consent_at` on the member document.
- **Research exports (AC-05.1):** the farm owner gives a separate opt-in for each export.
- **Farm photos:** taken only with the farm owner's permission. Workers are asked to keep people out of the frame.

| Data item | Source (diagram arrow) | Purpose | Consent | Storage | Access | Retention and deletion |
|---|---|---|---|---|---|---|
| Account email and password | User sign-in (D2 §2.6) | Log in (US-03) | Sign-up notice | Firebase Authentication. Google stores a salted scrypt hash, never the password itself. | Firebase Auth only. Org admins see the email. | Until the account is deleted. A Cloud Function deletes the Auth user when the member is removed. |
| User ID, role, permission flags | Gateway to Firestore `organizations/{orgId}/members/{userId}` (§2.6) | Role checks on every request (AC-03.1, AC-03.2) | Sign-up notice | Firestore, encrypted at rest by Google by default | Gateway service account and users with `canManageMembers` | Deleted with the membership |
| `changed_by`, `requested_by`, `user_id` in logs | Gateway (`ZONE_CONFIG_HISTORY`, `YIELD_RUN`, JSON log lines, §2.6) | Audit trail of who changed a threshold or started a run (NFR-05) | Sign-up notice | Firestore, per-run `run.json`, service logs | Owners and admins. Never in exports (D2 AC-05.1). | Logs: 90 days, enforced by log-bucket retention. Config history: for the life of the zone. |
| Org, site, zone IDs and names (e.g. `West_Zone2`) | User config; capture folder name (D1 `CAPTURE`) | Tenant isolation (`org_id`); show the field on results (AC-02.1) | Farm-owner agreement with OMID | Firestore, BigQuery, run CSVs | Members of that org only (gateway `org_id` filter) | Replaced by a stable `field_alias` in research exports (REQ-09, AQ-04) |
| Sensor readings (temperature, RH, wind, leaf wetness, soil temperature and moisture, battery, timestamp) | Sensor boxes → I2 `POST /api/v1/readings` | Frost and disease risk (I3, I4). History charts. | Farm owner, when the boxes are installed | BigQuery `SENSOR_READING` | Gateway and history function, filtered by org | 3 years, through BigQuery partition expiration |
| Sensor key | Sensor box header `X-Sensor-Key` (I2) | Authenticate the box | n/a (device) | Only a hash is stored (`SENSOR_STATUS.key_hash`) | Gateway | Revoked when the box is removed |
| Sensor `last_seen`, state | Gateway monitor (§2.4) | Stale-data warnings (AC-01.2) | n/a | Firestore `sensors/{id}` | Org members | For the life of the sensor |
| Zone thresholds and history | Farmer → `PUT /api/v1/zones/{id}/config` | Frost rules (DR-09) | Sign-up notice | Firestore `ZONE_CONFIG`, `ZONE_CONFIG_HISTORY` | Writes need `canManageSites`; members read | For the life of the zone |
| Frost alerts, notification events | I3 result → gateway (Figure F-1, §2.6) | Warn the farmer | Sign-up notice | Firestore | Org members | 1 year, through a Firestore TTL policy on `expire_at` |
| Weather forecast | External weather service → I3 (provider not yet chosen, Tech Spec §3.3) | All-clear check (Level 6) | n/a (public data). Only zone and time are sent. | Not stored (D2 §2.1) | Frost engine, in memory | Discarded after each cycle |
| Multispectral TIFFs (5 bands) | Drone → I1 → I4 `/process-capture` | Disease heatmap (D1 algorithm 2) | OMID and farm-owner agreement | Service disk, then a private Cloud Storage bucket | Gateway and disease service. D2 §2.2's public URLs are replaced by permission-checked downloads (Tech Spec §3.3, NFR-02, AQ-04, due November 18). | Raw TIFFs: deleted after 30 days by a Cloud Storage lifecycle rule |
| Disease outputs (overlay, heatmap, JSON summary) | I4 → Firestore `captures` | Show results; research records | As above | Private Cloud Storage and Firestore | Org members via the `org_id` filter | 1 year (lifecycle rule). Event records are kept for research exports (AQ-03). |
| Single leaf or insect photo | Phone → I4 `/predict`, I5 `/pests_predict` | One-photo diagnosis | Sign-up notice | **Not stored.** Only the result is kept, as an event record (D2 §2.2, §2.3; AQ-03). | Service memory only | The photo is discarded after the response |
| Side-view yield photos (JPEG or PNG) | Farm worker's phone → I1 → I6 | Bunch counts (D1 algorithm 5) | Farm permission; people kept out of frame | Capture folder plus SHA-256 in `MEDIA_RECORD`; annotated copies in `outputs/` | Yield service; org members via the web app | Photos and annotated copies are deleted 30 days after the run. Counts and run records are kept. |
| EXIF metadata in photos (GPS, device, time) | Inside uploaded files | Only Orientation is needed (D1 `MEDIA_RECORD.width/height`) | — | **Removed:** I1 strips every EXIF field except Orientation on upload | — | Never stored |
| Yield runs, issues, totals, calibration | I6 (Figure Y-1) | Counts, yield, traceability | Sign-up notice | Flat files per run; summary copied to Firestore `yield_runs` | Org members (gateway checks `org_id`) | Kept for research exports (AC-05.1); anonymized on export |
| Research CSV and buyer report | Owner export (AC-04.1, AC-05.1) | Share with a buyer or researcher | Owner's opt-in for each export | Downloaded file, outside our control | The recipient | Built from a fixed column allowlist, `date,crop,field_alias,record_type,value,unit` (Tech Spec §3.4, REQ-09). No names, contact details, precise locations, or account IDs. |
| Unlabeled vineyard images from our field work | Team field captures | Future labeling and validation (T11) | Project vineyard permission | OneDrive folder `04_Measurement_&_Data` | Team members with access to the shared folder | Handed over to OMID or deleted at project end |
| 43 `LiveImages` farm photos and the 10,010-image public set | Team development data (`GrapeBunchDetection/LiveImages`, `dataset/`) | Training and evaluation | Farm photos: taken at the vineyard of the professor who manages the project, shared for project use. Public set: CC BY 4.0. | Farm photos: in the public `GrapeBunchDetection` repos. Public set: team laptops only. | Anyone (public repo) | Kept for the life of the project |

**Fields removed or never collected because we could not justify them:**
- **GPS and precise farm location.** The yield module stores none (D2 AC-05.1), and exports exclude it.
- **EXIF metadata other than Orientation.** It is stripped at upload.
- **Server-local directory paths** in the I4 and I6 responses (removed in D2 §2.7).
- **`requested_by`, names, and contact details** in research exports.
- **Phone numbers, and email addresses passed to outside processors.** Plan.md task T4 proposed SendGrid email and Twilio SMS, which would have needed both. The Requirements Document (§7) marks SMS and email delivery "Won't for this release", so alerts stay in the in-app feed (REQ-15).

**Diversity and cultural considerations:**
- **Plain, translatable language.** OMID serves smallholder farmers in new regions (constraints essay, Social). The consent notice and alert text are written in plain language and kept as separate translatable strings in the Flutter app.
- **Readable in the field.** NFR-03 requires text contrast of at least 4.5:1 and a text label on every severity and "unavailable" state. Outdoor and color-blind users can read an alert without relying on color.
- **People in photos.** Farm workers who appear in photos may not have accounts. That is why consent covers the farm, and why workers are asked to stay out of the frame.

**When the project ends** (M5, April 20, 2027):
- The Firebase project and its data are handed over to OMID.
- Team members delete their local copies of farm photos and exports.
- The team's own service keys and member accounts are revoked.

## 2. Security summary

**Default credentials:**
- There is no shipped admin account. Every user signs in through Firebase Authentication, and OMID creates the first owner.
- **Sensor boxes:**
  - each box gets its own key, generated at installation;
  - only a hash of the key is stored (D2 §2.4);
  - keys are never shared between boxes.
- No factory passwords remain on the drone controller or the sensor-box firmware (confirmed with the hardware team).

**Unauthenticated endpoints:**
- **Finding:** D1 showed that `/predict`, `/pests_predict`, and `/process-capture` had no authentication checks.
- **Fix:** D2 §2.6 moves all three behind the gateway:
  - every user request needs a Firebase ID token, which expires after 1 hour, and the client refreshes it;
  - membership is re-read on every call, so a revoked role gets `403` on the next request (AC-03.2);
  - authentication fails closed (EC-A4);
  - modules accept only calls that carry `X-Internal-Service-Key`;
  - browser-supplied copies of the internal headers are not trusted (Tech Spec §3.2).
- **Left open on purpose:** the `/health` routes, which return no data.
- **Status:** built in task T1 (November 19–25).
- **Verification:** NFR-02 (a Must requirement) allows zero unauthorized successful operations. Its test covers two organizations, missing credentials, direct asset URLs, and revoked sessions.

**Secrets in source control:**
- We searched the full git history of both repositories in this audit, not only the current files, using the three commands below.
- No committed secrets were found.

```
git log --all -p | grep -inE "api[_-]?key|secret|password|passwd|token|aws_access|private key|BEGIN RSA|BEGIN OPENSSH|roboflow|sk-|ghp_|neon|postgres(ql)?://|DATABASE_URL"
git log --all --diff-filter=A --name-only --pretty=format: | grep -iE "\.env|secret|key|cred|\.pem"
git log --all -p | grep -E "AIza[0-9A-Za-z_-]{35}|hf_[A-Za-z0-9]{30,}|AKIA[0-9A-Z]{16}"
```

| Repository | Commits | Hits per command (1 / 2 / 3) | Real secrets | What the hits were |
|---|---|---|---|---|
| deliverables | 44 | 15 / 0 / 0 | 0 | Design prose about bearer tokens; "task-specific" matched `sk-` |
| GrapeBunchDetection | 6 | 93 / 0 / 0 | 0 | 89 hits inside base64 notebook images; one `GH_TOKEN: ${{ github.token }}` (the Actions runtime token, not a stored value); three "task-specific" |

**Where secrets live:**
- The service key and any third-party keys are kept in Google Secret Manager or Cloud Functions config, never in a repository.
- Rotation of the service key is not designed yet (D2 traceability, AC-03.1).

**HTTPS:**
- All gateway routes use HTTPS, and I4–I6 use internal HTTPS (Tech Spec §3.1, §3.2).
- Firebase Functions serve HTTPS only.
- The Docker disease and pest services sit behind HTTPS with managed TLS certificates.

**Rate limiting:**
- The gateway applies a per-user request limit, and Firebase App Check rejects clients that are not the real app.
- Upload size is capped: oversized uploads get `413 FILE_TOO_LARGE` (D2 §2.4).

**Issues this audit found in `GrapeBunchDetection`** (fixes owned by Dang):
- **No root `.gitignore`.** Running `git add .` could commit data or model weights. `grapevision/.gitignore` already blocks `*.pt` and `outputs/`.
- **An unneeded package.** The ML notebook runs `pip install ... plt`. `plt` is not matplotlib. It is a separate third-party PyPI package: a single-maintainer MIT shim that forwards to `matplotlib.pyplot` (v0.2.0, October 2025). It is an unvetted dependency we don't need.
- **Unpickling.** The ML notebook loads cache files with `pickle.load` and `joblib.load`. That is safe only for files the team created itself.
- **Narrow key check.** The pre-commit `detect-private-key` hook covers only `grapevision/`.

| Self-audit question | Answer |
|---|---|
| Any default or shared credentials? | No. There are no default users, each sensor has its own hashed key, and no factory passwords remain on the hardware. |
| Does every data endpoint require authentication? | Yes: the gateway checks a token, and modules require the service key. Only the health checks are open. Verified by the NFR-02 two-tenant test. |
| Any secret anywhere in git history? | None found in the two repositories searched (table above). The app, functions, disease, and pest repositories were not searched in this audit. |
| Where do secrets live? | Secret Manager or Functions config, never a repository. |
| Token lifetime? | The Firebase ID token lasts 1 hour. The refresh token lasts until it is revoked or the user is disabled. Service-key rotation is not designed yet. |
| HTTPS everywhere? | Yes, for every route (Tech Spec §3.1, §3.2), including the Docker services. |
| Rate limiting? | Yes: a per-user limit at the gateway, plus Firebase App Check. |
| Least privilege? | Yes. Roles are owner, admin, member, and viewer, with permission flags (D1, D2 §2.6). Service accounts get only the roles they need. |
| Do logs leak secrets or PII? | No. Logs carry `user_id`, never tokens or keys, and long messages are truncated (EC-L1). |
| Is any data publicly exposed? | Only the `LiveImages` vineyard photos, which are shared for the project with permission. Multispectral outputs move to permission-checked downloads (AQ-04). |

## 3. Licensing summary

**Our own license: MIT**, agreed by the team.
- No repository has a LICENSE file yet. One will be added to each repository: Dang for GrapeBunchDetection, Andrea for the app repositories.
- MIT is compatible with every permissive row below.
- **Ultralytics (AGPL) caveat:** MIT code may be combined into an AGPL work. Any deployment that runs Ultralytics as a network service must therefore offer the source of the whole combination under AGPL-3.0.
- **Closed deployment:** if OMID needs one, the detector is replaced by RT-DETR (Apache-2.0) behind the same interface (D1 license note).
- **Consistency:** this changes no position in the constraints essay. MIT matches the essay's choice to "favor open or freely available software".

**How the license values were found.**
- **Versions:**
  - Python: the `>=` minimums in `grapevision/pyproject.toml`. `requirements.txt` pins no versions, which is itself a reproducibility gap.
  - App stack (Flutter, FlutterFire, Node.js, Firebase npm packages): the newest stable releases on November 11, 2026, from the Flutter release feed, pub.dev, nodejs.org, and npm.
- **Licenses:**
  - Python packages: read from installed package metadata (`importlib.metadata`) and checked against the SPDX list.
  - npm packages: read with `npm view`.
  - Flutter plugins: read from pub.dev.
  - Datasets and the depth model: checked on their Zenodo and Hugging Face pages on November 11, 2026.
- **Ultralytics:** declares `"AGPL-3.0"` with the classifier "AGPLv3 or later", so we record it as AGPL-3.0-or-later.

| Name | Version | SPDX | Type | Obligation on us |
|---|---|---|---|---|
| Ultralytics (YOLO11 code and `yolo11m.pt` weights) | ≥ 8.3 | AGPL-3.0-or-later | **Strong copyleft (network)** | Offer the full source of any modified network service to its users, or buy an Enterprise license |
| SAHI | ≥ 0.11.20 | MIT | Permissive | Keep the copyright notice |
| PyTorch, torchvision | ≥ 2.2 | BSD-3-Clause | Permissive | Keep the notice; no endorsement |
| Hugging Face transformers | ≥ 4.45 | Apache-2.0 | Permissive | Keep the notice; state changes; keep their NOTICE file |
| Depth Anything V2 **Small** weights | HF `-hf` | Apache-2.0 | Permissive | Same as transformers. The Base and Large weights (CC-BY-NC-4.0) are excluded. |
| NumPy | ≥ 1.24 | BSD-3-Clause (bundles 0BSD, MIT, Zlib) | Permissive | Keep the notice |
| Pillow | ≥ 10.0 | MIT-CMU | Permissive | Keep the notice |
| PyYAML | ≥ 6.0 | MIT | Permissive | Keep the notice |
| tqdm (notebooks) | unpinned | MPL-2.0 AND MIT | **Weak copyleft (file-level)** | Share changes to tqdm's own files if we modify and distribute them. We don't modify them. |
| matplotlib (notebooks) | unpinned | LicenseRef-Matplotlib (PSF-style) | Permissive | Keep the notice |
| scikit-learn, scikit-image, SciPy, pandas, seaborn, joblib, ipywidgets, Jupyter | unpinned | BSD-3-Clause | Permissive | Keep the notice |
| opencv-python, XGBoost | unpinned | Apache-2.0 | Permissive | Keep the notice and their NOTICE file |
| SHAP, grad-cam | unpinned | MIT | Permissive | Keep the notice |
| pytest, pytest-cov, ruff, mypy, build, pre-commit (dev only) | see pyproject | MIT | Permissive | None (not shipped) |
| GitHub Actions `checkout@v5`, `setup-python@v6`, `upload-artifact@v4` | pinned tags | MIT | Permissive | None (CI only) |
| CVAT (labeling tool) | hosted | MIT | Permissive | None (a tool, not shipped) |
| Flutter SDK and Dart (dashboard, Tech Spec §4) | 3.50.0 (Dart 3.14.0) | BSD-3-Clause | Permissive | Keep the notice |
| FlutterFire `firebase_core`, `firebase_auth`, `cloud_firestore` | 5.1.0, 7.0.1, 7.1.0 | BSD-3-Clause | Permissive | Keep the notice |
| Node.js runtime (gateway functions) | 26.13.1 LTS | MIT | Permissive | None (runtime, not shipped) |
| `firebase-functions` (npm) | 7.4.0 | MIT | Permissive | Keep the notice |
| `firebase-admin` (npm) | 14.5.0 | Apache-2.0 | Permissive | Keep the notice and their NOTICE file |
| Disease models: a segmentation step feeding a RandomForest classifier, plus a separate ResNet (Bridget's 10/02 slides; D1 also lists a ViT) | Trained by the team | Our own (MIT) | — | None for our weights. The pretrained base checkpoint is inherited (see the pest row). |
| Botrytis UAV multispectral dataset, Tomiño, Spain (Zenodo, DOI 10.5281/zenodo.7064894, all versions; v2 10.5281/zenodo.7383601). The only disease training set. | v2, 16,504 images | CC-BY-4.0 | Permissive (attribution) | Credit the authors, link the license, state changes |
| Pest classifier (ResNet-18 per D1 and Tech Spec §4; ImageNet normalization, so an ImageNet-pretrained backbone) | Trained by the team | torchvision code BSD-3-Clause; ImageNet-pretrained weights under ImageNet terms | Permissive code; research-use data terms | Keep the notice. **Training data provenance is unknown:** the data paths point to a former member's machine. |
| Candidate multispectral and RGB datasets (Flavescence Dorée, Esca; slides 10/09, 10/16) | Not used | — | — | Licenses are checked before any of them is used |
| Grapevine Bunch Detection Dataset (Zenodo, DOI 10.5281/zenodo.7717055) | v2, 10,010 images | CC-BY-4.0 | Permissive (attribution) | Credit the author, link the license, state changes |
| CERTH Grape Dataset (Zenodo, DOI 10.5281/zenodo.10777647) | v2, 2,502 images | CC-BY-4.0 | Permissive (attribution) | Same as above |
| Embrapa WGISD | — | CC-BY-NC-4.0 | Non-commercial | **Dropped**, so OMID can use the model commercially |
| DejaVu Sans (figure font), Pillow default font | bundled | Bitstream-Vera / per Pillow | Permissive | Keep the notice if the font is redistributed |
| Weather forecast source (I3) | not yet chosen (Tech Spec §3.3) | — | — | Its terms will be recorded here when it is chosen |
| Firebase, BigQuery, Cloud Storage, Google Colab | service | Proprietary terms of service | Service | Follow Google's terms; no redistribution |

**Copyleft decisions:**
- **Ultralytics (AGPL):** accepted for the academic phase (D1, NFR-ECON). A fallback detector is designed, and Tech Spec CON-01 requires a license review before any closed deployment.
- **tqdm (MPL-2.0):** accepted, because it is unmodified and used only in notebooks.
- **WGISD (non-commercial):** removed.

## 4. Attribution list

The same list is in [NOTICE.md](NOTICE.md), which is linked from [README.md](README.md) §5. NOTICE.md will also be copied into GrapeBunchDetection.

**Libraries:**
- Ultralytics: https://github.com/ultralytics/ultralytics
- SAHI: https://github.com/obss/sahi
- PyTorch: https://github.com/pytorch/pytorch
- transformers: https://github.com/huggingface/transformers
- Depth Anything V2 Small: https://huggingface.co/depth-anything/Depth-Anything-V2-Small-hf
- NumPy: https://numpy.org
- Pillow: https://python-pillow.org
- PyYAML: https://pyyaml.org
- grad-cam: https://github.com/jacobgil/pytorch-grad-cam
- scikit-learn, scikit-image, SciPy, pandas, matplotlib, seaborn, XGBoost, SHAP, OpenCV, tqdm: see each project's PyPI page
- CVAT: https://github.com/cvat-ai/cvat
- Flutter: https://github.com/flutter/flutter
- FlutterFire: https://github.com/firebase/flutterfire
- firebase-functions: https://github.com/firebase/firebase-functions
- firebase-admin: https://github.com/firebase/firebase-admin-node

**Code we build on:**
- **The `GrapeBunchDetection` notebooks.** Hoang Nhat Duy Le wrote both: the traditional-ML pipeline and the YOLO11 + SAHI pipeline (commits 0efe488–0906377, upstream `hoangnhatduyle/GrapeBunchDetection`). `grapevision/` refactors their method into a package.
- **Tutorials and other repositories.** No code in `grapevision/` was copied from a tutorial or another repository. The ML notebook cites YouTube explainers for CLAHE, Gabor, LBP, HOG, and Canny as background reading.
- **Other components.** Andrea, Bridget, and Cyrus report no code adapted from documentation, Stack Overflow, or other repositories in the app, frost, disease, or pest components.

**Datasets:**
- **Grapevine Bunch Detection Dataset.** I. Pinheiro (INESC TEC), Zenodo, 2023, v2. DOI 10.5281/zenodo.7717055. CC BY 4.0.
  - Contents: 720×540 images with YOLO-format bunch boxes, split into train, val, and test.
  - Collection method: not stated on the record.
- **CERTH Grape Dataset.** A. Blekos, K. Chatzis, M. Kotaidou, T. Chatzis, V. Solachidis, D. Konstantinidis, K. Dimitropoulos (CERTH), Zenodo, 2024, v2. DOI 10.5281/zenodo.10777647. CC BY 4.0.
  - Collection: 2,502 iPhone 11 Pro photos of a Crimson Seedless vineyard, taken during the 2022–2023 season, at 2160×3840.
  - The team converted its COCO annotations to YOLO format.
  - Cite as *Agronomy* 2023, 13, 1995.
- **Botrytis UAV multispectral dataset** (disease training set). S. Vélez, M. Ariza-Sentís, J. Valente (Wageningen University & Research), Zenodo, 2022, DOI 10.5281/zenodo.7064894. CC BY 4.0. Described in *Data in Brief* 46 (2023) 108876, doi:10.1016/j.dib.2022.108876.
  - Collection: MicaSense RedEdge 3 five-band camera (1280×960 per band) on a DJI M210 drone, flown at 30 m over a 1.06 ha Loureiro vineyard in Tomiño, Pontevedra, Spain, on September 16, 2021, at 0°, 30°, and 45° angles.
  - 16,504 images, plus GPS locations of the diseased bunches and ground control points.
- **Pest training images.** Prepared by a former team member; source not recorded.
- **Team vineyard images.** Unlabeled field captures in the OneDrive folder `04_Measurement_&_Data`.
- **West Zone 2 capture (September 11, 2026) and LiveImages.** Phone photos from the vineyard of the professor who manages the project, shared with the team for project use. LiveImages arrived through Messenger, which explains the `_n.jpg` filenames.

**Non-code assets:**
- Fonts: DejaVu Sans (bundled with matplotlib) and Pillow's default font.
- No other icons, fonts, or images are used.
- `team_availability.png` was made by the team.

## 5. AI-use disclosure

> "Generative AI tools may be used for drafting and code assistance but not for the design decisions or written analysis that are assessed in this course." (Course syllabus)

**Sources.** The rows were rebuilt from three places:
- the AI attribution sections in D0, D2, User_Stories, the Requirements Document, the Technical Specification, and Plan.md;
- the `Co-Authored-By` trailers in git history;
- each member's own account.

| Tool | Used by | Task | Column | How verified |
|---|---|---|---|---|
| Gemini | Cyrus Speicher | Style and layout of the D0 Mermaid block and data-flow diagrams | Generally OK | Rendered and checked against the component and interface tables |
| Gemini | Bridget Trout | Style of the stakeholder-map Mermaid (User_Stories) | Generally OK | Rendered and checked against the stakeholder list |
| Gemini | Cyrus Speicher | Style of the D2 frost ER and state-machine Mermaid | Generally OK | Rendered and checked against the rule table and edge cases |
| Gemini | Cyrus Speicher | Table formatting and WBS layout in Plan.md | Generally OK | Totals and dates checked by hand |
| Claude | Dang Luu | Drafting and editing the wording of the D0 decision log (DL-1 to DL-6) | Not a substitute for (design rationale) | The decisions and alternatives came from team meetings. Claude only drafted the wording, which the team reviewed. |
| Claude | Andrea Dall'Ara | Consolidating and revising the D2 draft (glue components, edge-case table, traceability, rationale) | Not a substitute for (design rationale and analysis) | Each component owner made their own design decisions and checked their own section |
| ChatGPT | Andrea Dall'Ara | Drafting and formatting the Requirements Document and Technical Specification from the team's source documents | Drafting: Generally OK. Requirement targets and trace analysis: Not a substitute for. | The team set the NFR targets. Andrea checked the traceability matrix row by row against D0–D2. |
| Claude Code | Dang Luu | Code assistance for the `grapevision/` yield package | Generally OK (code assistance) | Reviewed by Dang. CI runs pytest with a 90% coverage gate, ruff, and strict mypy on every pull request. |
| Claude Code | Dang Luu | Drafting this statement; running the git-history secret search and the license lookups | Drafting: Generally OK. Judgments: Not a substitute for. | Each judgment (license, retention periods, consent, removals) was made by the team. Section owners reviewed their rows. |
| ChatGPT | Andrea Dall'Ara | Code assistance for the web app and gateway | Generally OK (code assistance) | Reviewed line by line by Andrea and tested in the component |
| ChatGPT | Bridget Trout | Code assistance for the disease and pest pipelines | Generally OK (code assistance) | Reviewed line by line by Bridget and checked on validation images |
| Gemini | Cyrus Speicher | Code assistance for the frost engine | Generally OK (code assistance) | Reviewed line by line by Cyrus and checked against the frost rule table |

I confirm that the architecture, data model, priorities, and design rationale in our D0–D2 documents and in this statement were the team's own.

- Andrea Dall'Ara: ____________________ (date ______)
- Bridget Trout: ____________________ (date ______)
- Dang Luu: ____________________ (date ______)
- Cyrus Speicher: ____________________ (date ______)
