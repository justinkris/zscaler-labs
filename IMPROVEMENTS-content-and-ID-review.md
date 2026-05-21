# Zscaler Labs — Content & Instructional Design Review

**Audience this review assumes:** self-paced learners working through the labs alone, without an instructor.
**Scope:** all six lab courses — EDU-200, 202, 230, 233, 260, 310.
**Date:** 2026-05-21

---

## TL;DR — the five things that matter most

1. **EDU-230's hero images are all broken** — `edu-230-lab/images/` contains only `prompts.txt`. Every `lab1-hero.png` … `lab8-hero.png` is a 404. P0 ship-blocker.
2. **EDU-202 has 18 images for 18 lab pages — one hero each, zero step screenshots.** Compare to EDU-200 (277 images). The most click-heavy course in the set has the least visual support.
3. **Half the labs have an orientation page (`lab0.html`), half don't.** Present in EDU-200, 202, 233; missing in EDU-230, 260, 310. A learner who jumps into EDU-310 first never sees a topology, VM list, or credential table.
4. **No troubleshooting guidance anywhere.** Across ~80 lab pages there are essentially zero "if this doesn't work, try X" callouts. Self-paced learners hit a dead end as soon as anything diverges from the happy path.
5. **No prerequisites, no per-lab outcomes, no real lab-to-lab handoffs.** Every lab opens with `Scenario:` and ends with a one-line flavor message. The framing layer that makes self-paced learning work isn't there yet.

Each of these is expanded below with file paths and quoted snippets.

---

## Cross-lab patterns

These are the issues that appear across multiple labs, ranked by impact on a self-paced learner.

### 1. No prerequisites or learner-prep framing on landing pages

None of the six hub pages tells the learner what they should already know, what software/account they need, or how to back out if they get stuck. A learner who lands on EDU-260 ("Troubleshooting — Part 2 of 2") has no on-page way to find out what Part 1 covered or where it lives.

- `edu-260-lab/index.html` title: `EDU-260 Hands-On Lab Guide — Introduction to Troubleshooting Part 2 of 2`. Searched the file for "Part 1" / "EDU-259" / "EDU-250" — zero references.
- `edu-200-lab/lab0.html` opens with "Welcome to the Zscaler for Users — Administrator (EDU-200) Hands-on Lab. You will practice skills from the EDU-200 e-learning…" — assumes the e-learning is done but never lists it as a prerequisite.
- `edu-310-lab/index.html` motivation banner mentions "14 hands-on labs covering ZDX navigation, Wi-Fi and CPU troubleshooting…" but never references EDU-200/202 background or the ZDX subscription requirements.

**Fix.** Add a "Before you start" block to every hub `index.html` with three lines: (1) Assumed knowledge / prior course, (2) Environment you'll be working in (Skytap pod + Zscaler tenant + any required apps), (3) Estimated total time for the whole track.

### 2. No "what you'll learn" framing on individual labs

Lab pages open with `Scenario:` (the task) but never tell the learner what skills they'll walk away with or what success looks like. EDU-200 `lab0.html` does have outcomes-style bullets ("By the end of the lab series you will be able to…"), but that pattern is not repeated on any individual lab.

- `edu-202-lab/lab15.html` opens with "Scenario: Configure a ZIdentity API client to enable programmatic access to Zscaler APIs. Use POSTMAN to authenticate…" — that's the task, not the outcome.
- `edu-310-lab/lab1.html` says "Before you can troubleshoot, you need to know the terrain." then launches into a 60-minute click-tour with no "by the end you'll be able to…" anchor.

**Fix.** Add a small "You'll learn to:" 3-bullet block right after the Scenario paragraph on every lab. Keep the existing Mental Note as the post-lab recap — they're complementary.

### 3. Almost no troubleshooting guidance

Across ~80 lab pages, there are effectively zero structured "if this doesn't work" callouts. A self-paced learner who hits a misnamed UI element, a slow Skytap VM, an unsynced user, or an SSL prompt has nowhere to go except restart.

- `edu-200-lab/lab9.html` walks the learner through starting and verifying the ZPA connector service but has no "if status shows failed, check the provisioning key path".
- `edu-230-lab/lab2.html` Task 2.5 walks through Signal failing to install, then says "End the Signal task in Task Manager, then re-install Signal — it should now succeed." — but if it doesn't (cert still cached, Activate didn't propagate), there's no fallback.
- `edu-200-lab/lab12.html` Task 12.1 step 3 ("In ZPA, navigate to the Gateway settings. Edit the existing gateway or add a SIPA configuration…") is one of the conceptually hardest steps in any lab and offers no recovery path.

