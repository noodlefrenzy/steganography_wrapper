<!-- markdownlint-disable-file -->
<!-- markdown-table-prettify-ignore-start -->
# ZIL Runner - Product Requirements Document (PRD)

Version 1.0 | Status Draft | Owner TBD | Team TBD | Target Q1 2026 | Lifecycle New

## Progress Tracker

| Phase | Done | Gaps | Updated |
|-------|------|------|---------|
| Context | ✅ Initial Draft | Needs stakeholder validation | 2026-01-06 |
| Problem & Users | ✅ Initial Draft | User research to validate personas | 2026-01-06 |
| Scope | ✅ Initial Draft | Technical feasibility assessment needed | 2026-01-06 |
| Requirements | ✅ Initial Draft | Prioritization to be refined with team | 2026-01-06 |
| Metrics & Risks | ✅ Initial Draft | Baseline metrics need validation | 2026-01-06 |
| Operationalization | ✅ Initial Draft | Infrastructure decisions pending | 2026-01-06 |
| Finalization | ⏳ In Progress | Awaiting technical validation and stakeholder review | 2026-01-06 |

Unresolved Critical Questions: 7 | TBDs: 0

## 1. Executive Summary

### Context

Interactive fiction, particularly text adventures in the tradition of Zork, represents a rich legacy of computational storytelling. ZIL (Zork Implementation Language) is the original language used to create classic Infocom adventures. While the format has passionate enthusiasts, modern tooling is limited, and creating new adventures requires deep technical knowledge. Additionally, there's no systematic way to test adventures beyond manual human playthrough.

This PRD defines a modern, web-based platform that democratizes ZIL adventure creation through generative AI, provides an accessible player experience, and introduces automated adventure validation through AI-powered player agents.

### Core Opportunity

Enable anyone to create, play, and validate ZIL text adventures through a web-based platform that combines traditional ZIL runtime capabilities with modern generative AI for both content creation and automated testing.

### Goals

| Goal ID | Statement | Type | Baseline | Target | Timeframe | Priority |
|---------|-----------|------|----------|--------|-----------|----------|
| G-001 | Enable non-programmers to create playable ZIL adventures | Adoption | 0 users | 100+ created adventures | 6 months | P0 |
| G-002 | Reduce adventure creation time from days to hours | Efficiency | ~40 hours | <4 hours | Launch | P0 |
| G-003 | Provide automated adventure validation | Quality | 0% automated testing | 80% coverage | 6 months | P1 |
| G-004 | Create engaging web-based player experience | Engagement | N/A | 70%+ completion rate | 3 months | P1 |

### Objectives (Optional)

| Objective | Key Result | Priority | Owner |
|-----------|------------|----------|-------|
| Launch MVP platform | 50 beta users creating adventures | P0 | Product |
| Establish content library | 25 publicly shared adventures | P1 | Community |
| Validate AI generation quality | 85%+ playability score for generated adventures | P0 | Engineering |

## 2. Problem Definition

### Current Situation

Creating ZIL adventures today requires:

- Deep knowledge of ZIL syntax and Infocom Z-machine internals
- Setting up vintage development tools or modern ZIL compilers
- Manual testing through complete playthroughs
- Limited distribution options (mostly hobbyist forums)
- No systematic way to validate adventure completability or detect dead ends

Playing ZIL adventures requires:

- Finding and downloading interpreter software
- Managing local files for game saves
- Limited accessibility features

### Problem Statement

The barrier to entry for creating and playing ZIL text adventures is too high, limiting both the creation of new content and the audience for existing adventures. There is no modern platform that combines easy adventure creation, web-based play, and automated quality validation.

### Root Causes

- ZIL was designed in the 1980s for expert programmers, not content creators
- No bridge between natural language game design and ZIL code generation
- Testing adventures is labor-intensive and prone to missing edge cases
- Distribution requires technical knowledge of Z-machine formats and interpreters
- Modern web users expect instant access without downloads or setup

### Impact of Inaction

- Interactive fiction remains a niche hobby accessible only to technical enthusiasts
- Potential storytellers cannot easily bring their creative visions to life
- Classic adventure game format fails to reach new audiences
- ZIL knowledge becomes increasingly rare as original practitioners age out
- Adventure quality issues remain undetected until player frustration

## 3. Users & Personas

