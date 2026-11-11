# Assignment 8: Task List, Milestones, Timeline, and Effort Matrix

**Project Title:** OMID AI-Based Farm Disease, Pest, Yield, and Frost Detection System

**Team Members:** Andrea Dall'Ara, Bridget Trout, Dang Luu, Cyrus Speicher

---

## 1. Task List (Assignment #5)

1. **T1.** *Develop* API Gateway authentication middleware and data contracts for the **Web App & Gateway**. Owner: **Andrea**.
2. **T2.** *Develop* stationary sensor telemetry ingestion and validation parser for the **Web App & Gateway**. Owner: **Andrea**.
3. **T3.** *Develop* asynchronous 202 drone upload queue and worker router for the **Web App & Gateway**. Owner: **Andrea**.
4. **T4.** *Develop* email (SendGrid) and SMS (Twilio) notification workers for **Glue Components**. Owner: **Andrea**.
5. **T5.** *Develop* interactive vineyard map and alert visualization UI for the **Web App & Gateway**. Owner: **Andrea**.
6. **T6.** *Refine* 6-level rule-based state machine and all-clear logic for the **Frost Prediction Engine**. Owner: **Cyrus**.
7. **T7.** *Develop* scheduled sensor staleness-sweep cloud function for the **Frost Prediction Engine**. Owner: **Cyrus**.
8. **T8.** *Refine* Magnus dew-point spread and projected cooling algorithms for the **Frost Prediction Engine**. Owner: **Cyrus**.
9. **T9.** *Develop* historical sensor event-replay test suite for the **Frost Prediction Engine**. Owner: **Cyrus**.
10. **T10.** *Validate* rule engine vs. AI frost model against Cincinnati historical data for the **Frost Prediction Engine**. Owner: **Cyrus**.
11. **T11.** *Obtain* and curate annotated vineyard imagery datasets for the **Disease Detection Module**. Owner: **Bridget**.
12. **T12.** *Refine* Vision Transformer (ViT) classifier for leaf-level diseases for the **Disease Detection Module**. Owner: **Bridget**.
13. **T13.** *Investigate* camera altitude and ground resolution limits for insects in the **Pest Detection Module**. Owner: **Bridget**.
14. **T14.** *Develop* pest localized bounding-box detection pipeline for the **Pest Detection Module**. Owner: **Bridget**.
15. **T15.** *Validate* sensor telemetry fusion (wetness/humidity) with disease inference in the **Disease Detection Module**. Owner: **Bridget**.
16. **T16.** *Obtain* ground-truth grape bunch counts and cluster harvest weights for the **Yield Estimation Module**. Owner: **Dang**.
17. **T17.** *Develop* SAHI tiling and duplicate box-merging pipeline for the **Yield Estimation Module**. Owner: **Dang**.
18. **T18.** *Develop* monocular depth estimation and Otsu foreground row filter for the **Yield Estimation Module**. Owner: **Dang**.
19. **T19.** *Refine* detection confidence threshold via validation-split MAE for the **Yield Estimation Module**. Owner: **Dang**.
20. **T20.** *Validate* vineyard harvest yield estimates against ground-truth weights for the **Yield Estimation Module**. Owner: **Dang**.

---

## 2. Milestones List (Assignment #6)

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
| **--** | *Spring Break* | *Mar 15–21, 2027* | *Scheduled* | Academic recess; buffer block. | University Calendar |
| **M3** | Cincinnati Field Validation Sign-Off | Apr 02, 2027 | **Planned** | Accuracy validation against real ground truth at Cincinnati vineyard. | Client / Advisor |
| **M4** | Senior Design Expo & Client Hand-Off | Apr 20, 2027 | **Planned** | Public capstone demonstration, poster, user manual, and software hand-off. | Public / Panel |

---

## 3. Table 1: Timeline & Schedule (Assignment #6)

