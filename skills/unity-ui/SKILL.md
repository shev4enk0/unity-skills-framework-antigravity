---
name: unity-ui
description: "Unity UI systems: UI Toolkit (UIElements) and UGUI. Canvas optimization, responsive layouts, runtime UI creation, TextMeshPro. Use for all Unity UI implementation including menus, HUD, and in-game interfaces."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Unity UI

UI implementation for Unity covering both UI Toolkit (modern) and UGUI (legacy/widely used).

## Use this skill when

- Creating menus, HUD, inventory UI, dialogs
- Canvas and draw call optimization
- Responsive UI for multiple resolutions
- UI animations and transitions
- UI Toolkit (UIElements) implementation
- UGUI optimization and best practices
- Screen size adaptation

## Do not use this skill when

- 3D world-space elements that are not UI
- TextMeshPro-specific zero-GC patterns (use unity-textmeshpro)

## Instructions

### UI Toolkit vs UGUI

| Feature | UI Toolkit | UGUI |
|---------|-----------|------|
| Performance | Better | Good |
| Styling | USS (like CSS) | Inspector |
| Editor tools | Full support | Limited |
| Runtime | Unity 2021.2+ | All versions |
| Ecosystem | Growing | Mature |
| Learning | Steeper | Gentler |

**Recommendation:** UI Toolkit for new projects, UGUI for existing projects.

### UGUI Canvas Optimization

```csharp
// CRITICAL RULE: Separate static and dynamic Canvas
// Static Canvas — background, labels that never change
// Dynamic Canvas — HP bar, score, timers

// BAD: One canvas for everything
// GameObject: Canvas > [Background] [HPBar] [ScoreText] [TimerText]
// Every frame the timer updates → entire canvas rebuilds

// GOOD: Split canvases
// Canvas (Static) > [Background] [Labels]
// Canvas (Dynamic) > [HPBar] [ScoreText] [TimerText]

// Show/hide with CanvasGroup instead of SetActive
// SetActive causes layout rebuild; CanvasGroup does not
private void ShowPanel(bool show)
{
    canvasGroup.alpha = show ? 1f : 0f;
    canvasGroup.interactable = show;
    canvasGroup.blocksRaycasts = show;
}
```

### Resolution and Scaling

```
Canvas Scaler settings for responsive UI:
- UI Scale Mode: Scale With Screen Size
- Reference Resolution: 1920x1080 (or your base)
- Screen Match Mode: Match Width Or Height
- Match: 0.5 (balance width/height)

For portrait mobile: Match = 0 (match width)
For landscape: Match = 1 (match height)
```

### UI Toolkit Runtime

```csharp
using UnityEngine.UIElements;

public class MainMenuController : MonoBehaviour
{
    void Start()
    {
        var root = GetComponent<UIDocument>().rootVisualElement;

        // Query elements by name
        var playButton = root.Q<Button>("play-button");
        var scoreLabel = root.Q<Label>("score-label");

        // Register events
        playButton.clicked += OnPlayClicked;

        // Set data
        scoreLabel.text = $"Best: {PlayerPrefs.GetInt("BestScore")}";

        // Add/remove USS classes for state
        playButton.AddToClassList("highlighted");
    }

    private void OnPlayClicked()
    {
        // Load game scene
    }
}
```

### Anchors for Responsive Layout (UGUI)

```
Top-left elements:    Anchor = top-left     (0, 1)
Top-right elements:   Anchor = top-right    (1, 1)
Bottom-left:          Anchor = bottom-left  (0, 0)
Center:               Anchor = center       (0.5, 0.5)
Stretch full:         Anchor min (0,0) max (1,1)

Always set anchors relative to parent for responsive UI.
```

### UI Animation

```csharp
// DOTween UI animations
using DG.Tweening;

// Fade in
canvasGroup.alpha = 0f;
canvasGroup.DOFade(1f, 0.3f);

// Scale popup
transform.localScale = Vector3.zero;
transform.DOScale(Vector3.one, 0.3f).SetEase(Ease.OutBack);

// Slide in from bottom
var startPos = rectTransform.anchoredPosition;
rectTransform.anchoredPosition = startPos - new Vector2(0, 200);
rectTransform.DOAnchorPos(startPos, 0.4f).SetEase(Ease.OutCubic);
```