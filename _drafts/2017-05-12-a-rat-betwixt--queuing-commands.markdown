---
layout: post
title: "A Rat Betwixt - Pre-beta Progress dump"
date: "2017-05-12 23:20:13 +0200"
author: "Adrian Hedqvist"
categories: game
comments: true
tags:
 - 5SD037
 - BGP
 - Big Game Project
 - Betwixt
---

So alright. Instead of doing a blog post for every large-ish thing I implement I'm going to pretty much dump everything that isn't something minor right here and now, since I've been way too irregular with these blog posts and I've gotta get to writing a bunch of things, so here we go!

### Command Queuing
Earlier, you could only move and then attack once. In that order. This was simple both in what you could do and how you did it but quite limiting - we wanted it to be possible to do more things during a single turn. So that's what I did: I made it possible to enqueue multiple actions during a single turn in any order as long as you had enough action-points for the action.

It was quite a hassle to implement, as I had to redo the way submitting your turn worked. Previously it simply told the server that we wanted to go to the currently selected tile if we didn't already stand on it, and do an attack there if the player wanted to. Instead, we need to store said commands in order in a list, be able to keep adding command to said list until we finally decide to submit and send the full list of commands to the server at once.

That combined with the way we do our networking and how we didn't really store how much action points each action took made it into quite a bit of work. We'd also have to make sure that all the enqueued actions are visible on the map. And then we also needed an undo function that wouldn't leave wierd visual artifacts behind, too.

The end result? It took quite a bit of time but it works pretty well if I can say so myself. The one thing I'd like to complain about is that I don't really want to use that "confirm" button to save every move I want to do each turn - it's quite cumbersome. But I'm afraid that the need to use the undo button would become too much if I decided to simply adding every action to the queue without confirming it first somehow, so this will have to do until we come up with something better.

### Line of Sight
