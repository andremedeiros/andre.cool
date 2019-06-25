---
author: "André Medeiros"
title: "Introducing: Shitshow Sidekick"
date: 2019-06-24T21:40:10-04:00
comments: true
tags:
- shitshow sidekick
---

Computers are just sand we convinced to do math, so it's no wonder they fail all the time. Sometimes, even, they'll fail while you're holding a pager.

If your experience of holding one is anything like mine, it'll always be at the worst possible time. It'll be at 3 am on a random Thursday or right as you sat down to have lunch on the one day you left your computer at your desk. On the best of days, this is incredibly stressful -- you're expected to deal with unknowns and deal with them fast.

In a high-pressure situation like that, it would be nice to have some help.

Whenever I deal with an incident, I try to do three things, in this order:

### Opening channels of communication
First and foremost, before you start opening SSH sessions and connecting to VPNs, you have to communicate. The chances are that what paged you might have a ripple effect on other systems and other people are probably looking to fix their side of things too. It doesn't have to be super fancy either. A simple "Hey, I got the page and am on it. Will update as I know more." is quite useful.

### Being transparent about what you're trying
The next thing is to think out loud. I treat Slack as a stream of thought whenever I'm resolving an incident. Communicating my thoughts gives other people the chance to course correct me if I'm going the wrong way, pitch in with some ideas, but most importantly, it can be a valuable teaching tool, as folks who either don't do operations or have just started carrying a pager can take some lessons from that.

### Report what happened
Finally, after it's all said and fixed, and your systems are back to healthy, you want to grab all the relevant facts, such as any infrastructure graphs, parties involved in the incident resolution and conversations, into an incident report and record it somewhere for later analysis.

## But this sucks
I don't remember the last time I felt like scouring through channels and threads in Slack to gather all the relevant details on an incident, especially having been the one dealing with another page. I don't ever recall anyone setting up a Google Hangouts call so people could coordinate efforts either. Communication tends to happen sort of organically, and while that is mostly good enough, I wouldn't consider it to be great.

Not only that, but there's this concept of playbooks, which are, in a nutshell,  step by step documents on how to deal with known or expected failures. If you've never heard of them, I believe you. If you tell me yours are amazing, and that they're always up to date, I'll have a hard time taking you seriously. Playbooks are another part of incident response that tends to grow organically and without much structure. Even when they provide commands, sometimes the operator is left with filling in placeholders where a hostname, port, or path should go. Sometimes, even, in the middle of the command, as I've seen in many situations. Not ideal.

I'm here to tell you there's a better way.

I started by calling it an Incident Assistant, as various people dropped by [my Twitch channel](https://twitch.tv/superdealloc) asking what I was working on, and I needed a quick catch phrase. [Mark Imbriaco](https://twitter.com/markimbriaco), whom I'm proud to say is one of my business partners in this small venture, brilliantly coined it a Shitshow Assistant. That name fits the use so much better, so I'm sticking with it.

## How can we do better?
Picture this. It's 4 am. You have a Datadog alert that fires. One of your MySQL hosts is experiencing higher than average replication delay. You stumble out of bed and drag yourself to your computer. As you open Slack, you notice a couple of things:

1. On your team's Slack channel, a bot has already created a Zoom meeting. All you have to do is click and join. It also posted that meeting URL to other teams' channels so they can pitch in.
2. Below that, the MySQL Replication playbook is made available for you. A couple of quick diagnostic commands and rich message options that make navigating the knowledge base a breeze. As you're navigating the playbook, the bot remembers your choices.
3. Have I mentioned the shitty command templates? These playbooks can pre-fill the exact commands you need to run because Datadog fed them the information they need. All done via a simple templating language and some JSON fishing. In 30 seconds, you're able to check the state of the secure tunnel between hosting sites, what the current query volume is, and some basic IO statistics.
4. 30 minutes into the incident, a private conversation pops up on Slack. The bot reminds you to hydrate.
5. Finally, when your system is back to healthy, it'll take all the conversation that happened in the channel, all the relevant graphs, and decisions you've made along the way, and it'll create a GitHub issue for you to track the incident and run an RCA with later.

Notice how all you had to do was navigate the knowledge base and deal with the actual incident — pure bliss.

This experience is what I want to deliver with the Shitshow Sidekick. A lot of it is still up in the air, but the concept is, through boxes, arrows, and simple templating, anyone should be able to create flows that make incident responder's lives a lot easier.

I'll be posting more as I finish and polish features. There's a plan written down for an MVP, and finally, I broadcast all development on [my Twitch channel](https://twitch.tv/superdealloc), so come and say hi sometime!
