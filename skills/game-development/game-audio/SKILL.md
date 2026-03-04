---
name: game-audio
description: "Game audio: Unity AudioMixer, spatial audio, adaptive music, sound effects pooling, audio performance."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Game Audio

Game audio implementation patterns. AudioMixer, spatial audio, adaptive music, SFX pooling.

## Instructions

### AudioMixer Setup

Create groups: Master > Music, Master > SFX, Master > UI.
Expose MusicVolume and SFXVolume parameters for settings menu.
Apply compression on Master group. Use Send/Receive for reverb zones.

### Volume in Code

AudioMixer uses decibels. Convert linear to dB: dB = Mathf.Log10(linear) * 20.
Clamp to -80 dB when linear is near zero.

### AudioSource Pool

Reuse AudioSources instead of instantiating per sound.
Pre-create pool of AudioSource components on Awake.
Dequeue, assign clip and position, play, then re-enqueue.

### Spatial Audio

Set AudioSource spatialBlend to 1 for full 3D.
Set minDistance for full volume range and maxDistance for silence.
Use logarithmic rolloff. Place AudioListener on player head or camera.

### Adaptive Music

Layer stems as separate AudioSources synced via AudioSettings.dspTime.
Crossfade between intensity layers based on game state.

### Audio Performance

Vorbis compression for music, ADPCM for short SFX.
LoadType Streaming for music, DecompressOnLoad for SFX.
Limit simultaneous AudioSources to 32 on mobile.

### Common Pitfalls

AudioMixer volume is dB: never set directly to linear value.
3D audio not working: check spatialBlend is 1 and AudioListener exists.
Music pops on scene load: preload audio before enabling AudioSource.