| Persona | Goals | Pain Points | Impact |
|---------|-------|------------|--------|
| **Creative Writer** (Primary) | Create engaging text adventures without coding; Share stories with players | Intimidated by programming; Can't test adventures thoroughly; Doesn't know ZIL syntax | High - Core target user for AI generation |
| **Retro Gaming Enthusiast** (Primary) | Play classic and new text adventures in browser; Discover new adventures easily | Tired of setup hassle; Wants instant play; Seeks fresh content | High - Primary player base |
| **Adventure Developer** (Secondary) | Create complex ZIL adventures efficiently; Validate adventure completability | Manual testing is time-consuming; Hard to catch all logical paths; Needs automated QA | Medium - Advanced use cases |
| **AI Researcher** (Secondary) | Study AI's ability to create coherent game narratives; Test reasoning capabilities | Needs testbed for narrative generation; Wants to measure agent performance | Medium - Research and validation |
| **Educator** (Tertiary) | Teach narrative design or classic computing; Engage students with interactive content | Needs easy content creation; Students need accessible platform | Low - Educational applications |

### Journeys (Optional)

**Creative Writer Journey:**

1. Arrives at ZIL Runner with adventure idea
2. Answers conversational prompts about setting, characters, puzzles
3. Reviews generated ZIL code (optional, can treat as black box)
4. Tests adventure in embedded player
5. Watches AI agent attempt to solve it
6. Reviews agent feedback on logical issues
7. Iterates on prompts to refine adventure
8. Publishes to platform library

**Player Journey:**

1. Browses adventure library or arrives via shared link
2. Clicks "Play Now" - instant launch in browser
3. Plays adventure with clean, accessible interface
4. Saves progress (persists in browser)
5. Shares favorite adventures with friends

## 4. Scope

### In Scope

- **Adventure Generator Module:** Conversational interface using generative AI (Claude or compatible) to create ZIL source code from simple prompts and Q&A
- **Web-based ZIL Runner:** Browser-based Z-machine interpreter to render and play ZIL adventures
- **Player Agent Module:** AI-powered agent that can attempt to solve adventures and provide feedback
- **Basic adventure library:** Simple storage and retrieval for generated adventures
- **State persistence:** Browser-based save system for player progress
- **Export functionality:** Download generated ZIL source code
- **Basic UI/UX:** Clean, accessible web interface for all three modes

### Out of Scope (justify if empty)

- **Advanced multiplayer features:** Real-time co-op or competitive modes (future consideration)
- **Mobile native apps:** Web-first approach, mobile browsers acceptable for V1
- **Monetization:** Free platform for initial launch; explore later
- **Full-featured IDE:** Advanced ZIL editing tools (users can export and use external tools)
- **Social features:** Comments, ratings, user profiles beyond basic attribution (post-MVP)
- **ZIL compiler from scratch:** Leverage existing open-source compilers (e.g., ZILF)
- **Graphical adventures:** Text-only for initial scope
- **Audio/Sound effects:** Text-only experience
- **Non-ZIL formats:** Initial focus on ZIL; see ADR-001 for format prioritization analysis

### Assumptions

- Generative AI models (e.g., Claude) are capable of producing valid ZIL code from natural language descriptions
- Users have modern browsers with JavaScript support (ES2020+)
- Open-source Z-machine interpreters (e.g., Parchment, ZVM) can be integrated into web platform
- Sufficient prompt engineering can guide AI to create playable, logical adventures
- AI player agents can parse game state and make reasonable action decisions
- Browser storage (localStorage/IndexedDB) is adequate for local save game persistence; backend storage is available for cloud-based features

### Constraints

- Frontend must be web-based; backend services are allowed for AI API calls, compilation, and cloud storage
- Must respect rate limits and costs of generative AI API usage
- Generated adventures must be compatible with standard Z-machine format (Z3, Z5, or Z8)
- Player agent must operate within reasonable time bounds (not hours to solve simple puzzles)
- Cannot guarantee AI-generated content is bug-free; must provide validation layer
- Must comply with AI model usage policies and content guidelines

## 5. Product Overview

### Value Proposition

**For Creative Writers:** Transform your story ideas into playable text adventures in hours, not days, without learning to code.

**For Players:** Instantly play classic-style text adventures in your browser with modern convenience and a growing library of AI-generated content.

**For Developers:** Automatically validate your adventures for completability and logical consistency using AI player agents, catching issues before human players encounter them.

### Differentiators (Optional)

- **AI-Native Creation:** First platform to use modern generative AI for end-to-end ZIL adventure creation
- **Integrated Validation:** Built-in AI agent testing provides unprecedented automated QA for text adventures
- **Zero Friction:** No downloads, no setup, instant play and creation
- **Educational Transparency:** Users can see generated ZIL code, learning the language organically
- **Modern UX on Vintage Format:** Brings 1980s adventure gaming to 2020s web standards

### UX / UI (Conditional)

**Three Primary Modes with Unified Interface:**

