# Product Requirements Document (v0.8 — Draft)
## Working Title: Village — Peer Support Network for Motherhood

---

## 1. Problem Statement

Existing mom-friend-finding apps (Peanut, Bumble BFF) use swipe-based, 1:1 matching on static traits (photo, bio, interests). This model has a well-documented, structural failure mode: matches are shallow, so conversations have nothing to sustain them past the opener, leading to widespread ghosting.

Beyond the structural matching problem, the format itself works against the exact user it's meant to serve. Swipe-based browsing still requires someone to make the first move — write the opener, wait, hope it lands — and that small social task is a real barrier for mothers who are already running on empty. Chronic sleep deprivation and burnout are the baseline state for many new mothers, not the exception; asking them to also perform the emotional labor of a cold introduction, on top of everything else, is a meaningful tax on people with the least bandwidth to pay it. The swipe/bio/opener format also can't help but feel like dating — which, for someone not looking for romance or courtship, adds an unwanted layer of self-presentation pressure to what should be a source of relief, not another performance.

**Supporting data:**
- 7 in 10 mothers report motherhood is lonelier than they expected; 1 in 5 feels that loneliness every single day (Motherly, 2025 State of Motherhood survey).
- Ghosting is the most-cited complaint across both Peanut and Bumble BFF reviews — described as "a ton of effort with no payoff."
- Bumble itself publicly abandoned pure swipe-matching for BFF in September 2025, pivoting to group/community structures built on its Geneva acquisition — a direct signal from the market leader that the mechanic is broken.
- Peanut's own review base separately flags low user density outside major cities, and trust/moderation issues (harassment, disputed bans).

**Root cause identified:** matches lack real shared context. Static traits (interests, location) are too thin to carry a conversation. Real, current, specific experiences are a much stronger connective thread — and critically, a shared struggle removes the "who speaks first" problem entirely, since there's already something real to respond to instead of a blank opener to write.

---

## 2. Target User

First-time and early-stage mothers, especially those without an existing local support network (new to an area, first in their friend group to have a baby, isolated by non-traditional work schedules, etc.). Initial focus: mothers of infants/toddlers (0–2 years), where day-to-day struggles are most acute and universal.

---

## 3. Product Concept

A peer-support network for motherhood, organized around **real, current shared experience** rather than static profile traits. At its core, the product is answering one question for an exhausted, worried parent: *"is this normal?"* — the reassurance that her child isn't uniquely difficult and that other mothers are, right now, in the same struggle. That validation is the emotional payoff the product is built to deliver; the matching mechanics below exist to get her there with as little effort as possible. Three core mechanics:

### 3.1 Twice-Weekly Topic/Struggle Matching
- User shares a short prompt: what's going on with them or their baby right now (can be a struggle — "she's refusing to eat" — or a milestone/moment — "first word today!").
- App clusters users with similar current topics (by life stage + category, not literal text match) into a small village chat.
- The user's own submitted prompt is used as the village's opening message, removing the "who speaks first" barrier.
- New cohort logic runs **twice a week (Sunday and Wednesday)**: a fresh round of villages is created at each match run based on that cycle's submitted prompts, rather than one static village per user. Villages can be **reshuffled or topped up** — quiet/non-responsive members are rotated into new villages; active, engaged members stay together. Avoids re-matching pairs that already went quiet on each other. Full cycle mechanics in §12.
- Because matching is driven by that cycle's specific prompt (not a fixed profile), a member can surface in more than one active village across cycles if her situation genuinely spans multiple topics (e.g., a sleep-regression prompt one cycle and a feeding prompt the next both landing her in relevant, live villages). This is a byproduct of topic-first matching, not a separate feature — worth watching in practice to make sure it reads as helpful rather than overwhelming.
- Village size: working hypothesis is **5–10 members** per village — small enough to feel intimate and low-pressure, large enough that a quiet week from a few members doesn't kill the conversation. Not yet validated (see Open Questions).
- Scaling rule: when enough users submit similar prompts in the same window to support more than one full village, the system splits them into multiple parallel villages on that topic rather than one oversized village — keeping each village inside the target size band instead of stretching it.
- Matching is **not limited to local geography** by default — nationwide matching solves the local-density problem smaller apps face, since critical mass is needed on *topic*, not *zip code*. Location-based matching remains available as a user option.

