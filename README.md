[🇰🇷 한국어](README.ko.md) | [🇯🇵 日本語](README.ja.md)

# NVatar Emotion Engine

**9-dimensional emotion tracking + MBTI continuous spectrum + personality evolution with decay/commit + 3-tier memory with compaction + user personality inference — a unified cognitive engine for stateful AI characters.**

> From the [NVatar](https://github.com/nskit-io/nvatar) project — an AI avatar chat system running entirely on local hardware.

---

## Why This Exists

LLM characters today are stateless. They forget, they don't grow, and they don't feel. Memory libraries exist (Letta, Mem0), but none integrate **emotion + personality evolution + memory** as a single coherent system.

This engine gives AI characters:
- **Emotions** that shift naturally during conversation and decay back to baseline (9 dimensions including curiosity)
- **MBTI Spectrum** that evolves on a continuous 0-100 scale with probabilistic expression — not fixed types
- **Personality** that evolves over weeks of interaction — with a novel decay/commit mechanism
- **Memory** that compacts from raw messages to summaries to keywords, like human memory fading
- **User Understanding** — the avatar infers YOUR personality from conversation patterns and adjusts its style

## Architecture

```
User Message
    │
    ├─→ Emotion Service ───→ 9D emotion vector update
    │                         (keyword triggers, natural decay)
    │
    ├─→ Memory Service  ───→ L1 raw → L2 events → L3 keywords
    │                         (automatic compaction + L2→L3 auto-transition)
    │
    ├─→ Persona Evolution ─→ Trait drift with decay/commit
    │                         (pending deltas → time decay → commit)
    │
    ├─→ MBTI Analyzer ─────→ Continuous spectrum evolution
    │                         (blind analysis → delta → weighted apply)
    │
    └─→ User Inference ────→ Infer user's personality from their messages
                              (accumulated across compaction cycles)
```

## Emotion System — 9 Dimensions

Not sentiment analysis. Not classification. A **continuous emotional state** that persists across conversations.

| Dimension | Tendency |
|-----------|----------|
| Joy | Rises with positive interaction |
| Sadness | Triggered by loss/disappointment topics |
| Anger | Spikes on conflict, decays fastest |
| Anxiety | Builds with uncertainty |
| Affection | Grows slowly with trust |
| Excitement | Spikes on novelty |
| Boredom | Rises with repetition |
| Trust | The slowest dimension to change |
| **Curiosity** | **Rises with novel topics — triggers follow-up questions and deeper exploration** |

**Mechanics:**
- Keywords trigger proportional emotional shifts
- Natural decay toward baseline over multiple turns
- Only significantly elevated emotions are surfaced in the prompt
- Each message stores an `emotion_snapshot` for retrospective analysis

## Persona Evolution — Decay/Commit

**No prior art exists for this mechanism in open source.**

8 personality traits evolve through a 3-stage pipeline:

| Trait | Range |
|-------|-------|
| Kindness | 3–97 |
| Humor | 3–97 |
| Seriousness | 3–97 |
| Warmth | 3–97 |
| Energy | 3–97 |
| Honesty | 3–97 |
| Emotional | 3–97 |
| Independence | 3–97 |

### The 3-Stage Pipeline

**Stage 1 — Pending Delta Accumulation**
Each message generates trait deltas — configurable caps prevent extreme swings.

**Stage 2 — Time Decay**
Exponential decay based on time since last interaction — recent conversations have strong influence, older ones fade naturally.

**Stage 3 — Compaction Commit**
Pending deltas are scaled by the decay factor and committed to the trait values, with configurable caps per commit.

**Resistance Curve:** Extreme values resist further change — like real people.

All commits are logged for analysis.

## MBTI Continuous Spectrum

Unlike fixed personality types (INTJ, ENFP...), NVatar uses a **continuous 0-100 spectrum** across 4 MBTI dimensions. Your avatar is never locked into a type — it's a living personality that shifts through conversation.

| Dimension | 0 | 50 (boundary) | 100 |
|-----------|---|---------------|-----|
| E/I | Deep introvert | Ambivert | Deep extrovert |
| S/N | Strong sensing | Balanced | Strong intuition |
| T/F | Strong thinking | Balanced | Strong feeling |
| J/P | Strong judging | Balanced | Strong perceiving |

### Probabilistic Expression

Even at T=70, the avatar shows empathetic responses ~30% of the time. This prevents personality rigidity and produces natural, human-like variation.

### 3-Stage MBTI Analysis Pipeline

MBTI evolution happens during memory compaction:

1. **Blind Analysis** — LLM analyzes the avatar's recent responses without knowing current scores
2. **Delta Calculation** — Difference between blind result and current spectrum
3. **Weighted Application** — Deltas applied with configurable weights at L1→L2 and L2→L3 transitions, with resistance at extremes

## User Personality Inference

The avatar doesn't just evolve its own personality — it **learns yours**.

During each compaction cycle, user messages are separately analyzed to infer their MBTI tendencies. This builds up over multiple cycles:

| Confidence | Avatar Behavior |
|-----------|----------------|
| Early (1-2 analyses) | Unknown — asks varied questions to learn about you |
| Emerging (2-3 analyses) | Subtle adaptation — adjusts communication style without mentioning it |
| Established (3+ analyses) | Explicit personalization — "I know you prefer direct answers, so..." |

The inferred user personality is injected into the avatar's system prompt, enabling genuinely personalized conversations that improve over weeks of interaction.

## Memory System — 3 Tiers

```
L1 (Raw)     → Recent messages in prompt
    ↓ compaction
L2 (Events)  → Summaries with relevance scores
    ↓ low relevance
L3 (Keywords) → Long-term fragments, spoken with uncertainty
```

### L1: Short-term
- Raw message storage, strict user/assistant alternation
- Configurable message limits with automatic compaction triggers

### L2: Medium-term
- Event summaries with status (success/failed/pending/ongoing)
- Relevance scores decay over time; re-mentions boost relevance
- Top most relevant injected into system prompt

### L3: Long-term
- Low-relevance events migrate to L3 as pure keywords
- Character speaks with genuine uncertainty: "I can't quite remember, but..."
- Recent keywords as "(old memory)..." in prompt

### Compaction Flow
1. Check uncompacted count → trigger based on configurable threshold
2. Keep recent raw, summarize remaining
3. LLM extracts events as structured JSON
4. Fallback chain: direct parse → regex → outer bounds
5. Persona trait commit runs simultaneously

## Database Schema

11 tables designed for the full lifecycle — including MBTI spectrum and evolution logs (schema available on request).

## Related Projects

Other open-source components from the NVatar AI avatar system:

- [**Try Live Demo**](https://nskit-io.github.io/nvatar-demo/) — Experience NVatar in your browser

- [**avatar-chat**](https://github.com/nskit-io/avatar-chat) — Prompt engineering patterns for Gemma character AI
- [**customize-local-llm**](https://github.com/nskit-io/customize-local-llm) — Local Gemma for personality, cloud Claude for facts
- [**vrm-studio**](https://github.com/nskit-io/vrm-studio) — 3D VRM avatar chat room with Three.js + WebSocket
- [**CSW**](https://github.com/nskit-io/csw) — Claude Subscription Worker powering the cloud layer
- [**portable-ai-companion**](https://github.com/nskit-io/portable-ai-companion) — Cross-app franchise architecture for avatar portability

## License

CC BY-NC-SA 4.0 — see [LICENSE](LICENSE)

---

## Support This Research

NVatar is an independent R&D project exploring the frontier of AI character systems. Built by a solo founder at Neoulsoft Inc. — independent R&D, no external funding yet. Built by a solo founder at Neoulsoft Inc. — independent R&D, no external funding yet.

If you find this work valuable:

**Donation & Investment Inquiry**
- Email: [nskit@nskit.io](mailto:nskit@nskit.io)
- Organization: [NSKit](https://nskit.io) by Neoulsoft Inc.

<p align="center">
  <a href="https://github.com/nskit-io">github.com/nskit-io</a>
</p>
