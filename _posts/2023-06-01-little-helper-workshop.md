---
layout: article
title:  "Workshop: Developer Onboarding Workshop Template"
date:   2023-06-01 09:00:00
categories: [workshop, onboarding, documentation, devex, team-practices]
---

TL;DR: Can a new developer build, run, and test your project in under 4 hours using only a 240-character message? This gamified workshop exposes onboarding documentation gaps through timed challenges—turning painful "where's the docs?" conversations into actionable improvement backlogs.

A new contractor joins your team Monday morning. By lunch, they're still installing dependencies. By Wednesday, they're Slacking you about environment variables. By Friday, they've made zero commits. Sound familiar? The problem isn't talent—it's missing documentation. This workshop surfaces those gaps in 60 minutes through a simple challenge: can someone set up your dev environment using only a 240-character starting message? If not, you've just found your next sprint's documentation work.

## Workshop Quick Facts

| Aspect             | Details                                 |
|--------------------|-----------------------------------------|
| **Duration**       | 60–90 minutes (1–4 rounds)              |
| **Participants**   | 1 player + 2–6 observers                |
| **Setup**          | Blank computer or VM + 240-char message |
| **Output**         | Prioritized documentation backlog       |
| **Repeat cadence** | Every 1–2 months or when onboarding     |

```plantuml
Setup (5 min) → Round 1 (15 min) → Feedback (5 min) →
Round 2 (15 min) → Feedback (5 min) → Backlog Creation (10 min)
```


# Goal
We try to highlight the possible lack of developer documentation by trying to set up a developer environment from a blank machine. If the complete working setup for a developer computer is longer than half a day, you can consider that either documentation or design of work environment and tooling is not clear and/or mastered enough and need improvement.

# Starting
Select one team member as the player or let the workshop facilitator be the player, the rest of the team will be observing.

1. Prepare a (reasonably) blank computer.
2. Give the player one (and only one) short message (up to 240 characters at most).

## The 240-Character Rule

**Why the limit?**  
Forces you to link to *discoverable* documentation instead of institutional knowledge. If your starting message is "Ask Sarah about the database," you've already failed.

**Good examples:**
- ✅ "Clone github.com/yourorg/app, open README.md, follow 'Quick Start'"
- ✅ "Join Slack #dev-setup channel, pin has devcontainer link + wiki URL"
- ✅ "Open Confluence: confluence.yourco.com/docs/APP → Dev Setup"

**Bad examples:**
- ❌ "Sarah knows the setup, DM her"
- ❌ "It's documented somewhere, check the wiki"
- ❌ "Run install.sh (ignore the errors, they're fine)"

# Round ~15min
The workshop surfaces gaps even with only 1 round, but you should be able to chain up to 4 rounds if needed.

##  10 min timed application step
The player should now try to follow the message and gather enough information to be able to:

## Scoring Checklist (8 core points)

Each round, award 1 point for each achievement:

- [ ] **Build:** Package all codebases
- [ ] **Build (clean):** No compiler warnings
- [ ] **Run:** Player can interact with Application
- [ ] **Run (local):** Player can interact on his machine
- [ ] **Endpoints:** Found API/service catalog
- [ ] **Endpoints (test):** Successfully called at least one
- [ ] **Tests:** Automated test suite runs
- [ ] **Tests (clean):** No warnings or ignored tests

**Bonus points** (see below) (optional, +10 max):
- IDE setup documented (2 pts)
- Version requirements specified (2 pts)
- Architecture docs + living documentation (2 pts)
- CI/CD access instructions (2 pts)
- Observability/monitoring access (2 pts)

If any questions arise, the observing team shouldn’t give any more information, but can take notes or start drafting the documentation they feel is missing.

