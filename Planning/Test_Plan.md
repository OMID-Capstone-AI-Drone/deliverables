# Assignment 10: Test Plan

**Project Title:** OMID AI-Based Farm Disease, Pest, Yield, and Frost Detection System

**Team Members:** Andrea Dall'Ara, Bridget Trout, Dang Luu, Cyrus Speicher

---

# 1. Scope
What is being tested this cycle, by requirement ID and component, and what is not. The out-of-scope list names deferred Won't and Could features, known limitations, and third-party components you will not retest.

|Component|Requirement ID| Owner | Scope
|-|-|-|-
|Frost Prediction Engine|REQ-01, 02, 14|Cyrus|In Scope
|Yield Estimation Module|REQ-03, 04, 13|Dang| In scope
|Authentication and Access Control | REQ-05, 06, 02 | Andrea | In scope
|Buyer Reporting | REQ-07, 08 | Andrea | In scope
|Research Data Export | REQ-09, 10 |Andrea | In scope
|Disease and Pest Screening | REQ-12|Bridget|In scope
|Notification and Alert Management | REQ-15 | Andrea/Cryus
|System Performance | NFR-01 |Dang|In scope
|Accessibility and Usability | NFR-03| Andrea| In scope
|Sensor Reliability |NFR-04|Andrea/Cyrus|In scope
|Traceability and Logging | NFR-05|Everyone|In scope
|Automated decisions like pesticide applicaions or equipment control | NA| NA|Won't
|SMS and email alert delivery| NA|NA|Won't
|Offline dashboard|NA|NA|Won't
|Pest bounding-box localization|NA|NA|Won't
|Cross-photo bunch tracking|NA|NA|Won't
|Zero-shot support for new crops|NA|NA|Won't
|Pest or disease forecast|REQ-11|NA|Could

# 2. Verification and validation strategy

## 2.1 Validation
The following requirements will be verified by the development team:

|Requirement ID|Verification|
|-|-|
|REQ-01|Frost forecast output and required info|
|REQ-02|Missing and stale sensor detection|
|REQ-03|Crop count and calibrated yield calculation|
|REQ-04|Unsupported and unreadable image handling|
|REQ-05|Account auth and role-based access|
|REQ-06|Permission revocation|
|REQ-07|Buyer report generation|
|REQ-08|Missing forecast handling in buyer reports|
|REQ-09|Research CSV completeness|
|REQ-10|Empty research query handling|
|REQ-12|Disease and pest classification confidence|
|REQ-13|Bunch counts and satisfying the configured row and quality rules|
|REQ-14|Frost warning held until all-clear conditions met
|REQ-15|Critical alerts sent and repeats suppressed
|NFR-01|2,200 image processing within 60 minutes
|NFR-02|Prevention of unauthorized operations
|NFR-03|Alert text contrast|
|NFR-04|Sensor reading persistence and duplicate prevention
|NFR-05|Frost and yield processing traceability

## 2.2. Validation

The following requirements will receive stakeholder validation:

|REQ-01|Frost forecasts provide farmers with understandable sensor information and predictions
|REQ-03|Crop counts and yield estimates are useful
|REQ-05|Users can access appropriate fields and functions
|REQ-07|Buyer reports provide useful information
|REQ-09|Research exports provide useful and anonymized agriculture data
|REQ-12| Disease and pest screening results and uncertanty are understanable
|REQ-14|Frost warnings provide growers with dependable alerts
|REQ-15|Alerts are useful and not excessive
|NFR-03|Alerts are readable

## 2.3 Test Pyramid 

|Layer|Estimated Count|Coverage Focus
|-|-|-
|Unit testing|40|Individual functions, thresholds, permissions
|Integration testing|20|Sensor ingestion, storage, inference pipelines
|System testing|15|Complete app workflows
|Acceptance testing|7|Processing time, retry behavior

## 2.4 Metrics
- Frost: *F, minutes, timestamp
- Disease: Accuracy, precision, recall, confusion matrix
- Pest: Accuracy, precision, recall, confusion matrix
- Yield: Count MAE, percent error

