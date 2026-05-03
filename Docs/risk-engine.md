# TacThor Risk Engine Design Note

## Purpose

TacThor is not only a map or tracking application.

Its long-term goal is to provide a shared operational picture and support human decision-making by estimating how situations evolve in space and time.

The core idea is that different scenarios — airsoft, milsim operations, natural disasters, logistics failures, weather-driven events or civil coordination — can be described by the same general principles.

The principles do not fundamentally change.

Only these things change:

- terminology
- input data
- intensity
- coefficients
- thresholds
- scenario-specific interpretation

In other words:

> The same equations can describe both an enemy convoy moving through the area and a stream overflowing a pond dam.

The system should not try to command people automatically.

The system should help humans notice important changes earlier, understand why an area is becoming risky, identify missing capacity and allocate available resources more effectively.

---

## Core Thesis

A threat is not just an object on the map.

A threat is a process.

It:

- exists in space
- changes over time
- has intensity
- has direction or trend
- affects objectives, people, terrain, routes or resources
- must be compared against available capacity

Examples of threats or hazards:

- enemy unit movement
- enemy pressure near an objective
- a convoy moving along a road
- a stream overflowing a dam
- flood water spreading through a village
- wildfire pushed by wind
- blocked access roads
- overloaded teams
- missing equipment
- missing transport capacity
- bad weather affecting movement or safety
- communication loss

Although these situations look different, the decision-making problem is similar:

> Estimate development, evaluate risk, identify capacity gaps and support resource allocation.

---

## Shared Principle

TacThor should use one shared conceptual and mathematical model for both game and civil coordination scenarios.

The system should avoid building two separate engines.

Instead, it should use one shared engine with different configuration profiles.

```text
SituationEngine
  ├── ObservationLayer
  ├── PredictionLayer
  ├── RiskLayer
  └── DecisionSupportLayer
```

Possible scenario-specific models:

```text
GameRiskModel
CrisisRiskModel
WeatherRiskModel
ResourceGapModel
```

The abstract model stays the same.

The terminology changes.

| Game / Milsim | Civil / Crisis Coordination |
| --- | --- |
| enemy pressure | hazard / affected area |
| enemy unit | threat source / incident source |
| friendly unit | team |
| objective | task / target location |
| respawn | operational base |
| HQ | coordination center |
| resources | people, vehicles, equipment |
| battlefield | affected operational area |
| attack route | access route |
| frontline | hazard boundary / affected zone |

---

## General Risk Formula

A simplified general formula:

```text
risk(area, time) =
  hazard_intensity
  * exposure
  * vulnerability
  * trend
  * access_difficulty
  * weather_modifier
  / effective_capacity
```

Where:

```text
hazard_intensity   = strength or severity of the threat
exposure           = what is affected in the area
vulnerability      = how sensitive the affected target or area is
trend              = whether the situation is improving or worsening
access_difficulty  = how hard it is to reach or operate in the area
weather_modifier   = current and predicted weather impact
effective_capacity = usable available resources, not only raw numbers
```

The simplest useful abstraction is:

```text
risk_pressure = demand / effective_capacity
```

or:

```text
risk_pressure = threat_development / available_capacity
```

This applies to both:

```text
enemy pressure > own planned units
```

and:

```text
requests / affected locations > available people, vehicles or equipment
```

---

## Situation Pressure

TacThor should evaluate pressure against capacity.

Pressure is created when a developing threat or demand exceeds what available resources can reasonably handle.

Examples:

```text
12 enemy reports in an area
6 friendly players planned there
low visibility
high-priority objective nearby
→ increased tactical pressure
```

```text
8 open damage reports
2 available volunteer teams
1 blocked road
heavy rain expected in 2 hours
→ increased crisis coordination pressure
```

The system should not only count units or resources.

It should estimate whether those resources are actually usable.

Example:

```text
10 people far away behind a blocked road may provide less effective capacity
than 3 people already nearby with the right equipment.
```

