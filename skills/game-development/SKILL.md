---
name: game-development
description: "General game development: game loops, state machines, command pattern, observer, camera systems. Engine-agnostic patterns for Unity and other engines."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Game Development

Engine-agnostic game development patterns.

## Instructions

### Game State Machine

public enum GameState { MainMenu, Loading, Playing, Paused, GameOver }

public class GameStateMachine
{
    public GameState Current { get; private set; } = GameState.MainMenu;

    public void TransitionTo(GameState next)
    {
        Current = next;
    }
}

### Command Pattern

public interface ICommand { void Execute(); void Undo(); }

var history = new Stack<ICommand>();

### Observer Pattern

public class HealthSystem
{
    public event Action OnDeath;
    public void TakeDamage(int amount)
    {
        _hp = Mathf.Max(0, _hp - amount);
        if (_hp == 0) OnDeath?.Invoke();
    }
}

### Core Principles

- Game Feel: screenshake, particles, sound on every action
- Feedback Loop: action -> visible result -> reward
- Difficulty Curve: start easy, ramp gradually
- Tutorial: show, do not tell