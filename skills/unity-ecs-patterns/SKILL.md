---
name: unity-ecs-patterns
description: "Unity DOTS/ECS patterns with Burst Compiler and Job System. ISystem, IComponentData, EntityCommandBuffer, Archetype queries, Hybrid ECS. Use for 1000+ entities, high-performance simulations, and data-oriented Unity projects."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Unity ECS Patterns

DOTS/ECS architecture for high-performance Unity. Covers ISystem, components, queries, jobs, and Hybrid ECS.

## Use this skill when

- Working with 1000+ entities (enemies, particles, projectiles)
- High-performance simulations requiring Burst/Jobs
- Migrating MonoBehaviour code to DOTS
- Data-oriented architecture in Unity

## Do not use this skill when

- Small projects with < 100 entities (MonoBehaviour is simpler)
- Rapid prototyping where iteration speed matters more
- Team unfamiliar with ECS concepts

## Instructions

### Core Component Types

```csharp
public struct Speed : IComponentData { public float Value; }
public struct Health : IComponentData { public float Current; public float Max; }
public struct EnemyTag : IComponentData { }
public struct Sleeping : IComponentData, IEnableableComponent { }
```

### Archetype Queries

```csharp
[BurstCompile]
public partial struct MoveSystem : ISystem
{
    public void OnUpdate(ref SystemState state)
    {
        float dt = SystemAPI.Time.DeltaTime;
        foreach (var (transform, speed) in
            SystemAPI.Query<RefRW<LocalTransform>, RefRO<Speed>>()
                     .WithAll<EnemyTag>()
                     .WithNone<Sleeping>())
        {
            transform.ValueRW.Position += new float3(0, 0, speed.ValueRO.Value * dt);
        }
    }
}
```

### EntityCommandBuffer for Structural Changes

```csharp
[BurstCompile]
public partial struct DeathSystem : ISystem
{
    [BurstCompile]
    public void OnUpdate(ref SystemState state)
    {
        var ecb = new EntityCommandBuffer(Allocator.TempJob);
        foreach (var (health, entity) in
            SystemAPI.Query<RefRO<Health>>().WithEntityAccess())
        {
            if (health.ValueRO.Current <= 0)
                ecb.DestroyEntity(entity);
        }
        ecb.Playback(state.EntityManager);
        ecb.Dispose();
    }
}
```

### Hybrid ECS Baker

```csharp
public class EnemyBaker : Baker<EnemyAuthoring>
{
    public override void Bake(EnemyAuthoring authoring)
    {
        var entity = GetEntity(TransformUsageFlags.Dynamic);
        AddComponent(entity, new Speed { Value = authoring.MoveSpeed });
        AddComponent(entity, new Health { Current = authoring.MaxHealth });
    }
}
```

### Performance Rules

| Rule | Reason |
|------|--------|
| `[BurstCompile]` on all systems | 10-100x speedup via LLVM |
| Use `RefRO` for read-only access | Allows parallel reads |
| Avoid `GetComponent` in jobs | Use `ComponentLookup` instead |
| Prefer `IJobEntity` | Cleaner, query-integrated |
| Use `EntityCommandBufferSystem` | Avoids manual ECB lifetime |