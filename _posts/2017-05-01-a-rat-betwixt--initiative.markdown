---
layout: post
title: "A Rat Betwixt - Initiative"
date: "2017-05-01 15:27:37 +0200"
author: "Adrian Hedqvist"
categories: game
comments: true
tags:
 - 5SD037
 - BGP
 - Big Game Project
 - Betwixt
---

The turn order of all enemies and players are determined by their initiative. This is pretty much how things have been done in turn-based RPG's since forever in some form. We could've determined a characters initiative with rolling a dice, add a base value and call it a day, but we thought that it wasn't interesting enough. And we'd like to avoid pure dice rolls if possible.

![The initiative cards menu. It's still all placeholder art.](http://i.imgur.com/50wqeI2.png)

At the start of the planning phase of each turn, the player is shown a menu that lets them choose between up to five initiative values - the one they choose is consumed when they submit their turn and in the next turn they'll have to choose one of the remaining ones. When the player runs out of initiative "cards" the player is given five new ones. This is great as it makes a previously pure RNG part of the game into an interesting and important choice - do I want to move first or last? Do I save this high initiative card so I can run away if I get into a bad spot? They can't ignore whatever low cards they get either since they're not getting any new ones until they've used all of them, so it's in their best interest to use low-value cards if they can afford it just to get rid of them.

The current algorithm (as of this post) to generate the initiative card values looks like this:

```cs
// Character.cs
// function Character.newTurn()
// ...
if (initiativeCards.Count == 0)
{
    int initiativePool = startPool; // The sum of all the cards initiative values will equal this

    while (initiativePool > 0 && initiativeCards.Count < cards)
    {
        if (initiativeCards.Count < cards - 1)
        {
            int initiative = Random.Range(1, initiativePool / 2);
            initiativePool -= initiative;
            initiativeCards.Add(initiative);
        }
        else
        {
            // Only one card left, let's just use what's left in the pool
            initiativeCards.Add(pool);
            pool = 0;
        }
    }
}
```

I don't think the algorithm is perfect, as it produces some very visible quirks when you look at multiple sets of generated numbers. The numbers tend to essentually look like a downward slope with a large spike at the end. We don't think it will have any large impact on the gameplay though, so we will be using it for now. And if the visible patterns for some reason becomes an issue you could put it under the rug by shuffling the list before giving it to the player just to hide the pattern. It's not a real solution to the issue but oh well, we'll see if it becomes an problem.