# 3. Success Criteria 

|Metric|Target Threshold|Measurement Method|Requirement
|-|-|-|-
|Frost forecast completeness|100% of required fields displayed|Check fields|REQ-01
|Stale sensor detection|100% of stale/missing sensor identified|Simulate missing and aged readings|REQ-02
|Crop count validity|Nonnegative integer count|Check output|REQ-03
|Unsupported image handling|No count or yield generated|Simulate unreadable image sets|REQ-04
|Unauthorized operations|Zero unauthorized successful operations|Two-organization access testing|REQ-05, NFR-02
|Permission revocation|Next restricted request denied|Test active session after revocation|REQ-06
|Buyer report completeness|All five required report fields present|Inspect exported report|REQ-07
|Missing buyer forecast|Unavilable label with no subsituted zero values|Test unmatched features|REQ-08
|Research export privacy|No names, contacts, precise, locations, or account ids|Inspect CSV|REQ-09
|Empty research query|Displays correct message and zero files|Query an empty date range|REQ-10
|Disease confidence|Correct flagging and manual-review below 0.5|Test labeled confidence fixtures|REQ-12
|Bunch count consistency|Countable <= near-row <= total|Compare pipeline stage outputs|REQ-13
|Frost all-clear|>= 30 minutes stable/rising and >= 2*F above critical temperature|Replay recovery sequence and inspect transitions|REQ-14
|Alert batching|At most one digest per recipient per 15 minutes|Use a controlled clock and repeated or escalating events; inspect the feed and batch count
|Yield processing time|All three 2,200-photo runs complete within 60 minutes| Measure accepted-to-result time|NFR-01
|Accessibility contrast| At least 4.5:1|Contrast measurement tool|NFR-03
|Sensor recovery|10 sensor readings replayed after a 60-second simulated store outage| Interrupt the store, replay the buffered set, then compare stored composite keys and count|NFR-04
|Result traceability|100% of sampled runs traceable|Select three of each result type and recover the matching inputs and versions from records and logs|NFR-05

# 4. Test Cases

## 4.1 Frost Detection and Sensor Validation
|ID|Description|Steps|Expected Result|Status
|-|-|-|-|-|
| TC-001 | REQ-01: Valid frost forecast | 1. Load fresh sensor readings. 2. Submit a frost forecast request. 3. Open the forecast result. | Field, risk likelihood, two-hour forecast window, and sensor timestamp are displayed. | Not Run |
| TC-002 | REQ-01: Multiple fields | 1. Load valid readings for two fields. 2. Request a forecast for each. 3. Compare displayed field information. | Each forecast identifies the correct field and associated readings. | Not Run |
| TC-003 | REQ-01: Missing forecast inputs | 1. Remove a required sensor reading. 2. Request a frost forecast. | No unsupported current prediction is displayed. The missing input is identified. | Not Run |
| TC-004 | REQ-02: Stale sensor reading | 1. Load a reading older than the configured freshness limit. 2. Request a forecast. | The sensor is identified as stale and the prediction is unavailable. | Not Run |
| TC-005 | REQ-02: Multiple missing sensors | 1. Remove two required sensor feeds. 2. Request a forecast. | Both missing sensors are listed and the prediction is unavailable. | Not Run |
| TC-006 | REQ-02: Freshness boundary | 1. Load a reading exactly at the freshness boundary. 2. Load another just beyond it. 3. Compare results. | Freshness handling follows the configured limit consistently. | Not Run |
| TC-007 | REQ-14: Active frost warning | 1. Replay readings below the critical temperature. 2. Trigger a frost event. 3. Continue the event sequence. | The frost warning remains active while the event is in progress. | Not Run |
| TC-008 | REQ-14: Sensor failure during warning | 1. Establish an active frost warning. 2. Interrupt sensor input. 3. Inspect the warning state. | The warning remains active and transitions to a degraded state rather than clearing. | Not Run |
| TC-009 | REQ-14: Recovery threshold | 1. Establish an active event. 2. Replay temperatures that remain at least 2°F above the critical threshold for 30 minutes. 3. Ensure the forecast does not decline. | The system permits all-clear only after all recovery conditions are satisfied. | Not Run |

