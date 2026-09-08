# 18 — Design Partner Program

Three committed partners, recruited during M0, signed before M1 ends. This runs
in parallel with foundation engineering and is owned by the product owner, not
by an engineer.

## 1. Why this gates the build

The dispatch board and the mobile job flow cannot be specified into existence.
Every FSM product that failed did so because it was designed from a plausible
model of field operations rather than an observed one. Specifically:

- A dispatcher's real day is interruption-driven — emergencies, no-shows, sick
  engineers — and the board must be built for re-planning, not planning.
- Technicians do not read. They tap. A form flow that seems reasonable on a
  laptop is unusable in a plant room in August wearing gloves.
- The words matter (`17-terminology-ar.md`) and only users can supply them.

## 2. Target profile

| Attribute | Target |
|---|---|
| Size | 15–60 field staff |
| Sector | HVAC/MEP maintenance, or fire & safety systems |
| Location | Dubai, Sharjah, Ajman, Abu Dhabi — close enough to visit |
| Model | Annual maintenance contracts, recurring visits |
| Current tools | Paper job sheets, WhatsApp groups, Excel |
| Attitude | An operations manager who is visibly frustrated |

**Deliberately avoid:** companies already running a CAFM or FSM system (they
want migration, not co-design), companies under 10 staff (no dispatcher role
exists, so the core workflow is absent), and integrated FM majors (procurement
cycles longer than the build).

Aim for variety across the three: one paper-only, one Excel-heavy, one that has
tried and abandoned a tool.

## 3. Where to find them

Ranked by conversion rate, best first:

1. **HVAC parts distributors and suppliers.** They know every contractor in the
   emirate, they know who is growing, and an introduction from them carries
   trust. The single highest-yield channel and the least used.
2. **Existing personal network.** One warm introduction beats fifty cold messages.
3. **Civil Defence approved-contractor lists** — public, and pre-filtered to
   companies with real compliance obligations.
4. **LinkedIn**, targeting Operations Manager, Maintenance Manager and Service
   Manager titles at companies of 20–200 in the UAE.
5. **Trade events** — The Big 5, HVAC R Expo. Dense concentration of exactly the
   right people.
6. Contractor WhatsApp and Facebook groups — high noise, occasional gems.

## 4. The offer

Framed as co-design, never as free software:

**They get:** 18 months free, then 50% off for a year. Their own forms built and
loaded by us. A direct WhatsApp line to the team. Genuine influence over the
roadmap. Their logo and a case study only with their permission.

**They give:** two hours a week of the operations manager for eight months, one
full day shadowing a dispatcher, two engineer ride-alongs, honest feedback
including the unflattering kind, and a reference call for future prospects.

A **one-page letter of intent**, not a contract. Anything requiring their legal
department to review will not be signed inside three weeks.

## 5. Qualification

Say no to a prospect who will not commit a dispatcher day, whose enthusiasm sits
with the owner but not the operations manager, who wants a custom build, or who
is shopping for free software rather than a better way of working.

Three genuinely engaged partners beat eight nominal ones. A nominal partner
produces polite feedback, which is worse than none because it feels like
validation.

## 6. Research schedule

| When | Activity | Output |
|---|---|---|
| M0 wk 1–3 | Recruit and sign three partners | Signed LOIs |
| M0 wk 4 | **Dispatcher shadowing day** at each | Interruption log, the real day |
| M0 wk 5 | **Two engineer ride-alongs** each | Job flow, dead zones, glove test |
| M1 | Collect their real forms and asset registers | Template library input, import test |
| M1 wk 4 | Terminology review session | `17-terminology-ar.md` frozen |
| M2 | Dispatch board prototype testing | Board redesign before it is built |
| M3–M4 | Mobile prototype in the hands of real technicians | Flow corrections |
| **M5 (wk 26)** | **First partner live in production on real jobs** | Pilot begins |
| M6 | Second and third partners live | Scale validation |
| M7 | Pricing and packaging conversations | Q14 validated |

The dispatcher shadowing and ride-alongs are **blocking for M2 and M3
respectively**. Building either surface without them is the highest-risk
shortcut available in this plan.

## 7. What to actually observe

Do not ask what they want. Watch what they do, and count things:

- How many times is the day's plan changed after 08:00, and why each time?
- What is written on paper, and what happens to that paper afterwards?
- Which WhatsApp messages are actually work items in disguise?
- How does an engineer discover a needed part is not on the van?
- What happens when a customer disputes that work was done?
- How long does an engineer spend on paperwork per job, measured?
- What does the operations manager check first every morning?
- Which report gets printed and shown to the owner?

The last two determine the default dashboard, and they are almost never what a
product team guesses.

## 8. Running the relationship

- One named owner on our side. Partners should never wonder whom to contact.
- A weekly 30-minute call, same slot, even when there is nothing to show.
- Every piece of feedback logged with its source, so decisions can be traced to a
  person and re-checked with them.
- Ship something they asked for within the first month, and tell them it was
  theirs. Nothing sustains engagement like visible influence.
- Be explicit about what is not being built and why — partners tolerate "no"
  much better than silence.