---

## Effective Capacity

Raw resource count is not enough.

Effective capacity should include:

```text
effective_capacity =
  resource_count
  * suitability
  * readiness
  * access_factor
  * eta_factor
```

Where:

```text
resource_count = number or strength of available resources
suitability    = whether the resource can solve this type of problem
readiness      = whether the resource is ready to act
access_factor  = whether the resource can reach the area
eta_factor     = how soon the resource can realistically arrive
```

Examples:

- a vehicle without a driver has limited capacity
- a pump without transport has limited capacity
- a squad without communication has reduced operational value
- a team behind a blocked road may be unavailable
- a medic outside useful range has low immediate effect
- a sniper without visibility has reduced value
- a radio operator without line-of-sight or relay may not solve communication loss
- a bagger without operator or fuel is not full capacity
- thirty volunteers without tools may be less effective than five equipped people

---

## Demand

Demand describes how much response or attention a situation requires.

A generic demand model:

```text
demand =
  hazard_intensity
  * exposure
  * vulnerability
  * trend
  * access_difficulty
  * weather_modifier
```

For airsoft / milsim:

```text
demand =
  enemy_presence
  * objective_importance
  * own_plan_weakness
  * movement_trend
  * terrain_difficulty
  * weather_modifier
```

For civil / crisis coordination:

```text
demand =
  hazard_severity
  * affected_assets
  * vulnerability
  * time_urgency
  * access_difficulty
  * weather_modifier
```

---

## Risk Pressure Saturation

Risk pressure should not grow infinitely in the UI.

A useful normalized risk score can be calculated with saturation:

```text
risk_score = 100 * (1 - exp(-k * risk_pressure))
```

Where:

```text
risk_score    = normalized value between 0 and 100
k             = sensitivity coefficient
risk_pressure = demand / effective_capacity
```

This prevents unrealistic jumps while still making capacity shortages visible.

Example:

```text
low pressure   → low score
medium pressure → rising score
very high pressure → approaches 100
```

---

## Risk, Priority, Attention and Confidence

TacThor should keep these values separate:

```text
risk_score
priority_score
attention_score
confidence
```

They are related, but not the same.

```text
risk_score      = objective pressure or danger in the area
priority_score  = importance of the area, objective or task
attention_score = what should be brought to human attention now
confidence      = how reliable the assessment is
```

Suggested form:

```text
attention_score =
  risk_score
  * priority_modifier
  * forecast_modifier
  * uncertainty_modifier
```

This allows the system to distinguish between:

- high-risk but low-priority areas
- medium-risk but critical-priority areas
- low-confidence situations requiring verification
- worsening trends that need early attention
- old reports that should no longer dominate the map
- areas where missing capacity matters more than raw danger

---

## Priority

Priority is not the same as risk.

A high-risk area may have low operational priority.

A medium-risk area may have critical operational priority.

Examples:

```text
High risk, low priority:
Enemy activity far away from the main objective.
```

```text
Medium risk, high priority:
Enemy movement near a critical objective.
```

```text
High risk, low priority:
Flooded field with no people, infrastructure or access requirement.
```

```text
Medium risk, high priority:
Partially blocked road that is the only route to several unresolved tasks.
```

Therefore, TacThor should not collapse all values into one hidden number.

It should calculate multiple values and then derive attention.

```text
risk_score      → how bad the situation is
priority_score  → how important it is
attention_score → how urgently humans should look at it
```

---

## Information Age and Confidence

Reports are not permanent truth.

They are observations with age and uncertainty.

Every observation should include:

```text
timestamp
source
confidence
decay_rate
```

Suggested confidence decay:

```text
confidence_now =
  initial_confidence * exp(-age / half_life)
```

Different report types should have different half-life values.

Airsoft contact reports decay quickly.

Examples:

```text
enemy seen 3 minutes ago  → still relevant
enemy seen 45 minutes ago → low confidence
```

