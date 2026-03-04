---
name: unity-textmeshpro
description: "TextMeshPro best practices for Unity: zero-GC text updates with SetText, font atlas setup, material presets, mobile optimization. Use for all dynamic text in Unity projects to eliminate string allocation GC spikes."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Unity TextMeshPro

TextMeshPro (TMP) best practices focusing on zero-GC text updates and font optimization.

## Use this skill when

- Implementing dynamic text (score, timer, health, labels)
- Font atlas setup and optimization for mobile
- Avoiding GC allocations from string operations in UI
- Rich text effects and TMP styling
- Localization with TMP
- Reducing UI rebuild frequency

## Instructions

### Zero-GC Text Updates

```csharp
using TMPro;

public class HUDController : MonoBehaviour
{
    [SerializeField] private TMP_Text _scoreText;
    [SerializeField] private TMP_Text _timerText;
    [SerializeField] private TMP_Text _hudText;

    private readonly System.Text.StringBuilder _sb = new(128);

    // BEST: SetText with format args — zero allocation
    public void UpdateScore(int score)
    {
        _scoreText.SetText("Score: {0}", score);
    }

    public void UpdateTimer(float seconds)
    {
        _timerText.SetText("{0:F1}s", seconds);
    }

    // GOOD: SetText with StringBuilder
    public void UpdateHUD(string playerName, int score, int level)
    {
        _sb.Clear();
        _sb.Append(playerName);
        _sb.Append(" | Lv.");
        _sb.Append(level);
        _sb.Append(" | ");
        _sb.Append(score);
        _hudText.SetText(_sb);
    }

    // AVOID: these allocate a new string every call
    // _scoreText.text = $"Score: {score}";          // BAD
    // _scoreText.text = "Score: " + score;           // BAD
    // _scoreText.text = score.ToString();            // BAD
}
```

### Update Only On Change

```csharp
// BAD: rebuilds TMP mesh every frame even when value unchanged
void Update()
{
    _scoreText.SetText("{0}", _score); // rebuilds every frame!
}

// GOOD: track previous value, update only on change
private int _displayedScore = -1;

void Update()
{
    if (_displayedScore != _score)
    {
        _displayedScore = _score;
        _scoreText.SetText("{0}", _score); // only when changed
    }
}
```

### Font Atlas Setup

```
TMP Font Asset Creator: Window → TextMeshPro → Font Asset Creator

Static Font (recommended for most cases):
- Source Font: your .ttf/.otf
- Atlas Resolution: 512x512 (mobile) or 1024x1024 (PC)
- Character Set: Custom Range (only characters you need)
- Render Mode: SDF32

Dynamic Font (flexible, more memory):
- Generated at runtime when new characters encountered
- Use for user-generated content or full Unicode

Mobile tip: Define exact character set (A-Z, a-z, 0-9, punctuation)
to minimize atlas size — avoids runtime generation.
```

### Rich Text

```csharp
// TMP supports rich text tags inline
_label.text = "You got <color=#FFD700>1000</color> gold!";
_label.text = "<b>CRITICAL HIT!</b> <size=120%>x2.5</size>";
_label.text = "<sprite index=0> 42"; // inline sprite from Sprite Asset

// Disable if user-generated content (security)
_label.richText = false;
```

### Material Presets (Outline, Shadow, Glow)

```csharp
// Create material presets in Inspector:
// TMP Font Asset → click material → duplicate → adjust settings
// (Outline, Underlay = shadow, Glow)

// Switch preset at runtime — do NOT create new Materials!
[SerializeField] private Material _normalMat;
[SerializeField] private Material _highlightMat;

public void SetHighlighted(bool on)
{
    // This is safe — reuses shared material instances
    _text.fontMaterial = on ? _highlightMat : _normalMat;
}

// NEVER do this — leaks memory every call:
// _text.fontMaterial = new Material(_text.fontMaterial);
```

### Common Pitfalls

| Issue | Cause | Fix |
|-------|-------|-----|
| GC spikes in UI | `text = $"..."` every frame | Use `SetText("{0}", val)` |
| TMP mesh rebuild | Setting `.text` unchanged | Cache last value, compare before set |
| Font characters missing | Static atlas too small | Increase atlas size or use Dynamic |
| Memory leak | `new Material(...)` per frame | Use pre-made material presets |
| Blurry text on mobile | Wrong Atlas Render Mode | Use SDF or SDF32 |