## 4.2 Yield Estimation

| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-010 | REQ-03: Successful yield analysis | 1. Upload readable supported photos. 2. Load valid mass calibration. 3. Run yield analysis. | A nonnegative integer count, field, capture date, and calibrated yield in kg are displayed. | Not Run |
| TC-011 | REQ-03: Yield calculation | 1. Load a fixture with a known bunch count and mean mass. 2. Run analysis. 3. Compare calculated yield with the reference calculation. | Yield equals count multiplied by mean bunch mass within 0.01 kg rounding tolerance. | Not Run |
| TC-012 | REQ-03: Missing calibration | 1. Upload readable crop photos. 2. Remove the mass calibration. 3. Run analysis. | Crop analysis does not produce an unsupported calibrated yield value. | Not Run |
| TC-013 | REQ-04: Unsupported image format | 1. Upload an unsupported image format. 2. Submit the analysis. | The system explains the failure and prompts for supported images. No count or yield is displayed. | Not Run |
| TC-014 | REQ-04: Corrupt image | 1. Upload a corrupt JPEG file. 2. Run analysis. | The image is rejected with an understandable error. No invalid result is generated. | Not Run |
| TC-015 | REQ-04: Empty image submission | 1. Submit an analysis with zero images. 2. Inspect the result. | The user is prompted to submit supported images. No count or yield is displayed. | Not Run |
| TC-016 | REQ-13: Bunch count consistency | 1. Upload an annotated test image. 2. Run bunch counting. 3. Compare total, near-row, and countable results. | Countable ≤ near-row ≤ total. | Not Run |
| TC-017 | REQ-13: Background bunches | 1. Upload an image containing foreground and background rows. 2. Run counting. | Background bunches are excluded from the near-row count according to configured row rules. | Not Run |
| TC-018 | REQ-13: Poor image quality | 1. Upload a sparse or partially obscured image. 2. Run bunch counting. 3. Compare against annotations. | The system follows configured quality rules and does not report unsupported countable bunches. | Not Run |

## 4.3 Authentication and Authorization

| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-019 | REQ-05: Valid login | 1. Open the application. 2. Enter valid credentials for an enabled account. 3. Submit login. | The user is authenticated and can access permitted application functions. | Not Run |
| TC-020 | REQ-05: Role-based restrictions | 1. Log in as a viewer. 2. Attempt an administrative action. 3. Attempt to access an unauthorized field. | Restricted operations are denied. Only authorized resources are accessible. | Not Run |
| TC-021 | REQ-05: Invalid credentials | 1. Enter an incorrect password. 2. Attempt login. | Authentication fails and protected resources remain inaccessible. | Not Run |
| TC-022 | REQ-06: Permission revocation | 1. Log in with an account having access. 2. Revoke the account's permission. 3. Repeat the restricted request. | The next request is denied without requiring the user to log out. | Not Run |
| TC-023 | REQ-06: Active session revocation | 1. Open an authorized session. 2. Revoke its permission from another administrator session. 3. Repeat the operation. | The active session cannot continue performing the revoked function. | Not Run |
| TC-024 | REQ-06: Direct URL after revocation | 1. Save a previously accessible direct URL. 2. Revoke permission. 3. Request the URL again. | The request is denied and protected data is not returned. | Not Run |

## 4.4 Buyer Reporting and Research Exports

| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-025 | REQ-07: Buyer report generation | 1. Select a matching crop, field, and harvest forecast. 2. Generate a buyer report. 3. Open the exported file. | Crop, field, expected harvest period, estimated yield with unit, and generation date are present. | Not Run |
| TC-026 | REQ-07: Offline report readability | 1. Generate a buyer report. 2. Download the file. 3. Open it without an application account. | The exported report is readable independently of OMID. | Not Run |
| TC-027 | REQ-07: Invalid report selection | 1. Select an invalid or unmatched forecast. 2. Request a buyer report. | The system does not substitute unrelated forecast data. | Not Run |
| TC-028 | REQ-08: Missing forecast | 1. Select a crop, field, and period without a matching forecast. 2. Generate a report. | Yield is clearly labeled unavailable rather than zero. | Not Run |
| TC-029 | REQ-08: Valid zero-yield forecast | 1. Load a matching forecast with a genuine zero-yield value. 2. Generate a report. | The actual zero-yield value is displayed and distinguished from unavailable data. | Not Run |
| TC-030 | REQ-08: Different field forecast | 1. Load a forecast for another field. 2. Request a report for the original field. | The unrelated forecast is not substituted. Yield is labeled unavailable. | Not Run |
| TC-031 | REQ-09: Research CSV export | 1. Load yield, pest, disease, and frost records. 2. Select an inclusive date range. 3. Export the research CSV. | All applicable record types and required fields are included. | Not Run |
| TC-032 | REQ-09: Privacy protection | 1. Generate a research export. 2. Inspect column names and values. 3. Compare against the approved data allowlist. | Names, contacts, precise locations, and account IDs are excluded. | Not Run |
| TC-033 | REQ-09: Date boundaries | 1. Load records before, on, and after the selected date boundaries. 2. Export the selected range. | Only records within the inclusive date range are included. | Not Run |
| TC-034 | REQ-10: Empty date range | 1. Select a date range without records. 2. Submit the research query. | The specified no-records message appears and zero files are generated. | Not Run |
| TC-035 | REQ-10: Change date range | 1. Submit an empty query. 2. Select a different range containing records. 3. Submit again. | The application permits another query and returns matching records. | Not Run |
| TC-036 | REQ-10: Empty export failure | 1. Request an empty research range. 2. Inspect the download behavior. | No empty or misleading CSV file is generated. | Not Run |

## 4.5 Disease and Pest Screening

| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-037 | REQ-12: Successful classification | 1. Upload a supported multispectral TIFF capture. 2. Run screening. 3. Inspect the result. | The classification displays the assigned class and confidence when a class is scored. | Not Run |
| TC-038 | REQ-12: Low disease confidence | 1. Submit a disease test fixture with confidence below 0.5. 2. Run screening. | The result is flagged for manual review. | Not Run |
| TC-039 | REQ-12: No qualifying vine tiles | 1. Upload a TIFF capture without qualifying vine tiles. 2. Run screening. | The result distinguishes zero qualifying vine tiles from a healthy finding. | Not Run |
| TC-040 | REQ-12: Pest uncertainty | 1. Submit a pest screening fixture below the 0.5 cutoff. 2. Inspect the output. | The result is labeled uncertain. | Not Run |
| TC-041 | REQ-12: Confidence boundary | 1. Submit fixtures with scores below, at, and above 0.5. 2. Compare result labels. | Confidence labels and manual-review behavior follow the configured cutoff consistently. | Not Run |
| TC-042 | REQ-11: Forecast model unavailable | 1. Request pest forecasting without a configured model. 2. Inspect the response. | The application does not present photo confidence as a future-event forecast. | Not Run |

## 4.6 Alert Notifications

| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-043 | REQ-15: Critical notification | 1. Trigger a qualifying critical event. 2. Observe the notification feed. 3. Compare event and delivery timestamps. | A critical in-app notification is generated immediately. | Not Run |
| TC-044 | REQ-15: Digest grouping | 1. Generate multiple qualifying noncritical events within 15 minutes. 2. Inspect notifications. | No more than one digest per recipient is generated during the 15-minute period. | Not Run |
| TC-045 | REQ-15: Duplicate suppression | 1. Generate repeated alerts at the same severity. 2. Inspect the notification feed. | Repeated alerts at the same severity are suppressed. | Not Run |

## 4.7 Non-Functional Requirements

