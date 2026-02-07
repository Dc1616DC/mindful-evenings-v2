# Mindful Evenings — MVP Spec for Codex

> **What you're building:** A React Native/Expo mobile app that owns "the evening" as a wellness category. Evening eating support + wind-down routines + sleep optimization in one app. Dark theme, conversational UI, fully offline.

---

## Table of Contents

1. [Product Vision](#product-vision)
2. [Voice & Language Guide](#voice--language-guide) ⚠️ READ THIS FIRST
3. [Target User](#target-user)
4. [Tech Stack](#tech-stack)
5. [Design System](#design-system)
6. [Navigation & App Structure](#navigation--app-structure)
7. [File Structure](#file-structure)
8. [Type Definitions](#type-definitions)
9. [Feature Specs](#feature-specs)
   - [Onboarding](#1-onboarding)
   - [Tonight (Home)](#2-tonight-home-screen)
   - [Evening Check-In](#3-evening-check-in-the-core-feature)
   - [Guided Activities](#4-guided-activities)
   - [SOS Mode](#5-sos-mode)
   - [Wind-Down Routine Builder](#6-wind-down-routine-builder)
   - [Toolkit](#7-toolkit-screen)
   - [Morning Mirror](#8-morning-mirror)
   - [Evenings (History & Patterns)](#9-evenings-history--patterns)
10. [constants/Copy.ts Reference](#constantscopysts-reference)
11. [Data & Storage](#data--storage)
12. [Notifications](#notifications)
13. [Monetization](#monetization)
14. [Build Rules](#build-rules)

---

## Product Vision

Evening eating, doom scrolling, staying up too late, and poor sleep are all symptoms of the same thing — unmet needs at the end of the day. This app helps people figure out what they actually need and gives them something better than the fridge or their phone.

Not a sleep app. Not a food app. Not a routine checklist. The whole evening.

---

## Voice & Language Guide

> ⚠️ **THIS IS THE MOST IMPORTANT SECTION OF THIS SPEC.** The voice IS the product. Read this before writing ANY user-facing text. Every string in `constants/Copy.ts` must pass these rules.

### Tone

**70% warm friend, 20% gentle expert, 10% wry humor.**

Like a friend who happens to know a lot about why you're standing in front of the fridge at 9pm.

### Rules — ALWAYS

- Eating is a valid option. Always. It's never "giving in."
- Treat cravings as data, not enemies.
- Normalize the struggle: "yeah, evenings are hard. that's not a character flaw."
- Short sentences. Fragments okay. Contractions always.
- Conversational, not clinical. "What's going on?" not "How are you feeling this evening?"
- A little humor when appropriate: "Your couch isn't going anywhere. But let's check in first."
- Use "notice" and "check-in" instead of "track", "session", or "log."

### Rules — NEVER SAY

These words/phrases are **banned** from the entire codebase:

| Banned | Why |
|--------|-----|
| "Good job!", "Great choice!", "You resisted!", "Stay strong" | Patronizing. We're not a rewards chart. |
| "Healthy choice", "Diet", "Cheat", "Clean eating", "Guilt-free" | Diet culture language. Hard no. |
| "Calm" (as branding/adjective for the app) | We're not Calm. |
| "Track", "Session", "Log" | Clinical. Use "notice", "check-in" instead. |
| "Willpower", "Discipline", "Self-control" | Implies moral failure. |
| Anything that sounds like a fitness app | We're not that. |
| Anything that sounds like a therapist | We're not that either. |
| Anything condescending or overly cheerful | Kills trust instantly. |

### Time-Aware Greetings

```
6-8 PM:   "Hey. How's your evening going?"
8-10 PM:  "Hey. Winding down or just getting started?"
10 PM-12: "Still up? No judgment. Let's check in."
12 AM+:   "Late night, huh? What's keeping you up?"
```

### Voice Examples

**Good:**
- "What's going on right now?"
- "What do you actually need tonight?"
- "Eating is always an option. This isn't about stopping you."
- "Missed your 9pm wind-down? That's fine. Want to do a quick 5-minute version instead?"
- "Your couch isn't going anywhere. But let's check in first."

**Bad:**
- "Great job completing your evening session!" ❌
- "You made a healthy choice tonight!" ❌
- "Stay strong — you've got this!" ❌
- "How are you feeling this evening?" ❌ (too formal)
- "Let's log your progress!" ❌

### The Eating Rule

Every path that involves food must include implicit or explicit permission. Eating is ALWAYS presented as a valid, judgment-free option. The app never tries to stop someone from eating. It helps them eat with intention if that's what they choose, or explore what they actually need if they're open to it.

**No calorie counting. No food logging. No weight tracking. Ever.**

---

## Target User

Anyone who:
- Snacks mindlessly in the evening and feels bad about it
- Doom scrolls until midnight then wonders why they can't sleep
- Knows they "should" have a bedtime routine but doesn't
- Feels like their evenings just disappear

**NOT** eating disorder recovery. Include a disclaimer and the NEDA helpline (1-800-931-2237) in Settings/About.

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| React Native + Expo SDK 52+ | Framework |
| expo-router (file-based) | Navigation |
| expo-notifications | Push notifications |
| expo-haptics | Tactile feedback for guided exercises |
| @react-native-async-storage/async-storage | All local data persistence |
| react-native-reanimated | Animations (breathing circle, transitions) |
| expo-linear-gradient | Background gradients |
| date-fns | Date/time utilities |

**No backend for MVP.** All data is stored locally on device via AsyncStorage. App works fully offline.

---

## Design System

### Colors (`constants/Colors.ts`)

```typescript
export const Colors = {
  // Backgrounds
  bgPrimary: '#0f172a',       // Deep navy — main background
  bgSecondary: '#1e1b4b',     // Dark purple — cards, secondary surfaces
  bgCard: '#1e293b',          // Slightly lighter navy — card backgrounds
  bgElevated: '#334155',      // Elevated surfaces, modals

  // Gradients (use with expo-linear-gradient)
  gradientStart: '#0f172a',
  gradientEnd: '#1e1b4b',

  // Accent
  purple: '#a78bfa',          // Primary accent — buttons, highlights
  purpleLight: '#c4b5fd',     // Lighter purple — selected states
  purpleDim: '#7c3aed',       // Darker purple — pressed states
  emerald: '#10b981',         // Secondary accent — positive states, completion
  emeraldDim: '#059669',      // Darker emerald
  amber: '#f59e0b',           // Warmth accent — streaks, warmth indicators
  amberDim: '#d97706',

  // Text
  textPrimary: '#f1f5f9',     // Primary text — almost white
  textSecondary: '#94a3b8',   // Secondary text — muted
  textMuted: '#64748b',       // Tertiary — timestamps, hints
  textOnAccent: '#0f172a',    // Text on colored buttons

  // Functional
  border: '#334155',
  divider: '#1e293b',
  overlay: 'rgba(15, 23, 42, 0.8)',

  // Emotion chips (used in check-in)
  chipDefault: '#1e293b',
  chipSelected: '#a78bfa',
  chipText: '#f1f5f9',
  chipSelectedText: '#0f172a',
};
```

### Typography

Use **Inter** (or **DM Sans** as fallback). Install via `expo-font`.

```typescript
export const Typography = {
  h1: { fontSize: 28, fontWeight: '700', lineHeight: 36, fontFamily: 'Inter-Bold' },
  h2: { fontSize: 22, fontWeight: '600', lineHeight: 28, fontFamily: 'Inter-SemiBold' },
  h3: { fontSize: 18, fontWeight: '600', lineHeight: 24, fontFamily: 'Inter-SemiBold' },
  body: { fontSize: 16, fontWeight: '400', lineHeight: 24, fontFamily: 'Inter-Regular' },
  bodySmall: { fontSize: 14, fontWeight: '400', lineHeight: 20, fontFamily: 'Inter-Regular' },
  caption: { fontSize: 12, fontWeight: '400', lineHeight: 16, fontFamily: 'Inter-Regular' },
  button: { fontSize: 16, fontWeight: '600', lineHeight: 24, fontFamily: 'Inter-SemiBold' },
};
```

### Spacing

```typescript
export const Spacing = {
  xs: 4, sm: 8, md: 16, lg: 24, xl: 32, xxl: 48,
};
```

### Animation Principles

- All transitions: 300-400ms, ease-in-out
- Check-in steps: horizontal slide or fade between full-screen steps
- Cards: fade in with slight upward translate (translateY: 10 → 0)
- Breathing circle: smooth scale animation synchronized to breath timing
- No spring animations — everything gentle, nothing bouncy
- Haptic feedback: light impact on selections, medium on confirmations

### Component Patterns

- **Large touch targets:** Minimum 48px height for all interactive elements. Buttons: 56px height.
- **Cards:** Rounded corners (16px), bgCard background, 1px border.
- **Chips (multi-select):** Pill-shaped, 40px height, chipDefault background → chipSelected when active.
- **Sliders:** Custom styled, large thumb (28px), purple accent.
- **Full-screen flows:** Check-in and guided activities use full-screen pages, one question/step at a time, with a subtle progress indicator at top.

---

## Navigation & App Structure

### Tab Navigator (3 tabs)

| Tab | Icon | Screen | Route |
|-----|------|--------|-------|
| Tonight | Moon/Home icon | Home screen | `/(tabs)/` |
| Toolkit | Grid/Compass icon | Browse activities | `/(tabs)/toolkit` |
| Evenings | Calendar/Chart icon | History & patterns | `/(tabs)/history` |

Tab bar: bgSecondary background, purple active tint, textMuted inactive tint.

### Modal/Stack Screens (pushed on top of tabs)

| Screen | Route | Type |
|--------|-------|------|
| Check-In Flow | `/check-in` | Full-screen modal |
| Guided Mindful Eating | `/mindful-eat` | Full-screen modal |
| Guided Breathing | `/guided-breathing` | Full-screen modal |
| Guided Grounding (5-4-3-2-1) | `/guided-grounding` | Full-screen modal |
| Progressive Muscle Relaxation | `/guided-pmr` | Full-screen modal |
| Post-Activity Reflection | `/post-activity` | Full-screen modal |
| SOS Mode | `/sos` | Full-screen modal |
| Routine Builder | `/routine` | Full-screen modal |
| Morning Check | `/morning-check` | Full-screen modal |
| Onboarding Welcome | `/onboarding/welcome` | Full-screen (no tabs) |
| Onboarding How It Works | `/onboarding/how-it-works` | Full-screen (no tabs) |
| Onboarding Setup | `/onboarding/setup` | Full-screen (no tabs) |
| Settings | `/settings` | Stack push |

---

## File Structure

```
mindful-evenings/
├── app/
│   ├── _layout.tsx                    # Root layout — font loading, onboarding gate
│   ├── (tabs)/
│   │   ├── _layout.tsx                # Tab navigator config
│   │   ├── index.tsx                  # Tonight (home)
│   │   ├── toolkit.tsx                # Browse all activities
│   │   └── history.tsx                # History & patterns
│   ├── check-in.tsx                   # Evening check-in flow (3 steps)
│   ├── mindful-eat.tsx                # Guided mindful eating walkthrough
│   ├── guided-breathing.tsx           # 4-7-8 breathing with animation + haptics
│   ├── guided-grounding.tsx           # 5-4-3-2-1 grounding exercise
│   ├── guided-pmr.tsx                 # Progressive muscle relaxation
│   ├── post-activity.tsx              # "How are you feeling now?" reflection
│   ├── sos.tsx                        # SOS mode — quick relief
│   ├── routine.tsx                    # Wind-down routine builder
│   ├── morning-check.tsx              # Morning mirror check-in
│   ├── settings.tsx                   # Settings + about + NEDA helpline
│   └── onboarding/
│       ├── welcome.tsx                # Screen 1
│       ├── how-it-works.tsx           # Screen 2
│       └── setup.tsx                  # Screen 3 — time picker
├── components/
│   ├── Button.tsx                     # Primary/secondary/ghost button
│   ├── Card.tsx                       # Standard card component
│   ├── Chip.tsx                       # Selectable chip (emotions, needs)
│   ├── ChipGroup.tsx                  # Multi-select chip group
│   ├── Slider.tsx                     # Custom styled slider
│   ├── ProgressDots.tsx               # Step progress indicator
│   ├── BreathingCircle.tsx            # Animated breathing visualization
│   ├── CravingTimer.tsx               # SOS "ride the wave" countdown
│   ├── TimeGreeting.tsx               # Time-aware greeting text
│   ├── ActivityCard.tsx               # Card for guided activities
│   ├── RoutineStep.tsx                # Single step in routine builder
│   ├── InsightCard.tsx                # Pattern insight display
│   └── SOSButton.tsx                  # Floating SOS access button
├── constants/
│   ├── Colors.ts                      # All colors (defined above)
│   └── Copy.ts                        # ALL user-facing text — single source of truth
├── hooks/
│   ├── useCheckIn.ts                  # Check-in state management & persistence
│   ├── useRoutine.ts                  # Routine builder state & notifications
│   ├── useStorage.ts                  # Generic AsyncStorage hook
│   └── useTimeGreeting.ts             # Returns appropriate greeting for current hour
├── utils/
│   ├── storage.ts                     # AsyncStorage wrapper (get/set/remove/getAll)
│   ├── insights.ts                    # Pattern detection from check-in history
│   ├── notifications.ts              # Push notification scheduling helpers
│   └── haptics.ts                     # Haptic feedback helpers
├── types/
│   └── index.ts                       # All TypeScript type definitions
├── assets/
│   └── fonts/                         # Inter font files
├── app.json                           # Expo config
├── package.json
└── tsconfig.json
```

---

## Type Definitions

**`types/index.ts`**

```typescript
// === Emotions & Body States ===

export type Emotion =
  | 'tired' | 'wired' | 'bored' | 'stressed' | 'lonely'
  | 'restless' | 'content' | 'anxious' | 'sad' | 'numb'
  | 'wound-up' | 'other';

export type BodyState =
  | 'low-energy' | 'tense' | 'restless' | 'hungry'
  | 'full' | 'light' | 'neutral';

export type Need = 'rest' | 'connection' | 'comfort' | 'accomplishment' | 'play' | 'peace';

export type DesiredFeeling =
  | 'settled' | 'cared-for' | 'satisfied'
  | 'ready-for-sleep' | 'lighter' | 'at-peace';

export type CheckInResponse = 'eat-with-intention' | 'try-something-else' | 'wind-down';

// === Check-In Data ===

export interface CheckInData {
  id: string;                    // UUID
  timestamp: number;             // Unix ms
  date: string;                  // YYYY-MM-DD

  // Step 1: Notice
  emotions: Emotion[];
  bodyStates: BodyState[];
  hungerLevel: number;           // 0-10

  // Step 2: Understand
  needs: Need[];
  desiredFeeling: DesiredFeeling;

  // Step 3: Respond
  response: CheckInResponse;
  activityChosen?: string;       // activity ID if they chose one

  // Post-activity
  postFeeling?: number;          // 1-5 how they feel after
  completed: boolean;
}

// === Morning Check ===

export interface MorningCheckData {
  id: string;
  timestamp: number;
  date: string;                  // YYYY-MM-DD (matches previous evening)
  sleepQuality: number;          // 1-5
  morningFeeling: number;        // 1-5
}

// === Activities ===

export type ActivityCategory = Need; // activities map to needs

export interface Activity {
  id: string;
  name: string;
  description: string;           // Copy from Copy.ts
  category: ActivityCategory;
  durationMinutes: number;       // 3-10
  premium: boolean;              // false = free tier
  route: string;                 // expo-router path
}

// === Routine ===

export interface RoutineStep {
  id: string;
  time: string;                  // HH:MM (24h)
  activityId?: string;           // links to Activity
  label: string;                 // custom label or activity name
  completed: boolean;
}

export interface Routine {
  steps: RoutineStep[];
  active: boolean;
}

// === Insights ===

export interface Insight {
  id: string;
  text: string;                  // Human-readable sentence
  generatedDate: string;
  type: 'sleep' | 'emotion' | 'activity' | 'pattern';
}

// === User Settings ===

export interface UserSettings {
  onboardingComplete: boolean;
  checkInTime: string;           // HH:MM default "20:00"
  morningCheckTime: string;      // HH:MM default "07:30"
  morningCheckEnabled: boolean;
  routineEnabled: boolean;
  premiumActive: boolean;
  weeklyCheckInCount: number;    // for free tier gating
  weekStartDate: string;         // YYYY-MM-DD for weekly reset
}
```

---

## Feature Specs

### 1. Onboarding

**Route:** `/onboarding/welcome` → `/onboarding/how-it-works` → `/onboarding/setup`

Show only on first launch (check `UserSettings.onboardingComplete`).

**Screen 1 — Welcome (`welcome.tsx`)**
- Full-screen, gradient background
- Large text (h1): Copy from `Copy.onboarding.welcome.title`
- Body text: Copy from `Copy.onboarding.welcome.body`
- Button: "Next" → navigates to how-it-works

```
Title: "Your evenings are about to change."
Body: "Evenings are hard. Not because you lack willpower. Because by 8pm, you've used up most of your decision-making energy, and your brain is looking for the easiest reward it can find. Usually that's food or your phone.

This app helps you figure out what you actually need — and gives you something better."
```

**Screen 2 — How It Works (`how-it-works.tsx`)**
- Three-step visual (icons + short text)
- Copy from `Copy.onboarding.howItWorks`

```
Title: "Here's how it works."
Step 1: "We check in. You tell us what's going on."
Step 2: "We suggest something that might actually help."
Step 3: "If food is what you want, we'll help you enjoy it. If it's something else, we've got you."
```

**Screen 3 — Setup (`setup.tsx`)**
- Time picker for evening check-in time (default 8:00 PM)
- Toggle for morning check-in (default on, 7:30 AM)
- Button: "Let's get started" → sets `onboardingComplete: true`, schedules notifications, navigates to tabs

```
Title: "One last thing."
Body: "When should we check in with you?"
Label: "Evening check-in"
Morning toggle label: "Morning check (10 seconds, totally optional)"
Button: "Let's get started"
```

---

### 2. Tonight (Home Screen)

**Route:** `/(tabs)/index.tsx`

**Layout (top to bottom):**

1. **Time-aware greeting** (h1) — uses `useTimeGreeting()` hook
2. **Check-in CTA card** — large, prominent
   - If no check-in today: "Ready to check in?" with purple button
   - If checked in today: "You checked in tonight." with summary of what they chose
   - If premium gated (3/week used): "You've used your 3 free check-ins this week. [Upgrade]"
3. **Current routine status** (if routine is active)
   - Shows next upcoming step: "Next up: Breathing exercise at 9:15 PM"
   - Or completed: "Routine done for tonight. Nice."
4. **Quick activity suggestions** — 2-3 cards based on time of day
5. **SOS button** — fixed position, bottom-right or prominent placement. Always accessible.

**SOSButton component:** Floating action button or prominent card. Label: "Need help right now?" Always available regardless of premium status.

---

### 3. Evening Check-In (THE CORE FEATURE)

**Route:** `/check-in.tsx`

Full-screen modal. One question per screen. Horizontal slide transitions between steps. Progress dots at top (3 steps).

**Step 1 — Notice**

Screen 1a: Emotions
```
Title: "What's going on right now?"
Subtitle: "Pick as many as you want."
```
- ChipGroup with multi-select: Tired, Wired, Bored, Stressed, Lonely, Restless, Content, Anxious, Sad, Numb, Wound up, Other
- Light haptic on each selection
- "Next" button (enabled when ≥1 selected)

Screen 1b: Body states
```
Title: "What's your body telling you?"
```
- ChipGroup with multi-select: Low energy, Tense, Restless, Hungry, Full, Light, Neutral
- "Next" button

Screen 1c: Hunger scale
```
Title: "Where are you on the hunger scale?"
Subtitle: "0 = not hungry at all. 10 = ravenous."
```
- Custom Slider (0-10), large thumb, labels at 0/5/10
- "Next" button

**Step 2 — Understand**

Screen 2a: Needs
```
Title: "What do you actually need tonight?"
Subtitle: "Not what you think you should need."
```
- ChipGroup with multi-select (max 2): Rest, Connection, Comfort, Accomplishment, Play, Peace
- "Next" button

Screen 2b: Desired feeling
```
Title: "How do you want to feel in 30 minutes?"
```
- Single-select chips: Settled, Cared for, Satisfied, Ready for sleep, Lighter, At peace
- "Next" button

**Step 3 — Respond**

Screen 3: Suggestions
```
Title: "What feels right?"
```

Three option cards based on their answers:

**Option A: "Eat — with intention"**
- Subtitle: "We'll walk you through it."
- Routes to `/mindful-eat`

**Option B: "Try something else first"**
- Subtitle: Personalized based on selected needs (e.g., "Something for rest" → shows breathing, body scan)
- Shows 2-3 suggested activities as cards
- Tapping a card routes to that activity

**Option C: "I just want to wind down"**
- Subtitle: "Let's ease into the rest of your night."
- Routes to routine flow or a quick wind-down sequence

**Permission reminder** (shown at bottom of Step 3, always):
```
"Eating is always an option. This isn't about stopping you. It's about making sure whatever you do next actually gives you what you're looking for."
```

**Suggestion logic** (for Option B):

| Need | Suggested Activities |
|------|---------------------|
| Rest | 4-7-8 Breathing, Body scan |
| Comfort | Mindful eating, Cup of tea ritual, Cozy setup |
| Connection | Conversation starters, Gratitude reflection |
| Accomplishment | Quick wins, Tomorrow prep |
| Play | Fun prompts, Creative micro-activities |
| Peace | 5-4-3-2-1 Grounding, PMR, Simple meditation |

**Data:** Save CheckInData to AsyncStorage on completion. Increment `weeklyCheckInCount`.

---

### 4. Guided Activities

Each guided activity follows the same structure:

1. **Intro screen:** Activity name, duration, brief description. "Start" button.
2. **Step-by-step guided screens:** One instruction per screen. Auto-advance or manual "Next." Timer/progress visible.
3. **Completion → Route to `/post-activity`**

#### 4a. Guided Mindful Eating (`/mindful-eat`)

Duration: ~5 minutes. 7 steps.

```
Step 1: "First, get your food. Whatever you want. No rules here."
Step 2: "Before you eat, just look at it for a second. Notice the colors, the shape. Weird? Maybe. Just try it."
Step 3: "Take a smell. What do you notice?"
Step 4: "Take one bite. Don't chew yet — just let it sit there. What do you taste?"
Step 5: "Now chew. Slowly. Notice the texture, the flavor changing."
Step 6: "Before your next bite, check in: still hungry? Still enjoying it? Both are fine."
Step 7: "Keep going at whatever pace feels right. When you're done — or when you've had enough — you're done."
```

End: "How was that?" → route to `/post-activity`

#### 4b. Guided Breathing (`/guided-breathing`)

Duration: 3-5 minutes. Uses `BreathingCircle` component.

**4-7-8 Breathing Pattern:**
- Inhale: 4 seconds (circle expands)
- Hold: 7 seconds (circle holds at full size)
- Exhale: 8 seconds (circle contracts)
- Repeat 4-8 cycles

```
Intro: "This one's simple. Breathe in for 4. Hold for 7. Out for 8. The app will guide you — just follow the circle."
```

- Animated circle: scale 0.4 → 1.0 on inhale, hold, 1.0 → 0.4 on exhale
- Text overlay on circle: "Breathe in..." / "Hold..." / "Breathe out..."
- Haptic pulse at start of each phase (light impact)
- Cycle counter: "3 of 6"
- Option to add more cycles or end early

End → `/post-activity`

#### 4c. Guided Grounding — 5-4-3-2-1 (`/guided-grounding`)

Duration: ~3 minutes. Step-by-step.

```
Intro: "This one gets you out of your head and into the room. Ready?"
Step 1: "Name 5 things you can see right now." (pause 15s)
Step 2: "4 things you can touch." (pause 12s)
Step 3: "3 things you can hear." (pause 10s)
Step 4: "2 things you can smell." (pause 8s)
Step 5: "1 thing you can taste." (pause 5s)
Outro: "You're here. Right here. That's enough."
```

Each step: display the prompt, show a countdown timer, light haptic at start. User can tap "Next" early.

End → `/post-activity`

#### 4d. Progressive Muscle Relaxation (`/guided-pmr`)

Duration: ~8 minutes. Body part sequence.

```
Intro: "We're going to tense and release each muscle group. Sounds weird, works great. Let's go."
```

Body parts sequence (for each: tense 5s, release 10s):
1. Feet — "Curl your toes tight... and release."
2. Legs — "Tighten your calves and thighs... and let go."
3. Stomach — "Tighten your core... and release."
4. Hands — "Make fists... and open."
5. Arms — "Tighten your biceps... and drop."
6. Shoulders — "Shrug up to your ears... and let them fall."
7. Face — "Scrunch everything... and relax."

```
Outro: "Notice how your body feels now compared to when we started."
```

Haptic: medium impact at "tense," light at "release."

End → `/post-activity`

#### 4e. Additional Activities (simpler screens, text-guided)

These don't need complex animations — just guided text cards:

| Activity | Category | Duration | Description |
|----------|----------|----------|-------------|
| Cup of tea ritual | Comfort | 5 min | Step-by-step making & enjoying tea mindfully |
| Cozy environment setup | Comfort | 3 min | Dim lights, adjust temp, set up your space |
| Conversation starters | Connection | 5 min | Random prompts to discuss with someone |
| Gratitude reflection | Connection | 3 min | "Name 3 things from today. They don't have to be big." |
| Quick wins | Accomplishment | 5 min | Pick from: tidy one surface, prep tomorrow's outfit, reply to one text, set one intention |
| Tomorrow prep | Accomplishment | 5 min | "What's one thing you can do now to make tomorrow easier?" |
| Fun prompts | Play | 5 min | Random creative/silly prompts |
| Simple meditation | Peace | 5 min | Basic breath-focused sitting, minimal guidance |

Each follows: intro → guided steps → end → `/post-activity`

#### 4f. Post-Activity Reflection (`/post-activity`)

**Route params:** receives `activityId` and `checkInId` (if from check-in flow)

```
Title: "How are you feeling now compared to when we started?"
```

- 5-point scale (emoji or numbered): 1 = Worse, 2 = Same, 3 = A little better, 4 = Better, 5 = Much better
- Optional: one-tap mood chip (same emotion list)
- "Done" button → saves postFeeling to CheckInData, returns to Tonight tab

```
Closing line (varies):
- "Nice. See you tomorrow night."
- "That's a wrap. Sleep well."
- "Done. Your evening, your way."
```

---

### 5. SOS Mode

**Route:** `/sos.tsx`

Accessible from anywhere (SOSButton on Tonight screen + option to add to all screens).

**No check-in required.** Skips straight to help.

**Layout:**

```
Title: "Hey. Let's take a breath."
Subtitle: "Pick whatever you need right now."
```

Four option cards:

1. **"Ground me" (60 seconds)**
   - Quick 5-4-3-2-1 grounding — abbreviated version
   - 60-second timer
   
2. **"Help me breathe"**
   - Routes to `/guided-breathing` with a 3-minute default

3. **"Ride the wave" (craving timer)**
   - `CravingTimer` component
   - 20-minute countdown (urges typically pass in 15-20 min)
   - Display: "Most urges pass in 15-20 minutes. Let's see."
   - Progress ring animation
   - Can exit early: "Feeling better?" / "Still want to eat? That's fine too." → routes to `/mindful-eat`

4. **"I want to eat — help me enjoy it"**
   - Routes directly to `/mindful-eat`

**SOS is ALWAYS free.** Never gated behind premium.

---

### 6. Wind-Down Routine Builder

**Route:** `/routine.tsx`

**Building the routine:**
```
Title: "Build your wind-down."
Subtitle: "Pick what works for you. We'll nudge you at the right times."
```

- User adds steps from a menu of activities + custom labels
- Each step gets a time (time picker)
- Drag to reorder
- Default template offered:
  - 8:30 PM: Check-in
  - 9:00 PM: Phone on Do Not Disturb
  - 9:15 PM: Breathing exercise
  - 9:30 PM: Read / journal
  - 10:00 PM: Lights out

**Running the routine:**
- Tonight tab shows current routine progress
- Notifications at each step time
- If user misses a step:
  ```
  "Missed your 9pm wind-down? That's fine. Want to do a quick 5-minute version instead?"
  ```
- Tapping a routine step that has an associated activity opens that activity

**Premium feature.** Free users see routine builder with "Upgrade to unlock" overlay.

---

### 7. Toolkit Screen

**Route:** `/(tabs)/toolkit.tsx`

Browse all activities by category.

```
Title: "Your toolkit."
Subtitle: "Everything in one place."
```

**Layout:** Section headers by category (Rest, Comfort, Connection, Accomplishment, Play, Peace). Each section has horizontal-scrolling `ActivityCard` components.

Each card shows:
- Activity name
- Duration
- Premium lock icon if applicable
- Tap → opens that activity

**Free tier:** Show all activities but lock premium ones with upgrade prompt.

---

### 8. Morning Mirror

**Route:** `/morning-check.tsx`

**Triggered by:** Push notification at user's set morning time (default 7:30 AM). Optional — can be disabled.

```
Title: "Morning. Quick one."
```

Question 1: "How'd you sleep?" — 1-5 scale (moon icons or stars)
Question 2: "How do you feel right now?" — 1-5 scale

```
Done message: "Got it. Have a good one."
```

Total time: ~10 seconds. Save as `MorningCheckData`.

**Premium feature.**

---

### 9. Evenings (History & Patterns)

**Route:** `/(tabs)/history.tsx`

**Layout:**

**Section 1: Recent evenings**
- List of past check-ins, most recent first
- Each row: date, primary emotion(s), what they chose (ate/activity/wind-down), post-feeling
- Tap to expand details

**Section 2: Patterns (unlocked after 7+ check-ins)**

```
Title: "What we're noticing."
Subtitle: "Based on your last [N] check-ins."
```

`InsightCard` components displaying insights as sentences:

Insight generation logic (`utils/insights.ts`):

1. **Sleep correlation:** Compare morning sleep scores with previous evening activities. "You sleep best on nights when you do the breathing exercise."
2. **Emotion frequency:** Most common emotions + day-of-week patterns. "Stress shows up most on weekday evenings."
3. **Activity effectiveness:** Compare post-activity feeling scores. "When you try [activity], you feel better afterward 70% of the time."
4. **Need patterns:** Most commonly selected needs. "You usually need comfort or rest. That's worth knowing."
5. **Hunger patterns:** Average hunger levels by day/time. "Your hunger is usually highest around 9pm on weekdays."

Generate insights fresh each time the screen loads. Only show insights with enough data (minimum 7 check-ins, 3+ data points for any specific pattern).

**Section 3: Streak**
- Simple counter: "You've checked in [N] evenings this week."
- No gamification language. No "streak" emphasis. Just a number.

**Patterns and history are premium features** (except basic history showing last 3 check-ins for free).

---

## constants/Copy.ts Reference

> This file is the **single source of truth** for all user-facing text. Every string displayed to the user MUST come from this file. Components reference `Copy.sectionName.key`.

Structure the file as a deeply nested object:

```typescript
export const Copy = {
  // Onboarding
  onboarding: {
    welcome: {
      title: "Your evenings are about to change.",
      body: "Evenings are hard. Not because you lack willpower...", // full text above
    },
    howItWorks: {
      title: "Here's how it works.",
      steps: [
        "We check in. You tell us what's going on.",
        "We suggest something that might actually help.",
        "If food is what you want, we'll help you enjoy it. If it's something else, we've got you.",
      ],
    },
    setup: {
      title: "One last thing.",
      body: "When should we check in with you?",
      eveningLabel: "Evening check-in",
      morningLabel: "Morning check (10 seconds, totally optional)",
      button: "Let's get started",
    },
  },

  // Greetings (keyed by time range)
  greetings: {
    earlyEvening: "Hey. How's your evening going?",
    midEvening: "Hey. Winding down or just getting started?",
    lateNight: "Still up? No judgment. Let's check in.",
    veryLate: "Late night, huh? What's keeping you up?",
  },

  // Check-in flow
  checkIn: {
    notice: {
      emotionsTitle: "What's going on right now?",
      emotionsSubtitle: "Pick as many as you want.",
      bodyTitle: "What's your body telling you?",
      hungerTitle: "Where are you on the hunger scale?",
      hungerSubtitle: "0 = not hungry at all. 10 = ravenous.",
    },
    understand: {
      needsTitle: "What do you actually need tonight?",
      needsSubtitle: "Not what you think you should need.",
      feelingTitle: "How do you want to feel in 30 minutes?",
    },
    respond: {
      title: "What feels right?",
      optionEat: "Eat — with intention",
      optionEatSub: "We'll walk you through it.",
      optionTry: "Try something else first",
      optionWindDown: "I just want to wind down",
      optionWindDownSub: "Let's ease into the rest of your night.",
      permission: "Eating is always an option. This isn't about stopping you. It's about making sure whatever you do next actually gives you what you're looking for.",
    },
    emotions: {
      tired: "Tired", wired: "Wired", bored: "Bored", stressed: "Stressed",
      lonely: "Lonely", restless: "Restless", content: "Content", anxious: "Anxious",
      sad: "Sad", numb: "Numb", "wound-up": "Wound up", other: "Other",
    },
    bodyStates: {
      "low-energy": "Low energy", tense: "Tense", restless: "Restless",
      hungry: "Hungry", full: "Full", light: "Light", neutral: "Neutral",
    },
    needs: {
      rest: "Rest", connection: "Connection", comfort: "Comfort",
      accomplishment: "Accomplishment", play: "Play", peace: "Peace",
    },
    desiredFeelings: {
      settled: "Settled", "cared-for": "Cared for", satisfied: "Satisfied",
      "ready-for-sleep": "Ready for sleep", lighter: "Lighter", "at-peace": "At peace",
    },
  },

  // Tonight screen
  tonight: {
    checkInCta: "Ready to check in?",
    checkInDone: "You checked in tonight.",
    sosLabel: "Need help right now?",
    routineNext: "Next up:",
    routineDone: "Routine done for tonight. Nice.",
    premiumGate: "You've used your 3 free check-ins this week.",
    upgradeButton: "Unlock unlimited",
  },

  // SOS
  sos: {
    title: "Hey. Let's take a breath.",
    subtitle: "Pick whatever you need right now.",
    ground: "Ground me",
    groundSub: "60 seconds. Get out of your head.",
    breathe: "Help me breathe",
    breatheSub: "Simple breathing exercise.",
    wave: "Ride the wave",
    waveSub: "Most urges pass in 15-20 minutes.",
    waveTimer: "Let's see if this one does too.",
    waveCheck: "Feeling better?",
    waveEat: "Still want to eat? That's fine too.",
    eat: "I want to eat — help me enjoy it",
    eatSub: "We'll make it intentional.",
  },

  // Guided activities
  activities: {
    mindfulEat: {
      intro: "Whatever you're eating, let's enjoy it.",
      steps: [
        "First, get your food. Whatever you want. No rules here.",
        "Before you eat, just look at it for a second. Notice the colors, the shape. Weird? Maybe. Just try it.",
        "Take a smell. What do you notice?",
        "Take one bite. Don't chew yet — just let it sit there. What do you taste?",
        "Now chew. Slowly. Notice the texture, the flavor changing.",
        "Before your next bite, check in: still hungry? Still enjoying it? Both are fine.",
        "Keep going at whatever pace feels right. When you're done — or when you've had enough — you're done.",
      ],
    },
    breathing: {
      intro: "This one's simple. Breathe in for 4. Hold for 7. Out for 8. The app will guide you — just follow the circle.",
      inhale: "Breathe in...",
      hold: "Hold...",
      exhale: "Breathe out...",
    },
    grounding: {
      intro: "This one gets you out of your head and into the room. Ready?",
      steps: [
        "Name 5 things you can see right now.",
        "4 things you can touch.",
        "3 things you can hear.",
        "2 things you can smell.",
        "1 thing you can taste.",
      ],
      outro: "You're here. Right here. That's enough.",
    },
    pmr: {
      intro: "We're going to tense and release each muscle group. Sounds weird, works great. Let's go.",
      tense: "Tighten...",
      release: "And release.",
      parts: [
        { tense: "Curl your toes tight...", release: "and release." },
        { tense: "Tighten your calves and thighs...", release: "and let go." },
        { tense: "Tighten your core...", release: "and release." },
        { tense: "Make fists...", release: "and open." },
        { tense: "Tighten your biceps...", release: "and drop." },
        { tense: "Shrug up to your ears...", release: "and let them fall." },
        { tense: "Scrunch everything...", release: "and relax." },
      ],
      outro: "Notice how your body feels now compared to when we started.",
    },
    teaRitual: {
      intro: "Let's make a cup of tea. Not because you have to — because it's nice.",
      steps: [
        "Pick your tea. Whatever sounds good.",
        "While the water heats, just stand there. No phone. Just waiting.",
        "Pour the water. Watch the color change.",
        "Hold the mug. Feel the warmth.",
        "First sip. Don't rush it.",
        "Keep going. This is your time.",
      ],
    },
    cozySetup: {
      intro: "Let's set up your space.",
      steps: [
        "Dim the lights. Or turn off the overhead and use a lamp.",
        "Temperature check — too warm? Too cold? Adjust.",
        "Grab a blanket if you want one.",
        "Put your phone somewhere you can't reach it from the couch. (Yeah, really.)",
        "That's it. You made your space better. Enjoy it.",
      ],
    },
    conversationStarters: {
      intro: "Got someone nearby? Here's something to talk about.",
      prompts: [
        "What was the best part of today?",
        "What's something you're looking forward to this week?",
        "If you could be anywhere right now, where?",
        "What's something you've been thinking about but haven't said?",
        "If tomorrow could go perfectly, what would it look like?",
      ],
    },
    gratitude: {
      intro: "Name 3 things from today. They don't have to be big.",
      prompt: "What's one more?",
      outro: "That's it. Simple stuff. But it adds up.",
    },
    quickWins: {
      intro: "Pick one. Just one. Then you're done.",
      options: [
        "Tidy one surface.",
        "Prep tomorrow's outfit.",
        "Reply to one text you've been ignoring.",
        "Set one intention for tomorrow.",
        "Put one thing away.",
        "Write tomorrow's top priority.",
      ],
    },
    tomorrowPrep: {
      intro: "What's one thing you can do now to make tomorrow morning easier?",
      prompt: "Just one thing. That's it.",
      outro: "Future you says thanks.",
    },
    funPrompts: {
      intro: "Let's do something pointless and fun.",
      prompts: [
        "Draw something with your non-dominant hand. Don't think about it.",
        "Write a haiku about your day.",
        "What would your autobiography be called?",
        "Describe your day as a movie genre.",
        "If your pet/plant could talk, what would they say right now?",
      ],
    },
    meditation: {
      intro: "Nothing fancy. Just sit and breathe for a few minutes.",
      steps: [
        "Find a comfortable position. Eyes open or closed — whatever.",
        "Breathe normally. Just notice it.",
        "When your mind wanders — and it will — just come back to the breath.",
        "That's it. That's the whole thing.",
      ],
    },
  },

  // Post-activity
  postActivity: {
    title: "How are you feeling now compared to when we started?",
    scale: ["Worse", "Same", "A little better", "Better", "Much better"],
    closings: [
      "Nice. See you tomorrow night.",
      "That's a wrap. Sleep well.",
      "Done. Your evening, your way.",
    ],
  },

  // Routine
  routine: {
    buildTitle: "Build your wind-down.",
    buildSubtitle: "Pick what works for you. We'll nudge you at the right times.",
    missedStep: "Missed your {time} wind-down? That's fine. Want to do a quick 5-minute version instead?",
    defaultSteps: [
      { label: "Check-in", time: "20:30" },
      { label: "Phone on Do Not Disturb", time: "21:00" },
      { label: "Breathing exercise", time: "21:15" },
      { label: "Read / journal", time: "21:30" },
      { label: "Lights out", time: "22:00" },
    ],
  },

  // Morning mirror
  morning: {
    title: "Morning. Quick one.",
    sleep: "How'd you sleep?",
    feeling: "How do you feel right now?",
    done: "Got it. Have a good one.",
  },

  // History / Patterns
  history: {
    title: "Your evenings.",
    patternsTitle: "What we're noticing.",
    patternsSubtitle: "Based on your last {count} check-ins.",
    needMoreData: "Check in a few more times and we'll start spotting patterns.",
    streakLabel: "Checked in {count} evenings this week.",
  },

  // Toolkit
  toolkit: {
    title: "Your toolkit.",
    subtitle: "Everything in one place.",
  },

  // Settings
  settings: {
    title: "Settings",
    checkInTime: "Evening check-in time",
    morningTime: "Morning check time",
    morningEnabled: "Morning check",
    premium: "Upgrade to premium",
    about: "About",
    disclaimer: "This app is not a substitute for professional mental health care. If you're struggling with disordered eating, please reach out to the National Eating Disorders Association helpline.",
    nedaPhone: "1-800-931-2237",
    nedaLabel: "NEDA Helpline",
  },

  // Notifications
  notifications: {
    eveningCheckIn: "Hey, how's your evening going?",
    morningCheck: "Morning. Quick check — 10 seconds.",
    routineStep: "Time for: {label}",
    routineMissed: "Missed your wind-down? No big deal. Want a quick version?",
  },

  // Premium / Paywall
  premium: {
    title: "Get more from your evenings.",
    subtitle: "Unlimited check-ins, all activities, patterns, routines, and more.",
    monthly: "$6.99/month",
    yearly: "$49.99/year",
    yearlyNote: "That's about $4/month.",
    restore: "Restore purchase",
    features: [
      "Unlimited evening check-ins",
      "All guided activities",
      "Wind-down routine builder",
      "Patterns & insights",
      "Morning mirror",
    ],
  },
};
```

---

## Data & Storage

### `utils/storage.ts`

AsyncStorage wrapper with typed helpers:

```typescript
// Key constants
const KEYS = {
  SETTINGS: 'mindful_settings',
  CHECK_INS: 'mindful_checkins',      // JSON array of CheckInData
  MORNING_CHECKS: 'mindful_morning',   // JSON array of MorningCheckData
  ROUTINE: 'mindful_routine',          // JSON Routine object
};

// Core functions
async function getSettings(): Promise<UserSettings>
async function saveSettings(settings: Partial<UserSettings>): Promise<void>
async function getCheckIns(): Promise<CheckInData[]>
async function saveCheckIn(data: CheckInData): Promise<void>
async function getMorningChecks(): Promise<MorningCheckData[]>
async function saveMorningCheck(data: MorningCheckData): Promise<void>
async function getRoutine(): Promise<Routine | null>
async function saveRoutine(routine: Routine): Promise<void>

// Weekly reset logic
async function checkWeeklyReset(): Promise<void>
// If current date > 7 days past weekStartDate, reset weeklyCheckInCount to 0
```

### `utils/insights.ts`

```typescript
function generateInsights(
  checkIns: CheckInData[],
  morningChecks: MorningCheckData[]
): Insight[]
```

Logic:
- Requires minimum 7 check-ins
- **Sleep correlation:** Match morning sleep scores to previous evening's activities. If activity X correlates with higher sleep scores (>0.5 points avg difference), generate insight.
- **Emotion frequency:** Count emotion occurrences, group by weekday vs weekend. Report most common + any day-of-week pattern.
- **Activity effectiveness:** Average post-activity feelings per activity type. Compare to baseline (no activity). Report activities that score >3.5 avg.
- **Need patterns:** Most frequently selected needs. Report top 2.
- Return array of 3-5 Insight objects with human-readable `text` strings using the app's voice.

---

## Notifications

### `utils/notifications.ts`

```typescript
async function scheduleEveningCheckIn(time: string): Promise<void>
// Daily repeating notification at user's chosen time
// Content: Copy.notifications.eveningCheckIn

async function scheduleMorningCheck(time: string): Promise<void>
// Daily repeating notification at user's chosen time
// Content: Copy.notifications.morningCheck

async function scheduleRoutineSteps(routine: Routine): Promise<void>
// Individual notifications for each routine step
// Content: Copy.notifications.routineStep with label interpolated

async function cancelAllNotifications(): Promise<void>

async function requestPermissions(): Promise<boolean>
```

Use `expo-notifications` with `Notifications.scheduleNotificationAsync` and daily trigger.

---

## Monetization

### Free Tier
- 3 check-ins per week (tracked via `UserSettings.weeklyCheckInCount`)
- Basic activities: breathing, grounding (always free)
- SOS mode: ALWAYS free, never gated
- History: last 3 check-ins visible

### Premium ($6.99/month or $49.99/year)
- Unlimited check-ins
- All guided activities
- Routine builder
- Patterns & insights
- Morning mirror
- Full history

### Implementation
- For MVP: use a simple boolean flag (`premiumActive`) in settings
- Gate features with a `usePremium()` hook that checks the flag
- Show premium upsell screen when user hits a gated feature
- Actual IAP integration (RevenueCat or expo-in-app-purchases) can be added post-MVP — for now just the gating UI

---

## Build Rules

1. **ALL user-facing copy MUST live in `constants/Copy.ts`.** No hardcoded strings in components. This is non-negotiable.
2. **Read the Voice & Language Guide** before writing ANY user-facing text. Every string must pass the voice rules.
3. **Dark theme only.** No light mode for MVP. This is an evening app.
4. **Animations:** subtle and smooth. Use `react-native-reanimated` with 300-400ms ease-in-out. No spring physics. No bounce.
5. **Check-in = conversation.** One question per full screen. Transitions between steps. Not a scrolling form.
6. **Eating is ALWAYS a valid, judgment-free option.** Every food-related path must include permission language.
7. **No calorie counting. No food logging. No weight tracking. Ever.**
8. **The couch test:** Would someone use this at 9pm on the couch? If it feels like effort, simplify.
9. **Fully offline.** AsyncStorage only. No network calls. No backend.
10. **NEDA helpline** (1-800-931-2237) in Settings/About screen.
11. **Large touch targets.** Minimum 48px interactive elements. Buttons 56px height.
12. **expo-haptics** on all selections and guided exercise transitions.
13. **Generate UUIDs** for check-in IDs using a simple `Date.now().toString(36) + Math.random().toString(36)` pattern (no external UUID library needed).

---

## Quick Start for Codex

```bash
npx create-expo-app mindful-evenings --template blank-typescript
cd mindful-evenings
npx expo install expo-router expo-notifications expo-haptics expo-linear-gradient expo-font @react-native-async-storage/async-storage react-native-reanimated react-native-gesture-handler react-native-safe-area-context
```

Then create the file structure listed above and start building. Begin with:
1. `constants/Colors.ts` and `constants/Copy.ts` (the foundation)
2. `types/index.ts`
3. `app/_layout.tsx` with font loading and onboarding gate
4. Onboarding screens
5. Tab navigator and Tonight screen
6. Check-in flow (the core feature)
7. Guided activities (breathing first — it's the most visual)
8. SOS mode
9. Everything else

---

*End of spec.*
