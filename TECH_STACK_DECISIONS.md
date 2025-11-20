# Cell Garden - Tech Stack Decision Matrix
**Use this document when making build decisions**

---

## Decision Framework

Each major component has been evaluated against:
- **AI Generatability** (Can Claude Code auto-generate it?)
- **Learning Curve** (How fast to proficiency?)
- **Performance** (Sufficient for 1000+ cells?)
- **Free Tier** (Production viable without paying?)
- **Community** (How many tutorials/examples?)
- **Maintenance** (Is it actively developed?)

---

## SECTION 1: FRONTEND FRAMEWORK

### Decision: React 18 ✅ SELECTED

| Criteria | React | Vue 3 | Svelte | Solid.js | Verdict |
|----------|-------|-------|--------|----------|---------|
| **AI Generation** | 99% | 85% | 60% | 30% | React wins |
| **Learning Curve** | Easy | Easy | Medium | Hard | Tie (React/Vue) |
| **Pixi.js Integration** | Excellent | Good | Good | Poor | React wins |
| **Bundle Size** | 42 KB | 33 KB | 18 KB | 8 KB | Svelte best, not critical |
| **Claude Code Support** | Excellent | Good | Fair | Poor | React wins |
| **Ecosystem** | Massive | Large | Growing | Niche | React wins |
| **Community** | Biggest | Large | Growing | Small | React wins |

**Decision: React 18**
- Reason: Best Claude Code support + Pixi integration
- Backup: Vue 3 (nearly identical result)
- NOT: Svelte or Solid (overkill optimization)

**Implementation:**
```bash
npm create vite@latest -- --template react-ts
# OR if using TypeScript starting from Vite template
```

---

## SECTION 2: CANVAS/RENDERING

### Decision: Pixi.js ✅ SELECTED

| Criteria | Pixi.js | Three.js | Konva | Babylon.js | Verdict |
|----------|---------|----------|-------|-----------|---------|
| **2D Performance** | Excellent | Overkill | Good | Overkill | Pixi wins |
| **Bundle Size** | 220 KB | 1.2 MB | 350 KB | 1.5 MB | Pixi wins |
| **Cell Rendering** | Perfect | Over-engineered | Good | Over-engineered | Pixi wins |
| **AI Generation** | Good | Excellent | Fair | Fair | Three.js best (not needed) |
| **60fps @ 1000 cells** | Yes | Yes (wasted) | Yes | Yes (wasted) | Pixi wins (leaner) |
| **Learning Time** | 2 hours | 10 hours | 3 hours | 12 hours | Pixi wins |
| **Free Tier** | Yes | Yes | Yes | Yes | All free |

**Decision: Pixi.js**
- Reason: Optimized for 2D particles, smallest bundle, fast learning curve
- When to upgrade: Never (scale Pixi to 10k cells first)
- If you really want 3D: Three.js after MVP succeeds (bonus feature)

**Key Pixi Patterns:**
```typescript
// Initialize
const app = new PIXI.Application({
  width: 1920,
  height: 1080,
  antialias: true,
});

// Render cells as circles
const cell = new PIXI.Graphics();
cell.beginFill(0xff0000);
cell.drawCircle(x, y, radius);
cell.endFill();

// Update position (game loop)
cell.x = newX;
cell.y = newY;

// Destroy when dead
cell.destroy();
```

---

## SECTION 3: STATE MANAGEMENT

### Decision: Zustand ✅ SELECTED

| Criteria | Zustand | Redux | Jotai | Valtio | MobX | Verdict |
|----------|---------|-------|-------|--------|------|---------|
| **Bundle Size** | 10 KB | 60 KB | 30 KB | 8 KB | 25 KB | Valtio best, Zustand close |
| **Learning Time** | 30 min | 8 hours | 2 hours | 1 hour | 4 hours | Zustand wins |
| **Boilerplate** | Zero | Massive | Minimal | Minimal | Medium | Zustand/Valtio win |
| **Claude Code** | Excellent | Excellent | Good | Fair | Fair | Zustand wins |
| **Devtools** | Good | Excellent | Fair | Good | Good | Redux best (not critical) |
| **Game State** | Perfect | Overkill | Good | Good | Good | Zustand wins |
| **React Integration** | Native | Middleware | Hooks | Proxies | Decorators | Zustand/Jotai win |