1. **Generator Mode:**
   - Conversational interface guiding users through adventure design
   - Progressive Q&A flow (setting → characters → rooms → puzzles → items → win condition)
   - Real-time ZIL code preview (collapsible, optional)
   - "Generate Adventure" action triggers AI synthesis
   - Visual feedback during generation (loading states, progress)

2. **Player Mode:**
   - Clean text display area for game narrative
   - Command input field with autocomplete for common verbs
   - Inventory display, compass/direction helper
   - Save/Load interface
   - Transcript export option

3. **Agent Mode:**
   - Adventure selection (from generated library or upload)
   - AI agent configuration (model selection, max turns, strategy)
   - Live playthrough display showing agent commands and game responses
   - Analysis summary showing: success/failure, dead ends found, solution path, logic issues
   - Downloadable report for adventure creator

**Design Principles:**

- Accessibility-first: WCAG 2.1 AA compliance, keyboard navigation
- Retro aesthetics with modern usability (monospace fonts, terminal-inspired but clean)
- Mobile-responsive (though desktop-primary experience)
- Fast feedback loops (instant command response, clear AI progress indicators)

UX Status: **Design Required** (wireframes and mockups needed before implementation)

## 6. Functional Requirements

| FR ID | Title | Description | Goals | Personas | Priority | Acceptance | Notes |
|-------|-------|------------|-------|----------|----------|-----------|-------|
| FR-001 | Adventure Prompt Interface | Conversational UI that guides users through adventure creation via Q&A | G-001, G-002 | Creative Writer | P0 | User can complete prompt flow in <15 min; AI generates valid ZIL | Multi-turn conversation with context retention |
| FR-002 | ZIL Code Generation | Use generative AI (Claude) to synthesize ZIL source from user prompts | G-001, G-002 | Creative Writer, Developer | P0 | Generated ZIL compiles without errors 90%+ of time | Requires robust prompt engineering and validation |
| FR-003 | Generated Code Preview | Display generated ZIL source code with syntax highlighting | G-001 | Developer, Educator | P1 | Code displayed in readable format with line numbers | Educational feature; helps users learn ZIL |
| FR-004 | In-Browser ZIL Compiler | Compile ZIL source to Z-machine bytecode in browser or backend | G-001, G-004 | All users | P0 | Compilation succeeds for valid ZIL; errors surface with line numbers | Leverage ZILF or similar; may require WASM port |
| FR-005 | Web-Based Z-Machine Runner | Render and execute Z-machine bytecode in browser | G-004 | Retro Gamer, Creative Writer | P0 | Adventures play smoothly; commands processed in <200ms | Use existing JS Z-machine (Parchment, ZVM) |
| FR-006 | Command Input & Parser | Accept player text commands and dispatch to Z-machine | G-004 | Retro Gamer | P0 | All standard ZIL verbs recognized; custom commands work | Standard Infocom verb set + game-specific |
| FR-007 | Game State Display | Show current room, inventory, score, turn count | G-004 | Retro Gamer | P0 | UI updates immediately on state change | Standard adventure game HUD elements |
| FR-008 | Save/Load Functionality | Persist game state to browser storage; restore on demand | G-004 | Retro Gamer | P1 | Players can save, close browser, return and load | Use localStorage or IndexedDB |
| FR-009 | AI Player Agent Core | Agent that can read game text, decide actions, send commands | G-003 | Developer, AI Researcher | P0 | Agent completes simple adventure (5-10 rooms) successfully | Requires prompt design for agent reasoning |
| FR-010 | Agent Strategy Selection | Configure agent behavior (e.g., explore-first, goal-directed, random) | G-003 | Developer, AI Researcher | P2 | User can choose from 3+ strategies and observe differences | Enables experimentation with agent approaches |
| FR-011 | Agent Playthrough Visualization | Display agent's commands and game responses in real-time or replay | G-003 | Developer, AI Researcher | P1 | Full transcript visible; can pause/step through | Critical for debugging adventures |
| FR-012 | Adventure Validation Report | Analyze agent playthrough for dead ends, unsolvable states, logic errors | G-003 | Creative Writer, Developer | P0 | Report identifies specific issues with room/item references | Must be actionable for creators |
| FR-013 | Adventure Export | Download generated ZIL source code as .zil file | G-001 | Creative Writer, Developer | P1 | Clean download with proper file structure | Enables external editing/archiving |
| FR-014 | Adventure Import | Upload ZIL source or compiled Z-code file to play or test | G-003, G-004 | Developer, Retro Gamer | P1 | Accepts .zil and .z3/.z5/.z8 files; validates format | Enables testing external adventures |
| FR-015 | Adventure Library Storage | Store generated adventures with metadata (title, author, created date) | G-001 | Creative Writer | P1 | Adventures persist across sessions; searchable list | Browser storage initially; DB later |
| FR-016 | Quick Play from Library | One-click launch of any saved adventure | G-004 | Retro Gamer | P1 | Adventure loads and starts in <2 seconds | Smooth UX for replaying content |
| FR-017 | Prompt Template Library | Provide starter templates for common adventure types (mystery, dungeon, sci-fi) | G-002 | Creative Writer | P2 | Users can select template and customize quickly | Accelerates initial creation |
| FR-018 | Accessibility Controls | Keyboard shortcuts, screen reader support, adjustable text size | G-004 | All personas | P1 | Meets WCAG 2.1 AA; full keyboard navigation | Essential for inclusive platform |

