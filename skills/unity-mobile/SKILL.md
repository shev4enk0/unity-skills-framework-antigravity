---
name: unity-mobile
description: "Unity mobile development for Android and iOS: build settings, IL2CPP, ARM64, texture compression, touch input, safe area, battery optimization."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Unity Mobile

Unity mobile development for Android and iOS.

## Instructions

### Android Build Settings

Player Settings Android:
- Scripting Backend: IL2CPP
- Architecture: ARM64
- Minimum API Level: 24
- Target API Level: 34+

### iOS Build Settings

Player Settings iOS:
- Scripting Backend: IL2CPP
- Architecture: ARM64

### Texture Compression

Android and iOS: use ASTC (best quality/size ratio)

### Touch Input

Using New Input System with EnhancedTouchSupport.

### Safe Area

Apply Screen.safeArea to UI RectTransform anchorMin/anchorMax to handle notches.

### Frame Rate

Application.targetFrameRate = 60;
QualitySettings.vSyncCount = 0;

### Common Pitfalls

- Play Store rejection: enable ARM64 + target API 34+
- Overheating: cap FPS, use Adaptive Performance
- Blurry UI: Canvas Scaler Scale With Screen Size
- Notch cuts UI: apply Screen.safeArea
- Large APK: use Play Asset Delivery or Addressables