**Fix.** Add a `callout-troubleshoot` variant to `lab-style.css` (the CSS already supports custom callout types). Drop one into every step that depends on a previous configuration propagating. Use a consistent shape: "If you don't see X → check Y → if still stuck → see Lab N task Z."

### 4. Massive screenshot inconsistency between labs

| Lab     | HTML pages | Images in `images/` |
| ------- | ---------- | ------------------- |
| EDU-200 | 18         | 277                 |
| EDU-202 | 18         | 18 (heroes only)    |
| EDU-230 | 9          | 0 usable (broken)   |
| EDU-233 | 13         | 38                  |
| EDU-260 | 9          | 40                  |
| EDU-310 | 15         | 151                 |

EDU-202 and EDU-230 are the outliers — and EDU-202 contains some of the most click-heavy tasks in the set (Postman API client setup, Browser Isolation profile, ZWA). A learner being told "Click Get New Access Token. Authentication completes. In the Manage Access Tokens window, click Use Token" with no visual aid will lose confidence fast.

**Fix.** EDU-230's broken images is a P0. EDU-202's screenshot gap is the highest-leverage learning-quality improvement available — prioritize labs 3, 8, 11, 15, 16.

### 5. Inconsistent entry-point structure (`lab0.html` present or absent)

Three labs have a proper orientation page; three don't:

| Lab     | Orientation page                  |
| ------- | --------------------------------- |
| EDU-200 | `lab0.html` ✅ (the model)        |
| EDU-202 | `lab0.html` ✅                    |
| EDU-230 | ❌ first page is `lab1.html` task |
| EDU-233 | `lab0.html` ✅                    |
| EDU-260 | ❌ first page is `lab1.html` task |
| EDU-310 | ❌ first page is `lab1.html` task |

EDU-200's `lab0.html` includes topology diagram, 3-VM table, credentials, Quick Reference glossary, and a Lab Configuration panel. EDU-230/260/310 learners get none of that — they're dropped straight into "Sign into ZIdentity" with no environment context.

**Fix.** Build a standard lab-0 template based on EDU-200's, and add it to EDU-230, 260, 310 as the new entry point. This single change closes the orientation gap for half the courses.

### 6. Breadcrumb root inconsistency

The all-labs hub at `index.html` is only linked in two of six breadcrumbs:

```
edu-200-lab/lab1.html   <a href="../index.html">Zscaler Labs</a> › <a href="index.html">EDU-200</a> › Lab 1   ✅
edu-202-lab/lab1.html   <a href="../index.html">Zscaler Labs</a> › <a href="index.html">EDU-202</a> › Lab 1   ✅
edu-230-lab/lab1.html   <a href="index.html">EDU-230</a> › Lab 1                                              ❌
edu-233-lab/lab1.html   <a href="index.html">EDU-233 Hub</a> › <a href="lab0.html">Lab 0</a> › Lab 1          ❌ (different shape)
edu-260-lab/lab1.html   <a href="index.html">EDU-260</a> › Lab 1                                              ❌
edu-310-lab/lab1.html   <a href="index.html">EDU-310</a> › Lab 1                                              ❌
```

**Fix.** Standardize all to `Zscaler Labs › EDU-XXX › Lab N`. Scriptable in 30 seconds.

### 7. Inconsistent tenant/domain names

The lab universe uses three different tenant domains and they leak across course boundaries:

- `edu-200-lab/` uses `labtenant.com` (71 instances) but `lab0.html` line 198 references `patraining.safemarch.com` — a stray from another lab: "Joined to Active Directory `patraining.safemarch.com`".
- `edu-230-lab/` mostly uses `safemarch.com` but mixes in `thezerotrustexchange.com`.
- `edu-260-lab/lab1.html` line 381 hardcodes both: `var d1='thezerotrustexchange.com',d2='thezerotrustlab.com';`. The Sales user uses the second domain only — exactly the kind of detail that will send a learner down a 10-minute "wrong domain" detour.

**Fix.** Pick one tenant per course, scrub the rest. The EDU-200 stray is a one-line fix; EDU-230 and EDU-260 need deeper attention.

