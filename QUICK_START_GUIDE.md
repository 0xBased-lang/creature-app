# Cell Garden - Quick Start Implementation (Cheat Sheet)
**TL;DR Version | Read in 10 Minutes | Reference During Development**

---

## ONE-PAGE SUMMARY

Cell Garden is a browser-based cell evolution game. Reuse CREATURE's 2,300 LOC (physics, spatial logic), strip 1,100 LOC (all LLM stuff), add 3,500 LOC new game code = Shipped in 6 weeks with AI tools.

**Stack:**
- Frontend: React 18 + Pixi.js + Zustand (180 KB)
- Backend: Rust + Warp WebSocket (reuse CREATURE)
- Database: SQLite on Railway volume
- Deployment: Vercel (frontend) + Railway (backend) = $0/year

---

## CREATURE CODE: COPY/KEEP/KILL

### COPY (As-Is, 2,300 LOC)
```
✓ src/systems/cell.rs (450) - structure, position, neighbors
✓ src/systems/lenia.rs (300) - physics engine
✓ src/systems/colony.rs (550) - clustering, stats (remove API calls)
✓ src/models/types.rs (200) - Coordinates, Cell struct
✓ src/models/constants.rs (100) - constants
✓ src/server.rs (200) - Warp WebSocket setup
```

### KILL (1,100 LOC)
```
✗ src/api/openrouter.rs - DELETE (all LLM stuff)
✗ src/api/gemini.rs - DELETE
✗ src/interface/*.rs - DELETE (TUI stuff)
✗ src/utils/animations.rs - DELETE
```

### REFACTOR (in place)
```
~ src/systems/cell.rs
  - Remove: generate_thought() method
  - Remove: mission_alignment_score field
  + Add: growth_rate, reproduction_threshold

~ src/systems/colony.rs
  - Remove: process_cell_sub_batch(), API calls
  + Keep: neighbor clustering, DFS detection
```

**Time Saved:** 120 hours of physics/networking work

---

## TECH STACK QUICK PICKS

| Layer | Tech | Why | Alternative |
|-------|------|-----|---|
| **Frontend** | React 18 | Best Claude Code support | Vue 3 |
| **Canvas** | Pixi.js | 2D games, 60fps | Three.js (3D) |
| **State** | Zustand | Zero boilerplate, 10KB | Redux |
| **UI** | shadcn/ui | Copy-paste, free | DaisyUI |
| **WebSocket** | Socket.io | Auto-reconnect | Raw WS |
| **Backend** | Rust + Warp | Already have it | Node.js + Express |
| **Database** | SQLite | Zero setup | PostgreSQL (Neon) |
| **Frontend Deploy** | Vercel | React-native, free | Netlify |
| **Backend Deploy** | Railway | No cold starts, $0 | Fly.io |

---

## AI CODE GENERATION: WHO DOES WHAT

### Claude Code (Talk to AI, get code)
```
Best for: Architecture, complete functions, boilerplate
Examples:
  "Generate Zustand store with cells, colonies, resources"
  "Create React component for game canvas with Pixi.js"
  "Write Rust game loop, 30 FPS tick with physics"
  
Time: 50 LOC per request, ~5 min each
Use for: 70% of project
```

### Cursor (Incremental, in-editor AI)
```
Best for: Copy-modify, debugging, file completion
Examples:
  - Complete a function similar to another one
  - Fix TypeScript errors
  - Add feature to existing component
  
Time: ~2 min per iteration
Use for: Polish, debugging, adaptation
```

### V0.dev (UI generator, free limited)
```
Best for: Form layouts, settings panels, leaderboards
Examples:
  - Generate settings UI form
  - Create leaderboard table
  - Build stats panel layout
  
Limit: 2-3 pages before paywall
Use for: Non-game UI only
```

---

## DEPLOYMENT: LITERALLY 10 MINUTES

### Step 1: Frontend (2 minutes)
```bash
# Create React app
npm create vite@latest cell-garden-frontend -- --template react-ts

# Deploy to Vercel
npm i -g vercel
cd cell-garden-frontend
vercel --prod
# Done, live at vercel-auto-domain.vercel.app
```

### Step 2: Backend (3 minutes)
```bash
# Create Rust project
cargo new cell_garden_backend

# Push to GitHub
git init && git remote add origin ...
git push -u origin main

# On Railway
# 1. Connect GitHub repo
# 2. Set environment variable ROCKET_ADDRESS=0.0.0.0
# 3. Done, auto-deploys on git push
```