Flood or infrastructure reports may decay more slowly.

Examples:

```text
road blocked 30 minutes ago → probably still relevant
water level from yesterday  → may be outdated
```

Weather can change decay rates.

Example:

```text
heavy rain forecast → water-related reports age faster
strong wind forecast → fire-related reports age faster
```

---

## Threat / Hazard Development Types

TacThor should support multiple threat development models.

### 1. Sudden / Stochastic Threats

Examples:

- unexpected enemy contact
- ambush
- surprise attack
- tornado
- sudden incident
- unexpected road collapse

Characteristics:

- low predictability
- fast change
- high uncertainty
- short report half-life
- needs fast confirmation

Airsoft often behaves like this.

A threat may appear suddenly and unpredictably.

The model should therefore handle uncertainty and report decay aggressively.

---

### 2. Front-Based / Progressing Threats

Examples:

- flood
- wildfire
- spreading affected area
- advancing enemy pressure
- expanding search area
- moving storm cell

Characteristics:

- visible or estimated boundary
- direction can often be estimated
- speed can sometimes be estimated
- weather and terrain matter heavily
- forecast horizon is useful

Floods are often closer to this model.

They may behave like a slow-moving front.

This makes them more predictable than sudden tactical contact, but they can still change rapidly when a dam, bridge, road or other critical point fails.

---

### 3. Logistical / Capacity Threats

Examples:

- lack of equipment
- blocked routes
- overloaded base
- exhausted teams
- missing transport
- communication loss
- missing operator for available equipment
- lack of fuel
- too many unresolved requests
- too many objectives assigned to one team

Characteristics:

- may not look like a direct threat
- often becomes visible through delays
- can cause cascading failures
- directly affects effective capacity

This type is important because not every dangerous situation is caused by an external hazard.

Sometimes the risk comes from the system being unable to respond.

---

## Map Representation

TacThor should evaluate risk spatially.

A threat may be represented as:

```text
Point
LineString
Polygon
Grid / raster cell
Network node
Network edge
```

Examples:

```text
Point      = report, incident, team, resource, blocked location
LineString = road, route, river, movement corridor
Polygon    = affected area, flood zone, fire zone, enemy pressure zone
Grid cell  = computed risk field
Node       = base, bridge, objective, depot, relay point
Edge       = access route, supply route, communication link
```

An area should not be evaluated only by size.

A large empty field may matter less than a small bridge, road or base.

Better model:

```text
area_impact =
  area_size
  * exposure_density
  * importance_factor
```

Or, for grid-based calculation:

```text
risk(area) =
  sum(risk(cell)) for all cells inside the area
```

Example:

```text
cell_risk =
  hazard_intensity_cell
  * exposure_cell
  * vulnerability_cell
  / effective_capacity_nearby
```

---

## Access Network and Bottlenecks

Some places matter more than their physical size.

Examples:

- bridge
- dam
- road junction
- access road
- operational base
- supply point
- radio relay
- checkpoint
- main objective
- evacuation point
- equipment depot

TacThor should treat these as network nodes.

```text
node = place, base, bridge, objective, depot, relay
edge = road, route, access path, supply path, communication path
```

The system should detect when a threat affects a bottleneck.

Example:

```text
This location is not the largest affected area,
but its loss blocks access to 3 unresolved tasks.
```

Airsoft example:

```text
This road is not the main objective,
but losing it cuts off reinforcement route to Objective Delta.
```

Civil coordination example:

```text
This bridge is one point,
but if it fails, two teams and one equipment depot become unreachable.
```

---

## Cascading Effects

One event can create another problem.

TacThor should support basic dependency and cascade modeling.

Civil example:

```text
flood
→ blocked road
→ team cannot reach task
→ task remains unresolved
→ priority increases
→ base becomes overloaded
```

Airsoft example:

```text
radio operator lost
→ communication confidence drops
→ unit positions become outdated
→ objective risk increases
→ HQ attention required
```

