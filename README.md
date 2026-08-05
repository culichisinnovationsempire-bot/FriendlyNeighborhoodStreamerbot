# 🎮 Stream Like Counter — Streamer.bot + OBS

A feature-rich like counter system for Twitch streamers using **Streamer.bot** and **OBS**. Track likes, hit milestone tiers with escalating visual effects, run countdowns, and let your chat interact with commands — all driven by a single C# script.

---

## ✨ Features

- **Like Counter** — Track likes with a live OBS text overlay
- **Milestone Tiers** — 4 escalating tiers with bigger effects at each level
  - **T1** (25 likes): Standard flash
  - **T2** (50 likes): Bigger flash + sound
  - **T3** (100 likes): Max flash + sound + chat announcement
  - **T4** (200 likes): Full hype overlay + chat announcement
- **Chat Commands** — Let viewers and mods interact via Twitch chat
- **Countdown Timer** — Start, tick, and reset countdowns with OBS display
- **Countdown Presets** — Quick-start 30s, 1m, 5m, or 10m timers
- **Multiplier Mode** — Toggle 2x likes during hype moments (or set custom)
- **Progress Bar** — Live percentage toward next milestone in OBS
- **Auto-Save Per Stream** — Tracks daily like total, resets at midnight
- **Sound Alerts** — Plays sounds on milestone hits and countdown events

---

## 📋 Requirements

- [Streamer.bot](https://streamer.bot/) (latest version)
- OBS Studio with [OBS WebSocket](https://obsproject.com/forum/resources/obs-websocket-5-x-remote-control-protocol.1610/) enabled
- Twitch channel (for chat commands)
- Sound files (see Sound Setup below)

---

## 🚀 Quick Start

### 1. Add the Script to Streamer.bot

1. Open Streamer.bot → **Actions**
2. Create a new action named `LikeSystem`
3. Add a **C# Code** sub-action
4. Paste the contents of `streamer-bot-extended.cs`
5. Set execution to the `Run()` method

### 2. Configure OBS Sources

Add these text sources to your OBS scene (names must match exactly):

| Source Name | What It Shows |
|---|---|
| `LikesText` | Current like count |
| `MilestoneText` | Milestone number |
| `CountdownText` | Countdown timer |
| `ProgressText` | Percentage (e.g. "45%") |
| `ProgressLabel` | Raw numbers (e.g. "12/25") |
| `MultiplierText` | Multiplier badge (e.g. "2x") |
| `StreamTotalText` | Today's total likes |

Add these visual effect sources (set all to **hidden** by default):

| Source Name | Purpose |
|---|---|
| `MilestoneFlash` | T1 flash |
| `MilestoneFlashBig` | T2 flash |
| `MilestoneFlashMax` | T3 flash |
| `HypeOverlay` | T4 full hype animation |
| `CountdownDone` | "TIME'S UP!" banner |
| `MultiplierBadge` | Badge behind multiplier text |
| `ProgressBar` | Progress bar graphic |

### 3. Set Up Triggers

Wire up these triggers in Streamer.bot:

| Trigger | Arguments |
|---|---|
| Channel Point Redemption | `mode = addLike` |
| Timer (every 1 second) | `mode = tickCountdown` |
| Chat message starting with `!` | `mode = chatCommand`, `command = <word>`, `argsStr = <rest>`, `user = <display name>`, `isModerator = <true/false>` |

### 4. Add Sound Files

Add `.wav` files to Streamer.bot's sound library:

| File | When It Plays |
|---|---|
| `milestone_tier2.wav` | T2 milestone hit |
| `milestone_tier3.wav` | T3 milestone hit |
| `milestone_tier4.wav` | T4 milestone hit |
| `countdown_tick.wav` | Last 5 seconds of countdown |
| `countdown_done.wav` | Countdown reaches 0 |

**Free sound sources:** [myinstants.com](https://www.myinstants.com), [soundsnap.com](https://www.soundsnap.com), YouTube Audio Library

---

## 💬 Chat Commands

| Command | Who Can Use | What It Does |
|---|---|---|
| `!likes` | Everyone | Shows current likes + progress |
| `!stats` | Everyone | Full stream summary |
| `!countdown 30` | Everyone | Starts a 30-second countdown |
| `!preset short` | Mods | 30s countdown (`medium` = 1m, `long` = 5m, `end` = 10m) |
| `!multiplier 3` | Mods | Sets like multiplier to 3x |
| `!reset` | Mods | Resets all counters to zero |

---

## ⚙️ Configurable Thresholds

All thresholds are constants at the top of the script. Change them to fit your stream:

```csharp
public const int NEXT_MILESTONE_STEP = 25;    // likes per milestone
public const int TIER2_THRESHOLD = 2;         // milestone #2+ triggers T2
public const int TIER3_THRESHOLD = 4;         // milestone #4+ triggers T3
public const int TIER4_THRESHOLD = 8;         // milestone #8+ triggers T4
public const int T1_FLASH_MS = 1500;          // T1 flash duration
public const int T2_FLASH_MS = 2000;          // T2 flash duration
public const int T3_FLASH_MS = 3000;          // T3 flash duration
public const int T4_FLASH_MS = 4000;          // T4 flash duration
public const int COUNTDOWN_TICK_SECONDS = 5;  // tick sound in last N seconds
public const int DEFAULT_MULTIPLIER = 2;     // default multiplier on toggle