### 3.2 Pinboard / Low-Effort Reassurance
- A shared, lightweight feed of moments — struggles and wins — that other users can "nudge" (a same/me-too style low-effort reaction) without needing to compose a reply.
- Serves a different need than the village chats: normalization ("is this normal?") rather than problem-solving. Designed for low-bandwidth moments (e.g., 2am, one-handed use).

### 3.3 Mentor Loop (Returning Experienced Moms)
- When a user's active struggle resolves, she can **opt in** to be re-matched months later into a new village of moms currently facing that same issue — bringing lived, resolved experience into the village instead of two equally-lost strangers guessing together.
- This is the most differentiated mechanic in the product — no direct competitor is doing this. It also creates a retention loop: once a mother's immediate need is resolved, she still has a reason to stay engaged (as a helper, not just a help-seeker).

---

## 4. MVP Scope Recommendation

Given this is a portfolio-scale build, recommend going deep on **one** pillar rather than shallow on three. **Recommended focus: the Mentor Loop**, since it is the most differentiated mechanic and the clearest demonstration of original product thinking.

**In scope for MVP:**
- Twice-weekly prompt submission + basic topic/category clustering (age-stage + topic tag)
- Village formation and reshuffle/top-up logic (can be simplified/simulated for demo purposes)
- Opt-in mentor flow: mark a struggle "resolved," opt in to future re-matching, get re-matched into a new relevant village later

**Explicitly out of scope for MVP (Fast Follow):**
- Pinboard/nudge feature
- Location-based matching option
- Real-time chat infrastructure at scale (can be simulated/mocked for demo)
- Push notifications
- Multi-child profiles

---

## 5. Key Risks & Open Questions

