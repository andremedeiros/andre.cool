---
author: "André Medeiros"
title: "On Development Infrastructure"
date: 2019-11-20T19:27:53-04:00
comments: true
tags:
- development tools
---

When was the last time you've moved a project from development to production where you felt the transition was smooth? How about a project where development infrastructure felt right?

Never? Neither have most of us.

Picture this. You have a cool idea, and a couple of hours to kill, so you open up a terminal and generate a project skeleton. More often than not, the next step has nothing to do with making forward progress on your idea, because you'll have to find and install some form of database, job queue, cache, and other bits and pieces.

After that, you have to get those bits of infrastructure going. Before your code even runs, of course, because it needs those services to be up for it to be able to do anything of use. So every time you want to work on that world-changing idea, you have to open 5 terminal tabs: one for your editor, one for your app server (which you'll probably use last), and the others for those services I've mentioned. And when you're done working? You have to take all of that down.

All good so far? Sure, unless you want true parity with production. Does your package manager make the exact database version you'll be using available? Don't forget the other things you have to think about:

* How easy is it to get that environment going again? Laptops die, get stolen, or sometimes develop a taste for coffee. It's not a matter of if, it's a matter of when.
* Are you exposing ports and services on your laptop that you shouldn't? That Starbucks Wi-Fi you love to use is probably a more significant security risk than you think.
* Does your project consist of several micro-services, each with their database instance? Don't you want to run things that way? Have fun managing different configurations and ports, and figuring out when to launch what.

I know it sounds like I'm beating a dead horse with a stick. That's because I am. The truth is that this is way harder than it should be, although it's been a solved problem in some places.

The most common approach to handling a lot of this is a combination of `scripts/bootstrap` or a `Brewfile,` which aren't the best solution possible for the problem, and a `Procfile` on the root of your project.

A `Procfile` is OK if your application consistently takes longer to start than your database. However, if your application boots faster than the database, it most likely fails to start. And no, I'm not adding a `sleep` instruction before launching the process. What am I, a monster? Having a `while` loop that checks for the port isn't necessarily a solution either, as, for instance, Redis lets clients open connections before the dataset loads fully into memory.

## How do you solve the problem?
One of my favourite things about working at Shopify was `dev,` a tool that takes away some of this pain, and has sensible task sets and defaults for different types of projects. The basic idea is that each project has a manifest that consists of:

* What kind of project it is (Rails, Ruby, Golang)
* What services/infrastructure it needs
* What hosts should be mapped

When you want to work on a project, you run `dev up.` This command figures out what needs to start up and does that. So, just like that, you have a database, a job queue, and a caching layer up and running. And when you're done working? Just run `dev down,` and it stops everything.

It's hard to overstate this: this tool is the one thing I miss about working there. It took all that friction away. Whether you're new to a project or picking up where you left off last Friday, it's effortless to get back up and running.

I miss not having to deal with the specifics of how to run services that revolve around my core idea.  So, in an ideal world scenario, a tool that helps you from inception to deployment needs to handle:

* Different operating systems (pipe dream, but should be built from the ground up assuming this is a scenario)
* Different languages
* Different project types (have to express dependencies with language plugins.)
* Different tasks
* Different services
* Certificate creation
* Different IP addresses per project
* Local DNS entries
* Exporting to different runtime environments (GCP, Heroku, Kubernetes)

So, it needs to be modular. Right. Easy enough. I can even say I want it to work on the OS that I use on my day-to-day, and let outside contributors bring in the rest. It also needs to be inclusive at its core, so having any outside dependencies that might not run on what you have right now a non-starter. With those constraints, a useful MVP needs to support:

* macOS
* Rails, Ruby, Golang, Node.JS, React Native
* Postgres, RabbitMQ, Memcached
* Customizable tasks (a-la Makefile)
* Different IP addresses per project
* Local DNS entries
* Exporting to different runtime environments (GCP, Heroku)

I've been thinking about the specifics, and here are some thoughts.

### Languages and versions
It's OK for the tool to have some dependencies, so I consider this problem mostly solved. Specifically, there are lots of tools out there that address this problem, my favourite being `ASDF.` Add a small shell hook and interface with this tool.

### Running services
One of the things I'm trying to avoid depending on is Docker. The filesystem layer has terrible performance on macOS, and if you have Windows, you need to run Professional at least, otherwise, Docker won't be supported. I would also hate to rely on a specific Virtual Machine Host software, as I usually prefer to have 0-1 of those installed, and not all of those providers give you ways to interact with the software programmatically (I'm looking at you, VMWare.) So my conclusion here is that it needs to run on the host machine, which is probably the most efficient way of doing it.

Luckily, there's Nix. If you have never heard about it, the TL;DR is that it is a package manager that 1) is available for all major operating systems, 2) offers reproducible builds and 3) different versions of the same software installed side by side. Need Postgres 12.1 for one project and 11.6 for another? No problem.

We've successfully integrated it into the biggest project we have at Status, and it made setting up my development environment incredibly easy.

Also, a nice extra benefit is that most provider plugins should have a simple implementation, where they add a nix package/version pair to a manifest, a simple readiness/health check, and that's it.

### Local DNS entries / IP address allocation
I'll admit, I haven't thought about this one past macOS, but I'm positive it's manageable on other OSs.

macOS has a feature where you can create an IP alias. On the surface, this creates a new address for an existing network interface, allowing devices to talk to that network interface using the new address. The software picks a subnet that will most likely never get used, creates aliases on that subnet pointing to the loopback device, and we're off to the races. Also, because each project has a uniquely assigned IP address, you can use standard ports on your services because they bind to their specific project's address. Not only that, but the tools already have most of the correct connection parameters in their defaults, but your muscle memory will always be right too.

The only downside is that this requires `sudo` access, which, in a perfect world, I wouldn't rely on, but it's something I'm willing to accept.

Also, if I'm willing to accept `sudo` access, why not insert a local instance of `dnsmasq` when starting the first project, or installing the tool? It can handle the local DNS nicely, and it barely takes up any resources. That, or manipulating `/etc/hosts.`

Generating IPs should be simple enough too. Have the tool use its database, say `~/.config/tool/projects.json,` where it remembers projects' IP addresses by their absolute paths. Deciding on a new IP address is always `<count of existing entries> + 1`.

### Exporting to Runtime / Production environment
Here's the cherry on top of the cake.

You've finished your MVP. The corners are smoothed out, and there are mostly no edges where anyone can get hurt. You run the export command, and the tool generates a Terraform configuration for Heroku. You deploy, and you're the hero of your own story.

## What now?
Maybe I'm naive. Perhaps it's not as simple as I think it is. But I want to give this a shot. After all, that's the best part about being an engineer: you can imagine a world around you, and build it. I'll probably stream some of it too, so come hang out and nerd out with me if you see me on.

Lastly, I'm surprised that this hasn't yet been "solved." Companies like Heroku have a real chance at differentiation here, where they could offer a polished developer tool and make it painless to bring your app into production with them.