### Step 3: Connect (2 minutes)
```typescript
// In React, connect to backend
const socket = io('https://your-railway-backend.railway.app', {
  reconnection: true,
});
```

### Step 4: Database (3 minutes, optional)
```bash
# SQLite automatically on Railway volume
# Just use: sqlx + Tokio in Rust
# Leaderboards in-memory, flush to DB every 10s
```

**Total cost:** $0
**Total time:** 10 minutes
**Status:** Live game, ready for users

---

## WEEK-BY-WEEK BUILD PATH

```
WEEK 1: Backend
├─ Day 1: Copy CREATURE code, strip LLM
├─ Day 2: Wire up game loop (30 FPS tick)
├─ Day 3: WebSocket broadcasting (cells only)
└─ Day 4: Deploy to Railway
Result: Server running, sends game state every 30ms

WEEK 2: Frontend + Connect
├─ Day 1: React + Pixi canvas scaffold
├─ Day 2: WebSocket client, receive state
├─ Day 3: Render cells on canvas
└─ Day 4: Deploy to Vercel, wire up
Result: Game renders live cell positions

WEEK 3-4: Gameplay
├─ Energy/death mechanic
├─ Reproduction system
├─ Mutation/genetics
└─ Cell visuals (colors, sizes)
Result: Playable 1st version

WEEK 5-6: Polish
├─ UI panels (stats, leaderboard)
├─ Settings (speed, pause, zoom)
├─ Mobile responsive
└─ Performance tuning
Result: Feature-complete beta

WEEK 7: Launch
├─ Bug fixes
├─ Monitoring setup
├─ Documentation
└─ Deploy to production
Result: Live game
```

---

## CODE GENERATION PROMPTS (Copy-Paste Ready)

### Prompt 1: Full Zustand Store
```
Create a Zustand store for Cell Garden game state.
Include:
- cells: { id, x, y, energy, age, type, color, size }[]
- colonies: { id, name, cellCount, energy }[]
- resources: number
- gameSettings: { speed, isPaused, zoom, selectedCellId }

Actions:
- updateCell(id, data)
- removeCell(id)
- addCell(x, y)
- getColonyStats()

Export useGameStore hook.
```
**Time:** 4 min | **LOC:** 180 | **Quality:** 95%

### Prompt 2: Cell Reproduction Logic
```
Create Rust functions for cell reproduction.
Rules:
- Cell reproduces when energy > 150
- Offspring: energy=40, parent energy-=60
- Mutations: 5% per gene (speed, size, color)
- Return: (new_cell: Cell, updated_parent: Cell)
Include error handling.
```
**Time:** 3 min | **LOC:** 150 | **Quality:** 90%

### Prompt 3: Game Canvas
```
Create React component for Pixi.js game canvas.
Requirements:
- Initialize Pixi Application (full screen)
- Render circles for cells (color based on type)
- Connect to WebSocket (receive game state)
- Update positions @ 60fps
- Handle canvas resize

Use TypeScript.
```
**Time:** 5 min | **LOC:** 220 | **Quality:** 85%

---

## COMMON GOTCHAS (DON'T GET STUCK)

### Issue #1: Async Rust Mess
**Problem:** Tokio, async/await, lifetime errors
**Solution:** Copy game loop from CREATURE exactly, don't optimize
**Time saved:** 20 hours

### Issue #2: WebSocket Serialization
**Problem:** Complex types don't serialize to JSON
**Solution:** Use Serde, derive Serialize/Deserialize on all types
**Time saved:** 10 hours

### Issue #3: React Re-renders
**Problem:** Component re-renders too fast, canvas flickers
**Solution:** Use useRef for Pixi app, don't store in state
**Time saved:** 5 hours

### Issue #4: Pixi Canvas Scaling
**Problem:** Canvas blurry or wrong size on resize
**Solution:** Use app.renderer.resize() on window resize event
**Time saved:** 2 hours

### Issue #5: WebSocket Reconnection
**Problem:** Game freezes when server restarts
**Solution:** Socket.io handles this automatically (use it, not raw WS)
**Time saved:** 8 hours

---

## PERFORMANCE TARGETS

