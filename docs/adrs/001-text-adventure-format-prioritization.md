# ADR-001: Text Adventure Format Prioritization

**Status:** Proposed  
**Date:** 2026-01-06  
**Decision Maker:** Product & Engineering Team  
**Related PRD:** [ZIL Runner PRD](../prds/zilrunner.md)

## Context

The ZIL Runner platform aims to democratize text adventure creation through AI-powered generation and web-based play. While the initial PRD focused exclusively on ZIL (Zork Implementation Language), there are multiple text adventure formats in use today, each with different characteristics, tooling ecosystems, and community support. This ADR evaluates the available formats and provides a prioritized roadmap for support based on ease of integration, feature richness, and strategic value.

## Decision

We will prioritize text adventure format support in the following order:

1. **Phase 1 (MVP):** ZIL (Zork Implementation Language) via Z-machine
2. **Phase 2 (Post-MVP):** Inform 7 via Glulx
3. **Phase 3 (Future):** TADS 3
4. **Phase 4 (Exploratory):** Ink, ChoiceScript

Other formats (Quest, Twine, Texture) are deprioritized due to limited technical advantages or community overlap.

## Rationale

### Format Evaluation Criteria

We evaluated each format against the following criteria:

- **Ease of Integration:** How straightforward is it to integrate compilation and runtime into our web platform?
- **AI Generation Suitability:** How well can modern LLMs generate syntactically correct and semantically meaningful code?
- **Runtime Robustness:** Are there mature, well-tested interpreters available for web deployment?
- **Feature Richness:** Does the format support complex narratives, state management, and interactive mechanics?
- **Community & Content:** Is there an active community and existing content library?
- **Strategic Fit:** Does the format align with our goals of AI-powered creation and automated validation?

### Format Analysis

#### 1. ZIL (Z-machine) - Phase 1 (MVP)

**Pros:**
- **Historical Significance:** Original Infocom language; strong nostalgic appeal
- **Mature Runtime:** Z-machine interpreters are battle-tested (Parchment, ZVM); excellent web support via JavaScript
- **Deterministic Format:** Z-machine bytecode format is well-documented and stable
- **AI Generation:** ZIL syntax is relatively simple and structured; LLMs can generate valid code with proper prompting
- **Compact:** Z-machine games are small, efficient to store and distribute
- **Validation:** Deterministic execution makes agent-based testing reliable

**Cons:**
- **Limited Modern Tooling:** ZILF is the main modern compiler but has smaller ecosystem than Inform
- **Verbose Syntax:** More boilerplate than modern alternatives like Inform 7
- **Smaller Active Community:** Fewer active creators compared to Inform

**Integration Effort:** **Low-Medium**
- ZILF compiler can potentially run via WASM or backend service
- Multiple proven JavaScript Z-machine interpreters available
- Z-code format is stable and well-understood

**Verdict:** **Priority 1 - MVP Foundation**  
ZIL provides the best balance of technical feasibility, AI generation capability, and nostalgic appeal. The Z-machine's maturity and web-ready interpreters make it ideal for rapid MVP development.

---

#### 2. Inform 7 (Glulx) - Phase 2 (Post-MVP)

**Pros:**
- **Natural Language Syntax:** Inform 7 uses English-like rules ("The red key is in the wooden box"), making it extremely AI-friendly
- **Rich Feature Set:** Advanced world modeling, sophisticated parser, extensive standard library
- **Active Community:** Largest modern IF community; extensive documentation and examples
- **Powerful Runtime:** Glulx VM supports advanced features (sound, graphics, unlimited memory)
- **Excellent Tooling:** Mature IDE (Inform 7 app), debugger, extensive standard library

**Cons:**
- **Complex Compiler:** Inform 7 compiler (ni) is sophisticated and may be challenging to port to WASM
- **Larger Bytecode:** Glulx files are bigger than Z-code
- **More Complex to Validate:** Richer feature set means more edge cases for AI agents to handle

**Integration Effort:** **Medium-High**
- Compiler integration more complex than ZILF
- Glulx interpreter available in JavaScript (Quixe, Parchment supports Glulx)
- May require backend compilation service initially

**Verdict:** **Priority 2 - Strategic Growth**  
Inform 7's natural language syntax is exceptionally well-suited for AI generation. Supporting Inform would dramatically expand our addressable community and enable more sophisticated adventures. Plan for Phase 2 integration after ZIL MVP is proven.

---

#### 3. TADS 3 - Phase 3 (Future)

**Pros:**
- **Object-Oriented Design:** Clean OOP model; well-structured
- **Advanced Features:** Rich multimedia support, sophisticated conversation systems
- **Good Documentation:** Comprehensive manuals and guides
- **Dedicated Community:** Smaller but dedicated user base

**Cons:**
- **C-like Syntax:** More traditional programming language; higher barrier for AI generation
- **Complex Compiler:** TADS 3 compiler is C++ and not trivial to port
- **Limited Web Runtime:** TADS JavaScript interpreter exists but is less mature than Z-machine/Glulx options
- **Smaller Ecosystem:** Fewer modern creators compared to Inform

**Integration Effort:** **High**
- Compiler integration challenging (C++ codebase)
- Runtime (FrobTads JS) less mature than Z-machine/Glulx alternatives
- Likely requires backend compilation service

**Verdict:** **Priority 3 - Niche Support**  
TADS 3 offers powerful features but integration complexity is significant. Given the smaller community and more challenging AI generation (C-like syntax), prioritize only after Inform 7 is established.

