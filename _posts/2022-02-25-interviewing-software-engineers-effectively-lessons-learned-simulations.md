---
title:  "Interviewing Software Engineers Effectively: Lessons Learned with Simulations"
permalink: /blog/interviewing-software-engineers-effectively-lessons-learned-with-simulations.html
published: true
toc: true
toc_label: "Table of Contents"
tags: 
  - software engineering 
  - interviews
summary: "Interviewing for a company is hard. Being the interviewer can be harder. There are so many ways to approach a software engineering interview, from delivering a plethora of technical questions, to simulating team or company-based environments, to whiteboarding engineering problems with a subset of people. Given the wide number of vectors to gauge someone's competency and compatibility with your team, how can you be sure that you're interviewing someone effectively if you employ a simulation?"
---

Over the past handful of years, I've had amazing opportunities to conduct interviews for the teams I've been a part of. We've all been through interviews at some point or another. For people like myself, I get terribly anxious when I'm on either side of the table. As the interviewer, what if I mess up? What if the candidate attempts to correct me on something that is actually invalid? What if the candidate gets flustered or physically uncomfortable during the interview process? This blog details some fables of what I've tried in interviews, what I've learned from my peers, and what I feel has been a decent formula for interviewing a candidate objectively and fairly. 

# Motivation

To be honest, this is a topic I've been meaning to write about for a long time. After reading blog posts from some of my former coworkers about scoring success when interviewing for a management or an engineering position, it made me want to write about the opposite perspective: how to *interview* the interviewee. I'm no expert at this, as I only base this on my personal experience and advice that I've been given by my colleagues. However, I hope that if anything, there's something in here that will help you consider how you interview and how to be more effective at building an amazing team! 

Due to how I can go on for hours about this topic, I want to break them down into segments. This one focuses on my experiences with a simulation within the interview: that is, presenting a programming problem that the candidate must solve in-person. The examples I provide may tend towards the Swift programming language since I'm an iOS engineer, but these principles apply universally to any language.

# All the Simulations

## The Whiteboarding Simulation

Over the past five or so years, I've gone back and forth on whiteboarding questions. It was a long journey for me to figure out whether or not I enjoyed executing them in an interview. I ended up with the following conclusion on simulations:

> Whiteboarding is only effective if you can articulate and demonstrate your goals behind it.

### The Motivation 

Initially, I enjoyed them because I thought of it as a great way to exercise one's technical prowess. This would be the time that someone could really show off their competency and breadth of knowledge. In 2015, I wanted to see just how far someone could go with a question, so I would give what I believed was a simple example. My go-to question was actually a derivative of a question that I was given when I interviewed at Evernote early 2012:

> You are a TA for a math professor at a university and he wants you to create a program that can display a collection of shapes. Before we can create the GUI, we need to come up with a framework to support the creation of two-dimensional shapes. Create a framework that can support the following shapes: Circle, Square, Triangle. Additionally, create methods that can calculate the outline length (i.e. perimeter/circumference) and area of each of these shapes. 

I thought: if they could answer the question, then great! If not, then were they actually a fit for the role they were applying for? After all, this is "Computer Science 101": you've got easy opportunities to demonstrate conforming to a common interface, abstracting those interfaces, inheritance, polymorphism, and/or overriding if we consider additional two-dimensional shapes like a rectangle or parallelogram; encapsulation if we wanted to conceal some of the properties of these shapes, and so on. Surely, these were easy finds.

I mentioned I had this interview question at Evernote when I was on the verge of graduating in 2012. Spoilers: I failed this question, and I failed it hard. I was nervous and had seemingly forgotten everything that I had learned in the past four years. The engineers interviewing me looked like they were dead inside and hardly bothered to help a flailing senior college kid. I kept asking questions like: "So what kind of framework do you want me to make? What language should it be written in?" and they just said "Just make a framework, use a language that works for you."

Yet now, I've decided this was a good question to ask. Why? Deep down, perhaps this was something to personally vindicate myself on to see if this question really was as difficult as it was for me. On paper, this is a simple question, but it becomes _exponentially_ harder in a faux-simulated environment where the time to demonstrate your skills are limited. 

### The Results