| Task ID | Task Statement | Owner | Start Date | End Date | Buffer | Dependencies |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **A8** | **Assignment 8 Submission** | **All** | **Oct 15, 2026** | **Oct 28, 2026** | **Delayed** | **A7 (D2 Complete Oct 14)** |
| **A9** | **Assignment 9: Requirements & Spec** | **All** | **Oct 28, 2026** | **Oct 30, 2026** | **Delayed** | **A8** |
| **A10** | **Assignment 10: Test & Validation Plan** | **All** | **Oct 31, 2026** | **Nov 04, 2026** | **Delayed** | **A9** |
| **A11** | **Assignment 11: Ethics Statement** | **All** | **Nov 05, 2026** | **Nov 11, 2026** | **1 day (17%)** | **A10** |
| **A12/13** | **Assignments 12 & 13: Presentation & Video** | **All** | **Nov 12, 2026** | **Nov 18, 2026** | **1 day (17%)** | **A11, Advisor Meeting** |
| T1 | API Gateway contract & auth middleware | Andrea | Nov 19, 2026 | Nov 25, 2026 | 1 day (20%) | A12/13 |
| T6 | Refine 6-level frost state machine | Cyrus | Nov 19, 2026 | Nov 25, 2026 | 1 day (20%) | A12/13 |
| *Break* | *Thanksgiving Holiday Break* | *All* | *Nov 26, 2026* | *Nov 29, 2026* | *Holiday* | *--* |
| T11 | Curate disease imagery dataset | Bridget | Nov 30, 2026 | Dec 02, 2026 | 0.5 day (20%) | A12/13, EE2 Drone Feed |
| T16 | Obtain ground-truth bunch counts | Dang | Nov 30, 2026 | Dec 02, 2026 | 0.5 day (20%) | A12/13 |
| **A14** | **Assignment 14: Final Design Report** | **All** | **Nov 30, 2026** | **Dec 02, 2026** | **Protected** | **A12/13, Notebook Logs** |
| *Break* | *Fall Exams & Winter Break* | *All* | *Dec 13, 2026* | *Jan 10, 2027* | *Recess* | *Fall Term Closes* |
| T2 | Develop sensor ingest pipeline (I2) | Andrea | Jan 11, 2027 | Jan 20, 2027 | 1.5 days (19%) | T1 |
| T7 | Scheduled staleness sweep cloud function | Cyrus | Jan 11, 2027 | Jan 20, 2027 | 1.5 days (19%) | T6 |
| T3 | Async 202 upload queue & worker router | Andrea | Jan 21, 2027 | Jan 28, 2027 | 1.5 days (20%) | T2 |
| T8 | Refine Magnus spread & cooling math | Cyrus | Jan 21, 2027 | Jan 28, 2027 | 1.5 days (20%) | T7 |
| **M1** | **Core Ingestion & Frost Live** | **Andrea, Cyrus** | **--** | **Jan 29, 2027** | **1 day block** | **T2, T3, T7, T8** |
| T12 | Refine ViT disease classifier | Bridget | Feb 01, 2027 | Feb 10, 2027 | 1.5 days (19%) | T11 |
| T17 | SAHI bunch detection pipeline | Dang | Feb 01, 2027 | Feb 10, 2027 | 1.5 days (19%) | T16 |
| T13 | Investigate pest resolution limits | Bridget | Feb 11, 2027 | Feb 18, 2027 | 1.5 days (20%) | EE2 Camera Tests |
| T18 | Monocular depth & foreground row filter | Dang | Feb 11, 2027 | Feb 18, 2027 | 1.5 days (20%) | T17 |
| **M2** | **Vision Pipelines Alpha** | **Bridget, Dang** | **--** | **Feb 19, 2027** | **Protected** | **T12, T13, T17, T18** |
| T4 | Notification workers (SMS / Email) | Andrea | Feb 22, 2027 | Mar 03, 2027 | 1.5 days (19%) | M1 |
| T9 | Historical sensor event-replay test suite | Cyrus | Feb 22, 2027 | Mar 03, 2027 | 1.5 days (19%) | M1 |
| T14 | Pest bounding-box detection pipeline | Bridget | Feb 22, 2027 | Mar 03, 2027 | 1.5 days (19%) | T13 |
| T19 | Tune confidence cutoff via val MAE | Dang | Feb 22, 2027 | Mar 03, 2027 | 1.5 days (19%) | M2 |
| T5 | Map UI & alert dashboard frontend | Andrea | Mar 04, 2027 | Mar 11, 2027 | 1.5 days (20%) | T4 |
| T10 | Validate rule engine vs. AI frost model | Cyrus | Mar 04, 2027 | Mar 11, 2027 | 1.5 days (20%) | T9 |
| T15 | Validate sensor telemetry + image fusion | Bridget | Mar 04, 2027 | Mar 11, 2027 | 1.5 days (20%) | T12 |
| **M3** | **Integration Freeze** | **All** | **--** | **Mar 12, 2027** | **1 day block** | **T5, T10, T14, T15, T19** |
| *Break* | *Spring Break Recess* | *All* | *Mar 15, 2027* | *Mar 21, 2027* | *Holiday* | *--* |
| T20 | Validate yield estimates vs. harvest weights | Dang | Mar 22, 2027 | Apr 01, 2027 | 2 days (20%) | M3 |
| **M4** | **Field Validation Sign-Off** | **All** | **--** | **Apr 02, 2027** | **Protected** | **T20, Client Review** |
| **M5** | **Senior Design Expo & Final Hand-Off** | **All** | **Apr 05, 2027** | **Apr 20, 2027** | **Final Polish** | **M4** |

