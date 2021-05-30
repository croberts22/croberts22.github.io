---
layout: default
title:  "Using Combine's `Future` in Existing Asynchronous Code"
author: "Corey Roberts"
permalink: /blog/using-combines-future-in-existing-asynchronous-code.html
published: false
summary: "Ever since SwiftUI's arrival a few years ago, functional reactive programming has become a more vibrant topic in the Apple development world. As someone who has little experience with FRP, I started digging into the world of Combine to see what all the hype was about. I've recently learned how to adapt existing asynchronous code to feel more FRP-esque. The `Future` of Swift is now! 🤓"
---

Reactive programming is truly a fascinating shift in paradigms and traditional conventions. As a concept that didn't garner first-party support until recently, I was actually a strong opposer of technologies like RxSwift. I never really enjoyed the idea of having such a large dependency for language semantics. Furthermore, given the communities frustration with the early Swift language migrations, that made me even _more_ hesitant to adopt something like Rx. If even Apple was causing problems for yearly language migrations, I couldn't imagine what trying to update a library would look like!

When SwiftUI and Combine were introduced, it made me realize a few things. One, Apple recognized a familiar paradigm in software development and realized an opportunity to marry it with the ideas of SwiftUI. Two, I was entirely ignorant of functional reactive programming wholly because I only considered the architectural and system sustainability implications. After reading up on Combine and some of its use cases and the problems it solves, I became pretty annoyed at myself for not looking into FRP earlier. I immediately thought of all the frustrations I had with asynchronous code, particularly any situations where I had boomerangs of network responses.

The good news is that there's never been a better time to learn Combine! While I'm sure there are going to be some incredible updates to come when WWDC 21 occurs, I figured I would help cement my knowledge (and accuracy thereof) by writing some small blog posts of ideas I've come across. 

Today's blog post is about how you can adapt `Future` with any existing asynchronous closures for use in Combine APIs.

## `Promise` & `Future`


