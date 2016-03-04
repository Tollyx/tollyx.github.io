---
layout: post
title: "The Minimap"
date: "2016-03-03 21:18:48 +0100"
author: "Adrian Hedqvist"
categories: game
comments: true
tags:
 - 5SD033
---

When you have a playing field that's large enough so the screen barely covers 1/5th of the map, you're going to have issues with knowing where the enemies, obstacles or what you now have is coming from. When you're also limited in how fast you can check all directions, it's just going to be an frustrating experience unless you have some other way to see where the enemies are coming from.

The most obvious and straight-forward to this is a minimap. It has been used in a huge amount of games in a wide variety of genres so it's pretty safe to say that it works. And that's why we decided to implement one. It's not a huge of an issue in the first few waves in the game, but as you progress and unlock more sectors to defend it can be really hard to defend the planet since you have no idea where the aliens are coming from. (Unless you're the guy (I'm that guy) who made the waves that is)

![Green dot: You. Red dots: Them.](http://i.imgur.com/H0CEFDs.png)

The way it works is pretty simple, since we know that `(0, 0)` is the center of the planet and we don't need anything more than the players and enemies position.

First off, I tried getting the entire enemy vector and iterate through it to get all the enemies positions, but I realised pretty quickly that it's a pretty bad idea to loop through all the enemies yet another time each frame when we're already doing it once.

The way I ended up doing it, all entities can simply "register" themselves on the minimap whenever they are updated. When they do, they provide their own position and the desired color on their dot on the minimap. How we get their position translated to the proper position in the minimap is the trickiest part, and even then it's not that hard.

```cpp
void HeadsUpDisplay::addRadarDot(sf::Vector2f pos, sf::Color color)
{
  sf::Sprite dot;
  dot.setTexture(radarTexture);
  dot.setTextureRect(sf::IntRect(416, 225, 23, 23));

  sf::FloatRect rect = dot.getLocalBounds();
  dot.setOrigin(rect.width / 2, rect.height / 2);
  dot.setColor(color);
  dot.setScale(map.getScale().x * 2, map.getScale().y * 2);

  sf::Transform transform;
  transform.scale(map.getScale().x * 0.07, map.getScale().y * 0.07);
  transform.rotate(map.getRotation() - 45);

  pos = transform.transformPoint(pos);
  pos.x += map.getPosition().x;
  pos.y += map.getPosition().y;

  dot.setPosition(pos);
  radarDots.push_back(dot);
}
```

Most of the code is pretty self-explanatory, the function is called in the enemy manager for each active ship in the same loop that they are updated in. The scale-multiplication is there so their position will always be relative to the actual maps scale so we won't have to deal with changing multiple variables if we want to change the size of the map.

And a note on the transform: I apparently don't understand transforms 100% correctly, since *in my head*, it should've worked with simply calling `map.getTransform()`, scale it down and then use `.transformPoint(pos)` from it. I tried that and it didn't work. I still haven't figured out why and it's annoying me quite a bit.

But oh well, what I have now works and I'm happy with the result. So the time investigating transforms will have to wait, since I currently have some more important stuff I have to finish before the beta deadline (!!!) next monday.
