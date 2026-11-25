# Run Sheet and Rehearsal Log

**Project:** OMID AI-Based Farm Disease, Pest, Yield, and Frost Detection System  
**Deck:** `OMID_Final_Deck.pptx`, V1, Prepared November 18th 2026
**Run sheet prepared:** November 18, 2026  
**Main presentation:** Slides 1-10; 10:00 total (600 seconds)
**Timekeeper:** Dang Luu

## 1. Speaking and support roles

| Team member | Main speaking role | Slides | Planned speaking time | Support and Q&A role |
|---|---|---|---|---|
| Andrea Dall'Ara | Introduce the project and team; explain system design; close with the Expo demo | 1, 2, 5, 10 | 2:30 / 150 seconds | Slide controller; web app, gateway, data contract, access, and demo questions |
| Bridget Trout | Present the abstract and project constraints | 3, 6 | 2:30 / 150 seconds | Rehearsal log recorder; disease, pest, local evaluation, and budget questions |
| Cyrus Speicher | Explain user stories and end-of-term targets | 4, 8 | 2:30 / 150 seconds | Backup timekeeper during Dang's sections; frost rules and stale-data questions |
| Dang Luu | Review progress and division of work | 7, 9 | 2:30 / 150 seconds | Primary timekeeper; yield, counting, calibration, and overlap questions |
| **Total** | **Four presenters** | **1-10** | **10:00 / 600 seconds** | **All members participate in the full-group rehearsal** |

Andrea advances slides on the current speaker's final sentence. Each outgoing speaker says the hand-off line; the incoming speaker starts immediately. Hand-offs are included in the slide durations, not added afterward. For consecutive slides by the same speaker, the line is a transition rather than a change of speaker.

Dang starts a continuous stopwatch at Andrea's first spoken word, records elapsed times at slide changes, and stops at the end of the closing sentence. Cyrus records the slide-change times while Dang presents. Bridget consolidates their measurements into the rehearsal log afterward. Give a discreet cue with about 15 seconds left on a speaker's final slide and a clear cue when the planned section end is reached.

## 2. Main presentation run sheet

All start and end times are elapsed time from the first spoken word. Planned finish: **10:00**. Questions and backup slides follow separately.

| Slide | Slide title | Speaker | Planned start | Duration | Planned end | Hand-off or transition line at the end |
|---|---|---|---|---|---|---|
| 1 | OMID farm monitoring | Andrea | 00:00 | 00:30 | 00:30 | "Here is the team responsible for building that system." |
| 2 | Project team | Andrea | 00:30 | 00:15 | 00:45 | "Bridget will give a quick overview of how the system supports growers." |
| 3 | Project abstract | Bridget | 00:45 | 01:00 | 01:45 | "Cyrus will explain the user stories that shaped this design." |
| 4 | User stories | Cyrus | 01:45 | 01:00 | 02:45 | "Andrea will show how those needs connect to our system architecture." |
| 5 | System design | Andrea | 02:45 | 01:15 | 04:00 | "Bridget will explain the constraints that guide these design choices." |
| 6 | Project constraints | Bridget | 04:00 | 01:30 | 05:30 | "Dang will walk through our progress and the work that remains." |
| 7 | Review of project progress | Dang | 05:30 | 01:15 | 06:45 | "Cyrus will outline what we expect to accomplish by the end of the term." |
| 8 | End-of-term accomplishments | Cyrus | 06:45 | 01:30 | 08:15 | "Dang will explain how we have divided that work across the team." |
| 9 | Division of work | Dang | 08:15 | 01:15 | 09:30 | "Andrea will close with the workflow we plan to demonstrate at Expo." |
| 10 | Expected demo at Expo | Andrea | 09:30 | 00:30 | 10:00 | "Thank you. We are ready for questions." |

### Content cues

- **Slides 1-2:** State the purpose and goal, then identify the four module owners and Dr. Mohsen Rezayat. Avoid reading every contact entry aloud.
- **Slide 3:** Explain the sensor and imagery inputs, analysis modules, human review, and organized records. Distinguish the overall design from features already integrated.
- **Slide 4:** Connect the owner, worker, administrator, buyer, and researcher stories to their expected outcomes.
- **Slide 5:** Trace hardware inputs I1/I2 through the gateway to independent interfaces I3-I6. Mention that yield uses side-view RGB photos.
- **Slide 6:** Cover the combined $2,000 budget, authorized access, outdoor operation, and affordability. Explain the effect of each constraint.
- **Slide 7:** Separate active sensor ingestion and visible frost readings from documented prototypes, pending drone uploads, and remaining validation.
- **Slide 8:** Present the end-of-term targets as targets: shared upload contract, frost replay, local screening evaluation, and calibrated yield example.
- **Slide 9:** Identify each primary owner and the output that owner hands to the shared integration pipeline.
- **Slide 10:** Describe the planned authorized-zone workflow and the local narrated-video fallback. Do not switch into a live demo during this 30-second closing slide.

## 3. Backup slide run sheet

These slides have **no scheduled start time** and **0 seconds allocated in the main talk**. Open only the relevant slide when answering a question. Andrea controls navigation; the subject owner below answers. These Q&A assignments supplement the main speaking plan.