---

## 4. Table 2: Effort Matrix (Assignment #6)

Individual hours are computed using the PERT formula: $E = \frac{O + 4M + P}{6}$. (The hours listed for tasks with non-owners are just approximations and may not require any work from other members)
| Task ID | Task Statement | Andrea | Cyrus | Bridget | Dang | Total Effort |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **T1** | API Gateway contract & auth middleware | **26\*** (72%) | 4 | 3 | 3 | 36 hrs |
| **T2** | Sensor ingest & telemetry parser | **28\*** (70%) | 6 | 3 | 3 | 40 hrs |
| **T3** | Async 202 drone upload queue & router | **25\*** (69%) | 3 | 4 | 4 | 36 hrs |
| **T4** | Email (SendGrid) & SMS (Twilio) workers | **24\*** (71%) | 5 | 3 | 2 | 34 hrs |
| **T5** | Web map UI & alert dashboard | **35\*** (67%) | 5 | 6 | 6 | 52 hrs |
| **T6** | Refine 6-level frost state machine | 4 | **28\*** (74%) | 3 | 3 | 38 hrs |
| **T7** | Scheduled staleness-sweep function | 5 | **24\*** (71%) | 3 | 2 | 34 hrs |
| **T8** | Refine Magnus spread & cooling math | 3 | **22\*** (73%) | 3 | 2 | 30 hrs |
| **T9** | Historical event-replay test suite | 4 | **26\*** (72%) | 3 | 3 | 36 hrs |
| **T10** | Validate rule engine vs. AI frost model | 4 | **30\*** (68%) | 5 | 5 | 44 hrs |
| **T11** | Curate disease imagery dataset | 3 | 3 | **30\*** (71%) | 6 | 42 hrs |
| **T12** | Refine ViT disease classifier | 3 | 3 | **32\*** (73%) | 6 | 44 hrs |
| **T13** | Investigate pest resolution limits | 3 | 3 | **22\*** (73%) | 2 | 30 hrs |
| **T14** | Develop pest bounding-box pipeline | 3 | 3 | **30\*** (75%) | 4 | 40 hrs |
| **T15** | Validate sensor + disease image fusion | 5 | 5 | **26\*** (65%) | 4 | 40 hrs |
| **T16** | Obtain ground-truth bunch counts | 2 | 2 | 6 | **30\*** (75%) | 40 hrs |
| **T17** | Develop SAHI bunch detection pipeline | 3 | 3 | 4 | **32\*** (76%) | 42 hrs |
| **T18** | Monocular depth & foreground row filter | 3 | 3 | 3 | **30\*** (77%) | 39 hrs |
| **T19** | Tune confidence cutoff via val MAE | 2 | 2 | 3 | **26\*** (79%) | 33 hrs |
| **T20** | Validate harvest yield vs. actual weights | 4 | 4 | 4 | **32\*** (73%) | 44 hrs |
| **Total** | **Individual Effort Totals** | **201 hrs** | **199 hrs** | **196 hrs** | **198 hrs** | **794 hrs** |
| **Share** | **Percentage Division of Labor** | **25.3%** | **25.1%** | **24.7%** | **24.9%** | **100.0%** |

*\* Denotes primary module owner carrying the primary operational workload.*

---

## 5. Work Breakdown Structure (WBS)

