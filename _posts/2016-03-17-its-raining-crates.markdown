---
layout: post
title: "It's Raining Crates"
date: "2016-03-17 23:04:08 +0100"
author: "Adrian Hedqvist"
categories: game
comments: true
tags:
 - 5SD033
---

Oh wow, what a week. Tomorrow is the deadline and I've gotten a lot of things done these last few days. It was pretty hard to decide what to write about!

At first, our powerups were dropped by the enemies when they explode. It didn't make much sense because they're not trying to shoot anyone down, they're just trying to land on your planet. So we switched it up: The powerups now comes in crates (Worms-style), sent by whatever company you're working for so you can keep doing your job.

![I hope I'll get a holy hand grenade. Or a donkey. The donkey is pretty awesome too.](http://i.imgur.com/ybqgumd.png)

The way powerups is currently implemented is using two classes: the crate and the actual powerup. I could've mashed them together into a single class and I don't really have a good reason as to why I didn't. But I did manage to add the crates this way without making any major modifications to the old powerup code which is at least something.

```cpp
// Crate.cpp

void Crate::update(float dt)
{
  if (!opened)
  {
    // If the crate has landed
    if (radiusPos <= planetRadius)
    {
      radiusPos = planetRadius;
      opened = true;
    }
    else
    {
      radiusPos -= 100 * dt; // Descend
    }

    // Convert angle and radius to x and y coordinates
    float angleRadians = (anglePos - 90) * M_PI / 180;
    setPosition(cos(angleRadians) * radiusPos, sin(angleRadians) * radiusPos);
    setRotation(anglePos);

    // Update the sprite positions
    fallAnimation.setPosition(getPosition());
    fallAnimation.setRotation(anglePos);
    landAnimation.setPosition(getPosition());
    landAnimation.setRotation(anglePos);

    fallAnimation.updateAnimation(dt);
  }
  else
  {
    // Play the non-looping landing animation
    landAnimation.updateAnimation(dt);

    // Despawn after 10 seconds
    despawnTimer -= dt;
    if (despawnTimer <= 0)
    {
      destroy();
    }
  }
}
```

When a crate is spawned, all it does is descend until it hits the planets surface while playing this nice swaying animation. (If you're wondering what kind of magic we're using for positions, [I've written about it here.]({% post_url 2016-02-11-moving-in-circles %})) When it hits the ground, it flicks a switch and starts to play the landing animation and starts counting down until it despawns.

The spawning of the powerup is handled by the powerupmanager:

```cpp
// PowerUpManager.cpp

void PowerUpManager::Update(float deltaTime)
{
  {
    // Update all the powerups, erase them if they're despawned
  }
  {
    auto it = crates.begin();
    while (it != crates.end())
    {
      if ((*it)->isOpened())
      {
        // Crate is already opened
        (*it)->update(deltaTime);
      }
      else
      {
        // Crate isn't opened
        (*it)->update(deltaTime);
        if ((*it)->isOpened()) // If it's opened after updating it
        {
          // Spawn a new powerup at the crates position
          powerups.push_back(new Powerup(&powerupTexture, (*it)->getPowerupType(), (*it)->getAnglePos(), (*it)->getRadiusPos()));
        }
      }
      // Add the crate to the minimap
      miniMap->addDot((*it)->getPosition(), sf::Color(55, 100, 200, 200));

      // Erase the crate if it's despawned
      if ((*it)->isDestroyed())
      {
        delete (*it);
        it = crates.erase(it);
      }
      else
      {
        ++it;
      }
    }
  }
}
```

Before updating a crate, it checks if the crate is already opened. If it is, then it simply updates it, adds it to the minimap and continues to the next crate. But if the crate is unopened before updating it, it checks once more if it's opened after it has updated the crate. This way we know that the crate was opened this frame so we can spawn a new powerup without worrying about spawning duplicates.

Last but not least, how I spawn the crates in the first place. I won't paste a large code snippet of it since I've pasted two pretty large ones already and that one has even more unrelated code in it since it's baked into the enemy wave code. The waves are quite simple. At the beginning of each wave we fill a vector with a bunch of `Spawn` structs that contains position, enemy/powerup type and a spawntime. We then iterate it through each frame, spawning enemies/crates whenever our clock passes their spawntime.

The only reason why I put the spawning logic together with the enemy waves was because it was the simplest solution I could come up with, even if it isn't a very good-looking solution. I also could've put the spawns on some timer, but then I'd need to know which sectors that are unlocked and make a bunch of checks for that. In the enemy wave code all that is already handled and there already was a reference to the powerup manager there from the previous powerup spawning system, so I didn't have to change much at all to get that working. I probably would've thought up a proper solution to this if I wasn't running out of time.

And that's pretty much it! I haven't explained what or how the actual powerups work, but the other programmers in my group has probably already written about it, you can find [Karl over here](https://karlmalmgamedev.wordpress.com/) and [Arash  over here](https://arashaffrabakhtiari.wordpress.com/). This is also my last post about Planet Suburbia, because the final version is supposed to be turned in tomorrow! So see you next time, I have no idea what I'll write here in the future.

PS: Sorry for the huge code snippets!
