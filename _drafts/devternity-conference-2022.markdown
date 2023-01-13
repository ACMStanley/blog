---
title: DevTernity conference 2022
date: 2023-01-13 14:28:00 Z
author: rwilliams
---

Late last year I had the chance to attend [DevTernity](https://devternity.com), an all-remote generalist software development conference. The first day was the main conference day, with the second (optional) day offering a choice of workshops by the speakers. It was a great conference. In this post I'll cover off some points of interest from the talks I chose to attend, and reflect on the remote conference experience.

# Improving eBay's Development Velocity

Randy Shoup told the story of how at eBay they stepped up to double their engineering productivity, starting from a base of 27 years' worth of grown IT estate and ways of working. They chose to start by focusing on software development and delivery/deployment, as an enabler for possibly later improving other aspects of building and operating software.  Billed as a "velocity initiative", the programme applied aspects of team topologies (platform teams, enabling teams, senior individual contributors) to bring cohorts of feature/product teams into the fold every quarter. Much of the work backlog could be found by asking the question "if you had to deploy every day, why can't you?". Noting that transformation is hard, he used the term "immune system" to describe how a company/organisation naturally and unintentionally tends to fight/resist change.

# Simple and Powerful Things That Work for Me

Jakub Nabrdalik shared some tips about thinking and doing within software development, grouped by before coding, while coding, and after the coding. While some of these would probably be picked up over years of working in software, it was great to see them identified and shared. One that sticks with me is his approaches for implementing something when you can't see far ahead enough to know what the end result looks like, i.e. how not to have coder's block when you're out of the comfort zone of your knowledge and familiar patterns. These were "baby steps", "backward programming", "top-down and layer on the detail", and "learning by chaos". He observes that once you know what you want, it's quick to rewrite rough code that helped you figure that out. Another tip he had was on observability - that if you can't reason about your code or failing tests via logs alone (i.e. you use a debugger), then you won't be able to reason about what's happening in production either.

# The Secrets of the Fastest Java Developers on Earth

Victor Rentea demonstrated his arsenal of tips and tricks for IntelliJ IDEA. He stresses the importance of having unconscious competence of the tool, so menial tasks don't distract or slow down our thinking. This talk conflicted with another I wanted to attend on another track, so I watched this one later while trying things out in my own IDE - which worked well. There was a lot to learn from this one even for seasoned users, covering: typing less, selecting code, inspections, refactoring, and navigation.

# Clean Code: Eternal Principles

Jakub Pilimon argued and demonstrated that clean code is a derivative of the fundamentals of having a clean architecture and a clean model. Traditional clean-code thinking (smaller functions, good naming, etc.) don't help us much if we haven't modelled our domain well, he observes. His demonstration of implementing scooter hire program then throwing new requirements at it showed how coupling, poor testability, repetition, lack of layering, and high cognitive load make things harder and dirtier than they ought to be. Complication, he notes, is often seen as or argued to be a bad thing - but ultimately it leads to proper abstractions and simpler code.

# Everything You Should Know About Web Development in 2022

Stefan Judis took us through the highlights of what's new in the web space this year. These included CSS is/where/has, CSS container queries, view transitions, ECMAScript type annotations, and tooling written in Go/Rust. With the constant stream of new things in frontend technology, it was useful to have a recap of what the real big things were this year on the web platform itself. He closes with the thought that knowing some of the web fundamentals could become less important for application/website developers in the future, with frameworks and toolkits taking care of ever more concerns for us.

# The 7 Pillar Developer: A Holistic Approach to Building an Exceptional Career

Cory House gave a profound and thought-provoking talk (while walking on his treadmill) about what underpins an exceptional developer. We'd all do well to take (or consider) one or two things from each pillar, even if the whole structure looks like a daunting list of things we could be doing. Starting with **psychology**, he stresses the importance of taking care of yourself and the advantage that happiness brings. He's quite strongly of the view that **focus** is important in a growing and maturing industry; being a remarkable and wanted individual by being exceptional at something - rather than being just good at a bunch of things. He shares some things we can do for our **education** to remain relevant, our **image** and reputation to increase our "luck surface area", and our **communication** skills to be effective as an expert. On the subject of **time**, he discusses the idea of using money to buy time and mental bandwidth, and thinking through the consequences of where we spend our time. The final pillar is **systems**, which he favours over goals.

# Practical Leadership for Software Developers

David Neal gave this quite reflective talk on how we're all leaders and what behaviours we can take to be better at doing that. He argues that leadership isn't about management or control; rather anyone whose actions inspire others to dream more, learn more, do more, and become more - is a leader. With his hand-drawn slides (he's a talented illustrator), he walks us through 7 things we can do (such as managing time, taking responsibility, having gratitude, and encouraging others) , with some personal examples of him doing (or not doing) these in the past.

# Clean Architecture – A Craftsman's Guide to Software Structure

# Unlocking the Awesome Power of Refactoring

# Building Careers with Empathy

## 26 Heuristics for Effective Software Development