### Feature Hierarchy (Optional)

```plain
ZIL Runner Platform
├── Adventure Generator
│   ├── Conversational Prompting Engine (FR-001)
│   ├── ZIL Code Generator (FR-002)
│   ├── Code Preview & Export (FR-003, FR-013)
│   ├── Template Library (FR-017)
│   └── Compiler Integration (FR-004)
├── Adventure Player
│   ├── Z-Machine Runner (FR-005)
│   ├── Command Interface (FR-006)
│   ├── Game State Display (FR-007)
│   ├── Save/Load System (FR-008)
│   ├── Adventure Library (FR-015, FR-016)
│   ├── Import Functionality (FR-014)
│   └── Accessibility Features (FR-018)
└── AI Player Agent
    ├── Agent Reasoning Engine (FR-009)
    ├── Strategy Configuration (FR-010)
    ├── Playthrough Visualization (FR-011)
    └── Validation & Reporting (FR-012)
```

## 7. Non-Functional Requirements

| NFR ID | Category | Requirement | Metric/Target | Priority | Validation | Notes |
|--------|----------|------------|--------------|----------|-----------|-------|
| NFR-001 | Performance | Command response time | <200ms from input to display | P0 | Automated latency testing | Critical for player immersion |
| NFR-002 | Performance | Adventure generation time | <60 seconds for typical adventure | P1 | Log AI API response times | Depends on AI model speed |
| NFR-003 | Performance | Agent solution time | <5 minutes for 10-room adventure | P1 | Benchmark test suite | Prevents excessive wait |
| NFR-004 | Reliability | Generated adventure success rate | 90%+ compile successfully | P0 | Test harness with sample prompts | Measure prompt engineering quality |
| NFR-005 | Reliability | Platform uptime (when backend deployed) | 99.5% availability | P1 | Monitoring service | SLA for production |
| NFR-006 | Scalability | Concurrent players | Support 1000+ simultaneous players | P2 | Load testing | Client-side reduces server load |
| NFR-007 | Scalability | Adventure library size | Support 10,000+ stored adventures | P2 | Database capacity planning | Scales with backend storage |
| NFR-008 | Security | Input sanitization | Prevent XSS/injection via game commands | P0 | Security audit & pen testing | Critical web security |
| NFR-009 | Security | AI prompt injection protection | Detect and reject malicious prompts | P0 | Adversarial testing | Protect AI API from abuse |
| NFR-010 | Security | Rate limiting | Limit AI calls to 10/hour per user (free tier) | P1 | Backend rate limiter | Prevent API cost abuse |
| NFR-011 | Privacy | Local-first data | Game saves stored client-side by default | P1 | Privacy audit | No PII collection initially |
| NFR-012 | Privacy | Optional account creation | Users can create/play anonymously | P2 | Feature specification | Reduce friction |
| NFR-013 | Accessibility | WCAG 2.1 AA compliance | Pass accessibility audit | P1 | Automated accessibility testing | Legal/ethical requirement |
| NFR-014 | Accessibility | Screen reader support | Full navigation via screen reader | P1 | Manual testing with NVDA/JAWS | Critical for blind users |
| NFR-015 | Accessibility | Keyboard navigation | All features accessible without mouse | P1 | Manual keyboard-only testing | Essential UX baseline |
| NFR-016 | Observability | Error logging | Log all AI failures and compiler errors | P1 | Logging infrastructure | Debugging and monitoring |
| NFR-017 | Observability | Usage analytics | Track generation count, play time, completion rate | P2 | Analytics integration | Product insights |
| NFR-018 | Maintainability | Code documentation | All modules have JSDoc/TSDoc comments | P2 | Documentation coverage tool | Developer experience |
| NFR-019 | Maintainability | Automated testing | 80%+ code coverage | P1 | Jest/Vitest coverage report | Quality assurance |
| NFR-020 | Compatibility | Browser support | Chrome 90+, Firefox 88+, Safari 14+, Edge 90+ | P0 | Cross-browser testing | Modern browser baseline |
| NFR-021 | Compatibility | Mobile browser support | Functional on iOS Safari, Chrome Mobile | P2 | Mobile device testing | Degraded but usable |