| ID | Description | Steps | Expected Result | Status |
|---|---|---|---|---|
| TC-046 | NFR-01: Yield performance | 1. Prepare 2,200 supported photos. 2. Run analysis on the designated GPU worker. 3. Record completion time. | Processing completes within 60 minutes. | Not Run |
| TC-047 | NFR-01: Repeated performance | 1. Repeat the 2,200-photo test three times under the specified isolated conditions. 2. Record each duration. | All three runs complete within 60 minutes. | Not Run |
| TC-048 | NFR-02: Cross-organization access | 1. Create two test organizations. 2. Authenticate as a user in organization A. 3. Attempt access to organization B resources. | No unauthorized successful operation or protected data disclosure occurs. | Not Run |
| TC-049 | NFR-02: Direct asset access | 1. Attempt to access a protected asset without authorization. 2. Repeat with an unauthorized account. | Both requests are denied. | Not Run |
| TC-050 | NFR-02: Revoked-session security | 1. Establish an active session. 2. Revoke its permission. 3. Attempt another protected operation. | The protected operation is denied. | Not Run |
| TC-051 | NFR-03: Alert contrast | 1. Display every alert severity. 2. Measure foreground and background contrast. | Each alert achieves at least a 4.5:1 contrast ratio. | Not Run |
| TC-052 | NFR-03: Text-based severity | 1. Disable or ignore color information. 2. Inspect all alert states. | Every severity and unavailable state remains identifiable through text. | Not Run |
| TC-053 | NFR-04: Sensor retry handling | 1. Simulate a 60-second store outage. 2. Buffer 10 distinct readings. 3. Replay each reading with two retries. | Exactly 10 distinct records are stored. | Not Run |
| TC-054 | NFR-04: Duplicate detection | 1. Inspect stored sensor records after recovery. 2. Compare composite keys. | No duplicate records exist. | Not Run |
| TC-055 | NFR-04: Recovery integrity | 1. Restore the simulated store. 2. Replay the buffered readings. 3. Verify the stored values. | Every reading is persisted exactly once with its original data. | Not Run |
| TC-056 | NFR-05: Frost traceability | 1. Select three frost alert results. 2. Retrieve their request or cycle IDs, inputs, and configuration versions. | Each selected result can be traced to its corresponding inputs and rules version. | Not Run |
| TC-057 | NFR-05: Yield traceability | 1. Select three yield analysis results. 2. Inspect associated identifiers and model versions. | Each result can be traced to its inputs and model/configuration version. | Not Run |

# 5. Test environment

## 5.1 Development

Hardware:

- Personal computers
- Simulated stationary sensor boxes and temperature sensor readings
- University of Cincinnati GPU cluster

Software:

- Windows 11 operating system or Ubuntu Linux
- Google Chrome and Microsoft Edge browsers
- Local web application builds
- PyTorch python library
- Git and GitHub

Test Data:

- Simulated fresh, stale, and missing temperature readings
- Sample JPEG, PNG, and TIFF crop images
- Sample multispectral images
- Corrupt and unsupported image files
- Sample bunch counts and known mass calibration values
- Simulated user accounts representing owner, admin, member, and viewer roles
- Training datasets for disease prediction and pest detection

## 5.2 Staging

Hardware:

- Designated GPU worker for image-processing tests
- Stationary sensor boxes
- RGB and multispectral camera
- Personal computers for application testing
- Server and database infrastructure

Software:

- Windows 11 or Ubuntu Linux
- Google Chrome and Microsoft Edge
- Staging database containing test records
- Trained machine-learning models

Test Data:

- RGB and multispectral datasets
- Sensor readings covering normal, stale, missing, and duplicate conditions
- A dataset of 2,200 supported photos for performance testing
- Annotated crop images with known bunch counts
- Valid and missing mass calibration fixtures
- Disease and pest images with labeled reference classifications and confidence scores
- Buyer forecasts with valid, missing, and zero-yield examples
- Research records covering yield, pest, disease, and frost events
- Populated and empty date ranges for CSV export testing
- Simulated network interruptions and a 60-second data-store outage

## 5.3 Pilot

Hardware:

