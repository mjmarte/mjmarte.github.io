# Basketball RPG — Claude Code Instructions

## What This Is

A browser-based basketball RPG where you play as a single player inside a living,
breathing league. The narrative is fully generative — no story is pre-written.
Systems produce state. State produces events. Events produce text via Claude API.
The player never sees the math. They only see the story the math is telling.

This is closer to Crusader Kings II or Dwarf Fortress than a sports game.
Emergence comes from interlocking systems, not authored content.

## Repo Rules

- This repo is PRIVATE. Never reference, link, or suggest exposing it publicly.
- Do not suggest public deploys, public demos, or public URLs unless explicitly asked.
- Do not add analytics, tracking, telemetry, or any third-party service without asking.
- Do not commit .env files, API keys, or secrets under any circumstances.

-----

## Stack

- React + Vite
- Tailwind CSS (core utility classes only)
- Plain JS for all game logic — no sim libraries
- Claude API (claude-sonnet-4-20250514) for all narrative generation
- localStorage for persistence (no backend, no database)
- No backend. Everything runs in the browser.

-----

## Architecture — Four Layers

These layers are built and completed in strict order.
Do not start a higher layer until the layer below it is working and tested.

### Layer 1 — State Engine `src/state/`

Pure JS. No React. No UI. Just data and mutations.
This is ground truth. All other layers read from it, never write around it.

- Defines all schemas (see below)
- Exposes pure functions that mutate state
- Never imports from sim/, narrative/, or components/
- All state lives in a single `gameState` object
- Persisted to localStorage via a save/load module

### Layer 2 — Simulation Engine `src/sim/`

Pure JS. No React. No API calls.
Takes state as input, returns results and state deltas as output.

- All functions are pure — same input always produces same category of output
- Must complete a full game simulation in under 50ms
- Must support fast-forwarding an entire season without blocking the UI
- Reads from state, never writes to it directly — returns deltas for Layer 1 to apply
- Includes: game engine, schedule generator, season progression, draft, trades, free agency

### Layer 3 — Event + Narrative System `src/narrative/`

Watches Layer 1 state for threshold conditions and fires structured events.
Events are then sent to Claude API which returns prose, dialogue, and player choices.

- Event detection is deterministic and rules-based (not random)
- Claude API call is stateless — it receives current state context, returns text
- All narrative output is ephemeral — not stored in gameState, only displayed
- Player choices returned from narrative are fed back as state mutations via Layer 1

### Layer 4 — UI `src/components/`

Display and input only. No game logic. No API calls.
Reads from state. Captures player input. Calls Layer 1 mutations.

-----

## Project Structure

```
src/
  state/
    schema.js          # All type definitions and defaults
    gameState.js        # Single source of truth object
    mutations.js        # Pure functions that mutate gameState
    persistence.js      # localStorage save/load
  sim/
    engine.js           # simulateGame() — core sim loop
    schedule.js         # Season schedule generation
    season.js           # Season progression and phase management
    draft.js            # Draft logic and prospect generation
    transactions.js     # Trades, free agency, contracts
    ratings.js          # Stat calculation helpers
  narrative/
    events.js           # Event detection — watches state for thresholds
    generator.js        # Claude API calls — state in, prose out
    prompts.js          # Prompt templates per event type
    choices.js          # Maps player choices back to state mutations
  components/
    Game/               # In-game turn interface
    Locker/             # Locker room and team screens
    Conference/         # Press conference and dialogue UI
    Season/             # Schedule, standings, season overview
    MyPlayer/           # Player card, stats, reputation axes
  data/
    names.js            # Player name pools
    traits.js           # Full trait definitions and modifiers
    teams.js            # League team configs
    events.js           # Event type definitions and threshold rules
  hooks/
    useGameState.js     # Bridges state and React
    useNarrative.js     # Manages narrative generation flow
```

-----

## Core Schemas

Treat these as contracts. Do not change any schema without confirming first.

### Player