Categories: Performance, Reliability, Scalability, Security, Privacy, Accessibility, Observability, Maintainability, Compatibility.

## 8. Data & Analytics (Conditional)

### Inputs

- **User Prompts:** Natural language adventure descriptions (text)
- **Q&A Responses:** Structured answers to generation questions (text, selections)
- **Player Commands:** Text input during gameplay (text strings)
- **Uploaded Files:** ZIL source (.zil) or Z-code (.z3, .z5, .z8) files
- **Configuration:** Agent strategy selection, model choices (enums/selections)

### Outputs / Events

- **Generated ZIL Code:** Source code text files
- **Compiled Z-Code:** Z-machine bytecode (binary)
- **Game Transcripts:** Complete playthrough logs (text)
- **Validation Reports:** JSON/Markdown reports of adventure analysis
- **Save Game States:** Serialized game state (JSON/binary)

### Instrumentation Plan

| Event | Trigger | Payload | Purpose | Owner |
|-------|---------|--------|---------|-------|
| adventure_generation_started | User initiates generation | prompt_length, user_id (if auth), timestamp | Track generation demand | Analytics |
| adventure_generation_completed | ZIL code generated | success, duration, code_size, compile_success | Measure AI quality | Engineering |
| adventure_generation_failed | Generation error | error_type, error_message, prompt_hash | Debug issues | Engineering |
| adventure_play_started | User starts playing | adventure_id, source (generated/imported), timestamp | Track engagement | Analytics |
| player_command_issued | User enters command | command_type (verb), adventure_id | Understand player behavior | Analytics |
| game_saved | User saves game | adventure_id, turn_count, timestamp | Track engagement depth | Analytics |
| game_completed | User wins adventure | adventure_id, turn_count, duration, timestamp | Measure success rate | Analytics |
| agent_playthrough_started | Agent begins solving | adventure_id, strategy, model, timestamp | Track agent usage | Analytics |
| agent_playthrough_completed | Agent finishes | success, turn_count, duration, issues_found | Measure validation efficacy | Engineering |
| validation_report_generated | Report created | adventure_id, issue_count, severity_breakdown | Track adventure quality | Product |
| error_occurred | Any system error | error_type, context, stack_trace | Monitor reliability | Engineering |

### Metrics & Success Criteria

| Metric | Type | Baseline | Target | Window | Source |
|--------|------|----------|--------|--------|--------|
| Adventures Generated | Counter | 0 | 100+ | 3 months | adventure_generation_completed |
| Generation Success Rate | Percentage | N/A | 90%+ | Rolling 30 days | completed / (completed + failed) |
| Average Generation Time | Duration | N/A | <60 seconds | Rolling 7 days | duration from completed events |
| Total Adventure Plays | Counter | 0 | 500+ | 3 months | adventure_play_started |
| Average Play Duration | Duration | N/A | 20+ minutes | Rolling 30 days | game_completed - play_started |
| Completion Rate | Percentage | N/A | 30%+ | Rolling 30 days | game_completed / play_started |
| Agent Success Rate | Percentage | N/A | 70%+ | Rolling 30 days | agent success / total attempts |
| Validation Report Usage | Counter | 0 | 50+ | 3 months | validation_report_generated |
| Platform Error Rate | Percentage | N/A | <1% | Rolling 7 days | error_occurred / total sessions |
| User Retention | Percentage | N/A | 40%+ D7 | Weekly cohorts | Returning users / new users |

## 9. Dependencies

| Dependency | Type | Criticality | Owner | Risk | Mitigation |
|-----------|------|------------|-------|------|-----------|
| Generative AI API (Claude/OpenAI) | External Service | Critical | AI Provider | Service outage, rate limits, cost | Implement fallback prompts; cache common generations; consider self-hosted models |
| Z-Machine Interpreter (Parchment/ZVM) | Open Source Library | Critical | Open Source Community | Bugs, lack of maintenance, compatibility | Fork and maintain if needed; evaluate multiple options |
| ZIL Compiler (ZILF) | Open Source Tool | Critical | ZILF Project | Compilation bugs, limited features | Contribute fixes upstream; consider alternative compilers |
| Browser APIs (localStorage, IndexedDB) | Platform | High | Browser Vendors | API deprecation, storage limits | Implement graceful degradation; warn users of limits |
| Web Assembly (for compilation) | Platform | Medium | Browser Vendors | Performance issues, compatibility | Provide server-side compilation fallback |
| Frontend Framework (React/Vue/Svelte) | Open Source Library | Medium | Framework Team | Breaking changes, security issues | Pin versions; test upgrades; follow security advisories |
| Hosting Platform (Vercel/Netlify/AWS) | Cloud Service | High | Cloud Provider | Downtime, cost overruns | Multi-cloud strategy; cost monitoring |

