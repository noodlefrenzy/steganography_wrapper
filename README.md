---
title: "Robotics & Autonomous Driving Knowledge Base"
description: "Top-level overview and navigation for robotics & ADS primers, deep dives, and research log."
tags: [robotics, autonomous-driving, simulation, planning, perception, acceleration, rl]
last_reviewed: 2025-09-11
version: 0.1.0
---

**Concise, structured notes on modern robotics and autonomous driving systems:** simulation, middleware, perception, mapping, planning & control, reinforcement learning, world / vision-language(-action) models, and hardware acceleration. Deep dives expand on specialized topics; primers stay intentionally brief and scannable.

---

## Quick Navigation

- Documentation Index: [docs/README.md](docs/README.md)
- Robotics Primer: [docs/basic_robotics_notes.md](docs/basic_robotics_notes.md)
- Autonomous Driving (ADS) Primer: [docs/basic_ads_notes.md](docs/basic_ads_notes.md)
- Foundational Research Log: [.copilot-tracking/research/20250909-robotics-foundations-research.md](.copilot-tracking/research/20250909-robotics-foundations-research.md)

---

## Deep Dive Collections

| Domain | File |
|--------|------|
| Simulation & Digital Twins | [docs/robotics_deep_dive_simulation.md](docs/robotics_deep_dive_simulation.md) |
| Hardware Acceleration & Transport | [docs/robotics_deep_dive_hardware_acceleration.md](docs/robotics_deep_dive_hardware_acceleration.md) |
| Mapping & World Models | [docs/robotics_deep_dive_mapping_world_models.md](docs/robotics_deep_dive_mapping_world_models.md) |
| Planning & Manipulation | [docs/robotics_deep_dive_planning_manipulation.md](docs/robotics_deep_dive_planning_manipulation.md) |
| RL Frameworks & Training Pipelines | [docs/robotics_deep_dive_rl_frameworks.md](docs/robotics_deep_dive_rl_frameworks.md) |
| World / VLM / Foundation Models | [docs/robotics_deep_dive_models_world_vlm.md](docs/robotics_deep_dive_models_world_vlm.md) |
| Spot Platform & SDK | [docs/robotics_deep_dive_spot_platform.md](docs/robotics_deep_dive_spot_platform.md) |
| ADS Perception | [docs/ads_deep_dive_perception.md](docs/ads_deep_dive_perception.md) |
| ADS Prediction | [docs/ads_deep_dive_prediction.md](docs/ads_deep_dive_prediction.md) |
| ADS Planning & Control | [docs/ads_deep_dive_planning_control.md](docs/ads_deep_dive_planning_control.md) |

---

## Cross-Domain Themes

| Theme | Robotics Source | ADS Source |
|-------|-----------------|-----------|
| Mapping → Planning Interface | [Mapping & World Models](docs/robotics_deep_dive_mapping_world_models.md) | [ADS Primer](docs/basic_ads_notes.md) |
| GPU & Zero-Copy Pipelines | [Hardware Acceleration](docs/robotics_deep_dive_hardware_acceleration.md) | [Perception Deep Dive](docs/ads_deep_dive_perception.md) |
| Planning Hierarchies | [Planning & Manipulation](docs/robotics_deep_dive_planning_manipulation.md) | [Planning & Control](docs/ads_deep_dive_planning_control.md) |
| World / VLM Models | [World & VLM Models](docs/robotics_deep_dive_models_world_vlm.md) | Embedded in perception / prediction notes |
| Simulation Strategy | [Simulation Deep Dive](docs/robotics_deep_dive_simulation.md) | [ADS Primer](docs/basic_ads_notes.md) |

---

## Repository Layout (High-Level)

- `docs/` — Primers, deep dives, index, external resources.
- `.copilot-tracking/` — Internal research & curation logs (not primary reading material).
- `src/` — Reserved for future illustrative code snippets or minimal runnable examples.

---

## Primer & Deep Dive Philosophy

1. Primers stay brief: high-level stack mental model, minimal duplication.
2. Deep dives capture trade-offs, architecture choices, metrics, and open questions.
3. Shared terminology should migrate to a future centralized glossary (planned).
4. Date-based front matter supports periodic review and pruning of outdated ideas.

---

## Contribution & Maintenance Workflow (Lightweight)

When adding new material:

1. Decide: Primer (foundational) vs Deep Dive (specialized depth).
2. Add/update front matter with `last_reviewed` and bump `version` if structural changes occur.
3. Cross-link related sections (avoid orphan pages).
4. Add TODO or open question blocks rather than speculative paragraphs.

Suggested formatting conventions (mirroring existing docs):

- Use lists for layered detail exposure.
- Prefer tables for cross-cut comparisons (e.g., sensor modality trade-offs).
- Keep line length moderate for diff clarity.

---

## Research Log Reference

Foundational systems & taxonomy rationale lives here:

-.copilot-tracking/research/20250909-robotics-foundations-research.md

---

## Roadmap / Future Candidates

| Area | Candidate Addition |
|------|--------------------|
| Glossary | Introduce `docs/GLOSSARY.md` centralizing stack terms |
| Benchmarks | Template: latency / throughput / accuracy / resource footprint |
| Contribution Guide | `CONTRIBUTING.md` with style & review cadence |
| Example Pipelines | Minimal ROS 2 node launch + mapping / planning diagram |
| Taxonomy Diagrams | Unified perception→prediction→planning dataflow overlays |

---

## License / Attribution

If external figures or excerpts are added later, ensure proper citation and keep an attribution section. (Not yet required — current content is original notes.)

---

Last reviewed: 2025-09-11