```js
{
  id: string,
  name: string,
  age: number,
  position: "PG" | "SG" | "SF" | "PF" | "C",

  // Core stats (1–99)
  stats: {
    speed: number,
    shooting: number,
    defense: number,
    passing: number,
    stamina: number,
    iq: number,
    clutch: number       // multiplier in high-leverage moments
  },

  // RPG progression
  xp: number,
  level: number,
  traitSlots: number,    // unlocked by level
  traits: string[],      // e.g. ["lockdown", "floor_general", "clutch"]

  // Relational axes (0–100)
  morale: number,
  fatigue: number,       // resets with rest, degrades across games

  // Reputation axes (each -100 to 100)
  reputation: {
    teammateTrust: number,   // do teammates believe in you
    mediaRep: number,        // villain → underdog → hero
    coachRelationship: number,
    leagueNotoriety: number  // rivals play you differently
  },

  // Relationships (playerId → value -100 to 100)
  relationships: {},

  // Contract
  salary: number,
  contractYears: number,

  // Fog of war — only stats that have been scouted are visible to GM view
  scoutedBy: string[]
}
```

### Team

```js
{
  id: string,
  name: string,
  city: string,
  abbreviation: string,
  roster: string[],          // Player ids
  chemistry: {},             // "playerId_playerId" → bonus (-20 to 20)
  tension: number,           // 0–100, locker room health
  budget: number,
  salaryCap: number,
  wins: number,
  losses: number,
  playStyle: string          // affects sim weighting
}
```

### GameResult

```js
{
  id: string,
  homeTeamId: string,
  awayTeamId: string,
  homeScore: number,
  awayScore: number,
  quarterScores: [
    { home: number, away: number }  // x4
  ],
  keyMoments: [],            // 5–8 structured moment objects for narrative
  playerStats: {
    [playerId]: {
      pts: number, reb: number, ast: number,
      stl: number, blk: number, to: number,
      fgPct: number, fatigueDelta: number,
      xpEarned: number
    }
  },
  mvp: string                // playerId
}
```

### Event

```js
{
  id: string,
  type: string,              // e.g. "teammate_confrontation", "media_callout", "coach_benching"
  triggeredBy: string[],     // which state thresholds fired this
  involvedIds: string[],     // playerIds, teamIds
  stateSnapshot: {},         // relevant slice of state at fire time
  urgency: "low" | "medium" | "high" | "critical"
}
```

### Season

```js
{
  year: number,
  schedule: [],              // { homeTeamId, awayTeamId, week, gameId | null }
  results: string[],         // GameResult ids
  standings: [],
  phase: "preseason" | "regular" | "playoffs" | "offseason",
  draftClass: [],            // generated prospects
  freeAgents: string[]       // Player ids
}
```

### MyPlayer

```js
{
  playerId: string,          // ref to Player in gameState
  mpPoints: number,          // MyPlayer Points — currency for unlocks
  choiceHistory: [],         // log of choices made (for narrative context)
  activeArcs: [],            // ongoing narrative threads
  completedArcs: []
}
```

-----

## Simulation Rules

- `simulateGame(homeTeam, awayTeam, gameState)` lives in `src/sim/engine.js`
- Must be a pure function — no side effects, no API calls
- Returns a `GameResult` object and an array of state deltas
- Factors: player stats, morale, fatigue, chemistry, tension, traits, home court (+3–5 pts)
- Clutch stat activates when game is within 5 points in Q4
- Stamina degrades per game. Full recovery requires explicit rest actions
- High tension teams produce more turnovers and fewer assists
- Negative chemistry pairs reduce passing accuracy between those players

-----

## Event System Rules

Events are defined in `src/data/events.js` as threshold rules.
The event watcher in `src/narrative/events.js` checks these after every state mutation.

Example threshold rules:

```js
{
  type: "teammate_confrontation",
  condition: (state) =>
    state.team.tension > 70 &&
    twoPlayersCompetingForRole(state),
  urgency: "high"
},
{
  type: "media_callout",
  condition: (state) =>
    state.myPlayer.reputation.mediaRep > 60 &&
    lastGame.playerStats[myPlayerId].pts < 10,
  urgency: "medium"
},
{
  type: "coach_benching_warning",
  condition: (state) =>
    state.myPlayer.reputation.coachRelationship < -40 &&
    lastThreeGames.avgPts < 12,
  urgency: "critical"
}
```

