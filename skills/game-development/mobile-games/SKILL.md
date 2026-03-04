---
name: mobile-games
description: "Mobile game development: monetization, retention mechanics, mobile UX, analytics events, performance budgets for iOS and Android."
risk: safe
source: community
category: game-development
date_added: "2026-03-04"
---

# Mobile Games

Mobile game development patterns. Monetization, retention, UX, and analytics.

## Instructions

### Monetization

F2P: soft currency earned plus hard currency purchased.
IAP tiers: $0.99 impulse, $4.99 standard, $9.99 and above for whale tier.
Battle Pass: free plus premium progression track.
Rewarded Video: player opts in for reward.
Never show ads to paying players.

### Retention

Day 1: 40% plus, Day 7: 20% plus, Day 30: 10% plus.
Mechanics: daily login rewards, push notifications, limited-time events.

### Mobile UX

Primary actions in bottom two thirds of screen (thumb zone).
Touch target minimum 48x48dp.
Casual session: 2-5 min. Mid-core: 10-20 min.

### Analytics Events

session_start and session_end.
level_start, level_complete, level_fail.
iap_initiated and iap_complete.
ad_shown and ad_rewarded.
tutorial_step_complete.

### Performance Budget

Draw calls under 100. Texture memory under 150MB.
RAM under 1.2GB. APK initial download under 100MB.