```
Backend
└─ Game loop: <30ms per tick (30 FPS)
   ├─ Cell updates: 5ms
   ├─ Physics: 10ms
   ├─ Serialization: 5ms
   └─ WebSocket send: 2ms

Frontend
└─ Frame: <16ms (60 FPS)
   ├─ React render: 8ms
   ├─ Pixi draw: 6ms
   └─ Event handling: 2ms

Network
└─ WebSocket latency: <50ms
```

If you hit these, you're good. Don't over-optimize until you test.

---

## TESTING (90 SECONDS)

```bash
# Backend: Built-in Rust tests
cargo test

# Frontend: Vitest
npm run test

# Manual: Just play the game
# Open http://localhost:5173
# Spawn 100 cells
# Check FPS in DevTools
```

Coverage targets:
- Core game logic: 80%
- UI: 30% (happy path only)
- Physics: 95%

---

## FINAL CHECKLIST

### Before Week 1
- [ ] GitHub repo created (private)
- [ ] Rust backend scaffolded
- [ ] React frontend scaffolded
- [ ] .cursorrules file created
- [ ] Railway account ready
- [ ] Vercel account ready

### Before Shipping
- [ ] Server deploys to Railway
- [ ] Frontend deploys to Vercel
- [ ] WebSocket connects successfully
- [ ] Cells render on canvas
- [ ] Cells die when energy=0
- [ ] Cells reproduce at energy=150
- [ ] Mobile responsive (tested on iPhone)
- [ ] <50ms WebSocket latency
- [ ] 60fps on canvas (checked in DevTools)

---

## KEY SUCCESS METRICS

| Metric | Target | How to Check |
|--------|--------|---|
| **Build time** | 6 weeks | Calendar |
| **Code generated by AI** | 80% | LOC count |
| **Reused from CREATURE** | 2,300 LOC | Git diff |
| **Cost to launch** | $0 | Credit card |
| **Initial users** | 10+ | Discord |
| **Time to first playable** | 2 weeks | Shipped |

---

## RED FLAGS (STOP AND RETHINK)

If you catch yourself doing any of these, step back:

```
🚩 "I'm optimizing performance before testing"
   → Stop. Test first, optimize never.

🚩 "I need a custom game engine"
   → Stop. Use Pixi.js, it's perfect.

🚩 "I'm building 3D with Three.js"
   → Stop. Pixi.js + 2D first. 3D after 1M users.

🚩 "I want multiplayer from day 1"
   → Stop. Single-player MVP first. Multiplayer month 3.

🚩 "I need a fancy database"
   → Stop. SQLite works. Scale it later.

🚩 "I'm writing everything from scratch"
   → Stop. You have 2,300 LOC from CREATURE. Copy it.

🚩 "I should add LLM features"
   → Stop. They're not needed. Keep it simple.

🚩 "I'm spending >30 mins on styling"
   → Stop. Shadcn handles it. Move on.
```

---

## ONE-COMMAND DEPLOYMENT

```bash
# Frontend (Vercel)
npm run build && vercel --prod

# Backend (Railway)
git push origin main
# (auto-deploys)

# Test
curl https://your-backend.railway.app/ws
# Should upgrade to WebSocket

# Done! 🎉
```

---

## RESOURCES (READ ONLY IF STUCK)

**CREATURE Code:**
- `/home/user/creature-app/creature/src/systems/cell.rs` (copy structure)
- `/home/user/creature-app/creature/src/systems/lenia.rs` (copy physics)
- `/home/user/creature-app/creature/src/server.rs` (copy WebSocket)

**Documentation:**
- Claude Code: Ask "how do I..." (gets answers faster than docs)
- Pixi.js: https://pixi.dev (2D rendering)
- Zustand: https://github.com/pmndrs/zustand (state)
- Rust async: https://tokio.rs (already in CREATURE)

**AI Code Generation:**
- Claude Code built into this repo
- Cursor: https://cursor.sh (free tier)
- Codeium: https://codeium.com (free)

---

## SUMMARY: DON'T READ ANYTHING ELSE

1. Copy CREATURE backend (2,300 LOC)
2. Strip LLM stuff (-1,100 LOC)
3. Build React frontend (Claude Code, 2,000 LOC)
4. Wire WebSocket (200 LOC)
5. Deploy Vercel + Railway (10 minutes)
6. Ship in 6 weeks
7. Cost: $0
8. Users: Growing 🚀

**You got this.**

---

*Quick reference version: 1.0 | Use with CELL_GARDEN_IMPLEMENTATION_GUIDE.md for details*
*Read this file first, detailed guide second, code reference last*