Weather example:

```text
heavy rain forecast
→ road access worsens
→ ETA increases
→ effective capacity decreases
→ unresolved task pressure increases
```

Cascading effects should not be hidden.

The system should explain them in the assessment output.

---

## Weather as Risk Modifier

Weather is not just an informational overlay.

It is a direct risk modifier.

In airsoft / milsim:

- rain affects movement
- fog affects visibility
- wind affects sound and smoke
- heat affects fatigue and medical risk
- cold affects endurance and safety
- storms affect event safety and communication
- poor visibility lowers confidence in reports

In civil / crisis use:

- rain affects flooding and terrain access
- wind affects fire spread and falling trees
- temperature affects safety of people and teams
- visibility affects movement and search operations
- storms affect road safety and infrastructure
- forecasted rain may increase urgency even before the situation worsens

Weather should influence:

```text
hazard_intensity
access_difficulty
trend
confidence
effective_capacity
```

Example:

```text
weather_modifier = 1.0   → neutral
weather_modifier = 1.2   → worsens situation moderately
weather_modifier = 1.5   → worsens situation significantly
weather_modifier = 0.8   → improves or reduces pressure
```

Weather should support both:

```text
current weather
forecast weather
manual HQ weather note
```

---

## Scenario Examples

### Airsoft / Milsim Example

Situation:

```text
Objective Delta has high priority.
There are multiple enemy reports nearby.
Friendly units planned for the area are delayed.
Visibility is worsening.
Reports are recent but not fully confirmed.
```

Assessment:

```text
HIGH ATTENTION — Objective Delta

Reasons:
- reported enemy presence exceeds planned friendly capacity
- nearby friendly positions are outdated
- visibility is degraded
- objective has high strategic priority
- enemy report confidence is still relevant

Missing capabilities:
- recon
- communication relay
- reinforcement
```

---

### Civil / Flood Example

Situation:

```text
A stream is overflowing near a pond dam.
The flow direction points toward a village sector.
There are several unresolved requests in the area.
Only one team is nearby.
More rain is forecast.
The main access route may become blocked.
```

Assessment:

```text
CRITICAL ATTENTION — Village Sector B

Reasons:
- affected area contains multiple unresolved requests
- available capacity is below required threshold
- forecast indicates additional rainfall
- main access road may become degraded
- hazard trend is worsening

Missing capabilities:
- transport
- pump
- engineering support
- additional volunteer team
```

---

### Logistics Example

Situation:

```text
Several teams are available.
However, two are already assigned.
One has no transport.
One is behind a blocked road.
Only one team can realistically reach the task area.
```

Assessment:

```text
MEDIUM RISK / HIGH CAPACITY GAP

Reasons:
- raw resource count looks sufficient
- effective available capacity is low
- access limitations reduce usable resources
- task completion may be delayed

Missing capabilities:
- transport
- access route confirmation
```

---

## Suggested Data Structures

### Hazard State

```ts
type HazardType =
  | "enemy_pressure"
  | "flood"
  | "fire"
  | "weather"
  | "blocked_access"
  | "resource_gap"
  | "communication_loss";

type HazardState = {
  id: string;
  type: HazardType;

  geometry: Point | LineString | Polygon;

  intensity: number;       // 0..1
  direction?: number;      // degrees
  speed?: number;          // m/min, km/h, or abstract
  trend: "improving" | "stable" | "worsening";

  confidence: number;      // 0..1
  observedAt: Date;
  forecastUntil?: Date;

  affectedObjectives: string[];
  affectedRoutes: string[];
  affectedResources: string[];
};
```

### Resource State

```ts
type ResourceCapability =
  | "people"
  | "transport"
  | "medical"
  | "engineering"
  | "drying"
  | "pumping"
  | "radio"
  | "recon"
  | "at"
  | "sniper"
  | "command";

type ResourceState = {
  id: string;
  name: string;

  capabilities: ResourceCapability[];

  location: GeoPoint;
  assignedTaskId?: string;

  readiness: number;       // 0..1
  availability: number;    // 0..1
  fatigue?: number;        // 0..1

  capacityValue: number;
};
```

