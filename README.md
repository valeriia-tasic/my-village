# Village — Peer Support Network for Motherhood

A platform that matches mothers into small group chats around **real, current shared experience** — not static profile traits. The core mechanic replaces the cold open entirely: a mother's own submitted prompt becomes her village's first message, so no one has to speak first.

Full product detail: [`product-requirements/my-village-PRD.md`](product-requirements/my-village-PRD.md)
Architecture detail: [`product-requirements/my-village-architecture-design.md`](product-requirements/my-village-architecture-design.md)

---

## The Problem

Existing mom-friend apps (Peanut, Bumble BFF) match on static profile traits — photos, interests, bio — which produces shallow connections that die after the opener. Ghosting is the top complaint in both apps' review bases, and it's structural: if the match has nothing real to bond over, there's nothing to say after "hi."

The swipe format compounds this. It asks exhausted, sleep-deprived mothers to cold-compose an opener to a stranger and wait. That's real friction on people with the least bandwidth to pay it, layered on top of a format that feels uncomfortably close to dating.

**The market already agrees this is broken.** Bumble publicly abandoned pure swipe-matching for BFF in September 2025, pivoting to group/community structures after acquiring Geneva. The mechanic failed at scale; Village is built on the alternative from the start.

Supporting data:
- 7 in 10 mothers say motherhood is lonelier than expected; 1 in 5 feels that loneliness every single day (Motherly, 2025 State of Motherhood)
- Peanut review base independently flags low density outside major cities and unresolved moderation/trust issues
- Ghosting is the single most cited complaint across both Peanut and Bumble BFF stores

**Root cause:** matches lack shared context. Static traits are too thin to carry a conversation. A specific, current, lived experience is a much stronger connective thread — and a shared struggle removes the "who speaks first" problem entirely.

---

## How It Works

### Twice-Weekly Village Matching

A user submits a short prompt — a current struggle or a win — before each matching window closes (Tuesday and Saturday at 11pm ET). A backend job clusters users with similar prompts into small group chats (target: 5–10 members) using embedding similarity over free text, not keyword rules.

Her own submitted prompt seeds the village as the first message. No one has to speak first; someone already did.

**Villages are not permanent.** Each match cycle is independent. Quiet or non-responsive members are rotated into new villages; active members stay together. The same user can be in multiple active villages across cycles if her prompts span topics (e.g., sleep regression one cycle, feeding refusal the next).

**No geography gate by default.** Matching is on topic and life-stage band, not zip code — which solves the local-density problem that limits Peanut outside major cities.

### The Mentor Loop

This is the most differentiated mechanic and the MVP focus.

When a user's struggle resolves, she can opt in to be re-matched months later into a new village of mothers currently facing that same issue — bringing lived, resolved experience into the conversation instead of two equally-lost strangers guessing together.

No competitor is doing this. It also creates a durable retention loop: once her immediate need is met, she still has a reason to stay engaged — as a helper, not just a help-seeker. Mentor role is explicitly peer-framed ("been through this"), not an authority or clinical role.

### Soft Preview (auto-placement, not accept/deny)

Between a match notification and the live chat, users see a minimal preview: topic and member count only. No profiles to evaluate, no accept/deny. It's an orientation beat, not a decision gate. This deliberately avoids reintroducing the evaluate-and-approve pattern that Peanut and Bumble BFF are critiqued for.

### Pinboard *(post-MVP)*

A low-effort feed of struggles and wins. Other users can "nudge" (a same/me-too reaction) without composing a reply. Designed for 2am, one-handed use — normalization ("is this normal?") rather than problem-solving.

---

## MVP Scope

**In scope:**
- Twice-weekly prompt submission with embedding-based topic clustering
- Village formation, reshuffle, and top-up logic
- Mentor opt-in flow: mark resolved → opt in → re-matched as mentor in a future cycle
- Safety triage: crisis-adjacent prompts flagged and surfaced to professional resources before (not instead of) village placement
- Soft Preview screen before entering a matched village
- Three Home states: Waiting / Matched / Mentor Invite

**Explicitly out of scope for MVP:**
- Pinboard / nudge
- Location-based matching
- Real-time chat at scale (polling for MVP; delivery layer is behind an interface for upgrade)
- Push notifications at scale
- Multi-child profiles

**Scope rationale:** going deep on one pillar (the mentor loop) produces a stronger portfolio demonstration than shallow coverage of three. The mechanic is novel, the backend logic is non-trivial, and it's the clearest signal of original product thinking.

---

## Key Design Decisions

| Decision | Rationale |
|---|---|
| Auto-placement, no accept/deny | Removes the profile-evaluation gate that makes Peanut feel like dating; Soft Preview gives orientation without a decision |
| Classification fully server-side | Removes friction from onboarding; trade-off is loss of user-correction as a classifier feedback loop — mitigated by an offline eval set |
| Embedding similarity over keyword matching | "she won't eat" and "refusing solids" should cluster together; keyword rules would miss this |
| No geography requirement by default | Solves density problem; geography is a user option, not a prerequisite |
| Seed message posted at match time | Consistent with auto-placement; the village has content before she opens it |
| Release separate from match compute | Matching can run right after lock; villages stay hidden until a civilized release hour (8am ET), so "everyone gets matches at the same time" is true by construction |
| Chat delivery: polling for MVP | Delivery layer sits behind an interface; real-time provider (AppSync Events, Pusher) can be swapped in without touching storage |
| SNS mobile push, not Pinpoint | AWS is retiring Pinpoint (end of support announced late 2026) |

---

## Architecture

**Three services, one async batch job, one delivery layer.**

