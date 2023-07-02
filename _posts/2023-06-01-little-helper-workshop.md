---
layout: article
title:  "Little helper Workshop"
date:   2023-06-01 09:00:00
categories: workshop
---
Here is a short workshop you can use in your teams to help discover holes in
 your developers' environment documentation.

I created it to make the action of actively finding missing points in our
 documentation more enjoyable and interactive. You could try to use it every
 time a newcomer join your software development team, but I advise to play it
 around every 1 or 2 months to verify that all those documents are still
 up-to-date.

![A hole in your foundations - Photo by Bruce Kee on Unsplash](https://cdn-images-1.medium.com/max/1000/0*yeZKGhbmElPAiCgB "A hole in your foundations - Photo by Bruce Kee on Unsplash"){: width="50%"; margin="auto" }

## Goal
We try to put in evidence the possible lack of developer documentation by
 trying to set up a developer environment from a blank machine.

## Starting
We select a player from the team or let the workshop facilitator be the player,
 the rest of the team will be observing.

- Prepare a (reasonably) blank computer
- Give the player one (and only one) short message (up to 240 characters at
 most)

## Round ~15min
The workshop should be useful even with only 1 round, but you should be able to
 chain up to 4 rounds if needed.

If the complete working setup for a developer computer is longer than half a
 day, you can consider that either documentation, design of work environment
 and tooling is not clear and/or mastered enough, and need improvement.

### 10 min timed application step
The player should now try to follow the message and gather enough information
 to be able to:

- Build and package all relevant codebases (1pt) without warnings (1pt)
- Run the application (1pt) on the player computer (1pt)
- Find endpoints catalog (1pt) and get some results from them (1pt)
- Run automated test suites (1pt) without warnings or ignored tests (1pt)

If any question arise, the observing team shouldn't give any more information,
 but they can take notes or start to redact a documentation they feel is
 missing.

### 5 min feedback step
At the end of the timer, let's do a 5min feedback between the player and
 observers about the setup status of the player:

- Did he succeed in applying all the aspects listed?
- Are there some steps that are too complex?
- Are there long waiting time that could be improved?

Do not discuss the next steps of the setup during that step, focus only on what
 has been achieved or tried during the 15min.

## Ending
To end the session, let's do a recap on the missing documentation that felt
 needed to onboard a colleague, a coach or a maintenance team on the project.
 Probably add some items to your Jira board or team todo-list equivalent.

## Gamification?
If you want to compare several teams and/or make the workshop competitive, you
 can give out one point by succeeding items in the list, for every round.

### Example:

In the round 1, the player has been able to:
- build all codebases → 1pt
- run the automated tests → 1pt
Total: 2pts

In the round 2, the player has just been able to:
- build all codebases, now without warning → 2pts
- run the app on his machine → 2 pts
- as before, run the automated tests → 1pt
Total: previous total + 5pts → 7points

## Optional points
Those previous points were actually the minimum of the minimum requirements for
 a dev environment setup, but you could also choose to include the following
 points:
- Can we find a recommended and/or supported IDE and plugins like linters and
 formatters (1pt), including configuration and setup instructions (1pt)
- Can we find the expected/supported versions composing the development stack
 (build tools, language, major framework,…)? (1pt) Does the project still build
 and works with the latest version of all of those? (1pt)
- Can we find the architecture and dev documentation (architecture decision
 records, user manual,…) that explains how the application works (1pt), and
 where the live documentations is hosted/available (various tests reports,
 generated documentation)? (1pt)
- Can we find all information related to CI/CD, like how are artefacts
 versioned, and how is the application deployed into the target production
 environment? (1pt) Can we find how to get the right to create those new
 versions, and to start a new deployment ourselves? (1pt)
- Can we find relevant information related to observability (how do we get
 alerts when production environment fails, how do we monitor production
 environments metrics like CPU usage or query response times)? (1pt) Can we
 find how to get added to the list of notified people, or get access to
 monitoring dashboards? (1pt)

## Conclusion
I hope this workshop can be useful to you, or at least that it can be used as a
 checklist to remember the various part of your documentation that helps you
 with onboarding new members, or let you transfer the project ownership to
 another teams with confidence that nothing obvious will go wrong, and that you
 won't have to receive phone call much later to give some access that you
 forgot about, or give details on a components that was not documented
 adequately.

Have fun, and always watch over ways to improve your codebase.