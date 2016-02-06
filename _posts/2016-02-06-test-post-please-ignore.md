---
layout: post
title: "Test post please ignore"
categories: school
tags:
 - 5SD033
comments: true
date:   2016-02-06 16:15:36 +0100
---

Test post please ignore.

Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

Here, have some recursive shadowcasting:

```cpp
void TileMap::CastLight(int row, float start, float end, int xx, int xy, int yx, int yy, int startX, int startY, float radius)
{
  float newStart = 0.0f;
  if (start < end)
  {
    return;
  }
  bool blocked = false;
  for (int distance = row; distance <= radius && !blocked; distance++)
  {
    int deltaY = -distance;
    for (int deltaX = -distance; deltaX <= 0; deltaX++)
    {
      int currentX = startX + deltaX * xx + deltaY * xy;
      int currentY = startY + deltaX * yx + deltaY * yy;
      float leftSlope = (deltaX - 0.5f) / (deltaY + 0.5f);
      float rightSlope = (deltaX + 0.5f) / (deltaY - 0.5f);

      if (!(currentX >= 0 && currentY >= 0 && currentX < GetWidth() && currentY < GetHeight()) || start < rightSlope)
      {
        continue;
      }
      else if (end > leftSlope)
      {
        break;
      }

      if (sqrt(deltaX*deltaX + deltaY*deltaY) <= radius)
      {
        SetVisible(currentX, currentY, true);
      }

      if (blocked)
      {
        if (GetTile(currentX, currentY).isOpaque)
        {
          newStart = rightSlope;
          continue;
        }
        else
        {
          blocked = false;
          start = newStart;
        }
      }
      else
      {
        if (GetTile(currentX, currentY).isOpaque && distance < radius)
        {
          blocked = true;
          CastLight(distance + 1, start, leftSlope, xx, xy, yx, yy, startX, startY, radius);
          newStart = rightSlope;
        }
      }
    }
  }
}
```
