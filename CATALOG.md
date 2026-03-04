# Unity Skills Catalog — Mobile · Antigravity · Architecture

> Curated collection of skills, tools, and resources for Unity mobile development using the **Antigravity IDE** (Google DeepMind), covering DI/MVVM architecture, reactive programming with R3, async/await, code quality, and AI-assisted workflows.

---

## Table of Contents

- [Antigravity IDE & AI Coding Skills](#antigravity-ide--ai-coding-skills)
- [Unity Mobile Development (Android / iOS)](#unity-mobile-development-android--ios)
- [Architecture: DI with VContainer](#architecture-di-with-vcontainer)
- [Architecture: MVVM Toolkit for Unity](#architecture-mvvm-toolkit-for-unity)
- [Reactive Programming: R3](#reactive-programming-r3)
- [Async / Await: UniTask](#async--await-unitask)
- [Code Review in Unity](#code-review-in-unity)
- [Debugging in Unity](#debugging-in-unity)
- [Clean Code — Robert C. Martin](#clean-code--robert-c-martin)
- [Microsoft / Unity Coding Style](#microsoft--unity-coding-style)
- [UI with MVVM & R3](#ui-with-mvvm--r3)

---

## Antigravity IDE & AI Coding Skills

> **Antigravity** is Google DeepMind's AI coding IDE, built on top of Gemini CLI. Skills are markdown files placed in `~/.gemini/antigravity/skills/` that teach the agent domain-specific workflows.

| Skill / Resource | Description | Link |
|---|---|---|
| **Antigravity Awesome Skills** | 970+ battle-tested agentic skills for Antigravity IDE, Claude Code, Gemini CLI, Cursor, and Copilot. Includes bundles for Web Dev, Security, and general use. Install via `npx antigravity-awesome-skills`. | [sickn33/antigravity-awesome-skills](https://github.com/sickn33/antigravity-awesome-skills) |
| **get-shit-done (GSD)** | Lightweight meta-prompting, context-engineering, and spec-driven development system for Claude Code, Gemini CLI, OpenCode, and Codex. Solves context-rot. `npx get-shit-done-cc@latest` | [gsd-build/get-shit-done](https://github.com/gsd-build/get-shit-done) |
| **everything-claude-code** | Comprehensive collection of Claude Code patterns, best practices, and workflows for AI-assisted software development. | [affaan-m/everything-claude-code](https://github.com/affaan-m/everything-claude-code) |
| **claude-code-best-practice** | Practical guide on how to get the best results from Claude Code, including prompt patterns and workflow tips. | [shanraisshan/claude-code-best-practice](https://github.com/shanraisshan/claude-code-best-practice) |
| **Anthropic Skills (Official)** | Official skill examples from Anthropic for use with Claude Code agents. | [anthropics/skills](https://github.com/anthropics/skills) |
| **Skill placement — Antigravity** | Place `.md` skill files in `~/.gemini/antigravity/skills/` (global) or `.gemini/skills/` (project). Invoke in agent mode: `(Agent Mode) Use <skill-name>...` | — |
| **Skill placement — Claude Code** | Place `.md` skill files in `.claude/skills/`. Invoke with `>> /skill-name help me...` | — |

---

## Unity Mobile Development (Android / iOS)

| Skill / Resource | Description | Link |
|---|---|---|
| **Unity Android Build Settings** | Configure Player Settings for Android: target API level (API 33+), IL2CPP scripting backend, ARM64 architecture, Gradle template customization. | [Unity Docs — Android](https://docs.unity3d.com/Manual/android-building.html) |
| **Unity iOS Build Settings** | Configure Player Settings for iOS: Xcode project export, signing, capability configuration (push, background modes), bitcode deprecation. | [Unity Docs — iOS](https://docs.unity3d.com/Manual/iphone-GettingStarted.html) |
| **Performance Profiling on Mobile** | Use Unity Profiler with deep profiling disabled on device. Target 60 fps with ≤16.67 ms frame time (30 fps = ≤33.33 ms). Profile memory, GPU, and GC separately. | [Unity Profiler Guide](https://docs.unity3d.com/Manual/Profiler.html) |
| **Addressable Asset System** | Load assets on demand to reduce initial binary size. Critical for mobile where app size limits exist. Supports async loading via `LoadAssetAsync<T>`. | [Unity Addressables](https://docs.unity3d.com/Packages/com.unity.addressables@latest) |
| **Unity Adaptive Performance** | Optimize thermal and battery behavior on Android. Use `AdaptivePerformanceIndexer` to scale GPU/CPU load. Required for sustained performance on mobile. | [Unity Adaptive Performance](https://docs.unity3d.com/Packages/com.unity.adaptiveperformance@latest) |
| **Google Play (Android) Integration** | Use `Google Play Plugins for Unity` (official) to integrate Play Billing, Play Games Services, Play Asset Delivery. | [google.github.io/play-unity-plugins](https://google.github.io/play-unity-plugins/) |
| **Texture Compression for Mobile** | Use ETC2 (Android) and ASTC (iOS/Android) formats. ASTC offers best quality-to-size ratio. Set in Texture Import Settings per platform. | [Unity Texture Compression](https://docs.unity3d.com/Manual/class-TextureImporterOverride.html) |
| **IL2CPP & Code Stripping** | Always use IL2CPP on mobile for performance and security. Enable Managed Stripping Level `High`; maintain a `link.xml` to preserve reflection-dependent types. | [Unity IL2CPP](https://docs.unity3d.com/Manual/IL2CPP.html) |

---

## Architecture: DI with VContainer

> **VContainer** is the recommended DI container for Unity — 5–10× faster than Zenject, zero GC allocation in Resolve, and immutable/thread-safe containers.

| Skill / Resource | Description | Link |
|---|---|---|
| **VContainer** | Fast DI library for Unity. Supports constructor/method/property injection, PlayerLoop integration, async scoping, SourceGenerator acceleration. Install: `openupm add jp.hadashikick.vcontainer` | [hadashiA/VContainer](https://github.com/hadashiA/VContainer) |
| **LifetimeScope** | Root DI container in VContainer. Extend `LifetimeScope`, override `Configure(IContainerBuilder builder)`. One root scope per scene; create child scopes for scenes/levels. | [VContainer Docs](https://vcontainer.hadashikick.jp) |
| **Entry Points** | Register pure C# classes as Unity lifecycle entry points with `builder.RegisterEntryPoint<T>()`. Implement `IStartable`, `ITickable`, `IFixedTickable`, `ILateTickable`, `IDisposable`. | [VContainer Entry Points](https://vcontainer.hadashikick.jp/docs/registering/register-entry-point) |
| **Scoped Asset Loading** | Use `LifetimeScope.CreateChild()` or `CreateChildFromPrefab()` to create scoped containers for async-loaded scenes/levels. Dispose the scope on scene unload. | [VContainer Scoping](https://vcontainer.hadashikick.jp/docs/scoping/lifetime-overview) |
| **Source Generator Mode** | Enable `VContainer.SourceGenerator` for compile-time injection code generation. Eliminates runtime reflection. Add `[Inject]` attributes and partial classes. | [VContainer SourceGen](https://vcontainer.hadashikick.jp/docs/optimization/source-generator) |

---

## Architecture: MVVM Toolkit for Unity

> Port of Microsoft's MVVM Community Toolkit to Unity, with full source generator + analyzer support for clean, boilerplate-free ViewModels.

| Skill / Resource | Description | Link |
|---|---|---|
| **MVVM Toolkit for Unity** | Brings `[ObservableProperty]`, `[RelayCommand]`, `[NotifyPropertyChangedFor]`, `ObservableValidator`, and Messenger into Unity. Works with NoesisGUI. | [atcarter714/MVVM-Toolkit-Unity](https://github.com/atcarter714/MVVM-Toolkit-Unity) |
| **ObservableObject** | Base class for ViewModels. Use `[ObservableProperty]` on backing fields to auto-generate `INotifyPropertyChanged` properties with zero boilerplate. | [MVVM Toolkit Docs](https://learn.microsoft.com/en-us/dotnet/communitytoolkit/mvvm/) |
| **RelayCommand** | Use `[RelayCommand]` on a method to auto-generate an `ICommand` property. Supports async commands via `AsyncRelayCommand`. | [MS Learn — RelayCommand](https://learn.microsoft.com/en-us/dotnet/communitytoolkit/mvvm/relaycommand) |
| **Messenger (WeakReferenceMessenger)** | Decoupled communication between ViewModels/services. Use `WeakReferenceMessenger.Default.Send<TMessage>()` and `Receive<TMessage>()`. Avoids tight coupling between UI and logic. | [MS Learn — Messenger](https://learn.microsoft.com/en-us/dotnet/communitytoolkit/mvvm/messenger) |
| **MVVM Pattern in Unity** | Separate UI (View), state (ViewModel with `INotifyPropertyChanged`), and domain logic (Model/Service). Bind View to ViewModel via UI Toolkit data binding or NoesisGUI. | [Unity UI Toolkit Binding](https://docs.unity3d.com/Manual/UIE-runtime-binding.html) |
| **MVVM Samples** | Official Microsoft MVVM Toolkit sample application demonstrating all major features. | [CommunityToolkit/MVVM-Samples](https://github.com/CommunityToolkit/MVVM-Samples) |

---

## Reactive Programming: R3

> **R3** is the modern replacement for UniRx and dotnet/reactive, designed for Unity, Godot, and other game engines. Supports Unity's frame-based operations natively.

| Skill / Resource | Description | Link |
|---|---|---|
| **R3 — Reactive Extensions for Unity** | Zero-allocation, high-performance Rx library. Drop-in upgrade from UniRx. Adds frame-based operators (`ObserveEveryValueChanged`, `ThrottleLastFrame`, etc.). Install via OpenUPM: `openupm add com.cysharp.r3` | [Cysharp/R3](https://github.com/Cysharp/R3) |
| **Observable & Subject** | Core R3 types. Use `Subject<T>` for manual event sources. Use `Observable.EveryUpdate()` for frame-driven streams. Always dispose subscriptions with `AddTo(gameObject)` or `DisposableBag`. | [R3 Unity Guide](https://github.com/Cysharp/R3#unity) |
| **ReactiveProperty** | `ReactiveProperty<T>` is an observable value with `INotifyPropertyChanged`. Replaces raw backing fields in ViewModels for two-way binding. Combine with MVVM Toolkit. | [R3 ReactiveProperty](https://github.com/Cysharp/R3#reactiveproperty) |
| **DisposableBag** | R3's replacement for `CompositeDisposable`. Simpler API: `var bag = Disposable.CreateBuilder(); obs.Subscribe(...).AddTo(ref bag); _bag = bag.Build();`. | [R3 Disposal](https://github.com/Cysharp/R3#disposable) |
| **Frame-based operators** | `DelayFrame`, `ThrottleLastFrame`, `DebounceFrame`, `ObserveEveryValueChanged`. Essential for game logic that must align with Unity's PlayerLoop. | [R3 Frame Operators](https://github.com/Cysharp/R3#frame-based-operators) |
| **R3 + VContainer** | Register `ReactiveProperty` values and R3 observables inside VContainer scopes. Inject streams into Presenters; subscribe in `IStartable.Start()`. | — |

---

## Async / Await: UniTask

> **UniTask** provides zero-allocation async/await for Unity. All Unity async operations (coroutines, `AsyncOperation`, `UnityWebRequest`) are awaitable with zero GC overhead.

| Skill / Resource | Description | Link |
|---|---|---|
| **UniTask** | Struct-based `UniTask<T>` replaces `Task<T>`. Custom `AsyncMethodBuilder` gives zero allocation. Runs entirely on Unity's PlayerLoop — no background threads, works in WebGL/Wasm. | [Cysharp/UniTask](https://github.com/Cysharp/UniTask) |
| **Basic Usage** | `async UniTask<string> LoadAsync()` — await `Resources.LoadAsync`, `SceneManager.LoadSceneAsync`, `UnityWebRequest`, and coroutines. | [UniTask Getting Started](https://github.com/Cysharp/UniTask#getting-started) |
| **CancellationToken** | Pass `this.GetCancellationTokenOnDestroy()` to cancel async operations when a GameObject is destroyed. In Unity 2022.2+ use built-in `destroyCancellationToken`. | [UniTask Cancellation](https://github.com/Cysharp/UniTask#cancellation-and-exception-handling) |
| **UniTaskTracker** | Editor window (`Window > UniTask Tracker`) to see all in-flight async operations. Prevents async memory leaks. Enable via `UniTaskTrackerWindow`. | [UniTaskTracker](https://github.com/Cysharp/UniTask#unitasktracker) |
| **Frame-based Delays** | `await UniTask.DelayFrame(n)`, `await UniTask.Delay(TimeSpan)`, `await UniTask.Yield(PlayerLoopTiming.PreLateUpdate)` — replaces all coroutine wait patterns. | [UniTask PlayerLoop](https://github.com/Cysharp/UniTask#playerloop) |
| **UniTask + VContainer** | Inject `CancellationToken` via constructor (from `LifetimeScope.destroyCancellationToken`). Await async entry points with `IAsyncStartable`. | [VContainer + UniTask](https://vcontainer.hadashikick.jp/docs/integrations/unitask) |

---

## Code Review in Unity

| Skill / Resource | Description | Link |
|---|---|---|
| **Roslyn Analyzers** | Add `Microsoft.CodeAnalysis.NetAnalyzers` and `Unity.Analyzers` NuGet packages to your Unity project (`.asmdef`-based) to enforce code quality rules at compile time. | [Unity Analyzers](https://github.com/microsoft/Microsoft.Unity.Analyzers) |
| **SonarLint for Unity** | Run SonarLint in VS Code or Rider to detect bugs, code smells, and security vulnerabilities as you type. Integrates with SonarQube/SonarCloud for CI-based review. | [SonarLint](https://www.sonarlint.org/) |
| **JetBrains Rider Code Inspections** | Rider provides 2,500+ Unity-aware inspections. Highlights performance issues (e.g., `GetComponent` in `Update`), null-reference patterns, and common Unity antipatterns. | [Rider for Unity](https://www.jetbrains.com/rider/features/unity.html) |
| **EditorConfig** | Add `.editorconfig` to enforce consistent code style across the team (indentation, line endings, naming rules). Rider and VS both respect it. | [EditorConfig](https://editorconfig.org/) |
| **Pull Request Review Checklist** | Check: no `FindObjectOfType` in hot paths, no `string` concatenation in `Update`, all coroutines have cancellation, all `IDisposable` objects disposed, assets not loaded in constructors. | — |
| **Unity Code Review — Antigravity Skill** | Install the `antigravity-awesome-skills` bundle and use the `@code-review` skill to get AI-powered review of Unity scripts. | [sickn33/antigravity-awesome-skills](https://github.com/sickn33/antigravity-awesome-skills) |

---

## Debugging in Unity

| Skill / Resource | Description | Link |
|---|---|---|
| **Unity Debugger (Rider / VS)** | Attach debugger to the Unity Editor via `Debug > Attach Unity Debugger`. Set breakpoints in C# code. Rider provides the best Unity debugging experience. | [Rider Debugging](https://www.jetbrains.com/help/rider/Debugging_Unity_Applications.html) |
| **Unity Profiler** | CPU/GPU/Memory/Audio/Physics profiler built into the Editor. Use **Deep Profile** cautiously (high overhead); prefer custom `ProfilerMarker` in hot paths. | [Unity Profiler](https://docs.unity3d.com/Manual/Profiler.html) |
| **Memory Profiler (Package)** | Install `com.unity.memoryprofiler`. Capture heap snapshots on device. Find leaked `UnityEngine.Object` references and GC root chains. | [Memory Profiler](https://docs.unity3d.com/Packages/com.unity.memoryprofiler@latest) |
| **Frame Debugger** | `Window > Analysis > Frame Debugger` — step through every draw call in a frame. Identify overdraw, missing batching, unintended shadow casters. | [Frame Debugger](https://docs.unity3d.com/Manual/FrameDebugger.html) |
| **Android Logcat (Package)** | Install `com.unity.mobile.android-logcat`. View device logs filtered to your app from inside the Editor. Shows native crashes, ANRs, and plugin logs. | [Android Logcat](https://docs.unity3d.com/Packages/com.unity.mobile.android-logcat@latest) |
| **UniTaskTracker** | Tracks all in-flight `UniTask` operations. See task call stacks and detect leaked async operations. `Window > UniTask Tracker`. | [UniTask](https://github.com/Cysharp/UniTask#unitasktracker) |
| **VContainer Diagnostics Window** | `Window > VContainer > Diagnostics` — inspect all registered types and their resolved instances in each `LifetimeScope`. | [VContainer](https://vcontainer.hadashikick.jp) |
| **Conditional Debug Logging** | Use `#if UNITY_EDITOR || DEVELOPMENT_BUILD` or a custom `[Conditional("UNITY_EDITOR")]` logger wrapper to strip debug logs from release builds. | — |

---

## Clean Code — Robert C. Martin

| Principle | Application in Unity C# | Reference |
|---|---|---|
| **Single Responsibility Principle (SRP)** | One class, one reason to change. Split `PlayerController` into `PlayerInput`, `PlayerMovement`, `PlayerAnimator`. Register each as a separate VContainer entry point. | *Clean Code*, Ch. 10 |
| **Open/Closed Principle (OCP)** | Extend behavior via new classes, not by modifying existing ones. Use interfaces (`IWeapon`, `IMovementStrategy`) and inject implementations via VContainer. | *Clean Architecture*, Ch. 8 |
| **Dependency Inversion Principle (DIP)** | Depend on abstractions, not concretions. Always inject interfaces (`ICharacterService`), not concrete types. VContainer enforces this at the container level. | *Clean Code*, Ch. 11 |
| **Meaningful Names** | Use descriptive names: `CalculateDamage()` not `Calc()`, `enemyHealthPoints` not `ehp`. Follow Unity/Microsoft naming conventions (PascalCase methods, camelCase fields). | *Clean Code*, Ch. 2 |
| **Small Functions** | Keep methods under 20 lines. A `Start()` method should only orchestrate — delegate to descriptive private methods. | *Clean Code*, Ch. 3 |
| **DRY (Don't Repeat Yourself)** | Extract duplicated logic into shared services or base classes. Use `ScriptableObject`-based configuration to avoid magic numbers. | *Clean Code*, Ch. 17 |
| **No Magic Numbers** | Replace numeric literals with named constants or `ScriptableObject` fields: `const float GravityScale = 9.81f;`, not `* 9.81f`. | *Clean Code*, Ch. 17 |
| **Error Handling via Exceptions** | Don't use `null` returns to signal failure. Throw descriptive exceptions or use `Result<T>` pattern. Catch exceptions at entry points, not deep in domain logic. | *Clean Code*, Ch. 7 |

---

## Microsoft / Unity Coding Style

| Rule | Description | Reference |
|---|---|---|
| **Naming: PascalCase** | Public types, methods, properties, and events use PascalCase: `public void CalculateDamage()`, `public float MaxHealth`. | [MS C# Naming](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/identifier-names) |
| **Naming: camelCase** | Private/local variables and method parameters use camelCase: `private float _currentHealth;` (with underscore prefix for private fields). | [Unity C# Style Guide](https://unity.com/how-to/naming-and-code-style-tips-c-scripting-unity) |
| **Unity Naming Conventions** | Fields serialized in the Inspector: use `[SerializeField] private float _moveSpeed;` (private + underscore). Avoids polluting the public API. | [Unity Style Tips](https://unity.com/how-to/naming-and-code-style-tips-c-scripting-unity) |
| **Interfaces: I-prefix** | All interfaces start with `I`: `IMovable`, `IDamageable`, `ICharacterService`. Required by Microsoft and Unity guidelines. | [MS Naming Guidelines](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/names-of-classes-structs-and-interfaces) |
| **`var` usage** | Use `var` when the type is obvious from the right-hand side (`var player = new Player()`). Don't use `var` when type adds readability (`CharacterService service = ...`). | [MS var usage](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/implicitly-typed-local-variables) |
| **Avoid `public` fields** | Use `[SerializeField] private` + property instead of `public` fields. Keeps Inspector integration while maintaining encapsulation. | [Unity Style Tips](https://unity.com/how-to/naming-and-code-style-tips-c-scripting-unity) |
| **`#region` usage** | Avoid `#region` blocks — they hide code and encourage large files. Split large classes instead. | [MS Coding Conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions) |
| **XML Documentation** | Add `/// <summary>` docs to all public API members. Rider and VS IntelliSense will display them in tooltips. Required for shared libraries. | [MS XML Docs](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/xmldoc/) |
| **`.editorconfig`** | Enforce all style rules automatically via `.editorconfig` checked into source control. Eliminates style debates in code review. | [EditorConfig](https://editorconfig.org/) |

---

## UI with MVVM & R3

| Skill / Resource | Description | Link |
|---|---|---|
| **Unity UI Toolkit (UIElements)** | Modern Unity UI system using USS (CSS-like) and UXML (markup). Recommended for all new Unity 2022+ projects. Supports runtime data binding to `INotifyPropertyChanged`. | [UI Toolkit Manual](https://docs.unity3d.com/Manual/UIElements.html) |
| **UI Toolkit Runtime Binding** | Bind ViewModel properties to UXML elements via `BindingContext` + `data-source`. Works with `INotifyPropertyChanged` (MVVM Toolkit `ObservableObject`). | [UI Toolkit Binding](https://docs.unity3d.com/Manual/UIE-runtime-binding.html) |
| **R3 + UI Toolkit** | Subscribe to `ReactiveProperty<T>` and update UI elements in subscription callbacks. Use `AddTo(this)` or `DisposableBag` for lifecycle management. Pattern: Model→RP→Observable→UI update. | [Cysharp/R3](https://github.com/Cysharp/R3) |
| **MVVM Flow: Input → Command → State → View** | User input triggers `IRelayCommand` → ViewModel updates `ReactiveProperty` → View observes changes → UI re-renders. Strict one-way data flow ensures testability. | — |
| **Presenter Pattern (VContainer)** | Register `IStartable` Presenters in VContainer. Inject ViewModel + View. Subscribe to ViewModel's observables in `Start()`. Update View in callbacks. Dispose in `IDisposable.Dispose()`. | [VContainer Entry Points](https://vcontainer.hadashikick.jp/docs/registering/register-entry-point) |
| **NoesisGUI** | WPF-based UI framework for Unity. Supports full XAML data binding, animations, styles. Best-in-class MVVM support for Unity UI. Works with MVVM Toolkit for Unity. | [NoesisGUI](https://www.noesisengine.com/) |
| **Button click as Observable (R3)** | `button.OnClickAsObservable().Subscribe(_ => viewModel.SubmitCommand.Execute(null)).AddTo(bag);` — wire UI events to commands reactively without manual `onClick.AddListener`. | [R3 Unity](https://github.com/Cysharp/R3#unity) |

---

*Last updated: 2026-03-04 14:38:50 · Maintained by the Antigravity Unity Skills Framework project.*