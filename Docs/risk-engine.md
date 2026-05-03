## Situation Forecast & Risk Engine — Design Note

TacThor is not only a map or tracking application.  
Its long-term goal is to provide a shared operational picture and support human decision-making by estimating how situations evolve in space and time.

The core idea is that different scenarios — airsoft/milsim operations, natural disasters, logistics failures, or weather-driven events — can be described by the same general principles.

A threat is not just an object on the map.  
A threat is a process:

- it exists in space
- it changes over time
- it has intensity
- it has direction or trend
- it affects objectives, people, terrain, routes or resources
- it must be compared against available capacity

Examples:

- an enemy convoy moving along a road
- a stream overflowing a pond dam
- flood water spreading through a village
- a wildfire pushed by wind
- blocked access roads
- overloaded teams
- missing equipment or transport capacity

Although these situations look different, the decision-making problem is similar:

> estimate development, evaluate risk, identify capacity gaps and support resource allocation.

---

### Core Principle

TacThor should use a shared mathematical and conceptual model for both game and civil coordination scenarios.

The principles do not fundamentally change.  
Only the intensity, coefficients, terminology and input data change.

A simplified general form:

```text
risk(area, time) =
  hazard_intensity
  * exposure
  * vulnerability
  * trend
  * access_difficulty
  * weather_modifier
  / effective_capacity