Rules fire at most once per event type per week of game time unless urgency is critical.

-----

## Narrative Generation Rules

All narrative is generated via Claude API in `src/narrative/generator.js`.

### Structure of every API call

```js
{
  model: "claude-sonnet-4-20250514",
  max_tokens: 500,
  system: `You are the narrator of a basketball RPG. Your job is to translate
           game state into vivid, tense, character-driven prose and dialogue.
           Never break the fourth wall. Never reference stats directly.
           Always end with exactly 2–4 player choices as a JSON array.
           Respond in this format:
           { "prose": "...", "choices": [{ "label": "...", "mutation": "..." }] }`,
  messages: [{
    role: "user",
    content: buildPrompt(event, stateSnapshot, myPlayer.choiceHistory.slice(-5))
  }]
}
```

### Prompt construction rules

- Include: event type, involved players' names and traits, current reputation axes,
  locker room tension, last game result, last 3 choices made by player
- Never include raw numbers — translate to descriptive language in the prompt
  ("morale is low" not "morale: 23")
- Keep prompts under 400 tokens
- Choices must map to real state mutations — do not generate choices that can't be applied

### Narrative tone

- Terse and grounded — not florid
- Characters have voice based on their traits
- Consequences feel real because they feed back into systems
- No choice is clearly "right" — trade-offs always

-----

## RPG Progression Rules

- XP earned from game stats: pts (2xp each), reb (1.5xp), ast (2xp), clutch moments (10xp)
- Level up every 100 XP — unlocks one trait slot
- Traits are selected by the player from a contextual pool (not random)
- Trait pool offered based on playstyle pattern from last 5 games
- MyPlayer Points (MP) earned from: wins, high-leverage moments, narrative choices
- MP spent on: scouting opponents, unlocking dialogue options, negotiating contracts

-----

## GM Metagame Build Order

Build these in strict sequence. Do not start the next until the current is playable.

1. Single game simulation (engine.js working, result displayed)
1. Roster + player cards (UI reads from state)
1. Full season schedule + fast-forward
1. Narrative event system (Layer 3 online)
1. MyPlayer in-game turn layer
1. Draft
1. Free agency + trades
1. Salary cap enforcement
1. Multi-year seasons + legacy tracking

-----

## In-Game Turn Layer

During key moments (flagged by the sim engine as `keyMoment: true`),
the game pauses and presents a possession to the player as a turn.

Turn structure:

```js
{
  situation: string,       // "Down 2, 24 seconds left. Ball in your hands."
  options: [
    { label: "Drive baseline", statWeights: { speed: 0.7, clutch: 0.3 } },
    { label: "Pull-up mid", statWeights: { shooting: 0.6, iq: 0.4 } },
    { label: "Kick out to open man", statWeights: { passing: 0.8, iq: 0.2 } }
  ]
}
```

The sim resolves the chosen option probabilistically using stat weights + a roll.
Result feeds back into GameResult and state deltas.

Key moments are triggered by: clutch time, rivalry games, personal milestones,
high-tension locker room state, playoff situations.

-----

## Dev Conventions

- Every function in `src/sim/` and `src/narrative/` gets a JSDoc comment
- No function longer than 40 lines — extract helpers aggressively
- State mutations always go through `mutations.js` — never mutate gameState directly
- Console.log game events in dev mode with a `[SIM]` or `[EVENT]` prefix
- Write a simple test call at the bottom of each sim module that can be run with `node`
- Ask before installing any npm package

## What NOT To Do

- Do not build UI before the underlying sim logic for that feature works
- Do not write narrative content — the API generates it
- Do not add a backend, database, or any server-side code
- Do not connect to any external API except the Anthropic API
- Do not change any schema without confirming first
- Do not start Layer 3 before Layer 2 passes a full-season fast-forward test
- Do not make any file or URL public