### 8. Terminology inconsistencies for the same thing

Inside `edu-200-lab/` alone:

- "Experience Center" appears 36 times; "Experience Centre" appears 19 times — including in the page footer.
- "ZIA Admin Portal" appears 13 times; "ZIA Admin Console" appears 2 times in body text.

The other labs use "Portal" exclusively, so EDU-200 is the source of the inconsistency.

**Fix.** Project-wide search-replace, plus a one-page terminology style sheet in the repo root so this doesn't reappear.

### 9. Acronyms used before they're defined

Most labs have a Quick Reference sidebar (excellent), but the first body-text use of an acronym often precedes its definition by many paragraphs, and several labs reference concepts the Quick Reference doesn't cover.

- `edu-200-lab/lab0.html` opening paragraph drops ZIA, ZPA, ZDX, ZIdentity, ZCC, ZTE — only ZCC, ZTE, and Service Entitlement appear in Quick Reference; ZIA/ZPA/ZDX themselves are never defined inline.
- `edu-202-lab/index.html` header lists eleven acronyms in one sentence: "ZIdentity, ZPA Browser Access, Log Streaming, Firewall, SSL Inspection, Sandbox, Browser Isolation, Deception, Unified DLP, ZWA, MITRE ATT&CK / NIST CSF risk analysis, ZDX monitoring, and OneAPI automation."
- `edu-310-lab/lab1.html` Socratic Q1 references "Y-Engine" before Quick Reference defines it.

**Fix.** Add a one-paragraph expandable "Zscaler in one minute" block to every hub `index.html` defining ZIA, ZPA, ZDX, ZCC, ZTE, ZIdentity. ~150 words; unblocks every downstream lab.

### 10. Steps that assume context the learner doesn't have

Several tasks point at portal areas without saying how to get there:

- `edu-200-lab/lab12.html` Task 12.1.3: "In ZPA, navigate to the Gateway settings. Edit the existing gateway or add a SIPA configuration to enable SIPA forwarding." — no menu path, no screenshot, "the existing gateway" is unexplained.
- `edu-202-lab/lab15.html` Task 15.1.6 references tenant-specific scope names ("config:1:Super Admin", "API Full Access") — the learner has no way to confirm their view will match.
- `edu-230-lab/lab2.html` Task 2.5.2 says "Cloud Applications: Signal (under Instant Messaging)" — assumes the learner already knows where in the rule builder the "Cloud Applications" field lives.

**Fix.** For every "navigate to X" step, prefix with full menu path (e.g. `Policy > Web > Access Control > SSL Inspection`). EDU-230's SSL Inspection task already does this well — make it the standard.

### 11. No real lab-to-lab transitions

Every lab ends with a single italicized flavor line (e.g. "Internet and private access, unified. Two birds, one tunnel." in `edu-200-lab/lab12.html`). It's cute, but doesn't tell the learner what they just accomplished, why it mattered, or what to expect next. The Mental Note above acts as a topical recap but doesn't bridge.

**Fix.** Add a 2-line "Recap → Next up" block above the Prev/Next buttons:

> You just: provisioned the App Connector and saw it appear as Connected in ZPA.
> Next up: Lab 10 uses this connector to publish your first private app segment, then writes the access policy that lets Joe reach it.

Keep the flavor line, but separate it from the substantive handoff. Most important between phase boundaries (Setup → Policies, ZPA → ZDX in EDU-200; Troubleshooting → Operations → AI in EDU-310).

### 12. No "LAB X of Y" counter on three of six courses

EDU-230, EDU-260, EDU-310 all have a footer progress counter ("LAB 1 of 9", etc.). EDU-200, EDU-202, EDU-233 do not. With 17 labs in EDU-200, a learner has no idea how far along they are.

**Fix.** Add the counter to the three labs missing it. Scriptable in a minute.

---

## Per-lab notes

### EDU-200 — Zscaler for Users (Administrator)

**Strengths.** This is the model lab. `lab0.html` has the most thorough orientation: topology diagram, 3-VM table, credentials, Quick Reference glossary, Lab Configuration panel that fills in placeholders throughout the page. Screenshot coverage (277 images) is the best in the set. Socratic questions are well-targeted.

