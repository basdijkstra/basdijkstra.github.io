---
title: Case B
layout: page
permalink: /cases/b/
---
**The organization**

This case takes place at a semi-governmental organization. Having long been a traditional project-based organization, they’ve started to adopt Agile and Scrum principles about a year ago.

**The team and the product**

There are several development teams that all work on different applications in the IT ecosystem of this organization. These applications are not operating independently, since they share data and transactions by means of an Enterprise Service Bus (ESB). All teams consist of at least two developers, at least one tester, a business analyst and a scrum master. Most teams work on premise, but part of the software development and associated testing activities are nearshored to another country.

**The testing process**

Development and testing for the features where the scope is limited to the team itself is largely going fine. Developers develop, testers test and not a whole lot goes wrong. It’s the features and bug fixes that cross the boundaries of the team that create problems. Each development team has limited knowledge on what happens when they change a message or a process that impacts other systems, because these are developed in other teams.

At some point, there has been a team (the ‘ESB testing team’) that was responsible for testing across the entire IT ecosystem, but a year ago management decided that each team should from then on be responsible for testing the impact of the changes and features they developed.

**Automation challenges**

For testing the changes across the entire IT ecosystem, an automated test suite exists (a remnant from the time when the ESB testing team still existed). There’s one test automation engineer that dutifully maintains it. It does only cover those parts of the processes and the ecosystem that they know about, though, meaning that coverage is still rather limited.

The organization really wants to focus more on integration and end-to-end process testing (tests that traverse the ESB), because the potential impact and damage due to bugs can be high: there’s a lot of money involved with the day-to-day business of this organization. However, lack of time and lack of knowledge about the entire ecosystem is working against them.
