# Stakeholders
## Primary
- Farm owner
- Farm worker
 
## Secondary
- App administrator

## Hidden
- Crop buyer
- Agricultural researcher

## Stakeholder Map
```mermaid
flowchart TB
    subgraph Outer ["Outer Ring: Hidden Stakeholders (Never See UI / Downstream & Accessibility)"]
        direction TB
        H1["<b>Crop Buyer (US-04)</b><br/>Consumes exported yield forecasts<br/>for purchasing & distribution"]
        H2["<b>Agricultural Researcher (US-05)</b><br/>Consumes anonymized historical records<br/>of yield, pests, disease, & frost"]
        H3["<b>Accessibility & External Feeds</b><br/>High-glare/color-blind outdoor UI<br/>& Weather/Sensor APIs"]

        subgraph Middle ["Middle Ring: Secondary Stakeholders (Operational & Support)"]
            direction TB
            S1["<b>App Administrator (US-03)</b><br/>Manages user authentication &<br/>role-based account permissions"]

            subgraph Center ["Center Ring: Primary Stakeholders (Direct UI Users)"]
                direction LR
                P1["<b>Farm Owner (US-01)</b><br/>Monitors advance frost, pest,<br/>& disease risk alerts"]
                P2["<b>Farm Worker (US-02)</b><br/>Runs automated crop counting<br/>& yield estimation in field"]
            end
        end
    end

    style Outer fill:#fef3c7,stroke:#d97706,stroke-width:2px,stroke-dasharray: 5 5,color:#78350f
    style Middle fill:#e0f2fe,stroke:#0284c7,stroke-width:2px,color:#0c4a6e
    style Center fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#14532d
    style P1 fill:#ffffff,stroke:#16a34a,stroke-width:2px,color:#0f172a
    style P2 fill:#ffffff,stroke:#16a34a,stroke-width:2px,color:#0f172a
    style S1 fill:#ffffff,stroke:#0284c7,stroke-width:2px,color:#0f172a
    style H1 fill:#ffffff,stroke:#d97706,stroke-width:2px,color:#0f172a
    style H2 fill:#ffffff,stroke:#d97706,stroke-width:2px,color:#0f172a
    style H3 fill:#ffffff,stroke:#d97706,stroke-width:1px,stroke-dasharray: 3 3,color:#0f172a
```
# User Stories

## Farm owner
US-01 (Primary): As a farm owner, 
I want to know in advance when pests, disease, and frost will affect my crops, 
so that I can administer treatment only when necessary.  

AC-01.1  Given [starting context or precondition],
When  [the action the actor takes],
Then  [the verifiable outcome, with a number where one applies].

AC-01.2  Given [starting context or precondition],
When  [the action the actor takes],
Then  [the verifiable outcome, with a number where one applies].

### Use case draft
"Crop prediction"

Primary Actor: Farm owner

Secondary Actor: Web app, farm worker

Preconditions: 
Sensors are running and transmitting live information to the web app, 
prediction models are trained and finetuned to the specific crop field, 
the farm owner is logged in to the app.

Main success flow: 
1) Farm owner selects the disease event to be predicted. 
2) Web app uses sensor data to make a disease prediction and posts it to the screen.
3) Farm owner sees that black rot is highly likely to occur and takes preventative measures. 

Alternate success flow: 
1) Farm owner selects the disease event to be predicted. 
2) Web app uses sensor data to make a disease prediction and posts it to the screen.
3) Farm owner sees that crops are healthy and does not take preventative measures. 

Exception flow:
1) Farm owner selects the disease event to be predicted. 
2) Exception: Web app cannot access recent sensor data and states which sensors are down.

Postcondition:
Web app displays likelihood of disease, pest, and frost events. 

## Farm worker
US-02 (Primary): As a farm worker, 
I want automated crop counting and yield estimation, 
so that I save time from manually counting flower buds or early crop growth.

AC-02.1  Given [starting context or precondition],
When  [the action the actor takes],
Then  [the verifiable outcome, with a number where one applies].

AC-02.2  Given [starting context or precondition],
When  [the action the actor takes],
Then  [the verifiable outcome, with a number where one applies].

## App administrator
US-03 (Secondary): As an app administrator, 
I want user authentication and account permission management, 
so that I can only give access to privileged users. 

AC-03.1  Given [starting context or precondition],
When  [the action the actor takes],
Then  [the verifiable outcome, with a number where one applies].

AC-03.2  Given [starting context or precondition],
When  [the action the actor takes],
Then  [the verifiable outcome, with a number where one applies].

## Crop buyer
US-04 (Hidden): As a crop buyer, 
I want to know estimated crop yield, 
so that I can plan my purchasing and distribution.

AC-04.1  Given [starting context or precondition],
When  [the action the actor takes],
Then  [the verifiable outcome, with a number where one applies].

AC-04.2  Given [starting context or precondition],
When  [the action the actor takes],
Then  [the verifiable outcome, with a number where one applies].

## Agricultural researcher
US-05 (Hidden): As an agricultural researcher,
I want access to long term and organized records of crop yield, pests, disease, and frost, 
so that I can identify trends and factors affecting farms. 

AC-05.1  Given [starting context or precondition],
When  [the action the actor takes],
Then  [the verifiable outcome, with a number where one applies].

AC-05.2  Given [starting context or precondition],
When  [the action the actor takes],
Then  [the verifiable outcome, with a number where one applies].