```
1.0 OMID AI Vineyard Management System
├── 1.1 Web App & Systems Integration (Andrea)
│   ├── 1.1.1 API Gateway Auth Middleware & RBAC [T1]
│   ├── 1.1.2 Stationary Sensor Telemetry Ingestion Pipeline (I2) [T2]
│   ├── 1.1.3 Asynchronous 202 Drone Upload Queue & Router (I1) [T3]
│   ├── 1.1.4 Notification Workers (SendGrid Email / Twilio SMS) [T4]
│   └── 1.1.5 Interactive Farm Map & Alert Visualization UI [T5]
├── 1.2 Frost Prediction Engine (Cyrus)
│   ├── 1.2.1 Six-Level Frost State Machine & All-Clear Logic [T6]
│   ├── 1.2.2 Scheduled Sensor Staleness-Sweep Cloud Function [T7]
│   ├── 1.2.3 Magnus Dew-Point & Projected Cooling Algorithms [T8]
│   ├── 1.2.4 Historical Sensor Event-Replay Test Harness [T9]
│   └── 1.2.5 Comparative Study: Rule Engine vs. AI Frost Model [T10]
├── 1.3 Pathology & Entomology Modules (Bridget)
│   ├── 1.3.1 Local Cincinnati Disease Imagery Dataset Curation [T11]
│   ├── 1.3.2 Vision Transformer (ViT) Leaf Disease Classifier [T12]
│   ├── 1.3.3 Camera Altitude & GSD Pest Resolution Study [T13]
│   ├── 1.3.4 Bounding-Box Localized Pest Detection Pipeline [T14]
│   └── 1.3.5 Sensor Telemetry & Disease Inference Data Fusion [T15]
├── 1.4 Yield Estimation Module (Dang)
│   ├── 1.4.1 Ground-Truth Bunch Count & Harvest Weight Curation [T16]
│   ├── 1.4.2 SAHI Tiling & Duplicate Box-Merging Pipeline [T17]
│   ├── 1.4.3 Monocular Depth & Otsu Foreground Row Filter [T18]
│   ├── 1.4.4 Validation-Split Count MAE Cutoff Optimization [T19]
│   └── 1.4.5 Vineyard Harvest Yield Regression & Validation [T20]
└── 1.5 Project Management & Course Governance (Full Team)
    ├── 1.5.1 Specifications: Requirements & Tech Spec (A9 / Week 9)
    ├── 1.5.2 Quality Assurance: Test & Validation Plan (A10 / Week 10)
    ├── 1.5.3 Compliance: Ethics & Responsible-Use Statement (A11 / Week 11)
    ├── 1.5.4 Communication: Presentation, Video & Peer Reviews (A12/13 / Weeks 12–13)
    └── 1.5.5 Final Design Report & 45-Hour Notebooks (A14 / Week 14)
```

---

## 6. Risk Register

| Risk ID | Category | Risk Description | Likelihood | Impact | Strategy | Mitigation & Contingency Plan | Owner | Trigger Event |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **R1** | Technical | Pest Resolution Limits (insects < 5px from drone flight) | High | High | Reduce | Pivot to side-view canopy imaging or detect indirect pest damage symptoms (T13). | Bridget | Drone test flight shows spotted lanternflies cannot be distinguished. |
| **R2** | External | Inclement Winter Weather Cancels Drone Flights | Med | High | Avoid | Pre-curate ground-truth dataset from historical imagery and synthetic clock replay harness (T9). | Dang | Cincinnati weather grounding drone operations for >14 days in spring. |
| **R3** | Technical | Sensor Telemetry Communication Loss / Stale Data | Med | High | Reduce | Staleness-sweep guard retains active warning and flags missing station on dashboard (T7). | Cyrus | Stationary sensor fails to ping gateway over 2 consecutive reporting intervals. |
| **R4** | Technical | Cloud Inference Timeout on Large Orthomosaic Captures | High | Med | Avoid | Asynchronous 202 Accepted upload queue with job status polling and Cloud Storage decoupling (T3). | Andrea | Multispectral TIFF upload payload causes HTTP gateway 30s timeout. |
| **R5** | Technical | ViT Model Overfitting on Public Datasets | High | High | Reduce | Fine-tune weights on local Cincinnati vineyard imagery using k-fold cross-validation (T11/T12). | Bridget | Public dataset validation score drops >20% when evaluated on field imagery. |

# AI Attribution

The authors would like to acknowledge the use of Gemini, developed by Google, in the preparation of this assignment. Gemini was used in the following way(s) in this assignment: Formatting table structures and Work Breakdown Structure
