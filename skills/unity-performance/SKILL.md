---
name: unity-performance
description: "Unity performance optimization: Profiler, Memory Profiler, GC reduction, draw call batching, object pooling, ProfilerMarker."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Unity Performance

Performance optimization for Unity. Covers profiling, GC reduction, batching, and object pooling.

## Use this skill when

- Profiling and optimizing a Unity game
- Reducing GC allocations and hitches
- Improving frame rate on mobile
- Reducing draw calls and overdraw

## Instructions

### ProfilerMarker

```csharp
private static readonly ProfilerMarker _marker = new ProfilerMarker("MySystem.Update");

void Update()
{
    using (_marker.Auto())
    {
        // measured code
    }
}
```

### Reduce GC Allocations

```csharp
// BAD
void Update() => _label.text = "Score: " + _score;

// GOOD: zero allocation
void Update()
{
    if (_lastScore != _score)
    {
        _lastScore = _score;
        _label.SetText("Score: {0}", _score);
    }
}
```

### Object Pooling

```csharp
_pool = new ObjectPool<Bullet>(
    createFunc:      () => Instantiate(_prefab),
    actionOnGet:     b => b.gameObject.SetActive(true),
    actionOnRelease: b => b.gameObject.SetActive(false),
    defaultCapacity: 20
);
```

### Draw Call Reduction

- GPU Instancing on shared materials
- Static Batching for non-moving objects
- Sprite Atlas for UI sprites
- MaterialPropertyBlock instead of per-object material changes

### Common Issues

| Issue | Fix |
|-------|-----|
| GC hitches | Pool objects, avoid LINQ/string in Update |
| Too many draw calls | GPU Instancing, Static Batching, Sprite Atlas |
| Physics overhead | Reduce rigidbodies, configure collision matrix |