| Slide | Slide title | Q&A speaker | Planned start | Main-talk allocation | Hand-off line when the backup is needed |
|---|---|---|---|---|---|
| 11 | Recording and publication plan | Andrea | Q&A only | 00:00 | "This is our recording and publication plan." |
| 12 | Slide outline and timing | Dang | Q&A only | 00:00 | "Dang can explain the speaking allocation and timing." |
| 13 | D1 - Capture and detection model | Dang | Q&A only | 00:00 | "Dang can walk through the capture and detection records." |
| 14 | D1 - Labels and yield aggregation | Dang | Q&A only | 00:00 | "Dang can explain how labels, counts, and yield estimates relate." |
| 15 | D2 - Frost data model | Cyrus | Q&A only | 00:00 | "Cyrus can explain the frost inputs and zone configuration." |
| 16 | D2 - Frost alert lifecycle | Cyrus | Q&A only | 00:00 | "Cyrus can walk through the six alert states." |
| 17 | D2 - Transition guards | Cyrus | Q&A only | 00:00 | "Cyrus can explain the conditions behind those transitions." |
| 18 | Items for advisor review | Andrea | Q&A only | 00:00 | "These are the remaining items we have identified for review." |

## 4. Anticipated questions

Answers describe the supplied draft deck and its documented design. Confirm any implementation or performance updates with the module owner before presenting them as completed work.

| Anticipated question | Owner | Short answer | Relevant slides |
|---|---|---|---|
| Who will use this system? | Cyrus | Farm owners monitor risk, workers run counting, and administrators manage access. Buyers and researchers receive controlled exports. | 4 |
| What is the main integration responsibility? | Andrea | Connect sensor readings, capture metadata, model outputs, and the dashboard using a shared contract scoped to the correct organization, site, and zone. | 5, 9 |
| What currently works, and what remains? | Andrea | The draft reports sensor ingestion and visible frost readings. Image and yield prototypes are documented; drone upload integration, field validation, and reporting remain pending. | 7 |
| Why use a rule-based frost engine? | Cyrus | Zone and growth-stage settings make thresholds and alert transitions explicit and reviewable. The rules still need replay and local validation. | 5, 15-17 |
| What happens if sensor data is missing or stale? | Cyrus | The target behavior identifies affected sensors, withholds a current prediction, and retains an active warning rather than treating missing data as an all-clear. | 4, 8, 16-17 |
| Does a disease or pest confidence score predict a future outbreak? | Bridget | No. The current image workflows screen what appears in the image. A future-event forecast requires a separately validated forecasting model and time window. | 3, 7 |
| How accurate are the models? | Bridget | The deck does not claim measured accuracy. Report held-out local results and their limitations after evaluation; Dang handles count and yield error questions. | 7-8 |
| Why does yield analysis use side-view RGB images? | Dang | Bunches hang below the canopy, so the counting design uses side views that expose the fruiting zone. This refines D0's original multispectral assumption. | 5, 13-14 |
| How do bunch counts become yield in kilograms? | Dang | Multiply countable bunches by measured mean bunch mass for the relevant calibration. Without calibration, show yield as unavailable rather than zero. | 8, 14 |
| How do you avoid counting the same bunch twice? | Dang | The prototype merges duplicate detections within a photo. Aggregation assumes separate photos do not overlap; cross-photo duplicates remain a limitation. | 13-14 |
| How is farm data protected? | Andrea | The design uses authentication and backend membership and scope checks. Protected media access and revocation behavior still require verification. | 5-7 |
| How does the budget affect the design? | Bridget | The combined hardware and software budget is at most $2,000. Reusing existing equipment leaves resources for reliable collection, calibration, and protection. | 6 |
| What will the Expo demo show, and what if it fails? | Andrea | The target workflow covers a zone, readings, frost replay, image screening, counts, yield, and exports. Prepare a local narrated MP4 and saved outputs as the fallback. | 10-11 |
| What must be accomplished by the end of the term? | Cyrus | The targets are a common upload contract, frost replay, locally evaluated screening, a calibrated yield example, and a rehearsed integrated demonstration. | 8 |
| Does the system automatically apply treatment? | Cyrus | It supports the grower's decisions. The grower chooses and performs treatment; automatic equipment control is outside the baseline. | 1, 4 |

For a question crossing modules, the named owner gives the first answer and invites one short addition from the relevant teammate. If a result has not been measured or an interface is still planned, say so directly and identify the next validation step.

## 5. Full-group timed rehearsal log

### Rehearsal 1 - awaiting actual run

**Status:** Completed 11/25/2026

| Record field | Entry |
|---|---|
| Actual date and start time | 11/25/2026 3:00PM |
| Location or meeting format | Langsam Library |
| Deck version rehearsed | V1 |
| Participants required | Andrea Dall'Ara, Bridget Trout, Cyrus Speicher, Dang Luu |
| Actual attendees | All |
| Planned total | 10:00 / 600 seconds |
| Actual total, including hand-offs | Pending measurement |
| Full-group uninterrupted run completed? | Yes |