```
Mobile app (React Native / Expo)
    |
    | HTTPS + JWT (Cognito)
    v
API Gateway
    |
    +---> Orchestrator Lambda (BFF)
    |         |-- GET /v1/home
    |         |-- GET /v1/villages
    |         |-- GET /v1/villages/{id}/preview
    |         |-- POST /v1/villages/{id}/enter
    |         `-- GET/POST /v1/villages/{id}/messages
    |
    `---> Accounts Lambda
              |-- PATCH /v1/me/prompt   (409 after lock)
              |-- GET/PATCH /v1/me/profile
              |-- POST /v1/villages/{id}/resolve
              `-- POST /v1/mentor-invites/{id}/respond

Batch matching (async, twice weekly)
    EventBridge Scheduler
        --> Lock Lambda         (snapshot + SQS enqueue)
        --> SQS + DLQ
        --> Matching Fargate    (classify → embed → cluster → mentor step → write)
        --> Release Lambda      (reveal villages + SNS push)
```

**Data stores (three DynamoDB tables, one per bounded context):**
- **Accounts** — profiles, prompts per cycle, mentor opt-ins
- **Matches** — villages, memberships, run ledger (idempotency lock)
- **Chats** — one item per message, ULID sort keys (time-ordered, globally unique)

The village id is derived deterministically from the cycle and sorted member ids, so a retried matching run cannot create duplicates. The run ledger uses a conditional write (`NOT_STARTED → RUNNING`) so a double-fired EventBridge trigger does nothing.

**Matching pipeline (Fargate task):**
1. Safety re-check on each snapshotted prompt
2. Classify free text → taxonomy category + subcategory (LLM with structured JSON output)
3. Embed each prompt (text embedding model)
4. Pool by category × life-stage band (±1 band tolerance when a pool is thin)
5. Cluster by embedding similarity under a 5–10 size constraint; split large pools into parallel villages
6. Mentor selection step (up to 2 opted-in mentors per village, matching category and older age band)
7. Transactional write: villages, memberships, seed messages

**Safety triage — two gates:**
- Synchronous at prompt submit (fast, may fail open — never blocks the user if the classifier errors)
- Re-run at snapshot time (authoritative pass before placement)

The wording of the safety interstitial and what it promises is a legal question, not an engineering one — flagged as an open item requiring counsel before launch.

---

## Repo Layout

```
village/
├── apps/
│   └── mobile/              # React Native + Expo (TypeScript) — scaffolded
├── services/
│   ├── orchestrator/        # BFF Lambda (Python) — routes built
│   ├── accounts/            # Accounts Lambda (Python) — prompt, profile, mentor flows
│   └── matching/            # Fargate batch job (Python) — full pipeline implemented
│       └── src/pipeline/    # snapshot, classify, embed, cluster, mentor_step, writer
├── packages/
│   └── api-spec/            # OpenAPI contract (source of truth for client types)
├── infra/                   # IaC skeleton
├── tools/
│   └── simulator/           # Synthetic cohort generator for tuning matching offline
├── tests/
│   ├── behave/              # BDD feature specs (prompt submission, matching, mentor loop, safety)
│   └── integration/         # DynamoDB-local integration tests
└── product-requirements/    # PRD, architecture design, wireframes
```

---

## Testing Strategy

| Layer | Approach |
|---|---|
| Unit | Per-service unit tests for classification, clustering, triage, prompt logic |
| BDD / acceptance | `behave` feature specs covering the five core flows: prompt submission, batch matching, village chat, mentor loop, safety triage |
| Integration | DynamoDB-local end-to-end for the matching pipeline |
| Contract | Client types and request validation generated from the OpenAPI spec |
| Offline eval | Hand-labeled prompt set to validate classifier accuracy before any model change ships (replaces the user-confirmation feedback loop that was cut) |
| Simulator | `tools/simulator` generates realistic synthetic cohorts to tune village size distribution and unmatched rate without real users |

---

## Observability

Structured JSON logs with correlation ids (AWS Lambda Powertools) across the full request path and batch job. Metrics tracked per matching run: pool sizes, village size distribution, unmatched count, mean intra-village similarity, model latency and cost. Alarms on DLQ depth, 5xx rate, API p95 latency, push delivery rate, and matching run duration. Budget alarm on the AWS account from day one.

Product metrics (PRD §7) are derivable from message timestamps: reply-within-48h rate and village-active-past-first-cycle rate. Domain events (`PromptSubmitted`, `VillageCreated`, `MessagePosted`) emitted to EventBridge so analytics never reads production tables.

---

## Open Questions and Risks

| # | Item |
|---|---|
| R1 | Safety interstitial wording needs legal review before launch — liability exposure from implying a duty of care the app can't fulfill |
| R2 | Biggest untested assumption: mothers are willing to share something vulnerable with strangers on a recurring, twice-weekly basis |
| R3 | Optimal village size (5–10) is a hypothesis, not validated — needs a small spike with real users |
| R4 | Health-adjacent topics need a "peer experience, not medical advice" disclaimer pattern on every village |
| Q1 | IaC tool: CDK in Python (one language) or Terraform (more portable, common in job postings)? Default: CDK |
| Q2 | LLM provider for classify + embed: Bedrock (data stays in AWS) or another provider? Pending cost/quality comparison |
| Q3 | Users in pools too small to form a village: carry over to next cycle with priority, lower the minimum early on, or fold into a broader village? Default: carry over |

---

## Status

MVP — active build. Matching pipeline and accounts service are implemented. Mobile app scaffolded. See the [PRD](product-requirements/my-village-PRD.md) for open product questions, screen-by-screen flow detail, and success metrics. See the [architecture design](product-requirements/my-village-architecture-design.md) for full component specs, data model, and decisions log.
