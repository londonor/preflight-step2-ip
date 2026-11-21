# Gate 2 · IP screen — Are you legally allowed to build it?

A Claude skill that runs the **intellectual-property search of Step 2 of the Biotech Venture Qualification Framework** — the fatal-flaw screen. Before you pay a patent attorney, it reads the published patent record and tells you whether an in-force claim appears to cover an essential part of your biotech or medtech invention, with no realistic way around it or through it.

It returns a verdict (**clear / wall / unknown**), an element-by-element map of every claim it read against your invention, and a **counsel kit**: the specific questions to take into one hour with a patent attorney, so that hour is spent reading claims instead of searching for them.

**This is a preliminary screen of public records. It is not legal advice and it is not a freedom-to-operate opinion.** Every sheet it produces says so. Its job is to find out whether there is an obvious reason to stop before paying for that opinion — and if there isn't, to make the opinion cheaper.

Part of the *Pre-Flight Checklist* article series on LinkedIn: [Before You Jump](https://www.linkedin.com/pulse/01-before-you-jump-pre-flight-checklist-launching-londono-md-phd-8auvc/) · [04: Your Research Could Become a Startup. But Should It?](https://www.linkedin.com/pulse/04-your-research-could-become-startup-should-ricardo-londono-md-phd-9dlwc/) (the Step 2 deep dive). The Gate 1 skill lives in its own repo: [preflight-step1](https://github.com/londonor/preflight-step1).

---

## The one idea it runs on

Scientists frequently misunderstand this: **a patent on your invention does not give you the right to sell it.** Your patent might protect an improvement; someone else may hold a broader claim on the approach underneath. You can own the improvement and still infringe. That is the difference between *patentability* and *freedom to operate*, and this skill is about the second one only.

The method is the **all-elements rule**. A claim covers your product only if *every* element of that claim is present in your product. So the skill:

1. Writes your invention as a numbered list of elements — what it's made of, how it's built, what it does, how it's used — marking each **essential** (remove it and you lose the product) or optional. Elements you didn't state but a claim would turn on (material, structure, fixation, resorbable or not) go on the list as **unstated**.
2. Writes the fail condition before it searches: *an in-force claim reads on the essential elements, has seven or more years left, and there is no licensing path.* Plus one venture-specific condition about your likeliest wall.
3. Finds candidate patents by mechanism, material, application, intended use, and owner.
4. Reads the **independent claims** — not the abstracts — of the closest 8–12, and maps each claim element as **present**, **absent**, or **unclear** in your invention.
5. For anything that reads or is unclear, checks status, expiry, and whether the owner licenses or sues.
6. Writes the strongest honest case that you are blocked, *then* the verdict.

An absent element *is* a design-around. Whether dropping it costs you an essential element is on your list. That turns "read the claims, not the abstracts" from a warning into something you can actually do.

## What it does not do

- It does **not** give legal advice or an FTO opinion. It finds the pages and reads them the way a careful founder would; claim construction is counsel's work.
- It does **not** say "no patent exists." A **clear** is a verdict on a bounded search of public records. Applications stay unpublished for eighteen months; claim language varies; the search is time-boxed.
- It does **not** guess. A status or expiry it did not read on a page is **unknown**. A claim it could not read is **unknown**. Every "unknown" becomes a question for counsel.
- It does **not** evaluate your technology, the market, buyers, the regulatory path, or you. Those are other searches and later gates; it records a flag and moves on.

---

## Install

### Claude (web and desktop) — any plan

1. Download **`gate-2-ip-screen.zip`** from the [Releases](https://github.com/londonor/preflight-step2-ip/releases) page. (Or zip the `skills/gate-2-ip-screen/` folder yourself; the folder must be the root of the ZIP, with `SKILL.md` inside it.)
2. If you have never used skills: **Settings → Capabilities** and turn on code execution / file creation.
3. **Customize → Skills** (claude.ai/customize/skills) → the **+** button → **Create skill** → **Upload a skill** → choose the ZIP.
4. Start a new chat and say *run the IP screen on …*

### Claude Code

```bash
claude plugin marketplace add londonor/preflight-step2-ip
claude plugin install preflight-gate-2-ip@preflight-step2
```

Then, in any session: `/preflight-gate-2-ip:gate-2-ip-screen synthetic ligament for ACL surgery`, or describe the invention and ask for the IP screen.

---

## How to run it

Say any of: **run the IP screen** · **freedom to operate** · **FTO** · **is there a blocking patent** · **am I allowed to build this** · **step 2 IP** — followed by the invention.

### The input can be a few words or a full description

A few words runs. The skill maps the landscape around what you stated and marks every live claim **unclear** against the specific element you didn't state — then tells you which choices to make, instead of making them for you.

```
run the IP screen on a synthetic ligament for ACL surgery
```

A full description gives it the elements it needs for **reads / does not read** determinations:

```
Run the IP screen. A permanent, non-resorbable ACL replacement: a single
braided UHMWPE cord with denser intra-osseous ends and an open-weave
intra-articular section for tissue ingrowth, fixed with interference
screws at both ends. Used alone, not as augmentation. US and EP matter.
```

### Optional inputs

| Input | What it does |
| --- | --- |
| `mode: autonomous` (default) | Searches and decides without asking anything. |
| `mode: interactive` | Asks at most four questions, one at a time, only to settle an element the search cannot. |
| `mode: eval` | Autonomous, plus a comparison line against a verdict you expected. |
| `jurisdictions: US, EP` | Default is US only. Only the named ones are screened. |
| `gate 1 record: ideas/<slug>/gate1.json` | Reuses the problem sentence, first indication, and slug from a Gate 1 run. |
| `counsel findings: …` | The attorney's answers, on a re-run. They close unclear results and can change the verdict. |

---

## What you get back

Two files — `ideas/<slug>/gate2-ip.md` (the sheet) and `ideas/<slug>/gate2-ip.json` (the record) — and a condensed version in the reply.

The sheet opens with the verdict and an **at-a-glance** block. Here is the one from a real run on a four-word input:

> **Verdict — Unknown.** The category is open: no in-force claim was found that covers a synthetic ligament for ACL reconstruction as such, and the broad families from the 1980s–2000s have run out. What remains are species claims — on degradable braided scaffolds, on spacer-textile composites, on hollow-braid adjustable loops with specific fasteners — and whether any of them reads depends on three choices the input does not make: material, structure, and whether the ligament is permanent or resorbable. **The question, and who answers it:** the founder settles elements 4, 5, and 8; the screen re-runs on the answer.
>
> - **The invention** — a man-made ligament implanted to replace the torn ACL, fixed to femur and tibia
> - **Essential elements** — 3, listed below; 5 unstated and one optional
> - **Patents opened** — 10 of 31 candidates
> - **Reads or unclear** — none reads as stated. Unclear, depending on unstated elements: Biorez/CONMED braided-scaffold family (active to 2030); Biorez/CONMED composite-scaffold family (protection stated to 2040); Biomet Sports Medicine ligament-system family (status not read); Biomet UK ligament assembly (active, claims not readable)
> - **Next step** — settle the three unstated elements, then one hour with patent counsel; six questions in the kit below

Below that: the two fail conditions with their results · the invention as elements · the candidate table · one section per opened patent, with status, expiry (labeled as a database estimate), owner, and an element map per independent claim · the case that you are blocked · the verdict · the risk list for later gates · the counsel kit · flags · a research log of every page opened.

The full example sheet is in [`examples/synthetic-acl-ligament/gate2-ip.md`](examples/synthetic-acl-ligament/gate2-ip.md).

### Reading the verdict

| Verdict | Meaning |
| --- | --- |
| **clear** | No wall found within budget. Anything that reads but didn't trigger — expired, under seven years, design-around on an optional element, licensing evidence — goes on a **risk list** for later gates. |
| **wall** | The fail condition triggered: an in-force claim reads on an essential element, seven or more years left, no licensing path. Carries a **park note** with the patent's expiry as the re-trigger. |
| **unknown** | An essential element is unclear and the fact it turns on is named and routed — to you if it's about the invention, to counsel if it's a claim term, a status, or a jurisdiction. |

Every result reads without knowing the test's direction: fail conditions are **cleared** or **triggered**; each claim **reads**, **does not read**, or is **unclear** — and a does-not-read always names the absent element.

### The element map

This is the part that makes the output auditable. For each independent claim:

| claim element (page's words) | in the invention | why |
| --- | --- | --- |
| polymeric fibers degrading at between nine and twelve months | unclear | element 8 unstated |
| braided into a three-dimensional braided scaffold, two end sections, a middle section | unclear | element 5 unstated |
| a patellar tendon graft with femoral and tibial bone plugs | absent | element 1: synthetic, not autograft |

Counsel can audit a map in a minute. That is the point of handing it over.

---

## The counsel kit

The part that makes this complementary to a patent attorney rather than a substitute for one. After a **clear** or an **unknown**, the next step is one hour with counsel — a claim reading, not a formal opinion yet. The kit holds:

- **Your invention as elements**, so counsel reads claims against the same thing the skill did.
- **The candidate table and the element maps.**
- **Three to six questions**, each naming a patent, a claim, and the element it turns on — e.g. *"US 8,486,143 claim 1, element 'polymeric fibers degrading at 9–12 months': if the ligament is permanent polyester, confirm this does not read."*
- **What to ask for**: an hour of claim reading and a view on whether a formal FTO opinion is warranted. Tell counsel the stage: pre-company, pre-funding, screening.
- **Possible novelty, for counsel**: elements of your invention found in no claim it opened. A note, not an opinion.
- **Pointers**, the same every time: do not disclose publicly before you've talked about filing; a patent page's status is an estimate; an application is not a patent but can become one; "patent pending" on a competitor's product means an application exists — find it; family members in different countries can carry different claims; keep your element list current; a crowded field is a risk, one in-force claim on an essential element with no way around is a wall — don't confuse them.
- **A bring-back form**, so the screen can be re-run with counsel's answers as input.

---

## Rules it holds itself to

1. Elements first, search second. The fail conditions are written before any page is opened.
2. Claims, not abstracts. A patent is not screened until its independent claims have been read — all of them, not just claim 1.
3. Every status, expiry, owner, and claim element carries the page it was read on. Expiry dates are recorded as the database's estimate.
4. "Not found" and "found to be absent" are kept apart. Unknown is a legitimate result.
5. The case that you are blocked is written before the verdict.
6. Anything belonging to another search or a later gate — the biology, regulatory precedent, gatekeepers, market, buyers, your own patentability — is flagged, not evaluated.
7. Budget: about 60–90 minutes, 25 candidates, 8–12 patents opened.

## Limits worth knowing

- **Patent search engines are JavaScript.** The skill reaches patent pages through a web search scoped to the patent database, then opens each page. It works; it is not exhaustive, and the search leans toward older, heavily-cited patents. Owner searches are how it finds the recent families.
- **Patent pages are long.** Claims sit a third of the way into a 300,000-character page, so each patent costs two reads. That's why the budget is 8–12.
- **Some records are out of reach**: claims behind a login on third-party sites, office PDFs with no machine-readable text. The sheet says which, and each becomes a counsel question rather than a guess.
- **It reads US records by default.** Add jurisdictions explicitly; family members can carry different claims.

---

## Repository layout

```
skills/gate-2-ip-screen/SKILL.md   the skill
gate-2-ip-screen.zip               the same skill, packaged for upload to Claude (on the Releases page)
examples/                          full output from a real run
.claude-plugin/                    plugin and marketplace manifests for Claude Code
README.md
LICENSE
```

## License

MIT. Use it, fork it, run your inventions through it. If you improve the search plan or the counsel questions, open a pull request with a run in `examples/` that shows the difference.