## 10. Risks & Mitigations

| Risk ID | Description | Severity | Likelihood | Mitigation | Owner | Status |
|---------|-------------|---------|-----------|-----------|-------|--------|
| R-001 | AI generates syntactically invalid ZIL code | High | Medium | Multi-stage validation: prompt engineering → compilation test → agent playthrough; surface errors to user with suggestions | Engineering | Active |
| R-002 | AI generates logically broken adventures (unsolvable, dead ends) | High | Medium | Player agent validation before publication; provide detailed reports; allow iteration | Engineering | Active |
| R-003 | AI API costs spiral out of control | High | Medium | Implement strict rate limiting; cache generations; monitor costs closely; consider usage tiers | Product | Active |
| R-004 | Player agent cannot reliably solve even simple adventures | Medium | Medium | Start with simple test cases; iterate on agent prompts; provide multiple strategy options; accept partial solutions | Engineering | Active |
| R-005 | Z-Machine interpreter has bugs affecting specific games | Medium | Low | Extensive testing with classic adventures; maintain list of known issues; provide workarounds | Engineering | Monitor |
| R-006 | Users create inappropriate/offensive content via AI | Medium | Medium | Implement content policy; use AI safety features; community reporting; human moderation for public library | Product | Active |
| R-007 | Performance degrades with large, complex adventures | Medium | Low | Optimize Z-machine implementation; test with large games (e.g., Zork I); implement loading states | Engineering | Monitor |
| R-008 | Compilation cannot run in browser (ZILF requires complex environment) | High | Medium | Provide backend compilation service as fallback; explore WASM port; accept longer compile times | Engineering | Active |
| R-009 | Legal issues with ZIL language/Z-machine format ownership | Low | Low | Research IP status; ZIL is ancient and likely unencumbered; Z-machine is open standard | Legal | Monitor |
| R-010 | Low user adoption due to niche appeal | Medium | Medium | Marketing to retro gaming communities; educational outreach; simplify UX to broaden appeal | Product | Active |

## 11. Privacy, Security & Compliance

### Data Classification

**Public Data:**

- Published adventures (ZIL code, Z-code, metadata)
- Public-facing validation reports (if user opts in)

**User Private Data:**

- Unpublished adventure drafts
- Player command history
- Game save states
- Preferences and settings

**Sensitive Data:**

- AI API keys (server-side only, never exposed to client)
- User credentials (if authentication implemented)
- Usage analytics (anonymized before storage)

**No PII Collection Initially:** Platform designed to work anonymously; optional account system for advanced features.

### PII Handling

- **Minimize Collection:** No email, name, or contact info required for core functionality
- **Optional Authentication:** Users can create accounts for cloud saves, public attribution
- **Data Minimization:** Collect only what's necessary for feature delivery
- **User Control:** Users can export or delete their data
- **Anonymization:** Analytics do not include personally identifiable information
- **No Third-Party Sharing:** User data never shared except for essential services (AI API, hosting)

### Threat Considerations

**Threat Model:**

- **XSS via Game Text:** Malicious ZIL code could inject scripts into game output
  - **Mitigation:** Strict output sanitization; Content Security Policy; text-only rendering
- **Prompt Injection:** Users attempt to manipulate AI to generate harmful code or bypass limits
  - **Mitigation:** System prompt hardening; input validation; output validation; rate limiting
- **API Abuse:** Attackers spam generation endpoint to incur costs
  - **Mitigation:** Rate limiting; CAPTCHA for anonymous users; cost monitoring; IP-based throttling
- **Malicious Uploaded Adventures:** Users upload crafted Z-code to exploit interpreter
  - **Mitigation:** File validation; sandbox interpreter execution; disable dangerous opcodes if needed
- **DoS via Complex Adventures:** Intentionally complex games cause performance issues
  - **Mitigation:** Execution time limits; complexity analysis; resource quotas

**Security Practices:**

- Input validation on all user-provided data
- Output encoding for all displayed text
- HTTPS only for all connections
- Secure headers (CSP, X-Frame-Options, etc.)
- Regular dependency updates and vulnerability scanning
- Principle of least privilege for API access

### Regulatory / Compliance (Conditional)