- Installed stationary sensor boxes
- Drone for vineyard image collection
- RGB camera and Multispectral camera
- Battery or solar power equipment for sensor boxes
- Laptop or desktop computer for accessing the web application
- Designated GPU worker or connected processing infrastructure

Software:

- Approved OMID pilot build
- Windows 11 or Ubuntu Linux on the testing computer
- Google Chrome or Microsoft Edge
- Configured production-like database and sensor communication services
- Approved trained machine-learning models
- Logging and monitoring tools for sensor readings and analysis results

Test Data:

- Real vineyard temperature readings
- Real sensor timestamps and connectivity records
- Drone-captured RGB and multispectral crop images
- Actual crop images containing foreground and background bunches
- Crop-specific mass calibration measurements
- Real frost event and recovery observations
- Crop, disease, pest, and yield records
- Approved user accounts and field permissions

# 6. Pass and fail criteria

Overall Test Plan Acceptance: 

- All Must requirements pass their associated test cases.
- All Must security and data-reliability criteria are satisfied.
- No unresolved critical or high-severity defects remain.
- All agreed performance and accessibility thresholds are met.
- Should requirements are tested and their results documented, with any incomplete items formally recorded.
- Could requirements are documented as deferred when their dependencies are unavailable.
- The QA lead reviews the evidence and the advisor or designated stakeholder approves the final acceptance results.

|Area|Success Condition|Failure Condition
|-|-|-|
|Frost detection|Required forecast info is displayed correctly|Missing or incorrect forecast info
|Sensor validation|Missing and stale sensors are identified and unavailable predictionsare handled|Stale data is presented as current.
|Yield estimation|Count and calibrated yield meet documented calculation criteria|Incorrect count or yield
|Authentication|Authorized access succeeds and unauthorized access is denied|An unauthorized successful operation
|Permission revocation|Next restricted request is denied|Access continues after revocation
|Buyer reports|All required fields included|Missing or substituted data
|Research exports|Correct records with identifiying information excluded|Missingrecords, incorrect date handling, or privacy violations
|Disease and Pest Screening|Model accuracy or confidence is improved|Misleading results
|Frost recovery|All-clear occurs after documented recovery conditions|Warning clears early
|Notifications|Critical alerts are immediate and other notifications are grouped|Duplicate or delayed notifications
|Performance| All three 2,200-photo runs complete within 60 minutes|Any run exceeds 60 minutes
|Data reliability|Exactly 10 distinct readings persist without duplication|Lost or duplicate readings

# 7. Milestone