**Issues.**
1. "Experience Center / Experience Centre" inconsistency — 36 vs 19 uses, including in the footer.
2. Lab 12 (SIPA) Task 12.1 step 3 is the single vaguest step in any lab. SIPA is conceptually hard; this is where learners give up.
3. No "LAB X of Y" footer counter — with 17 labs, learners have no sense of progress.
4. `lab0.html` line 198 strays into `patraining.safemarch.com` — should be `patraining.labtenant.com`.

**Missing for self-paced use.** Per-lab outcomes statements; troubleshooting callouts on the highest-stakes labs (3, 5, 9, 12).

### EDU-202 — Zscaler for Users (Engineer)

**Strengths.** Phase structure on `index.html` with colour-coded chips (Setup → Connectivity → Security Controls → Threats & Data → Automation & Ops → Capstone) is the clearest IA across the six. Capstone framing is a great touch.

**Issues.**
1. **Massive screenshot gap.** 18 images for 18 lab pages — one hero each. Lab 15 (Postman + ZIdentity API client, conceptually the most complex lab in any course) has zero step screenshots.
2. Header acronym soup will scare a self-paced learner.
3. `EDU-202-Lab-Guide.pdf` sits next to the HTML but is not linked from `index.html` — learners who want to print or follow offline never find out it exists.
4. Inline `<style>` blocks for the SDC badge are repeated on lab9/lab11/lab12 — minor, but should move to `lab-style.css`.

### EDU-230 — Cyberthreat Protection

**Strengths.** Strong topical narrative: starts with "test your unprotected baseline" and revisits the same test at the end of Lab 4 to demonstrate improvement (`lab1.html`: "Record your findings — you will revisit these sites at the end of Lab 4 to confirm improvement."). This is excellent instructional design and worth preserving and replicating.

**Issues.**
1. **All hero images are broken.** `edu-230-lab/images/` contains only `prompts.txt`. P0 ship-blocker.
2. Zero step-by-step screenshots — a lab that walks the learner through certificate-pinning bypass dialogs (`lab2.html` Task 2.5) has no visual aids at all.
3. No `lab0.html` / orientation page. Learner is sent straight to "Sign into ZIdentity" without ever seeing a topology, VM list, or credentials.
4. Tenant domain mix (`safemarch.com` and `thezerotrustexchange.com`) will confuse learners typing the wrong domain at the login screen.

### EDU-233 — Browser Isolation

**Strengths.** Hub `index.html` cleanly distinguishes ZIA Isolation / ZPA Isolation / Data Protection phases. `lab0.html` Socratic intro frames why ZCC must be running before any Isolation policy can fire — a real conceptual hook.

**Issues.**
1. Per-lab progress checklist uses `sidebar-card` class while every other lab uses `checklist-card` — minor inconsistency but suggests divergence.
2. Page transition behavior is unique (`lab5.html` lines 296–308 use `e.preventDefault()` + opacity fade; the other labs use a simpler approach). Could break in some browsers in ways the rest of the set won't.
3. Breadcrumb format ("EDU-233 Hub › Lab 0 › Lab 1") is unique to this lab — should match the other five.
4. No capstone or summary at end — Lab 11 just ends with disabled Next.

### EDU-260 — Troubleshooting (Part 2 of 2)

**Strengths.** Best-named scenario framing: "You are a member of the Level 1 HelpDesk team at Safemarch." The Helpdesk persona is consistently maintained and gives the activities a real-world frame.

**Issues.**
1. **Title says "Part 2 of 2" but there is no link to, or summary of, Part 1.** Confirmed: searched `index.html` for "Part 1", "EDU-259", "EDU-250" — zero matches. A learner finding this guide first has no recovery path.
2. `lab1.html` line 381 hardcodes two domains — Sales user uses `thezerotrustlab.com` while everyone else uses `thezerotrustexchange.com`.
3. Lab pages have one long task each (Lab 5 = ~40 min, seven sub-areas). Learners have no way to sample 10 minutes of work; they must commit to the whole 40.
4. No `lab0.html` / orientation.

### EDU-310 — ZDX Operationalization

**Strengths.** Cleanest "name a persona, give them a problem" structure across labs 2–7 (Curtis Hardwick, Barry Bogdown, Linda Lucas, Quincy Martin, Grady Archie, Joni Sherman). High screenshot density for the troubleshooting labs. Footer has "LAB X of 14" progress counter.