| Regulation | Applicability | Action | Owner | Status |
|-----------|--------------|--------|-------|--------|
| GDPR | If EU users present | Implement data export/deletion; privacy policy; cookie consent | Legal | Planned |
| COPPA | If users under 13 | Age gate or ensure no child-directed features | Legal | Monitor |
| CCPA | If California users | Data disclosure; opt-out mechanisms | Legal | Planned |
| Accessibility Laws (ADA, Section 508) | If government/public sector use | WCAG 2.1 AA compliance | Engineering | Active |

## 12. Operational Considerations

| Aspect | Requirement | Notes |
|--------|------------|-------|
| Deployment | Static site hosting (Vercel/Netlify) for frontend; serverless functions for AI API calls; CDN for assets | Minimize operational overhead; leverage platform features |
| Rollback | Automated rollback on deployment failure; canary releases for major changes | Ensure safe deployments |
| Monitoring | Application performance monitoring (APM); error tracking (Sentry); uptime monitoring (Pingdom/UptimeRobot) | Real-time visibility into health |
| Alerting | Alerts on: API error rate >5%, generation success rate <80%, p95 latency >2s, cost spike >20% above baseline | Proactive issue detection |
| Support | Community Discord/forum for user questions; GitHub issues for bugs; documentation site | Community-first support model |
| Capacity Planning | Monitor AI API usage trends; scale serverless functions automatically; plan for storage growth | Avoid surprise costs or outages |

## 13. Rollout & Launch Plan

### Phases / Milestones

| Phase | Date | Gate Criteria | Owner |
|-------|------|--------------|-------|
| Phase 0: Technical Validation | Week 1-2 | Proof-of-concept: AI generates valid ZIL; Z-machine runs in browser; agent solves simple adventure | Engineering |
| Phase 1: Core Features Development | Week 3-8 | FR-001 through FR-009 implemented; basic UI functional; can generate, play, and validate adventures | Engineering |
| Phase 2: Alpha Testing | Week 9-10 | Internal team creates and tests 10+ adventures; agent validation on diverse adventure types; critical bugs fixed | QA |
| Phase 3: Beta Launch | Week 11-14 | Invite 50 beta users; collect feedback; iterate on UX; expand adventure library to 25+ | Product |
| Phase 4: Public Launch | Week 15 | Public announcement; documentation complete; onboarding flow polished; marketing push | Product |
| Phase 5: Iteration & Growth | Week 16+ | Monitor metrics; add P2 features; build community; expand template library | Product |

### Feature Flags (Conditional)

| Flag | Purpose | Default | Sunset Criteria |
|------|---------|--------|----------------|
| enable_agent_validation | Roll out AI agent gradually | Off | 90%+ success rate on test suite; 2 weeks stable |
| enable_code_preview | Show generated ZIL to users | On | N/A (permanent feature) |
| enable_adventure_library | Public library vs. local-only | Off | Storage infrastructure proven; moderation in place |
| enable_advanced_strategies | Multiple agent strategies vs. single default | Off | All strategies tested; documentation complete |
| enable_import_upload | Allow file uploads | Off | Security audit passed; file validation robust |

### Communication Plan (Optional)

**Internal:**

- Weekly sprint reviews with demo of new features
- Slack channel for real-time updates and bug reports
- Monthly stakeholder updates on progress toward goals

**External:**

- Beta announcement on retro gaming forums (r/InteractiveFiction, intfiction.org)
- Launch blog post explaining vision and capabilities
- Tutorial video series on creating first adventure
- Social media campaign targeting IF enthusiasts and AI researchers
- Press outreach to gaming and AI news outlets

## 14. Open Questions

| Q ID | Question | Owner | Deadline | Status |
|------|----------|-------|---------|--------|
| Q-001 | Which generative AI model performs best for ZIL generation (Claude vs GPT-4 vs others)? | Engineering | Week 2 | Open - Claude Sonnet assumed for planning but needs validation |
| Q-002 | Can ZILF compiler run in WASM, or must we use backend service? | Engineering | Week 2 | Open |
| Q-003 | What's the optimal prompt structure for adventure generation (single mega-prompt vs multi-turn)? | Engineering | Week 4 | Open |
| Q-004 | Should we support multiplayer/collaborative adventure creation? | Product | Week 8 | Open |
| Q-005 | What's the best monetization model if we move beyond free tier (subscriptions, pay-per-generation, ads)? | Product | Post-Launch | Open |
| Q-006 | How do we handle adventures that reference copyrighted IP (e.g., "Star Wars adventure")? | Legal | Week 10 | Open |
| Q-007 | Should player agent provide hints to human players, or validation only? | Product | Week 6 | Open |

## 15. Changelog