Out of all of the times I administered this question, about half of the candidates passed. Of that half, one half had (perhaps subjectively) passable solutions. The problem with my approach was actually quite simple: I was reflecting the ideas and actions that I had taken (which I had mulled over for *days* throughout the course of generating and administering this question), which only meant anything else that didn't fit this bill was invalid in my eyes. Now, there are always going to be inherently incorrect responses to these kinds of questions, and we often identify such responses as red flags (more on this later). However, the root problem I encountered was that I had a difficult time separating my *personal* optimal solution with a *candidate's* optimal solution, and that caused extreme bias in my feedback. 

After thinking about this suboptimal success rate, I discussed with a few engineering leaders about how they conducted interviews on their teams. One in particular abhorred whiteboarding for the reasons I had just reflected on:

> We often have personal bias in our personal solutions to problems we've seen, comprehended, and solved in a much longer time limit than the candidate will *ever* get.

It definitely made me reconsider my approach to this question. This question was personal to me because of my failed experiences at Evernote and the desire to prove to myself that I could solve this optimally. While this question opened up numerous opportunities to talk about rudimentary computer science knowledge, I failed to peel that onion because the candidate's solution's shape (no pun intended...maybe 🙂) didn't resemble mine. And *that* was a huge failure on my end. 

## The Programming Simulation

For a while, I stopped doing whiteboarding questions because I felt they were a bit unnecessary and frankly overused. With the growth of the internet comes a diversity of whiteboard simulations (with solutions attached), so trying to give something overused like an anagram or string reversal question almost felt like giving a free pass in the interview. So instead, we ended up replacing these with a two-hour programming block in hopes that we'd have the candidate be more comfortable with crafting a creative solution.

### The Motivation

For a programming simulation, we thought long and hard on what could be achievable in two hours. When you think of your work day or working on a personal project, two hours feels like a lot of time. Despite this, we wanted to overestimate how long a simulation would take and ensure they had enough time to review their solution and make it better. The problem we ended up choosing was the following:

> Construct an app that connects to a public API provided to you. This API returns a list of items that you should render in a table view. When tapping on a cell in the table view, a detail page should display.

In essence, we made this incredibly open-ended to observe some of the following ideas:

- Familiarity in creating an iOS application
- API familiarity with core networking stacks
- API familiarity with core UI frameworks and navigation behavior
- How the interviewer creates models from an API, and how they get passed over to a view (i.e. single responsibility)
- Bonus: Usage of third-party libraries and/or dependency manager tools, like Cocoapods or Carthage
- Bonus: How creative the interview can be with visuals and attention to detail
- Bonus: Adding tests to verify that the model parsing is valid, translating objects to another is correct, any additional flourish the candidate musters

### The Results 

Spoilers: This also failed drastically. The success rate was even lower! Why was this? We had almost set our candidate up for failure to begin with.

#### Environment Matters

While we had a two-hour block for the candidate to create their solution, we left them isolated in a small meeting room. Because engineers on the team had actual work meetings to attend, it was often difficult to have a two-hour block free for them to sit with the candidate. Thus, we'd encourage them to use Slack to discuss ideas with the team. They hardly utilized it. This was also a horrible decision by both the engineering and recruiting teams in _not_ prioritizing the two-hour window to be left dedicated for this candidate.

When we checked up on the candidate periodically, often times there was little code written due to sheer nervousness. Who would blame them? You're stuck in a room by yourself practically trying to code yourself out. By the end of the simulation, the code would hardly be complete. It took me time to realize that the environment we put the candidate through was indicative of how flawed our interview process was, and we failed to make it a representation of our team culture. 

Or, perhaps more candidly, it was likely *because* a large part of the team culture was focused on individuals working on their own projects, that this kind of environment actually felt normal to us.

## The Take-home Simulation

For those unaware, the take-home simulation is similar to the programming simulation, except that it is done on the candidate's own time instead of in-house. We had tried this briefly at my previous gig, and to save you some reading time: this failed tremendously. We saw the largest drop-off on interview processes at this stage, and the reasons are obvious:

- It requires the candidate to invest their personal time to complete a programming problem.
- The programming problem is generally designed to be more complex due to the free-form nature of time constraints. We often gave guidelines of finishing it within 3-4 hours, but most developers will likely spend more to perfect it to give off a good impression.
- Candidates are likely burnt out or stressed from other interviews they are doing in tandem with yours, and they are going to pick the one that is better worth their time.
- The interview now gives off an impression that the team will judge the code they receive from a candidate they haven't even met in person, which contradicts a collaborative team environment culture.

In my opinion, please consider avoiding this simulation in your interviewing process. 

## The Collaborative Simulation

After thinking back on these problems and talking with some close colleagues, I've bounced back to the idea of utilizing whiteboard questions but in a different way. I've found the most success by applying two principles that I had failed to realize in both situations. My current philosophy is the following:

> Whiteboarding questions should promote a team discussion environment. This discussion can be similar to talking about ideas for a system and/or reviewing a pull request. Considerations and expectations should be outlined explicitly. The goal is to encourage and foster diverse ideas and solutions that fit the problem statement in a collaborative and constructive way, without keeping the candidate feeling isolated.

I *love* using this approach at any level: whether the candidate is experienced at a staff level, or if the candidate is a high school or college student going for an internship. It solves many of the problems the other simulations had and tries to keep the interview as emotionally leveled as possible.

# Designing a Question

I recently did an interview for a candidate and was responsible for designing a question that I felt would be achievable in a thirty-minute window. My question ended up being a simple system design question in iOS that was relatively flexible and open-ended. The goal was to provide an empty canvas to the candidate in [Coderpad](https://coderpad.io/) with some expectations on what this system should do, but that they could design it in the style they'd like.

When I asked for feedback from my peers on this question, one piece of feedback that I now consider as a requirement is to enumerate any and all considerations or expectations. That is: don't assume a candidate is going to do something based on their experience or level. We assume that more experienced candidates might already write their design with SOLID principles, write unit tests to validate their design, ask several questions about any ambiguity in the problem statement, or the different edge cases that could occur. When the candidate doesn't match those internal assumptions however, we're left feeling confused and unsure about a candidate's competency. 

We also need to remember: we're providing a question that the team has likely spent hours thinking on in a cozy, non-time-restraining environment. What we consider simple may be difficult in an environment that the candidate may feel under duress in. 

When designing a question, be up front and explain some ideas the candidate can consider. I struggled on this because I felt like these assumptions felt like really obvious "hints." If we're not direct with some of the expectations though, it's impossible to objectively grade them. One of my friends made this remark about hints/considerations, and it's solidified my belief that they should always be present for the candidate to read:

> "In this case, it's not really a hint because they still need to know what it means and how to do it."

When you outline these ideas, you are letting the candidate know that these are ideas you should try to explore and explain. The candidate's level will shine based on their results and explanations. Some considerations I love to point out include:

- You may utilize any programming APIs and paradigms in the Swift programming language.
- Think about the single responsibility principle when designing this system.
- Think about how you would unit test this system. What about functional test support?
- Think about edge cases: networking, disk I/O, invalid data from the network.
- Please refrain from using third-party libraries.
- Lastly: please ask questions! The requirements are designed to be ambiguous enough so you can have flexibility in your design. Imagine this is a whiteboarding meeting where all of us are collaborating on the design. We're here to answer any questions and to stimulate conversations on ideas you may encounter.

I always make an effort to mention that this is not a simulation where engineers are watching the candidate like a science experiment. This should simulate a real-time discussion as if we all were whiteboarding the idea and providing feedback. I encourage the candidate to ask any kind of question they have, no matter how silly or difficult it is. Giving the candidate a free space to discuss their thoughts, questions, and garner feedback helps them feel more comfortable in an otherwise stressful, anxiety-inducing process.

These are just some ideas that I've been wanting to write for some time. Most of my experiences have given me a ton of lessons learned on how to conduct simulations, and these simulations are going to vary based on team culture and needs. However, providing a comfortable environment for the candidate and being explicit about the requirements and considerations can help bring out the best in both the interviewers and interviewee! After all, we're all simply trying to find teammates that can work well with the team. Why not provide them the environment that can give them the best chance to succeed?

May your interviews be good, and your experiences better. Until next time.

Corey