1. **Safety triage:** Not every submitted "struggle" belongs in a peer village unfiltered. Topics suggesting postpartum depression, marital strain, or other serious concerns need to be flagged and redirected toward professional resources rather than (or in addition to) village-based matching. This needs a defined triage step before launch, even in demo form.
2. **Clustering quality:** Free-text prompts need a categorization layer (e.g., "she's refusing to eat" and "won't touch solids" should cluster together). This is a real, scoped technical problem — not simple keyword matching.
3. **Peer advice guardrails:** Health-adjacent topics (feeding, sleep, development) need a lightweight "peer experience, not medical advice" disclaimer pattern to protect both parties.
4. **Mentor loop consent:** Re-matching should be opt-in, not automatic — a checkbox at the point of marking a struggle "resolved," not an assumed obligation.
5. **Optimal village size:** Working hypothesis is 5–10 members per village, but this is untested. Needs its own study/spike — too small and a couple of quiet members can stall the whole conversation; too large and it likely feels impersonal or generates noise that discourages the vulnerable sharing the product depends on. Also unresolved: how to handle the edge case of a member appearing in multiple concurrent villages across cycles (see 3.1) without it feeling scattered.
6. **Unproven assumption:** No data yet confirms mothers are willing to share something vulnerable with strangers on a recurring, twice-weekly basis. This is the biggest untested assumption in the whole concept and worth stress-testing early, even informally.
7. **Safety-screen liability:** The Safety Triage Interstitial (§9 step 4) surfaces professional resources for crisis-adjacent language — how it's worded and what it does/doesn't promise needs legal review before launch to avoid creating liability exposure (e.g., implying a duty of care the app can't actually fulfill). Not a lawyer, not offering legal advice here — flagging this as an open item that needs actual counsel, not a design decision to make unilaterally.

---

## 6. Competitive Landscape (Reference)

| Product | Model | Known Weakness |
|---|---|---|
| Peanut | Swipe-based 1:1, $21.8M raised, 29 active competitors | Ghosting, low density outside major cities, moderation/trust issues, billing complaints |
| Bumble BFF | Swipe-based 1:1 → pivoted to Groups (Sept 2025, Geneva acquisition) | Publicly acknowledged swipe model wasn't working |
| Timeleft | No swiping — matches into real-world dinner groups | Different vertical (general friendship, not motherhood-specific) |
| Introvrs Bestie | Compatibility questionnaire + written match context | Small player, not motherhood-specific |

**Positioning takeaway:** this product is not "a better mom friend-finder app" — it's a peer-support network for motherhood organized around real, current, shared experience. That reframing is the core differentiator, not a nicer UI on the same swipe mechanic.

---

## 7. Success Metrics (Draft — to refine)

- % of prompt submissions that result in at least one reply within 48 hours (proxy for "did the opener work")
- % of villages that remain active (2+ messages from 2+ people) past the first cycle
- Mentor-loop opt-in rate at struggle-resolution point
- Mentor-loop re-engagement rate (do opted-in mentors actually respond when re-matched months later)

---

## 8. MVP Screens & Features

**Navigation:** 3 bottom tabs — **Village**, **Home**, **Profile**. Mapped to the "In scope for MVP" list in §4:

| # | Screen | Purpose |
|---|---|---|
| 1 | Welcome | Value prop + account creation folded in (no separate auth step), single CTA into profile creation |
| 2 | Profile Creation | 1–3 photos, short bio, child's age, gender. *(Post-creation confirmation copy still pending — your "needs thinking" note, see §9.)* |
| 3 | First Prompt Submission | Core input — this cycle's struggle or win. Cadence/expectation copy is now built into this same screen (no separate expectation screen). First instance of the persistent Home prompt card described in §12 |
| 4 | Safety Triage Interstitial (conditional) | Surfaces professional resources when flagged, before/alongside matching. *(Liability/legal approach flagged as open risk — see §5.7.)* |
| 5 | Home (persistent, 3 states) | Waiting / Matched / Mentor Invite — the recurring prompt-and-status card. Full detail in §12; Mentor Invite state still needs a copy/UI pass |
| 6 | Soft Preview | New — shown when she taps into a fresh match, before landing in the live chat. Topic + participant count, single "Enter my village" CTA. No accept/deny, no profiles to evaluate — an orientation beat, not a decision gate |
| 7 | Village Chat View | The matched village, opened with the user's own prompt as the first message. Standard messaging-app format (bubbles, input bar) |
| 8 | My Villages (list) | Home view for the Village tab — lists her joined villages, since she can be active in more than one at a time. Back in MVP scope |
| 9 | Mark Resolved / Mentor Opt-In Modal | Triggered from within the chat when a struggle resolves |
| 10 | Mentor Re-Match Notification | Async, months later — invite to join a new village as a mentor. Surfaces via the Home Mentor Invite state |
| 11 | Profile (tab) | View/edit profile fields from screen 2 |
| 12 | Settings (minimal) | Pending your input |

Screens 1–4 make up the onboarding flow (§9) — down from 6, since Category Confirmation is cut (classification is now fully backend, see §10) and the old standalone Matching Expectation screen is folded into screen 3's copy. Screen 5 (Home) recurs every cycle post-onboarding — see §12. Screen 6 (Soft Preview) sits between a match notification and screen 7 — see §12.2. Screens 9–10 make up the mentor opt-in flow (§11, wireframed separately).

---

## 9. Onboarding Prompt Flow (Detail)

1. **Welcome** — "You're not alone in this. Twice a week, moms just like you connect over what's actually going on." Account creation folded in here rather than as a separate step. Single CTA into profile creation.
2. **Profile creation** — 1–3 photos, a short bio, child's age, and gender. Kept simple by design — this is the extent of structured profile data collected. *(Copy for the post-creation confirmation moment still needs deciding — flagging your "needs thinking" note here rather than guessing at it.)*
3. **First prompt** — Headline top-mid: "How are you and your baby today?" Free text field, placeholder "Share your thoughts here" (short cap, ~140–280 characters, so it reads like an opener rather than a journal entry). A lightweight Struggle / Milestone toggle sets tone without gating anything. Directly below the field: "We'll connect you to your village Wednesday! Feel free to edit until then." — cadence and edit-window expectations are now set inline here rather than on a separate screen. Button: **"Find my village."** This is the first instance of the persistent, editable prompt card — full lock/cycle mechanics in §12, not repeated here.
4. **Safety triage check (conditional, backend)** — On submit, the prompt runs through a lightweight classifier for crisis-adjacent language (self-harm, severe depression, marital crisis, abuse indicators). If flagged, an interstitial surfaces professional resources first — not gated behind or instead of matching, but ahead of it, with a clear, non-punitive option to still be placed in a village. Directly implements Risk #1 from §5. *(How to word this screen without creating liability exposure is still an open question — flagged as Risk #7 in §5 rather than guessed at here.)*
5. **(Async) Village-formed notification** — When her cohort forms, Home shows the Matched state (§12.2) and a push notification fires. Tapping either opens the Soft Preview (§12.2) before she lands in the live village chat with her own prompt already sitting there as the opening message.

Category confirmation is no longer a user-facing step — topic classification and matching now happen entirely server-side (see §10). The old standalone Matching Expectation screen is gone too; its copy is now built into step 3 above.

---

## 10. Categorization Taxonomy (Detail)

**Axis 1 — Life-Stage Band** (±1 band matching tolerance when a topic's pool is thin):
- Newborn: 0–3 months
- Infant: 3–6 months
- Infant: 6–12 months
- Toddler: 12–18 months
- Toddler: 18–24 months

**Axis 2 — Topic Category → Subcategories:**

| Category | Subcategories |
|---|---|
| Sleep | Night waking, naps, regressions, sleep training, crib/bed transitions |
| Feeding | Breastfeeding, bottle/formula, starting solids, refusal/pickiness, weaning |
| Development | Motor milestones, speech/language, cognitive, growth/weight |
| Behavior | Tantrums, separation anxiety, biting/hitting, clinginess |
| Health | Teething, illness, vaccines, allergies |
| Mom Wellbeing | Postpartum recovery, identity/loss of self, partner/relationship strain, returning to work, mental health *(cross-tagged to safety triage)* |
| Logistics | Childcare/daycare decisions, routines/schedules, travel with baby |
| Milestones & Wins | First word/steps, "made it through," small victories — kept as its own bucket so wins cluster with other wins rather than getting buried in struggle-heavy villages |

**Clustering logic:**
- Primary key: topic category (+ subcategory when volume supports it).
- Secondary key: life-stage band, with tolerance widened to ±1 band if the primary pool is too thin that week.
- Classification approach: free text is mapped to the nearest category/subcategory via a lightweight classifier or embedding similarity — not literal keyword matching (per Risk #2). This now happens **entirely server-side** — there's no user-facing confirmation step (the old editable-chip screen from earlier drafts is cut). Trade-off worth naming: this removes the "keeps her in control" benefit and the natural feedback loop that user-confirmed tags would have given the classifier over time. Accepted for MVP, but flagging it rather than letting it disappear quietly — worth revisiting if misclassification turns out to be common in practice.

---

## 11. Mentor Opt-In Flow (Detail)

**Trigger:** from within an active village chat (or a light periodic check-in), the user can mark her own original struggle "Resolved."

1. **Mark resolved** — A low-friction action attached to her own message in the chat, not a separate flow to hunt for.
2. **Opt-in prompt (modal)** — "Glad it's better! Down the road, want to be matched into a village of moms going through this same thing, to share what helped?" Yes / Not now. Copy clarifies it's opt-in, occasional (not immediate), and reversible later in Settings.
3. **Confirmation state** — "You're opted in as a mentor for [Topic]." Shown once — not a recurring nag.
4. **(Async, months later) Re-match notification** — "A village of moms is dealing with [topic] right now. Want to join and share what worked?" Join / Not this time. Declining is a per-invite choice, not a one-time-only offer — she stays eligible for future invites.
5. **Joining as a mentor** — She's dropped into the new village with a light, non-clinical framing (tagged as "been through this," not presented as an authority) — keeping the peer tone intact rather than creating an advice hierarchy.

*A wireframe of this flow (screens 2–5) is provided alongside this document.*

---

## 12. Home Screen & Matching Cycle (Detail)

**Navigation:** Home is one of 3 bottom tabs (Village, Home, Profile). It's a persistent screen, not a one-time onboarding step — the prompt card introduced in §9 step 3 lives here permanently and cycles twice a week for the life of the account.

### 12.1 The Cycle

Matching runs twice a week, Sunday and Wednesday, on two mirrored tracks. Lock time is the same for both tracks — **11pm** the night before each match run:

| Day | Track | State |
|---|---|---|
| Mon (morning) | → Wed match | Reopens — fresh prompt editable |
| Tue | → Wed match | Editable, then locks ~11pm |
| Wed | Wed match runs | Locked; match job runs; push notification on completion; stays locked all day |
| Thu (morning) | → Sun match | Reopens — fresh prompt editable |
| Fri–Sat | → Sun match | Editable, then locks ~11pm Sat |
| Sun | Sun match runs | Locked; match job runs; push notification on completion; stays locked all day |
| Mon (morning) | → Wed match | Cycle repeats |

**Missed-window rule:** if she doesn't submit anything by a given lock time, the field simply stays open past that deadline rather than closing — it rolls forward and locks in at the *next* cycle's lock time instead. Nothing is lost, there's just no guaranteed match that round.

**Backend note:** lock time is when a backend job (Fargate) picks up the batch of locked prompts and runs the matching/clustering logic.

### 12.2 Status Copy (below the prompt field)

The card shows different status text depending on where she is in the cycle:
- **Editable** — field is open for input/edits, no special status needed.
- **Locked, pre-match** — something like "waiting for your new village." Persists as **"next match is about to happen"** until the match job actually finishes running — not instantly at lock, since the backend needs processing time.
- **Matched** — Home shows a banner/badge noting new matches (e.g., "You have new matches!"); tapping it — or the match-completion push notification — opens the **Soft Preview** screen first, not the live chat directly. Soft Preview shows just the topic and how many members are in it (no profiles to evaluate, no accept/deny decision), with a single CTA: "Enter my village." Tapping it lands her in the live chat, already added to her joined villages list (§8, screen 8). This is an auto-placement flow, not a suggestion she can decline — the preview is purely an orientation beat before joining, not a gate.

### 12.3 Soft Preview Screen

Inserted between a match notification and the live chat (§8, screen 6). Deliberately minimal — topic + participant count only, no member profiles to look over, no Accept/Deny choice. This exists to give a beat of orientation before being dropped into an active conversation, without reintroducing the evaluate-and-decide pattern (profiles, accept/reject) that Peanut/Bumble BFF are critiqued for in §1. Single CTA: **"Enter my village."**

### 12.4 Three Home States for MVP

Agreed to design for all three at MVP:
1. **Waiting** — the prompt card + cycle status above.
2. **Matched** — banner/badge on Home + push notification, both routing into the Soft Preview screen first, then the live village chat (see 12.2).
3. **Mentor Invite** — the async, months-later re-match invite from §11 has no other natural home in the nav, so it surfaces here. *(Still open: copy and card design not yet defined — same treatment as the resolve-triggered opt-in modal in §11, or something distinct? To decide together.)*

### 12.5 Push Notification Tone

Match notifications should be playful, not a dry system alert — think of the cheeky, personality-forward tone The League used for its match notifications, where getting matched felt like a small delightful moment rather than an admin update. Illustrative starting points (directional, not final copy):
- "Your village just got bigger 👀"
- "6 moms who are also up at 2am want to meet you"
- "Village's assembled — go say hi!"
- "New humans who get it are waiting"

To be workshopped together closer to build.

---

*Next steps: resolve remaining open items — Settings screen content, confirmation-screen copy (§9 step 2), Mentor Invite Home state design (§12.4), and the safety-triage liability approach (§5.7); user-test the onboarding prompt copy for first-move friction; validate village-size hypothesis (§5.5) via a small spike; begin MVP build starting with onboarding + prompt submission.*
