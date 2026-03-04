---
name: unity-vcontainer
description: "VContainer DI framework for Unity: LifetimeScope, constructor injection, entry points, scoped containers, IStartable/ITickable. Fastest DI container for Unity."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Unity VContainer

VContainer is the fastest DI container for Unity. 5-10x faster than Zenject, zero GC in Resolve.

## Use this skill when

- Setting up dependency injection in Unity
- Registering services, presenters, or entry points
- Creating scoped containers per scene or level
- Replacing Zenject/Extenject

## Instructions

### Installation

openupm add jp.hadashikick.vcontainer

### Basic LifetimeScope

public class GameLifetimeScope : LifetimeScope
{
    protected override void Configure(IContainerBuilder builder)
    {
        builder.Register<IPlayerService, PlayerService>(Lifetime.Singleton);
        builder.RegisterComponentInHierarchy<PlayerView>();
        builder.RegisterEntryPoint<GameInitializer>();
    }
}

### Constructor Injection

public class PlayerService : IPlayerService
{
    private readonly IDataService _data;
    public PlayerService(IDataService data) => _data = data;
}

### Entry Points

public class GameInitializer : IStartable, IDisposable
{
    private readonly IPlayerService _player;
    public GameInitializer(IPlayerService player) => _player = player;
    public void Start() => _player.Initialize();
    public void Dispose() => _player.Cleanup();
}

### IAsyncStartable

public class DataLoader : IAsyncStartable
{
    private readonly IDataService _data;
    public DataLoader(IDataService data) => _data = data;
    public async UniTask StartAsync(CancellationToken ct)
    {
        await _data.LoadAsync(ct);
    }
}

### Lifetime Types

Singleton - One instance for entire scope
Scoped - One per child scope
Transient - New instance every Resolve