| Version | Date | Author | Summary | Type |
|---------|------|--------|---------|------|
| 1.0 | 2026-01-06 | AI Assistant | Initial PRD creation with full scope definition | Creation |
| 1.1 | 2026-01-06 | AI Assistant | Clarified backend/frontend architecture; added ADR-001 for format prioritization | Revision |

## 16. References & Provenance

| Ref ID | Type | Source | Summary | Conflict Resolution |
|--------|------|--------|---------|--------------------|
| REF-001 | Historical | Infocom ZIL Documentation | Original ZIL language specification and best practices | Primary source for ZIL syntax |
| REF-002 | Technical | ZILF Project (https://foss.heptapod.net/zilf/zilf) | Modern open-source ZIL compiler | Preferred compiler implementation |
| REF-003 | Technical | Parchment (https://github.com/curiousdannii/parchment) | JavaScript Z-machine interpreter | Primary interpreter option |
| REF-004 | Community | Interactive Fiction Community Forum (intfiction.org) | Community needs and pain points | User research source |
| REF-005 | Technical | Z-Machine Standards Document 1.1 | Z-machine specification | Format compliance reference |
| REF-006 | Decision | ADR-001: Text Adventure Format Prioritization | Analysis and prioritization of text adventure format support | Format roadmap and integration strategy |

### Citation Usage

- ZIL syntax and semantics derived from [REF-001]
- Compilation approach based on [REF-002] capabilities
- Z-machine runtime requirements from [REF-003, REF-005]
- User personas informed by [REF-004] community discussions
- Text adventure format prioritization from [REF-006]

## 17. Appendices (Optional)

### Glossary

| Term | Definition |
|------|-----------|
| ZIL | Zork Implementation Language - the programming language used to create Infocom text adventures in the 1980s |
| Z-Machine | Virtual machine designed to run ZIL-compiled adventures; platform-independent bytecode format |
| Z-Code | Compiled bytecode for the Z-machine (file extensions: .z3, .z5, .z8 indicate version) |
| Interactive Fiction (IF) | Text-based narrative games where players type commands to interact with story |
| ZILF | ZIL Implementation Language Forth - modern open-source ZIL compiler |
| Parser | Text interpreter that converts player commands (e.g., "take lamp") into game actions |
| Room | Location in adventure game world; connected to other rooms via exits |
| Object | Item, character, or entity in the game world that players can interact with |
| Verb | Action word in player commands (TAKE, DROP, EXAMINE, etc.) |
| Dead End | Game state from which player cannot progress or win; design flaw |
| Player Agent | AI system that autonomously plays adventures to test solvability |

### Additional Notes

**Technology Stack (Recommended):**

- **Frontend:** React or Svelte for UI components
- **Z-Machine Interpreter:** Parchment (JavaScript) or ZVM
- **ZIL Compiler:** ZILF (via WASM or backend service)
- **AI Provider:** Anthropic Claude API (primary), OpenAI GPT-4 (fallback)
- **Storage:** LocalStorage/IndexedDB (client), PostgreSQL (future backend)
- **Hosting:** Vercel or Netlify for static site; serverless functions for AI calls
- **Testing:** Jest/Vitest for unit tests; Playwright for E2E tests

**Prompt Engineering Strategy:**

The quality of generated adventures depends heavily on prompt design. Recommended approach:

1. **System Prompt:** Establish AI as expert ZIL developer; provide syntax reference; emphasize playability
2. **Context Gathering:** Multi-turn conversation to extract: setting, protagonist, goal, major locations, key items, puzzles
3. **Structured Output:** Request ZIL in specific format with clear room definitions, object properties, verb handlers
4. **Validation Loop:** Check compilation; if fails, provide error messages back to AI for correction
5. **Iteration:** Allow user to refine through additional prompts ("make the puzzle harder", "add a secret room")

**Agent Design Philosophy:**

The AI player agent serves two purposes:

1. **Validation:** Ensure adventures are completable and logically consistent
2. **Research:** Demonstrate AI reasoning capabilities in interactive fiction domain

Agent should balance exploration (discovering all game states) with exploitation (solving puzzles efficiently). Key techniques:

- **State Mapping:** Track visited rooms and object states
- **Goal Reasoning:** Maintain hypothesis about win condition and subgoals
- **Common Sense:** Apply world knowledge to puzzle solving ("keys open locks")
- **Backtracking:** Return to earlier states if stuck
- **Experimentation:** Try unusual command combinations
- **Reporting:** Document dead ends, missing hints, logical inconsistencies

Generated 2026-01-06T01:29:58Z by Copilot Workspace (mode: comprehensive)

<!-- markdown-table-prettify-ignore-end -->
