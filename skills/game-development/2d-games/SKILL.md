---
name: 2d-games
description: "2D game development: tilemaps, sprite animation, 2D physics, parallax scrolling, 2D camera follow."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# 2D Games

2D game development patterns. Tilemaps, sprites, physics, camera.

## Instructions

### Tilemap Setup

Separate tilemaps per layer: Ground, Walls, Decorations.
Use Tilemap Collider 2D plus Composite Collider 2D for efficient ground collision.
Rule Tiles auto-select sprite based on neighbors.

### 2D Character Controller

Use Rigidbody2D velocity for horizontal movement.
Use AddForce Impulse for jump when grounded.

### Parallax Scrolling

Track camera delta each LateUpdate.
Move layer by delta multiplied by parallaxFactor (0 = fixed, 1 = moves with camera).

### 2D Camera

Use Cinemachine 2D Virtual Camera with Confiner 2D for level bounds.
Set Body Damping for smooth follow.

### Common Pitfalls

Jittery camera: use Cinemachine or SmoothDamp.
Physics gaps in tilemap: use Composite Collider 2D.
Character sticks to walls: use Physics Material 2D with zero friction.
Sprite Z-fighting: set Order in Layer and Sorting Layer correctly.