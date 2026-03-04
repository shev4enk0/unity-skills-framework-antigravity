---
name: 3d-games
description: "3D game development: character controllers, NavMesh AI, LOD, lighting, shaders, 3D camera systems, Cinemachine, physics."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# 3D Games

3D game development patterns. Character controllers, AI, LOD, lighting, and camera.

## Instructions

### Character Controller

Use CharacterController for player movement, not Rigidbody.
Use Rigidbody only for physics-driven objects such as crates and ragdolls.

### 3D Camera

Use Cinemachine FreeLook for third-person camera.
Use CinemachineBrain on main camera to blend between virtual cameras.

### NavMesh AI

Bake NavMesh via Window > AI > Navigation > Bake.
Use NavMeshAgent on enemy. Set agent.SetDestination to target.position.
Use NavMeshObstacle with Carve on dynamic blockers.

### LOD

Add LODGroup to complex meshes.
LOD0 full detail, LOD1 medium poly, LOD2 low poly, Culled beyond max distance.

### Lighting

Baked: best performance for static scenes.
Mixed: baked indirect plus real-time direct shadows.
Mark non-moving objects as Static. Use Light Probes for dynamic objects.

### Common Pitfalls

Gimbal lock: use Quaternion.Slerp not Euler interpolation.
Physics jitter: set Rigidbody interpolation to Interpolate.
Shadow acne: adjust shadow bias on directional light.
LOD pop-in: use cross-fade LOD mode.