![Can you run all layers in time? — Photo by Dan Asaki on Unsplash](https://miro.medium.com/v2/resize:fit:1400/0*CtLRlDzE2_nE5qt4 "Can you run all layers in time? — Photo by Dan Asaki on Unsplash")

## 5-Min Feedback (Structured Retro)

Use these prompts to keep feedback focused:

**What worked?**
- "Which documentation did the player actually use?"
- "Were any links/files particularly helpful?"

**What blocked progress?**
- "Where did the player get stuck the longest?"
- "What information did they search for but couldn't find?"

**What surprised you?**
- "Did the player follow a different path than expected?"
- "Were there undocumented assumptions?"

**Quick wins:**
- "What could we document in under 30 minutes?"

💡 **Tip:** Use a shared doc (Google Doc, Miro board) for live note-taking.

# Ending
To end the session, create a prioritized backlog on the missing documentation that felt needed to onboard a colleague, a coach or a maintenance team on the project. Add these items immediately to your Jira board or team todo-list equivalent.

# Gamification?
If you want to compare several teams and/or make the workshop competitive, you can award one point for each succeeding item on the list, in each round.

## Example Workshop Run: E-Commerce Platform Team

**Round 1 (15 min)**
- **Message:** "Clone github.com/acme/shop-api, see README"
- **Player:** Found README, but it said "install Java" without version
- **Result:** Installed Java 21, project needs Java 17 → build failed
- **Score:** 0/8 points
- **Action items:** Add Java version + download link to README

**Round 2 (15 min)**
- **Message:** Same as Round 1
- **Player:** Built successfully, but `mvn test` skipped integration tests
- **Result:** Build works, tests run but incomplete
- **Score:** 4/8 points (build + build-clean + tests partial)
- **Action items:** Document how to run full test suite (with Docker deps)

**Round 3 (15 min)**
- **Message:** Same as Round 1
- **Player:** Everything worked! Found Swagger UI for endpoints
- **Score:** 8/8 points ✅
- **Outcome:** Team feels confident handing project to another squad

## How Mature Is Your Onboarding?

Use your workshop score to gauge maturity:

**Level 1: Broken (0–3 points)**  
Can't even build the project. Documentation likely doesn't exist or is severely outdated.  
🚨 **Action:** Stop feature work, invest 2 sprints in docs.

**Level 2: Fragile (4–6 points)**  
Builds work, but tests or runtime fail. Documentation exists but has gaps.  
⚠️ **Action:** Document missing prerequisites, add troubleshooting section.

**Level 3: Functional (7–10 points)**  
Core setup works. Some advanced features undocumented.  
✅ **Action:** Add IDE setup, CI/CD access, observability links.

**Level 4: Excellent (11–15 points)**  
Everything documented and discoverable. New devs productive in hours.  
🎯 **Action:** Maintain and update quarterly.

**Level 5: Automated (16+ points + automation)**  
One-command setup via devcontainer, Nix, or similar. Self-service everything.  
🚀 **Action:** Share your setup as a template for other teams.

##  Optional points
Those previous points were the minimum of the minimum requirements for a dev environment setup, but you could also choose to include the following:

- Is there a recommended and/or supported IDE and plugins like linters and formatters (1 point), including configuration and setup instructions (1 point)?
- Are there expected/supported versions for elements composing the development stack (build tools, language, major framework, …)? (1 point) Is the project built and running with a reasonably recent version of all these elements? (1 point)
- Does the architecture and dev practices documentation (architecture decision records, user manual, …) explain how the application works (1 point), and where to find the living documentation (various tests reports, generated documentation)? (1 point)
- Does the CI/CD related documentation exist? i.e., how are artifacts versioned, and how is the application deployed into the target production environment? (1 point) Can we find a way to get the right to create these new versions, and start a new deployment without help? (1 point)
- Is there any relevant information related to observability (how does the production environment alert the maintenance team, how do the production environments metrics like CPU usage or query response times get collected)? (1 point) Can we find how to be added to the list of notified people, or get access to monitoring dashboards? (1pt)

## Common Gaps Discovered

Based on running this workshop with dozens of teams, here's what usually breaks:

| Problem                      | Symptom                       | Fix                                                   |
|------------------------------|-------------------------------|-------------------------------------------------------|
| **Version hell**             | "Works on my machine"         | Add `.sdkmanrc`, `.nvmrc`, or devcontainer            |
| **Secret sprawl**            | "Where do I get the API key?" | Document secrets bootstrap (1Password CLI, vault URL) |
| **Database drift**           | "My migrations fail"          | Provide seed data script or Docker Compose            |
| **IDE assumptions**          | "Just use IntelliJ settings"  | Document VS Code + IntelliJ setup separately          |
| **Test environment missing** | "Tests need AWS credentials"  | LocalStack/Testcontainers setup in README             |
| **Proxy/VPN required**       | "I can't reach the registry"  | Document network requirements upfront                 |

## Running the Workshop Successfully

### Before the workshop:
- **Prepare the machine:** VM snapshot, cloud workspace (GitHub Codespaces), or wiped laptop
- **Set expectations:** Tell the player they can't ask questions during timed rounds
- **Prepare observers:** Give them a note-taking template (see below)
- **Choose the right player:** Either a new team member (realistic) or an experienced dev (finds gaps faster)

### During the workshop:
- **Keep strict time:** Use a visible timer, no extensions
- **Record the session:** Screen recording helps identify navigation struggles
- **Take notes on *silence*:** When the player stalls, that's a documentation gap
- **Ban helpful hints:** Observers must stay quiet (hard but essential!)

### After the workshop:
- **Prioritize the backlog:** Fix quick wins immediately (same day if possible)
- **Assign owners:** Don't leave action items orphaned
- **Schedule next run:** Set a calendar reminder for 1–2 months out

## When Teams Push Back

**"We don't have time for workshops"**  
→ Response: "How much time did you spend last month helping new people set up?"

**"Our setup is too complex to document"**  
→ Response: "This workshop will show you exactly what to simplify."

**"We only onboard once a year"**  
→ Response: "Documentation rots. This keeps it fresh for contractors, coaches, and team transfers."

**"Our docs are fine, we don't need this"**  
→ Response: "Great! This will prove it and give you a maturity score to brag about."

# Conclusion
The best documentation is the documentation that gets used. This workshop doesn't just find gaps—it forces you to experience onboarding from a newcomer's perspective. That empathy is what turns adequate docs into excellent ones. Challenge: Run this workshop once. Then look your next new hire in the eye and say "You'll be committing code by lunch." That's the standard we're aiming for.
