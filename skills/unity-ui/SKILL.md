---
name: unity-ui
description: "Unity UI systems: UI Toolkit (UIElements) and UGUI. Canvas optimization, responsive layouts, runtime UI creation. Use for all Unity UI implementation including menus, HUD, and in-game interfaces."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Unity UI

UI implementation for Unity covering both UI Toolkit (modern) and UGUI (widely used).

## Use this skill when

- Creating menus, HUD, inventory, or dialog UI
- Canvas and draw call optimization
- Responsive UI for multiple screen resolutions
- UI animations and transitions
- UI Toolkit (UIElements) implementation
- UGUI optimization and best practices

## Do not use this skill when

- 3D world-space elements (billboards, in-world labels — use `unity-developer`)
- Text-only tasks (use `unity-textmeshpro`)

## Instructions

### UI Toolkit vs UGUI

| Feature | UI Toolkit | UGUI |
|---------|-----------|------|
| Performance | Better | Good |
| Styling | USS (CSS-like) | Inspector |
| Editor tooling | Yes (UI Builder) | Limited |
| Runtime | Unity 2021.2+ | All versions |
| Learning curve | Higher | Lower |
| Ecosystem | Growing | Mature |

**Recommendation:** UI Toolkit for new projects Unity 2022+, UGUI for existing projects.

### UGUI Optimization

```csharp
// CRITICAL: Separate static and dynamic Canvases
// Static Canvas (background, fixed elements) — never rebuilds mesh
// Dynamic Canvas (health bar, timers) — only rebuilds when changed

// AVOID: Changing layout properties in Update (rebuilds canvas every frame)
void Update() { healthBar.rectTransform.sizeDelta = new Vector2(width, 20f); } // BAD

// GOOD: Use fillAmount for progress bars (no layout rebuild)
healthBar.fillAmount = currentHP / maxHP; // OK

// Show/hide with CanvasGroup instead of SetActive
// SetActive causes expensive layout rebuild
canvasGroup.alpha = 0f;
canvasGroup.interactable = false;
canvasGroup.blocksRaycasts = false;
```

### Canvas Setup Best Practice

```
Canvas (Screen Space Overlay) — ROOT
├── Canvas: StaticLayer       (separate Canvas component, no Graphic Raycaster)
│   ├── Background Image
│   └── Static Labels
└── Canvas: DynamicLayer      (separate Canvas component)
    ├── HealthBar              (changes frequently)
    ├── ScoreText              (changes frequently)
    └── AnimatedNotifications
```

### UI Toolkit Basics

```csharp
// Reference UIDocument component
var uiDocument = GetComponent<UIDocument>();
var root = uiDocument.rootVisualElement;

// Query by name (# prefix) or class (. prefix)
var playButton = root.Q<Button>("play-button");
var scoreLabel = root.Q<Label>("score-label");

// Event handling
playButton.clicked += OnPlayButtonClicked;

// Update value
scoreLabel.text = $"Score: {score} ";

// Create elements at runtime
var card = new VisualElement();
card.AddToClassList("card");  // apply USS class
root.Add(card);
```

### Safe Area (Mobile Notch)

```csharp
// Handle iPhone notch and Android camera cutout
public class SafeAreaHandler : MonoBehaviour
{
    [SerializeField] private RectTransform _safeArea;

    void Start() => ApplySafeArea();

    void ApplySafeArea()
    {
        Rect safe = Screen.safeArea;
        Vector2 anchorMin = safe.position;
        Vector2 anchorMax = safe.position + safe.size;
        anchorMin.x /= Screen.width;
        anchorMin.y /= Screen.height;
        anchorMax.x /= Screen.width;
        anchorMax.y /= Screen.height;
        _safeArea.anchorMin = anchorMin;
        _safeArea.anchorMax = anchorMax;
    }
}
```

### Canvas Scaler (Responsive UI)

```
Canvas Scaler Settings for Mobile:
- UI Scale Mode: Scale With Screen Size
- Reference Resolution: 1080 x 1920 (portrait) or 1920 x 1080 (landscape)
- Screen Match Mode: Match Width Or Height
- Match: 0 (width) for portrait, 1 (height) for landscape
```