---
layout: article
title:  "Workshop: Are you ready to add a contributor to your project?"
date:   2023-06-01 09:00:00
categories: [workshop]
---

In my everyday job, I noticed that a lot of software development teams struggle to have a useful set of documents to provide to newcomers and enable them fast access to their project. It results in a project that is sometimes hard to provide help to (from a coaching perspective), or a team without homogeneous practices, since they don’t build a common way of working.

To help prevent this kind of obstacle, here is a short workshop you can use to discover those holes in your developers’ onboarding documentation.

It should make finding those missing points more enjoyable and interactive. You can try to use it every time a newcomer joins your team, but I'd advise you to play it around every 1 or 2 months to check that all those documents are still up to date.

![A hole in your foundations - Photo by Bruce Kee on Unsplash]\
(https://miro.medium.com/v2/resize:fit:1400/0*yeZKGhbmElPAiCgB \
"A hole in your foundations - Photo by Bruce Kee on Unsplash")\
{: width="50%" }

# Goal
We try to highlight the possible lack of developer documentation by trying to set up a developer environment from a blank machine. If the complete working setup for a developer computer is longer than half a day, you can consider that either documentation or design of work environment and tooling is not clear and/or mastered enough and need improvement.

# Starting
We select a player from the team or let the workshop facilitator be the player, the rest of the team will be observing.

1. Prepare a (reasonably) blank computer.
2. Give the player one (and only one) short message (up to 240 characters at most).

# Round ~15min
The workshop should be useful even with only 1 round, but you should be able to chain up to 4 rounds if needed.

##  10 min timed application step
The player should now try to follow the message and gather enough information to be able to:

- Build and package all relevant codebases (1) without warnings (1)
- Run the application (1) on the player computer (1)
- Find the endpoints catalog (1) and get some results from them (1)
- Run automated test suites (1) without warnings or ignored tests (1)

If any questions arise, the observing team shouldn’t give any more information, but can take notes or start drafting the documentation they feel is missing.

![Can you run all layers in time? — Photo by Dan Asaki on Unsplash]\
(https://miro.medium.com/v2/resize:fit:1400/0*CtLRlDzE2_nE5qt4 \
"Can you run all layers in time? — Photo by Dan Asaki on Unsplash")\
{: width="50%" }

##  5 min feedback step
At the end of the timer, let's do 5 min feedback between the player and observers about the setup status of the player:

- Did he succeed in applying all the aspects listed?
- Are some steps too complex?
- Are there time-wasting periods that can improve?

Don’t discuss the next steps of the setup during that stage, just focus on what has been achieved or tried during the 15 min.

# Ending
To end the session, let's do a recap on the missing documentation that felt needed to onboard a colleague, a coach or a maintenance team on the project. Probably add some items to your Jira board or team todo-list equivalent.

# Gamification?
If you want to compare several teams and/or make the workshop competitive, you can award one point for each succeeding item on the list, in each round.

##  Example
In round 1, the player has been able to:

- build all codebases → 1 point
- run the automated tests → 1 point

Total: 2 points (out of 8)

In round 2, the player has just been able to:

- build all codebases, now without warning → 1 more points
- run the app on his machine → 2 points
- as before, run the automated tests → no more points

Total: previous total + 3 points → 5 points

##  Optional points
Those previous points were the minimum of the minimum requirements for a dev environment setup, but you could also choose to include the following:

- Is there a recommended and/or supported IDE and plugins like linters and formatters (1 point), including configuration and setup instructions (1 point)?
- Are there expected/supported versions for elements composing the development stack (build tools, language, major framework, …)? (1 point) Is the project built and running with a reasonably recent version of all these elements? (1 point)
- Does the architecture and dev practices documentation (architecture decision records, user manual, …) explain how the application works (1 point), and where to find the living documentation (various tests reports, generated documentation)? (1 point)
- Does the CI/CD related documentation exist? i.e., how are artifacts versioned, and how is the application deployed into the target production environment? (1 point) Can we find a way to get the right to create these new versions, and start a new deployment without help? (1 point)
- Is there any relevant information related to observability (how does the production environment alert the maintenance team, how do the production environments metrics like CPU usage or query response times get collected)? (1 point) Can we find how to be added to the list of notified people, or get access to monitoring dashboards? (1pt)

# Conclusion
I hope you'll find this workshop useful. Or at least that it can be used as a checklist for remembering the various parts of your documentation that will help you to integrate new members, or allow you to transfer projects ownership to other teams with confidence that nothing obvious will go wrong. Who knows? With a bit of luck, you won't have to receive a phone call much later to give an access you've forgotten, or to give details of a component that hasn't been properly documented.

Have fun, and always look for ways to improve your codebase.
