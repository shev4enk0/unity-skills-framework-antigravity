---
name: unity-async
description: "Unity async patterns: Coroutines, async/await with Unity constraints, Job System, and thread safety. Foundation for UniTask and R3. Use when implementing async operations, loading, or background processing in Unity."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Unity Async

Async programming patterns for Unity including Coroutines, async/await, the Job System, and thread safety rules.

## Use this skill when

- Implementing async loading (scenes, assets, resources)
- Using Coroutines for timed sequences and animation
- Working with async/await in Unity context
- Background processing with the Job System
- Network requests in Unity
- Async transitions and cutscenes

## Do not use this skill when

- Performance-critical zero-allocation async (use `unity-unitask`)
- Reactive event streams (use `unity-r3`)

## Instructions

### Unity Threading Rules

```csharp
// Unity APIs can ONLY be called on the main thread!
// This will throw NullReferenceException:
Task.Run(() => gameObject.SetActive(true)); // WRONG

// Use UniTask to switch back to main thread:
await UniTask.SwitchToMainThread();
gameObject.SetActive(true); // safe
```

### Coroutine Patterns

```csharp
// Basic coroutine with progress reporting
IEnumerator LoadSceneAsync(string sceneName, System.Action<float> onProgress)
{
    yield return new WaitForSeconds(0.5f);

    AsyncOperation op = SceneManager.LoadSceneAsync(sceneName);
    op.allowSceneActivation = false;

    while (op.progress < 0.9f)
    {
        onProgress?.Invoke(op.progress);
        yield return null; // wait one frame
    }

    op.allowSceneActivation = true;
}

// Start/Stop
Coroutine handle = StartCoroutine(LoadSceneAsync("Game", p => Debug.Log(p)));
StopCoroutine(handle);
// StopAllCoroutines() — careful, stops ALL on this MonoBehaviour
```

### async/await in Unity (with UniTask)

```csharp
using Cysharp.Threading.Tasks;

// Zero-allocation async — use UniTask not Task
async UniTask LoadDataAsync(CancellationToken ct)
{
    await UniTask.Delay(500, cancellationToken: ct);
    var asset = await Resources.LoadAsync<TextAsset>("config");
    ProcessConfig(asset as TextAsset);
}

// Cancellation on MonoBehaviour destroy (best practice)
private CancellationTokenSource _cts;
void Awake() => _cts = new CancellationTokenSource();
void OnDestroy() { _cts.Cancel(); _cts.Dispose(); }

async UniTask DoWorkAsync()
{
    await LoadDataAsync(_cts.Token);
}
```

### Job System

```csharp
using Unity.Jobs;
using Unity.Collections;
using Unity.Burst;

[BurstCompile]
struct AddJob : IJobParallelFor
{
    [ReadOnly] public NativeArray<float> A;
    [ReadOnly] public NativeArray<float> B;
    [WriteOnly] public NativeArray<float> Result;

    public void Execute(int index)
    {
        Result[index] = A[index] + B[index];
    }
}

// Usage
var a = new NativeArray<float>(1000, Allocator.TempJob);
var b = new NativeArray<float>(1000, Allocator.TempJob);
var result = new NativeArray<float>(1000, Allocator.TempJob);

var job = new AddJob { A = a, B = b, Result = result };
JobHandle handle = job.Schedule(result.Length, 64);
handle.Complete(); // blocks until done

// Always dispose NativeArrays!
a.Dispose(); b.Dispose(); result.Dispose();
```

## Key Principles

1. **Never block the main thread** — use async, coroutines, or jobs
2. **Always handle cancellation** — pass CancellationToken everywhere
3. **Dispose NativeArrays** — memory leaks in Jobs are silent bugs
4. **UniTask over Task** — zero allocation, Unity-aware scheduling
5. **Coroutines for Unity lifecycle** — yield WaitForSeconds, WaitForFixedUpdate etc.