---
layout: post
title: "Aimbotting for turrets"
date: "2016-02-18 23:22 +0100"
author: "Adrian Hedqvist"
categories: game
comments: true
tags:
 - 5SD033
---

It's pretty hard to defend a planet if you're alone. I mean, you're just this tiny guy on this huge planet and aliens are attacking from all over the place. You simply won't be able to defend the entire planet at once. The solution? Turrets!

<video autoplay loop>
  <source src="https://λ.pw/evQ.mp4" type="video/mp4">
</video>

These wasn't that hard to implement since we're placing the turret at the players position [that we've already calculated]({% post_url 2016-02-11-moving-in-circles %}) and reuse the aiming and shooting code that we have for the player! But alright, let's slow down, here's how it works:

In order to get the turrets to shoot at something, we need _something_ to aim at. For the player we had the mouse and for the turret, all we have to do is replace the mouse with an enemy target. Preferrably the enemy closest to the turret and within a specified range from the turret. For that, I created this very specific function inside our enemy manager:

```cpp
// EnemyManager.cpp
// ...

EnemyShip* EnemyManager::GetClosestShipInRange(sf::Vector2f turretPos, float range)
{
  EnemyShip* closestShip = nullptr;
  float closestDist = range;
  auto it = vecPtrEnemyShip.begin();
  while (it != vecPtrEnemyShip.end())
  {
    sf::Vector2f deltaPos = turretPos - (*it)->getPosition();
    float distance = sqrt(deltaPos.x * deltaPos.x + deltaPos.y * deltaPos.y);

    if (closestDist >= distance)
    {
       closestDist = distance;
       closestShip = (*it);
    }
    ++it;
  }
  return closest;
}
```

It iterates over all the enemy ships and use the [Pythagorean theorem](https://en.wikipedia.org/wiki/Pythagorean_theorem) in order to get the distance between the turret and the ships. While iterating, it'll save the ship to a seperate variable if its distance from the turret is less than whatever that's in `closestDist`, which starts out at the max range provided as an argument, effectively ignoring all ships outside the range. When it has iterated through all the ship it will simply return the ship that's currently saved. If there's no ship within the range it will simply return `nullptr`.

Now all we have to do is to get the turret to aim at the enemy that we get from that function and pull the trigger. As previously mentioned, if you know how to make the player to aim at the mouse, you probably already know how to do this.

```cpp
void Turret::Update(float dt)
{
  if (target != nullptr)
  {
    float deltaX = cannonSprite.getPosition().x - target->getPosition().x;
    float deltaY = cannonSprite.getPosition().y - target->getPosition().y;
    float angle = std::atan2(deltaY, deltaX) * 180 / M_PI;
    cannonSprite.setRotation(angle);
    Fire();
  }
}
```

We use some [mathemagical trigonometrics](https://en.wikipedia.org/wiki/Atan2) to figure out the angle we need to aim at in order to hit our target and convert it from radians to degrees. Then we simply aim our cannon and pull the trigger.

While the aiming as it is now would be perfect if the cannons projectiles were [raycasts](https://en.wikipedia.org/wiki/Ray_casting), our projectiles aren't. This means that as our target gets further away and goes faster, we're pretty likely to miss. This could be partially solved by keeping the targets velocity in mind when aiming so that we aim in front of it instead of straight at it, but that's an improvement for a later date.