**Decision: Zustand**
- Reason: Zero boilerplate + excellent Claude Code generation
- Backup: Valtio (if you prefer proxy syntax)
- NOT: Redux (overkill for this project)

**Zustand Game Store Template:**
```typescript
import { create } from 'zustand';

type GameState = {
  cells: Cell[];
  colonies: Colony[];
  selectedCellId: string | null;
  isRunning: boolean;
  speed: number;
  
  updateCell: (id: string, updates: Partial<Cell>) => void;
  removeCell: (id: string) => void;
  toggleRunning: () => void;
};

export const useGameStore = create<GameState>((set, get) => ({
  cells: [],
  colonies: [],
  selectedCellId: null,
  isRunning: false,
  speed: 1,
  
  updateCell: (id, updates) => set((state) => ({
    cells: state.cells.map(c => c.id === id ? {...c, ...updates} : c),
  })),
  
  removeCell: (id) => set((state) => ({
    cells: state.cells.filter(c => c.id !== id),
  })),
  
  toggleRunning: () => set((state) => ({
    isRunning: !state.isRunning,
  })),
}));
```

---

## SECTION 4: UI COMPONENTS

### Decision: shadcn/ui ✅ SELECTED

| Criteria | shadcn/ui | DaisyUI | Material UI | Chakra | Headless | Verdict |
|----------|-----------|---------|------------|--------|----------|---------|
| **Setup Time** | 5 min | 2 min | 10 min | 10 min | 15 min | DaisyUI fastest |
| **Customization** | Excellent | Good | Limited | Limited | Unlimited | shadcn wins |
| **Bundle Impact** | Zero (copied) | Small | Large | Medium | Zero | shadcn wins |
| **Claude Generation** | Excellent | Good | Poor | Poor | Fair | shadcn wins |
| **Copy-Paste** | Yes | No | No | No | No | shadcn only |
| **Game UI** | Great | OK | Poor | Worse | Best | Headless best (overkill) |
| **Theme System** | Tailwind | Tailwind | CSS-in-JS | Styled | CSS | shadcn wins |

**Decision: shadcn/ui**
- Reason: Copy-paste components + Tailwind + excellent Claude generation
- Backup: DaisyUI (if you want faster setup, less customization)
- NOT: Material UI or Chakra (too opinionated for game UI)

**Components You'll Use:**
```
- Button (play/pause, spawn cell)
- Slider (speed control, zoom)
- Dialog (settings, cell details)
- Card (stats panels, leaderboards)
- Progress (energy bars)
- Tabs (colony info, settings)
- Input (naming cells, search)
- Toast (notifications)
- Badge (cell type tags)
```

**Setup:**
```bash
npx shadcn-ui@latest init
npx shadcn-ui@latest add button
npx shadcn-ui@latest add slider
# ... add more as needed
```

---

## SECTION 5: WEBSOCKET LIBRARY

### Decision: Socket.io ✅ SELECTED

| Criteria | Socket.io | Raw WS | ws (Node) | Phoenix Channels | Verdict |
|----------|-----------|--------|-----------|-----------------|---------|
| **Auto Reconnect** | Yes | Manual | Manual | Yes | Socket.io/Phoenix |
| **Fallbacks** | Yes (HTTP) | No | No | Yes | Socket.io wins |
| **Bundle Size** | 20 KB | 0 KB | N/A | 15 KB | Raw WS wins (not worth it) |
| **Error Handling** | Built-in | Manual | Manual | Built-in | Socket.io wins |
| **Debugging** | Excellent | Poor | Poor | Excellent | Socket.io wins |
| **Rust Support** | socket.io-rs | ✓ | ✓ | No | Raw WS best for Rust |
| **Browser Support** | All | All | N/A | All | All equal |
| **Learning Time** | 1 hour | 1 hour | 1 hour | 2 hours | Socket.io/Raw equal |

