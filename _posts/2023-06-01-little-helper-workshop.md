---
layout: article
title:  "Workshop: Are you ready to add a contributor to your project?"
date:   2023-06-01 09:00:00
categories: [workshop]
---

In my everyday job, I noticed that a lot of software development teams struggle to have a useful set of documents to provide to newcomers and enable them a fast access to their project. It results in a project that is sometimes hard to provide help to (from a coaching perspective), or a team that cannot share and homogenize their practices, since they don’t build a common way of working.

To help prevent this kind of obstacles, here is a short workshop you can use in order to discover those holes in your developers’ onboarding documentation.

It should make finding those missing points more enjoyable and interactive. You can try to use it every time a newcomer join your team, but I’d advise you to play it around every 1 or 2 months to check that all those documents are still up-to-date.

![A hole in your foundations - Photo by Bruce Kee on Unsplash]\
(https://miro.medium.com/v2/resize:fit:1400/0*yeZKGhbmElPAiCgB \
"A hole in your foundations - Photo by Bruce Kee on Unsplash")\
{: width="50%" }

# Goal
We try to highlight the possible lack of developer documentation by trying to set up a developer environment from a blank machine. If the complete working setup for a developer computer is longer than half a day, you can consider that either documentation or design of work environment and tooling is not clear and/or mastered enough and need improvement.

# Starting
We select a player from the team or let the workshop facilitator be the player, the rest of the team will be observing.

1. Prepare a (reasonably) blank computer.
1. Give the player one (and only one) short message (up to 240 characters at most).

# Round ~15min
The workshop should be useful even with only 1 round, but you should be able to chain up to 4 rounds if needed.

##  10 min timed application step
The player should now try to follow the message and gather enough information to be able to:

- Build and package all relevant codebases (1pt) without warnings (1pt)
- Run the application (1pt) on the player computer (1pt)
- Find endpoints catalog (1pt) and get some results from them (1pt)
- Run automated test suites (1pt) without warnings or ignored tests (1pt)

If any questions arise, the observing team shouldn’t give any more information, but can take notes or start drafting the documentation they feel is missing.

![Can you run all layers in time? — Photo by Dan Asaki on Unsplash]\
(https://miro.medium.com/v2/resize:fit:1400/0*CtLRlDzE2_nE5qt4 \
"Can you run all layers in time? — Photo by Dan Asaki on Unsplash")\
{: width="50%" }

##  5 min feedback step
At the end of the timer, let’s do a 5min feedback between the player and observers about the setup status of the player:

- Did he succeed in applying all the aspects listed?
- Are some steps too complex?
- Are there time-wasting periods that could be shortened?

Don’t discuss the next steps of the setup during that stage, just focus on what has been achieved or tried during the 15min.

# Ending
To end the session, let’s do a recap on the missing documentation that felt needed to onboard a colleague, a coach or a maintenance team on the project. Probably add some items to your Jira board or team todo-list equivalent.

# Gamification?
If you want to compare several teams and/or make the workshop competitive, you can award one point for each succeeding items on the list, in each round.

##  Example
In round 1, the player has been able to:

- build all codebases → 1pt
- run the automated tests → 1pt

Total: 2pts

In round 2, the player has just been able to:

- build all codebases, now without warning → 2pts
- run the app on his machine → 2 pts
- as before, run the automated tests → 1pt

Total: previous total + 5pts → 7 points

##  Optional points
Those previous points were the minimum of the minimum requirements for a dev environment setup, but you could also choose to include the following:

- Can we find a recommended and/or supported IDE and plugins like linters and formatters (1pt), including configuration and setup instructions (1pt)?
- Can we find the expected/supported versions composing the development stack (build tools, language, major framework,…)? (1pt) Is the project still built and running with the latest version of all these elements? (1pt)
- Can we find the architecture and dev documentation (architecture decision records, user manual,…) that explain how the application works (1pt), and where the live documentation is hosted/available (various tests reports, generated documentation)? (1pt)
- Can we find all information related to CI/CD, like how are artefacts versioned, and how is the application deployed into the target production environment? (1pt) Can we find a way to obtain the right to create these new versions, and start a new deployment ourselves? (1pt)
- Can we find relevant information related to observability (how do we get alerts when production environment fails, how do we monitor production environments metrics like CPU usage or query response times)? (1pt) Can we find how to be added to the list of notified people, or get access to monitoring dashboards? (1pt)

# Conclusion
I hope you’ll find this workshop useful. Or at least that it can be used as a checklist for remembering the various parts of your documentation that will help you to integrate new members, or allow you to transfer projects ownership to other teams with confidence that nothing obvious will go wrong. Who knows? With a bit of luck, you won’t have to receive a phone call much later to give an access you’ve forgotten, or to give details of a component that hasn’t been properly documented.

Have fun, and always look for ways to improve your codebase.