### Area Assessment

```ts
type AreaAssessment = {
  areaId: string;

  riskScore: number;       // 0..100
  priorityScore: number;   // 0..100
  attentionScore: number;  // 0..100
  confidence: number;      // 0..1

  trend: "improving" | "stable" | "worsening";

  reasons: string[];

  missingCapabilities: ResourceCapability[];

  affectedTasks: string[];
  affectedRoutes: string[];
};
```

---

## Suggested Configuration

TacThor should not hardcode all coefficients.

Each event should be able to tune weights.

Example game configuration:

```yaml
risk_model:
  type: game

  weights:
    hazard: 1.3
    exposure: 1.0
    vulnerability: 1.1
    trend: 0.9
    weather: 0.6
    access: 0.7
    capacity: 1.4
    priority: 0.25

  report_decay:
    enemy_contact_half_life_minutes: 20
    position_half_life_minutes: 10
    route_status_half_life_minutes: 60

  thresholds:
    low: 25
    medium: 50
    high: 75
    critical: 90
```

Example crisis configuration:

```yaml
risk_model:
  type: crisis

  weights:
    hazard: 1.4
    exposure: 1.3
    vulnerability: 1.5
    trend: 1.2
    weather: 1.1
    access: 1.0
    capacity: 1.6
    priority: 0.35

  report_decay:
    hazard_half_life_minutes: 120
    road_status_half_life_minutes: 180
    resource_status_half_life_minutes: 60
    weather_forecast_refresh_minutes: 30

  thresholds:
    low: 20
    medium: 45
    high: 70
    critical: 85
```

The same core engine can therefore serve multiple scenarios.

Only weights, terminology and thresholds change.

---

## Testing Through Airsoft

Airsoft can be used as a safe testing environment for the broader situation-awareness model.

It provides:

- incomplete information
- fog of war
- moving units
- overloaded command
- radio communication issues
- changing weather
- uncertain reports
- limited resources
- time pressure
- unexpected tactical situations

After each event, TacThor should evaluate:

```text
precision:
  how many high-risk areas became actual problems

recall:
  how many actual problems were detected in advance

lead_time:
  how many minutes before the problem the system raised attention

false_positive_cost:
  how much attention was wasted on unnecessary alerts
```

The goal is not to make the system scream all the time.

The goal is to raise attention at the right moment.

---

## Decision Support, Not Autopilot

TacThor should not automatically command people.

The engine should support human decision-making by highlighting:

- where the situation is getting worse
- where capacity is insufficient
- where information is outdated
- where weather may change the situation
- where a route, base or task is becoming critical
- where a human decision is needed

The system should explain why an area is marked as risky.

Bad output:

```text
Area Delta risk: 87
```

Good output:

```text
HIGH ATTENTION — Area Delta

Reasons:
- threat pressure is increasing
- available capacity is below planned level
- access route is degraded
- weather forecast worsens the situation within 2 hours
- latest field report confidence is decreasing

Missing capabilities:
- transport
- engineering support
- communication relay
```

The goal is not to predict the future perfectly.

The goal is:

> to reduce the time between an important signal and correct human attention.

---

## Design Summary

TacThor should treat airsoft as a safe testing environment for a broader situation-awareness model.

The same general equations and principles apply across domains:

```text
threat development
+ exposure
+ vulnerability
+ access difficulty
+ weather
- effective capacity
= operational pressure
```

The coefficients, thresholds, terminology and input data change per scenario.

The principle remains the same:

> estimate development, evaluate risk, identify capacity gaps and help humans allocate resources effectively.

TacThor is therefore not only a player map.

It is a system for estimating development, risk and capacity in space and time.
