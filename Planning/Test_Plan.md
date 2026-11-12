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
|Unit testing|60|Individual functions, thresholds, permissions
|Integration testing|30|Sensor ingestion, storage, inference pipelines
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

# Test Cases

|ID|Description|Steps|Expected Result|Status
|-|-|-|-|-|