| Milestone ID | Deliverable / Milestone Description | Due Date | Status | Demonstration & Verification | Verifier |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **A1** | Assignment 1: Professional Biography | Sep 02, 2026 | **Complete** | Submitted bios and technical background profile. | Grader |
| **A2** | Assignment 2: Team Formation & Project Description (5%) | Sep 09, 2026 | **Complete** | Signed draft contract, team roster in spreadsheet, GitHub repo initialized. | Course Instructor / TAs |
| **A3** | Assignment 3: Advisor Selection & Constraints Essay (5%) | Sep 18, 2026 | **Complete** | Faculty advisor confirmation, ABET constraint evaluation. | Project Advisor |
| **A4** | Assignment 4: User Stories & Use Cases | Sep 28, 2026 | **Complete** | Accepted user stories and stakeholder use case backlog. | Grader |
| **A5** | Assignment 5: High-Level System Design (Design D0) (10%) | Oct 02, 2026 | **Complete** | System block diagram, I1–I6 interface contracts, data flow diagram. | Grader |
| **A6** | Assignment 6: Detailed System Design, Part 1 (Design D1) | Oct 08, 2026 | **Complete** | ViT, SAHI YOLO, and sensor storage models. | Grader |
| **A7** | Assignment 7: Detailed Design Part 2 (D2) & Peer Notes (15%) | Oct 14, 2026 | **Complete** | Edge cases, frost state machine, glue logic, peer review notes. | Grader / Peers |
| **A8** | **Assignment 8: Task List, Timeline & Effort Matrix (10%)** | **Oct 28, 2026** | **Complete** | Full planning package: 20 tasks, schedule table, effort matrix, WBS, risks. | Course Instructor |
| **A9** | **Assignment 9: Requirements Document & Tech Spec (15%)** | **Oct 30, 2026** | **In Progress** | Formal `Requirements_Document.md` & `Technical_Specification.md`. | Project Advisor / Grader |
| **A10** | **Assignment 10: Test & Validation Plan Draft** | **Nov 04, 2026** | **In Progess** | Master test plan (`Test_Plan.md`), test cases TC-01..TC-20, pass/fail metrics. | Grader |
| **A11** | **Assignment 11: Ethics & Responsible-Use Statement** | **Nov 11, 2026** | **Planned** | Data privacy table, security audit, SPDX licensing, AI disclosures. | Grader |
| **A12/13** | **Assignments 12 & 13: Presentation, Video & Reviews (15%)** | **Nov 18, 2026** | **Planned** | 10-slide deck, advisor feedback log, recorded 10-min talk, peer reviews. | Review Panel |
| **--** | *Thanksgiving Break* | *Nov 26–29, 2026* | *Scheduled* | Academic recess; no active development scheduled. | University Calendar |
| **A14** | **Assignment 14: Final Design Report (15%)** | **Dec 02, 2026** | **Planned** | Final design report (`README.md`), 45-hr notebooks, team assessments. | Instructor / Advisor |
| **--** | *End of Fall Term / Winter Break* | *Dec 13–Jan 10* | *Scheduled* | Semester break & transition to CS 5002 Senior Design II. | University Calendar |
| **M1** | Core Ingestion & Frost Engine Live | Jan 29, 2027 | **Planned** | Live HTTP sensor ingestion (I2) and 6-level rule engine execution. | Andrea / Cyrus |
| **M2** | Vision Pipelines Alpha (ViT + SAHI) | Feb 19, 2027 | **Planned** | Standalone inference on drone orthomosaics for disease and bunch count. | Bridget / Dang |
| **UT-01** | Unit Testing| During each development sprint| **Planned** | Test individual functions, frost thresholds, yield calculations, image validation, authentication permissions, and alert logic. Evidence includes automated unit test results, test logs, and code coverage reports. | Andrea / Bridget / Dang / Cyrus |
| **--** | *Spring Break* | *Mar 15–21, 2027* | *Scheduled* | Academic recess; buffer block. | University Calendar |
| **M3** | Cincinnati Field Validation Sign-Off | Apr 02, 2027 | **Planned** | Accuracy validation against real ground truth at Cincinnati vineyard. | Client / Advisor |
| **M4** | Senior Design Expo & Client Hand-Off | Apr 20, 2027 | **Planned** | Public capstone demonstration, poster, user manual, and software hand-off. | Public / Panel |

# Definition of Done

Feature-level definition of done:

- [ ] The feature implements its assigned requirement from the approved Requirements Document.
- [ ] At least three applicable test cases exist for every major requirement.
- [ ] Unit tests have been executed successfully.
- [ ] Required integration tests have passed.
- [ ] Expected error and edge-case behavior has been verified.
- [ ] No unresolved critical or high-severity defects remain.
- [ ] Code has been reviewed by another team member.
- [ ] Relevant technical documentation has been updated.
- [ ] Test evidence has been recorded.
- [ ] The QA lead has approved the feature for integration or demonstration.

Project-Level Definition of Done

- [ ] All Must requirements have passed their acceptance tests.
- [ ] Should requirements have been tested and their outcomes documented.
- [ ] Deferred Could and Won't features are clearly identified.
- [ ] Unit, integration, system, and acceptance testing have been completed.
- [ ] No unresolved critical or high-severity defects remain.
- [ ] Test evidence, screenshots, logs, and defect reports are stored in the repository.
- [ ] The Requirements Document and technical documentation reflect the final implementation.
- [ ] The project advisor or designated stakeholder has reviewed the acceptance evidence.
- [ ] The final release has received team approval.

# AI statement

The team acknowledges the use of ChatGPT to write and format the test cases for the requirements and definitions of done.