**Issues.**
1. Lab 1 is 60 minutes of click-tour with 9 steps and no checkpoint — the kind of lab where self-paced learners drop off.
2. No `lab0.html` despite the lab assuming familiarity with the ZDX Admin Portal URL and Read-Only role.
3. Labs 8–10 (Operations phase) zoom from individual troubleshooting to fleet-level Incident Monitoring without a conceptual transition.
4. No capstone framing — Lab 14 ends with disabled Next and no "you've finished this track" close.

---

## Quick wins (each under an hour)

These are concrete, scoped, do-them-this-week items.

1. Generate the missing `edu-230-lab/images/lab*-hero.png` files (8 PNGs — the prompts are already in `prompts.txt`).
2. Search-replace `Experience Centre` → `Experience Center` in `edu-200-lab/` (19 instances).
3. Search-replace `ZIA Admin Console` → `ZIA Admin Portal` in `edu-200-lab/` (2 instances).
4. Fix the stray `patraining.safemarch.com` → `patraining.labtenant.com` in `edu-200-lab/lab0.html` line 198.
5. Add `<a href="../index.html">Zscaler Labs</a> ›` to the breadcrumb root in every page of EDU-230, 233, 260, 310. Scriptable.
6. Add `LAB X of Y` footer counter to EDU-200, 202, 233 (the other three already have it).
7. Link `EDU-202-Lab-Guide.pdf` from `edu-202-lab/index.html` as "Download offline PDF guide".
8. Rewrite the worst-offender step — `edu-200-lab/lab12.html` Task 12.1 step 3 — with the full menu path and a screenshot.
9. Add a "Part 1 of this course is EDU-259/EDU-250 — start there if you haven't" sentence to `edu-260-lab/index.html`. Verify the exact course code with your training team.
10. Add an expandable "Zscaler in one minute" definitions block to every hub `index.html` defining ZIA, ZPA, ZDX, ZCC, ZTE, ZIdentity.

---

## Strategic improvements

Bigger investments worth planning for.

### A. Standardize a `lab0.html` orientation template across all six courses

Model on EDU-200's `lab0.html`: topology diagram, VM/system table, credentials, Quick Reference glossary, lab-track outcomes, prerequisites. Ship the template to EDU-230, 260, 310. This single change closes the orientation gap for half the catalog.

### B. Bring EDU-202 and EDU-230 to screenshot parity

These two are the outliers (18 and 0 usable images respectively, vs. 38–277 elsewhere). Doing the visual work is the highest-leverage learning-quality improvement available. Prioritize the most click-heavy tasks: EDU-202 labs 3, 8, 11, 15, 16; EDU-230 labs 2, 4, 6.

### C. Add a `callout-troubleshoot` pattern and use it at every dependency point

The CSS already supports a callout system. Add a yellow/wrench "Troubleshoot" variant and inject it at every step that depends on previous configuration propagating. Highest-impact targets: EDU-200 labs 3, 5, 9, 12; EDU-310 labs 2, 3, 4.

### D. Replace flavor-line endings with structured "Recap → Next up" handoffs

Keep the flavor if you like it, but separate it from a real handoff. Two lines max:

> You just: \<concrete accomplishment>.
> Next up: \<what Lab N+1 does and why it follows from this>.

Especially important at phase boundaries.

### E. Build a master progress view on the all-labs hub

`/index.html` is the gateway, but per-lab checkboxes are stored in `localStorage` per page. Aggregate the existing `chk-*` keys into a small JS widget on the all-labs hub so learners can glance at "I'm 8 of 16 in EDU-200, 0 of 9 in EDU-230". Turns a folder of guides into an actual self-paced learning path.

---

## Strengths worth protecting

Three things in the current design are unusually strong and should be flagged as protected during any refactor:

- **Socratic "Think First" blocks with delayed-reveal model answers** are an unusually strong feature. They front-load reflection before mechanical clicking and they're consistent across all six courses.
- **The Lab Configuration panel** that substitutes the learner's FQDN/prefix into placeholder code blocks throughout the page is excellent UX.
- **EDU-230's "baseline → improvement" narrative arc** (test unprotected, revisit at end of Lab 4) is the single best instructional move in the set. Replicate this pattern elsewhere — EDU-200 and EDU-310 in particular have natural "before vs. after" moments that could carry the same arc.
