---
name: unity-r3
description: "R3 reactive extensions for Unity: Observable, ReactiveProperty, Subject, DisposableBag, frame-based operators. Modern replacement for UniRx with zero-allocation design. Use for event streams, data binding, and reactive UI in Unity."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Unity R3

R3 is the modern reactive extensions library for Unity — zero-allocation replacement for UniRx with native frame-based operators.

## Use this skill when

- Implementing event-driven architecture in Unity
- Binding ViewModel properties to UI reactively
- Replacing UnityEvent or C# events with Observable streams
- Implementing debounce, throttle, or merge on input/data streams
- Replacing UniRx in existing projects

## Instructions

### Installation

```
openupm add com.cysharp.r3
```

### Observable and Subject

```csharp
using R3;

private readonly Subject<int> _onScoreChanged = new();
public Observable<int> OnScoreChanged => _onScoreChanged;

// Raise event
_onScoreChanged.OnNext(newScore);

// Subscribe
_onScoreChanged
    .Where(score => score > 100)
    .Subscribe(score => Debug.Log($"High score: {score}"))
    .AddTo(ref _bag);
```

### ReactiveProperty

```csharp
public ReactiveProperty<int> Score { get; } = new(0);
public ReactiveProperty<bool> IsAlive { get; } = new(true);

// Subscribe to changes
Score.Subscribe(s => _scoreText.SetText("{0}", s)).AddTo(ref _bag);

// Set value
Score.Value = 42;
```

### DisposableBag — Lifetime Management

```csharp
public class PlayerPresenter : MonoBehaviour
{
    private DisposableBag _bag;

    private void Start()
    {
        var builder = Disposable.CreateBuilder();

        _viewModel.Score
            .Subscribe(s => _scoreText.SetText("{0}", s))
            .AddTo(ref builder);

        _viewModel.IsAlive
            .Subscribe(alive => _deathScreen.SetActive(!alive))
            .AddTo(ref builder);

        _bag = builder.Build();
    }

    private void OnDestroy() => _bag.Dispose();
}
```

### Frame-based Operators

```csharp
// Throttle to last value per frame
_viewModel.Score
    .ThrottleLastFrame(1)
    .Subscribe(s => UpdateScoreUI(s))
    .AddTo(ref _bag);

// Debounce search input
_searchField.OnValueChangedAsObservable()
    .DebounceFrame(10)
    .Subscribe(text => Search(text))
    .AddTo(ref _bag);
```

### Common Operators

```csharp
obs.Where(x => x > 0)
obs.Select(x => x * 2)
Observable.Merge(stream1, stream2)
Observable.CombineLatest(healthRP, shieldRP, (hp, sp) => hp + sp)
    .Subscribe(total => UpdateTotalHP(total))
    .AddTo(ref _bag);
```

### Button Click as Observable

```csharp
_submitButton.OnClickAsObservable()
    .Subscribe(_ => _viewModel.SubmitCommand.Execute(null))
    .AddTo(ref _bag);
```

### ObserveEveryValueChanged

```csharp
this.ObserveEveryValueChanged(x => x.transform.position)
    .Subscribe(pos => OnMoved(pos))
    .AddTo(ref _bag);
```