**Decision: Socket.io**
- Reason: Built-in reconnection + error handling saves 10 hours of debugging
- Backup: Raw WebSocket (if you're minimalist, willing to handle reconnects)
- NOT: Phoenix Channels (Rust backend, not Elixir)

**Socket.io Setup (TypeScript Frontend):**
```typescript
import io from 'socket.io-client';

const socket = io('https://backend.railway.app', {
  reconnection: true,
  reconnectionDelay: 1000,
  reconnectionAttempts: 10,
});

socket.on('connect', () => {
  console.log('Connected to game server');
});

socket.on('gameState', (state: GameState) => {
  useGameStore.setState(state);
});

socket.on('disconnect', () => {
  console.log('Disconnected, will auto-reconnect');
});

// Send user action
socket.emit('action', {
  type: 'spawnCell',
  x: 100,
  y: 100,
});
```

**Socket.io Setup (Rust Backend with socket_io crate):**
```rust
use socket_io::{Socket, EngineIo};
use serde_json::json;

async fn handle_connection(socket: Socket) {
  socket.emit("gameState", json!({
    cells: game_state.cells,
    colonies: game_state.colonies,
  }));
  
  socket.on("action", |data| {
    // Process user action
  });
}
```

---

## SECTION 6: BACKEND FRAMEWORK

### Decision: Rust + Warp ✅ SELECTED (ALREADY HAVE IT)

Why not change?

| Criteria | Rust + Warp | Node + Express | Python + Flask | Go + Gin | Verdict |
|----------|------------|----------------|----------------|----------|---------|
| **Available** | Already written | Would rebuild | Would rebuild | Would rebuild | Rust wins (exists!) |
| **Performance** | Excellent | Good | Poor | Excellent | Rust/Go tie |
| **WebSocket** | Native async | Socket.io | Socket.io | Gorilla | All good |
| **Async Quality** | Best | Good | Poor | Good | Rust best |
| **Learning Time** | 20 hours | 1 hour | 1 hour | 3 hours | Node wins (not needed) |
| **Existing Code** | 5,600 LOC ready | 0 LOC | 0 LOC | 0 LOC | Rust wins (TIME!) |

**Decision: Keep Rust + Warp**
- Reason: 2,300 LOC already written, tested, production-ready
- Time saved: 120 hours of writing backend from scratch
- Cost of learning Rust: Already paid! Continue with it.

**Don't rebuild the backend in Node.js. Seriously.**

---

## SECTION 7: DATABASE

### Decision: SQLite on Railway ✅ SELECTED

| Criteria | SQLite | PostgreSQL | MySQL | DynamoDB | Verdict |
|----------|--------|-----------|-------|----------|---------|
| **Setup** | Zero (built-in) | 5 min (Neon) | 5 min (Planet) | 10 min | SQLite wins |
| **Scale** | To 100k users | Unlimited | 10M users | Unlimited | PostgreSQL/MySQL for >100k |
| **Free Tier** | Infinite | 256 MB (Neon) | 5 GB (Planet) | Free but complex | SQLite wins |
| **Backups** | Manual | Automatic | Automatic | Automatic | Postgres/MySQL win |
| **Rust Support** | sqlx | tokio-postgres | sqlx | rusoto | All good |
| **Game State** | In-memory | In-memory | In-memory | Not ideal | All need RAM cache |
| **Leaderboards** | On-disk | On-disk | On-disk | On-disk | All equal |
| **Cold Starts** | N/A | Potential (serverless) | Potential (serverless) | Yes | SQLite wins |

**Decision: SQLite**
- Reason: Zero setup, game state lives in Rust memory anyway
- Where to store: Railway volume mount (persists on restart)
- What to store: Leaderboards, saved games, user accounts (NOT real-time game state)

**SQLite Setup (Rust):**
```rust
use sqlx::sqlite::SqlitePool;

// Connect
let pool = SqlitePool::connect("sqlite:game.db").await?;

// Create table
sqlx::query(
  "CREATE TABLE leaderboards (
    id INTEGER PRIMARY KEY,
    colony_name TEXT,
    cell_count INTEGER,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
  )"
)
.execute(&pool)
.await?;

// Insert
sqlx::query(
  "INSERT INTO leaderboards (colony_name, cell_count) VALUES (?, ?)"
)
.bind("My Colony")
.bind(250)
.execute(&pool)
.await?;

// Query
let rows: Vec<_> = sqlx::query("SELECT * FROM leaderboards ORDER BY cell_count DESC LIMIT 10")
  .fetch_all(&pool)
  .await?;
```

**For larger leaderboards later:**
```
Neon (PostgreSQL) free tier: 256 MB
Migration: sqlx works with both SQLite + PostgreSQL
Switch when you hit limits: Zero code changes needed (same API)
```

---

## SECTION 8: DEPLOYMENT (FRONTEND)

### Decision: Vercel ✅ SELECTED

| Criteria | Vercel | Netlify | Cloudflare | GitHub Pages | Verdict |
|----------|--------|---------|-----------|--------------|---------|
| **Setup Time** | 1 minute | 2 minutes | 3 minutes | 5 minutes | Vercel fastest |
| **React Optimized** | Yes (Next.js) | OK | OK | OK | Vercel wins |
| **Environment Vars** | Easy | OK | OK | OK | Vercel wins |
| **Preview URLs** | Excellent | Excellent | OK | No | Vercel/Netlify tie |
| **Free Build Minutes** | 6000/month | 300/month | Unlimited | N/A | Vercel wins |
| **Performance** | Excellent | Excellent | Excellent | Good | All equal |
| **Free Tier Forever** | Yes | Yes | Yes | Yes | All equal |
| **Migration Later** | Easy | Easy | Easy | Hard | Vercel/Netlify win |

**Decision: Vercel**
- Reason: Fastest setup + best React support
- Backup: Netlify (virtually identical)
- When Vercel fails: Switch to Netlify in 10 minutes

**Deploy in 30 seconds:**
```bash
npm install -g vercel
vercel --prod
```

**First time setup (interactive):**
```
> vercel
? Set up and deploy "~/cell-garden-frontend"? [Y/n] Y
? Which scope do you want to deploy to? [your-name]
? Link to existing project? [y/N] N
? What's your project's name? cell-garden-frontend
? In which directory is your code? ./
? Auto-detected project settings correct? [Y/n] Y
✓ Linked to your-name/cell-garden-frontend (created .vercel)
✓ Built successfully
✓ Deployed to production
```

---

## SECTION 9: DEPLOYMENT (BACKEND)

### Decision: Railway ✅ SELECTED

| Criteria | Railway | Fly.io | Render | Heroku | AWS | Verdict |
|----------|---------|--------|--------|--------|-----|---------|
| **Setup** | 3 min (GitHub) | 5 min | 5 min | Deprecated | 20 min | Railway fastest |
| **Free Tier** | 5 GB/month | Generous | Limited | None | Complex | Railway/Fly tie |
| **No Cold Starts** | Yes | Yes (mostly) | Sleeps | N/A | No | Railway/Fly win |
| **Environment Vars** | Easy | Easy | Easy | N/A | Easy | All equal |
| **Volumes** | Yes | Yes | Yes | N/A | Yes | All equal |
| **Rust Support** | Excellent | Excellent | OK | N/A | Excellent | All good |
| **Docker Ready** | Yes | Yes | Yes | N/A | Yes | All equal |
| **Auto Deploy** | Yes (GitHub) | Yes | Yes | N/A | Complex | Railway wins |

**Decision: Railway**
- Reason: Fastest setup + no cold starts + generous free tier
- Backup: Fly.io (nearly identical, also excellent)
- NOT: Render (sleeps free tier) or Heroku (no free tier anymore)

**Deploy in 2 minutes:**
```bash
# 1. Push to GitHub
git push origin main

# 2. On Railway.app:
# - Connect GitHub repo
# - Auto-deploys on push
# - View logs in dashboard

# 3. Done! No configuration needed
```

**Environment Variables (Railway dashboard):**
```
RUST_LOG=info
DATABASE_URL=sqlite://game.db
PORT=3030
```

---

## SECTION 10: CI/CD

### Decision: GitHub Actions ✅ SELECTED

| Criteria | GitHub Actions | GitLab CI | CircleCI | Jenkins | Verdict |
|----------|----------------|-----------|----------|---------|---------|
| **Setup** | Built-in | Built-in | 5 min | 30 min | GitHub Actions fastest |
| **Cost** | 2000 free min/mo | 400 free min/mo | 1500 free min/mo | Self-hosted | GitHub Actions wins |
| **Rustfmt Check** | Yes | Yes | Yes | Yes | All equal |
| **Deploy Integration** | Native (Railway) | Native | Yes | Manual | GitHub wins |
| **Matrix Testing** | Yes | Yes | Yes | Yes | All equal |
| **Documentation** | Excellent | Good | Good | Poor | GitHub wins |

**Decision: GitHub Actions**
- Reason: Free, built-in, native Railway integration
- Setup: Create `.github/workflows/test.yml`

**GitHub Actions Workflow (Rust):**
```yaml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: dtolnay/rust-toolchain@stable
      - run: cargo test --release
      - run: cargo fmt -- --check
      - run: cargo clippy -- -D warnings
```

---

## SECTION 11: ERROR TRACKING

### Decision: Sentry (Free Tier) ✅ SELECTED

| Criteria | Sentry | Rollbar | LogRocket | Bugsnag | Verdict |
|----------|--------|---------|-----------|---------|---------|
| **Free Tier** | 10k events/mo | Limited | Limited | Limited | Sentry wins |
| **Setup** | 2 minutes | 3 minutes | 3 minutes | 3 minutes | All quick |
| **Rust Support** | Excellent | Good | No | Good | Sentry wins |
| **JavaScript** | Excellent | Good | Excellent | Good | All equal |
| **Performance Impact** | Minimal | Minimal | Minimal | Minimal | All equal |
| **Release Tracking** | Yes | Yes | No | Yes | Sentry/Rollbar win |

**Decision: Sentry**
- Reason: 10k free events/month is generous, excellent Rust support
- Cost: Free forever (within limits)
- Upgrade: When you hit 10k events, pay $29/month

**Sentry Setup (Rust):**
```rust
let guard = sentry::init("YOUR_DSN");

// Will automatically track panics and errors
```

**Sentry Setup (JavaScript):**
```typescript
import * as Sentry from "@sentry/react";

Sentry.init({
  dsn: "YOUR_DSN",
  environment: "production",
  tracesSampleRate: 1.0,
});
```

---

## SUMMARY TABLE

| Component | Selection | Runner-Up | Cost | Setup |
|-----------|-----------|-----------|------|-------|
| **Frontend** | React 18 | Vue 3 | Free | 1 min |
| **Canvas** | Pixi.js | Three.js (later) | Free | 2 min |
| **State** | Zustand | Valtio | Free | 1 min |
| **UI** | shadcn/ui | DaisyUI | Free | 5 min |
| **WebSocket** | Socket.io | Raw WS | Free | 1 min |
| **Backend** | Rust + Warp | (don't change) | Free | 0 min |
| **Database** | SQLite | PostgreSQL (later) | Free | 0 min |
| **Frontend Host** | Vercel | Netlify | Free | 1 min |
| **Backend Host** | Railway | Fly.io | Free | 3 min |
| **CI/CD** | GitHub Actions | GitLab CI | Free | 3 min |
| **Errors** | Sentry | Rollbar | Free (10k/mo) | 2 min |
| **Total Setup** | | | **$0** | **~20 min** |

---

## DECISION TREE: IF YOU'RE STUCK

```
"I want to use technology X instead"
  ├─ Is it on the SELECTED list?
  │  ├─ YES → Use it, it's the best choice
  │  └─ NO → Why? If because:
  │     ├─ "I know it better"
  │     │  └─ Use it, you'll ship faster (but research alternatives first)
  │     ├─ "It's more scalable"
  │     │  └─ You need 10k users first, stop optimizing
  │     ├─ "It's cheaper"
  │     │  └─ Check the COST column, probably not
  │     ├─ "It's newer/cooler"
  │     │  └─ Don't, stick to boring but proven tech
  │     └─ "The docs are better"
  │        └─ Acceptable reason, switch away
```

---

## WHEN TO REVISIT DECISIONS

| Metric | Threshold | Action |
|--------|-----------|--------|
| **Users** | >1,000 | Review database (consider PostgreSQL) |
| **Cells per game** | >5,000 | Review rendering (optimize Pixi shader) |
| **API calls/sec** | >1,000 | Review WebSocket batching |
| **Cost to operate** | >$0 | Review everything |
| **Code size** | >20,000 LOC | Review architecture |
| **Deploy frequency** | 10x/day | Review CI/CD efficiency |

Until you hit these: Don't change anything.

---

## THE GOLDEN RULE

**Don't optimize what hasn't been tested.**

Ship with the selected stack. After MVP:
1. Measure performance
2. Find bottlenecks
3. Only then optimize

Most "better" alternatives are premature optimization.

---

*Tech Stack Decisions v1.0 | Reference during development | Do not overthink*

