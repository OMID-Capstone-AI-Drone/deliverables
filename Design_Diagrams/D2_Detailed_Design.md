# Header, conventions, and D2 overview
**Project Title:** OMID AI-Based Farm Disease, Pest, Yield, and Frost Detection System

**Team Members:** Andrea Dall'Ara, Bridget Trout, Dang Luu, Cyrus Speicher

**Goal Statement:** Help crop farmers make timely, informed decisions by combining stationary sensor readings and drone imagery to assess frost, disease, and pest risks, estimate crop 
yield, and provide organized records for planning and research.

# Finished components, the D2 diagrams

## For the Frost Prediction Engine (Rule-based Model)
### Data Model
```mermaid
erDiagram
    ZONE_CONFIG ||--o{ SENSOR_READING : "defines thresholds for"
    SENSOR_READING ||--|{ FROST_ALERT : "triggers"

    ZONE_CONFIG {
        string zone_id PK
        string growth_stage "e.g., dormant, swollen bud, bud break"
        float critical_temp "adjusted by farmer"
        int min_exposure_duration "default 30 min"
        string expected_cloudiness "weather-service input"
    }
    SENSOR_READING {
        string sensor_id PK
        timestamp tstamp PK
        float air_temp_f "at bud/shoot zone"
        float relative_humidity
        float wind_speed_mph
        string leaf_wetness "wet or dry"
        float soil_temp
        string soil_moisture "dry, normal, moist, saturated"
    }
    FROST_ALERT {
        string alert_id PK
        string zone_id FK
        timestamp issued_at
        int alert_level "1 to 6"
        string frost_type "radiation, advective, mixed"
        string injury_likelihood "low, medium, high"
    }
```

### Behavior (State Machine):
Every 15 minutes the following derived variables are calculated by the engine:<br>
**Cooling rate:** calculated over previous timeframes<br>
$$CoolingRate_{60} = T_{now} - T_{60min}$$<br>
**Dew point (magnus approximation):** Where a = 17.625 and b = 243.04°C<br>
$$\gamma = \ln\left(\frac{RH}{100}\right) + \frac{aT}{b+T}$$<br>
$$T_d = \frac{b\gamma}{a-\gamma}$$<br>
**Dew point spread:**<br>
$$DewPointSpread = T_{air} - T_{dewpoint}$$<br>
**Projected temperature:**<br>
$$T_{projected,2h} = T_{now} + 2(CoolingRate_{60})$$<br>

```mermaid
stateDiagram-v2
    [*] --> Level1_Normal
    Level1_Normal --> Level2_FrostWatch : [bud swell, and rule 2A or 2B is satisfied (Rapid dry cooling or Forecast approach, respectively)]
    Level1_Normal --> Level3_FrostWarning : [sensitive tissue, and rule 3A or 3C is satisfied (Radiation pattern or Advective freeze, respectively)]
    Level2_FrostWatch --> Level3_FrostWarning : [Conditions worsen to 3A, 3B, or 3C thresholds (3B is rapid dry-air cooling)]
    Level3_FrostWarning --> Level4_FrostImminent : [Air <= 33°F and rule 4A or 4B is satisfied (Cooling or Wet tissue, respectively) or Bud zone <= 32°F (Rule 4C)]
    Level4_FrostImminent --> Level5_EventInProgress : [Bud zone <= critical temp for duration or <= 30°F]
    Level5_EventInProgress --> Level6_AllClear : [Temp >= critical+2°F and stable/rising for 30m and no forecasted decline]
    Level6_AllClear --> [*]
```
Each rule has specified thresholds for measurements (either derived variables or sensor readings)

### Interface (I3):
**Endpoint:** internal method call<br>
**Inputs:** JSON array with the last 6 hourly sensor reading objects and the zone config thresholds<br>
**Outputs:** JSON array with alert level, frost type, injury likelihood, zone id<br>
**Errors:** Returns an error if there are fewer than two data points provided or the historical database is unreachable<br>


# Edge cases and error handling

# Requirements traceability table

# Design rationale

# AI Attribution
The authors would like to acknowledge the use of Gemini, 
developed by Google, in the preparation of this assignment. 
Gemini was used in the following way(s) in this assignment: 
Style and layout of the mermaid data model and the mermaid
behavior state machine diagram for the frost engine.