---

#### 4. Ink - Phase 4 (Exploratory)

**Pros:**
- **Simple, Clean Syntax:** Minimal markup; easy to read and write
- **Excellent Web Support:** Ink-JS runtime is mature, actively maintained
- **Game Industry Adoption:** Used in commercial games (80 Days, Heaven's Vault)
- **Fast Compilation:** Lightweight compiler, easy to integrate
- **Choice-Focused:** Excellent for branching narratives

**Cons:**
- **Limited Parser Support:** Primarily choice-based, not traditional parser adventures
- **Different Paradigm:** Not a "classic" text adventure format; less puzzle-focused
- **Smaller IF Community:** More game dev than traditional IF community

**Integration Effort:** **Low**
- Excellent web tooling already available
- Compiler and runtime both lightweight and modern

**Verdict:** **Priority 4 - Alternative Paradigm**  
Ink is technically easy to integrate but represents a different style of interactive fiction (choice-based vs. parser-based). Consider as a complementary format for users who want branching narratives rather than puzzle adventures.

---

#### 5. ChoiceScript - Phase 4 (Exploratory)

**Pros:**
- **Commercial Success:** Powers Choice of Games titles
- **Simple Syntax:** Easy to learn and write
- **Stats & Variables:** Built-in character stats and relationship tracking

**Cons:**
- **Proprietary Ecosystem:** Primarily tied to Choice of Games platform
- **Limited Open Tooling:** Less open-source infrastructure
- **Choice-Based Only:** Not traditional parser adventures

**Integration Effort:** **Medium**
- Open-source interpreter available but ecosystem is proprietary-focused

**Verdict:** **Priority 4 - Commercial Alternative**  
Similar to Ink but with more proprietary constraints. Consider only if there's strong demand for choice-based formats and Ink proves insufficient.

---

### Deprioritized Formats

**Quest:** XML-based, Windows-focused; limited cross-platform runtime  
**Twine:** Excellent for hypertext but not traditional parser IF; different paradigm  
**Texture:** Modern but niche; minimal ecosystem  

These formats either don't align with classic text adventure goals or lack the technical maturity for robust integration.

## Implementation Roadmap

### Phase 1: ZIL (MVP) - Weeks 1-15
- Integrate ZILF compiler (WASM or backend)
- Implement Z-machine interpreter (Parchment)
- Build AI generation prompts for ZIL syntax
- Develop agent validation for Z-code
- **Success Metric:** 90%+ compile success rate, 100+ generated adventures

### Phase 2: Inform 7 - Months 4-6 (Post-MVP)
- Research Inform 7 compiler integration options
- Implement Glulx runtime (Quixe or Parchment)
- Develop Inform 7 AI generation prompts (leverage natural language syntax)
- Extend agent to handle Glulx format
- **Success Metric:** 85%+ compile success rate, 50+ Inform adventures generated

### Phase 3: TADS 3 - Months 7-9 (Future)
- Backend compilation service for TADS 3
- Integrate FrobTads JS runtime
- Develop TADS 3 generation prompts
- **Success Metric:** 75%+ compile success rate, 25+ TADS adventures

### Phase 4: Ink/ChoiceScript - Months 10+ (Exploratory)
- Evaluate community demand for choice-based formats
- Integrate Ink-JS if prioritized
- **Success Metric:** User demand validation, proof-of-concept

## Consequences

**Positive:**
- Clear prioritization reduces scope creep and enables focused execution
- ZIL-first approach allows rapid MVP with proven technology
- Inform 7 provides strategic growth path to largest modern IF community
- Modular format support enables incremental expansion

**Negative:**
- Inform 7 users may need to wait for Phase 2 support
- TADS 3 community may feel underserved
- Multi-format support increases maintenance complexity over time

**Neutral:**
- Platform architecture must be format-agnostic from the start
- AI generation prompts require per-format engineering effort
- Agent validation complexity scales with format diversity

## Alternatives Considered

**Alternative 1: Inform 7 First**  
Rejected because Inform 7's compiler integration is more complex; higher MVP risk.

**Alternative 2: Multi-Format MVP**  
Rejected due to scope creep; better to prove concept with one format then expand.

**Alternative 3: Choice-Based Only (Ink/Twine)**  
Rejected because it abandons classic parser-based IF market, which is our core differentiation.

## Related Decisions

- [PRD Section 4: Scope](../prds/zilrunner.md#4-scope) - Out of scope items include non-ZIL formats for MVP
- Future ADR needed for Z-machine version support (Z3 vs Z5 vs Z8)
- Future ADR needed for compiler deployment strategy (WASM vs backend service)

## References

- [Z-Machine Standards Document 1.1](https://www.inform-fiction.org/zmachine/standards/z1point1/)
- [ZILF Project Documentation](https://foss.heptapod.net/zilf/zilf)
- [Inform 7 Official Site](http://inform7.com/)
- [Glulx Specification](https://www.eblong.com/zarf/glulx/)
- [TADS 3 System Manual](https://www.tads.org/t3doc/doc/sysman/cover.htm)
- [Ink Documentation](https://github.com/inkle/ink)
- [Interactive Fiction Technology Foundation](https://iftechfoundation.org/)

---

**Review Status:** Awaiting stakeholder approval  
**Next Review Date:** Week 2 of development cycle
