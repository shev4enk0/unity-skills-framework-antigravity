# Unity Skills Framework — Antigravity

> Коллекция Unity скиллов для Antigravity, Claude Code, Gemini CLI, Cursor и других AI-ассистентов.

Один формат `SKILL.md` — работает везде. Просто укажи нужный путь при установке.

---

## 🚀 Установка

### Antigravity
```bash
git clone https://github.com/shev4enk0/unity-skills-framework-antigravity.git .agent/skills/unity
```

### Claude Code
```bash
git clone https://github.com/shev4enk0/unity-skills-framework-antigravity.git .claude/skills/unity
```

### Gemini CLI
```bash
git clone https://github.com/shev4enk0/unity-skills-framework-antigravity.git .gemini/skills/unity
```

### Cursor
```bash
git clone https://github.com/shev4enk0/unity-skills-framework-antigravity.git .cursor/skills/unity
```

---

## 📦 Скиллы

### 🎮 Unity Core

| Скилл | Описание | Когда использовать |
|-------|----------|-------------------|
| `unity-developer` | Unity 6 LTS: C#, URP/HDRP, cross-platform | Любой Unity проект |
| `unity-ecs-patterns` | DOTS/ECS + Burst + Job System | 1000+ сущностей, высокая производительность |
| `unity-async` | Coroutines + async/await + Job System | Async загрузка, фоновые задачи |
| `unity-unitask` | Zero-allocation async (Cysharp UniTask) | Мобайл, снижение GC |
| `unity-r3` | Reactive extensions R3 — современный | Новые проекты Unity 2022+, MVVM |
| `unity-vcontainer` | Dependency Injection (VContainer) | Сервисная архитектура, тестируемость |
| `unity-ui` | UI Toolkit + UGUI оптимизация | Всё что связано с UI |
| `unity-mobile` | iOS/Android оптимизация, IL2CPP | Мобильные игры |
| `unity-performance` | Profiling, draw calls, batching, LOD | Проблемы с производительностью |
| `unity-textmeshpro` | TMP без GC аллокаций | Динамический текст |

### 🕹 Game Development

| Скилл | Описание | Когда использовать |
|-------|----------|-------------------|
| `game-development` | Оркестратор — маршрутизация к суб-скиллам | Точка входа для любых игровых вопросов |
| `game-development/mobile-games` | Touch input, батарея, App Store | Дизайн мобильных игр |
| `game-development/2d-games` | Спрайты, тайлмапы, 2D физика | 2D игры |
| `game-development/3d-games` | Меши, шейдеры, Cinemachine | 3D игры |
| `game-development/game-design` | GDD, баланс, прогрессия | Гейм-дизайн |
| `game-development/game-audio` | Audio Mixer, 3D звук, Wwise/FMOD | Аудио |

---

## 🏗 Архитектура скилла

Каждый скилл — папка с `SKILL.md`:

```
skills/
└── unity-developer/
    ├── SKILL.md                    ← обязательный
    └── resources/
        └── implementation-playbook.md  ← опциональный
```

Формат `SKILL.md`:
```yaml
---
name: unity-developer
description: "Краткое описание до 200 символов"
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

## Use this skill when
...
## Do not use this skill when
...
## Instructions
...
```

---

## 📚 Источники

- [sickn33/antigravity-awesome-skills](https://github.com/sickn33/antigravity-awesome-skills) — `unity-developer`, `unity-ecs-patterns`, `game-development/*`
- [creator-hian/claude-code-plugins](https://github.com/creator-hian/claude-code-plugins) — `unity-async`, `unity-unitask`, `unity-r3`, `unity-vcontainer`, `unity-ui`, `unity-mobile`, `unity-performance`, `unity-textmeshpro`
- [mob-sakai/UIEffect](https://github.com/mob-sakai/UIEffect) — UIEffect паттерны для UGUI
- [shanraisshan/claude-code-best-practice](https://github.com/shanraisshan/claude-code-best-practice) — Claude Code best practices

---

## 🤝 Совместимость

| AI Tool | Путь установки |
|---------|---------------|
| Antigravity | `.agent/skills/unity` |
| Claude Code | `.claude/skills/unity` |
| Gemini CLI | `.gemini/skills/unity` |
| Cursor | `.cursor/skills/unity` |
| Codex CLI | `.codex/skills/unity` |
| Kiro | `.kiro/skills/unity` |

---

## 📄 Лицензия

MIT License