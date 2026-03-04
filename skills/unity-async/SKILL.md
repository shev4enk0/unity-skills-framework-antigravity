---
name: unity-async
description: "Async/await patterns in Unity C# using UniTask: zero-allocation async, CancellationToken lifecycle, async scene loading. Use instead of coroutines for all async operations in Unity."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Unity Async / Await

Async/await in Unity using UniTask. Zero-allocation, cancellable replacement for coroutines.

## Use this skill when

- Loading scenes, assets, or data asynchronously
- Replacing coroutines with async/await
- Cancelling async ops when GameObject is destroyed

## Instructions

### Basic UniTask

```csharp
public async UniTask InitializeAsync(CancellationToken ct)
{
    await LoadConfigAsync(ct);
    await LoadAssetsAsync(ct);
    await ShowUIAsync(ct);
}
```

### CancellationToken — Always Pass It

```csharp
private void Start()
{
    SpawnLoopAsync(this.GetCancellationTokenOnDestroy()).Forget();
}

private async UniTask SpawnLoopAsync(CancellationToken ct)
{
    while (!ct.IsCancellationRequested)
    {
        Spawn();
        await UniTask.Delay(TimeSpan.FromSeconds(2f), cancellationToken: ct);
    }
}
```

### Parallel Operations

```csharp
await UniTask.WhenAll(
    LoadPlayerDataAsync(ct),
    LoadInventoryAsync(ct),
    PreloadAssetsAsync(ct)
);
```

### Frame-based Waits

```csharp
await UniTask.DelayFrame(5, cancellationToken: ct);
await UniTask.WaitUntil(() => _isReady, cancellationToken: ct);
```

### Error Handling

```csharp
try { await LoadAsync(ct); }
catch (OperationCanceledException) { /* normal cancellation, not an error */ }
catch (Exception e) { Debug.LogError(e.Message); }
```