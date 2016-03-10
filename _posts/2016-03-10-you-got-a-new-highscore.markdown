---
layout: post
title: "You got a new Highscore!"
date: "2016-03-10 23:00:48 +0100"
author: "Adrian Hedqvist"
categories: game
comments: true
tags:
 - 5SD033
---

This week I finished our highscore system, but when I say finished I actually mean "we finally got this thing working properly don't touch it". We've actually been working on the highscore system on and off for a few weeks now and it didn't work nicely until recently. And we still need to work a bit on the UI. Why it took so long is because we had a bunch of small problems that together caused a pretty large mess.

![We still need to work on that UI.](http://i.imgur.com/06XxDVS.png)

Let me list off the issues that it still had when I started working on it this week:

1. The text you input was all garbled garbage.
2. The UI scaled incorrectly between fullscreen and windowed.
3. The list saved and loaded properly, but the UI didn't update correctly right after entering a new highscore.
4. There was no title to the menu
5. There was no indication that you had a new highscore and could enter your name

Out of those five I've fixed the first three. I'm going to work on the last two tomorrow.

As for the first, it was a really stupid mistake that took way too long to discover: We were using `KeyPressedEvent`s and not `TextInputEvent`s. I think the reason why we did that was simply because we thought we were using TextInput when we were'nt. The `sf::Event` class that has all the event types baked into it no matter what event type it actually is doesn't help either. If we just had checked if the event that we were using actually were a TextInputEvent we would've saved so much trouble.

The second one was a simple fix, instead of placing and scaling things statically, do everything in relation to the window size. Something like this:  
`highscore.setPosition(window.getSize().x * 0.3, window.getSize().y * 0.2);`

The third was a bit more tricky. The names and score names were only stored in two arrays (one for names, one for scores) with `sf::Text` objects. This made it pretty hard to simply insert a new highscore and push the others down a step. I solved it by adding a vector with name-score structs. When I add a new score I insert it at its position in the vector, erase the now 11th score in the list and then update the arrays with `sf::Text`s so they match the highscore vector. I'll have to redo it if I want the text entry to be at the position you get in the list, but I'll look for other solutions before remaking anything.
