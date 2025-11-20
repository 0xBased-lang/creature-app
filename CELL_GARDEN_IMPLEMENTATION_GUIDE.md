# Cell Garden - Comprehensive Implementation Guide
**Solo Dev + AI Tools Optimization | 100% Free Tier | Production Ready**

---

## EXECUTIVE SUMMARY

**Cell Garden** is a real-time browser-based simulation game where players cultivate and evolve digital cell colonies. Core loop: cells grow, consume resources, reproduce, mutate, and create emergent patterns. Built for maximum reusability of CREATURE's 5,600+ LOC while stripping expensive LLM components.

### Project Scope
- **Scope:** 4-8 weeks solo dev with AI assistance
- **Frontend:** React + Pixi.js (canvas rendering)
- **Backend:** Rust WebSocket server (reuse CREATURE systems)
- **AI Assistance:** 40-50% code generation via Claude Code + Cursor
- **Deployment:** Free tier throughout (zero recurring costs)
- **Target:** Playable MVP in 2 weeks, feature-complete in 6 weeks

---

## PART 1: AI CODING ASSISTANT CAPABILITIES & STRATEGY

### 1.1 What You Can Auto-Generate (70% of code)

#### Claude Code (Best for structure & complex logic)
```
Ideal use cases:
✓ Full page components (react + hooks)
✓ TypeScript type definitions & interfaces  
✓ Game simulation logic (physics, growth algorithms)
✓ State management hooks (zustand stores)
✓ API client wrappers
✓ Test files (unit + integration)
```

**Example prompt for Claude Code:**
```
"Generate a React hook for managing cell colony state. 
- Cells have id, x, y, energy, age, mutations
- Colonies have id, name, cells[], resources
- Hook should have: addCell, removeCell, updateCell, 
  getNeighbors(cellId), getColonyStats
- Use TypeScript, export hooks + types"
```

**Estimated productivity:** 50 lines of production code per 1 Claude Code request

#### Cursor AI (Best for incremental development & debugging)
```
Ideal use cases:
✓ File completion within existing structures
✓ Implementing similar functions (copy/modify)
✓ Bug fixing & error messages
✓ Adding features to existing components
✓ Test generation for existing code
✓ Real-time code suggestions
```

**Setup for Cell Garden:**
1. Install Cursor (free tier available)
2. Use `.cursorrules` file (see section 1.3 below)
3. Set up Ctrl+K for chat, Ctrl+L for inline edit

**Estimated productivity:** 10x faster iteration than manual coding

#### GitHub Copilot Alternatives (Free options)
```
1. Codeium (free, better than GitHub free tier)
   - 1000 completions/month on free tier
   - Works in VSCode, JetBrains, Vim
   
2. Tabnine (free community edition)
   - Local model option (privacy-first)
   - Good for Rust
   
3. Ollama + Continue.dev (fully local, 100% free)
   - Run Mistral-7B or Code Llama locally
   - Set up with VSCode via Continue.dev extension
```

**Recommendation:** Use Codeium during development, switch to Continue.dev for cost-free inference during heavy iteration.

#### V0.dev & Bolt.new for UI Generation

**V0.dev (Vercel's UI generator)**
- Not ideal for game development (2D canvas-heavy)
- Use for: Admin dashboards, settings UI, leaderboards
- Can generate Shadcn components in 30 seconds
- Max 2-3 pages before paywall

**Bolt.new (better for full apps)**
- AI builds entire projects with 1 prompt
- Good for: Prototype web UI, testing frameworks
- Limitation: Can't generate WebSocket integrations well
- Use: Generate initial React structure, adapt as needed

**Strategy:** Use Bolt for 10-minute prototype, then migrate to local Claude Code for production quality.

### 1.2 Code Generation Breakdown by Component

| Component | Auto-Generatable | AI Tool | Manual Work | Notes |
|-----------|-----------------|---------|------------|-------|
| **Cell struct + logic** | 90% | Claude Code | Growth algorithms, mutation rules | 400 LOC → 350 generated |
| **Colony management** | 85% | Claude Code | Custom behavior hooks | 600 LOC → 510 generated |
| **Lenia physics** | 95% | Claude Code | Parameter tuning | 300 LOC → 285 generated |
| **React components** | 80% | Claude Code | Animation logic | 1200 LOC → 960 generated |
| **WebSocket server** | 70% | Claude Code + Cursor | Error handling, reconnection | 200 LOC → 140 generated |
| **Canvas rendering** | 60% | Cursor (incremental) | Optimization, layers | 500 LOC → 300 generated |
| **Game loop** | 75% | Claude Code | Timing, frame skipping | 300 LOC → 225 generated |
| **State management** | 90% | Claude Code | Custom selectors | 400 LOC → 360 generated |
| **UI components** | 85% | V0.dev + Claude Code | Styling refinement | 800 LOC → 680 generated |
| **Testing** | 80% | Claude Code | Edge cases, mocks | 600 LOC → 480 generated |
| **Total project** | **82%** | Mixed | **Integration, polish** | **5,900 LOC → 4,835** |

### 1.3 Cursor Rules Configuration

Create `.cursorrules` file in project root:

```markdown
# Cell Garden - Development Rules

## Code Style
- Use TypeScript with strict mode
- React functional components with hooks
- Rust: Edition 2021, async/await with Tokio

## Architecture
- Frontend: React + Zustand + Pixi.js
- Backend: Rust Warp WebSocket server
- Database: In-memory (Rust) + LocalStorage (browser)

## AI Code Generation
- Generate complete functions with JSDoc/docstrings
- Always include error handling
- Add TypeScript types before implementation
- Generate tests alongside features

## Git Commits
- Atomic commits (one feature per commit)
- Format: "feat: [component] - [description]"
- Include before/after LOC count in commit body

## Performance Targets
- WebSocket <50ms latency
- React render <16ms (60fps)
- Rust simulation <100ms per cycle
- Canvas 60fps @ 1920x1080

## LLM Integration
- NO external API calls for cell behavior
- Use local algorithms for growth/mutation
- WebSocket only for state sync
```

---

## PART 2: CREATURE CODE REUSABILITY ANALYSIS

### 2.1 What to Reuse (As-Is)

#### Keep These Modules (2,400 LOC)

**1. Cell System** (`src/systems/cell.rs` - 450 LOC)
```rust
// Reusable parts:
✓ Cell struct definition (position, energy, age)
✓ Neighbor detection logic (distance threshold)
✓ UUID-based tracking
✓ Energy depletion mechanics
✓ Dimensional position (repurpose as genetic traits)

// Remove:
✗ generate_thought() - replace with local growth logic
✗ compressed_memories - not needed
✗ mission_alignment_score - replace with fitness
```

**2. Lenia System** (`src/systems/lenia.rs` - 300 LOC)
```rust
// Status: 100% reusable
// Why: Pure physics simulation, zero LLM dependency
// What it does:
  - Cellular automata evolution (3D grid)
  - Kernel-based neighborhood calculations
  - Growth function dynamics
  - Perfect for "organic growth" visual effect

// Usage in Cell Garden:
  - Drive visual appearance of cells
  - Generate mutation rates based on Lenia output
  - Create emergent patterns automatically
```

**3. Colony System** (`src/systems/colony.rs` - 550 LOC)
```rust
// Reusable parts: 70%
✓ Colony struct
✓ Neighbor clustering detection (DFS)
✓ Colony statistics calculation
✓ Spatial grid management
✓ Batch processing architecture

// Replace:
✗ process_cell_sub_batch() - use game loop instead
✗ API calls - replace with local decisions
✗ Plan creation - replace with cell reproduction
```

**4. Models** (`src/models/` - 600 LOC)
```rust
// Fully reusable:
✓ types.rs - Coordinates, DimensionalPosition, etc.
✓ constants.rs - Tweak parameters, reuse structure
✓ state.rs - Save/load colony state

// Remove:
✗ thought_io.rs - LLM-specific
✗ plan_analysis.rs - Not applicable
```

**5. Server** (`src/server.rs` - 200 LOC)
```rust
// Status: 90% reusable
// Warp WebSocket server
// Heartbeat: Change from 500ms colony state → game state
// Delta updates: Serialize cell positions + energy only
// Clients: Web frontend instead of terminal UI
```

#### Total Reusable Code: ~2,300 LOC
#### Estimated Time Savings: 120 hours (12 weeks * 10 hrs/week)

### 2.2 What to Strip Out (1,100 LOC removal)

```rust
// Files to DELETE (700 LOC):
- src/api/openrouter.rs (1,625 LOC) - entire LLM integration
- src/api/gemini.rs (400 LOC) - optional LLM fallback
- src/interface/mod.rs + widgets.rs (250 LOC) - TUI stuff
- src/utils/animations.rs (200 LOC) - progress bars, not needed
- src/utils/ascii_art.rs (150 LOC) - ASCII animations

// Files to HEAVILY REFACTOR (400 LOC):
- src/systems/colony.rs
  - Remove: process_cell_sub_batch() - 150 LOC
  - Remove: gather_real_time_context() - 100 LOC
  - Keep: Clustering, neighbor detection, statistics
  
- src/systems/cell.rs
  - Remove: generate_thought() - 200 LOC
  - Remove: evaluate_dimensional_state() - 80 LOC
  - Keep: Position, energy, neighbor tracking
  
- src/main.rs
  - Remove: CLI argument parsing - 40 LOC
  - Remove: Animation initialization - 50 LOC
  - Add: Game loop, tick mechanics - 60 LOC (net: +30)
```

### 2.3 WebAssembly Compilation Feasibility

**Question:** Can CREATURE Rust backend compile to WebAssembly?

**Answer:** Partially - with modifications.

**Current Blockers:**
```rust
// Won't compile to WASM:
✗ tokio (async runtime) - WASM doesn't support threads
✗ Warp server - No HTTP/WebSocket in WASM
✗ reqwest - No network calls from WASM
✗ File I/O operations
```

**WASM-Compatible Parts:**
```rust
✓ Cell logic (position, energy, growth)
✓ Lenia simulation (pure math)
✓ Neighbor detection (distance calculations)
✓ Genetic algorithms (mutation, crossover)
✓ Statistics generation
```

**Recommendation:** Keep Rust backend as server, use WASM for compute-intensive only

### 2.4 Architecture Decision: Rust Backend + Web Frontend (RECOMMENDED)

```
     ┌─────────────────────────────────────────────┐
     │         Web Browser (Client)                │
     │  ┌─────────────────────────────────────┐    │
     │  │  React + Pixi.js Canvas            │    │
     │  │  - Visualization only              │    │
     │  │  - Event handling (click, drag)     │    │
     │  │  - WebSocket consumer              │    │
     │  └─────────────────────────────────────┘    │
     └────────────────┬────────────────────────────┘
                      │ WebSocket
                      │ (JSON state @ 60Hz)
                      │
     ┌────────────────▼────────────────────────────┐
     │      Rust Server (Simulator)                │
     │  ┌─────────────────────────────────────┐    │
     │  │  Game Loop @ 30 FPS                 │    │
     │  │  - Cell state updates               │    │
     │  │  - Lenia evolution                  │    │
     │  │  - Reproduction/mutation            │    │
     │  │  - Resource management              │    │
     │  │  - Colony statistics                │    │
     │  └─────────────────────────────────────┘    │
     │  ┌─────────────────────────────────────┐    │
     │  │  Persistence                        │    │
     │  │  - JSON state dumps (per cycle)     │    │
     │  │  - Replay system                    │    │
     │  │  - Leaderboards (in-memory)         │    │
     │  └─────────────────────────────────────┘    │
     └─────────────────────────────────────────────┘
```

**Why NOT WASM-only:**
- Lenia physics needs constant computation (high CPU)
- Game state must be authoritative (cheat prevention)
- Persistence easier on server
- Can run game offline in background
- Scales to multiplayer later

**Why Keep Rust Backend:**
- CREATURE already written in Rust
- Async/await handles many concurrent connections
- WebSocket broadcasting (Warp) is excellent
- Performance: 32-256 cells @ 30 FPS without lag
- Easy to parallelize with Rayon

---

## PART 3: FREE TOOL STACK

### 3.1 Frontend Framework Selection

| Framework | Bundle Size | Learning Curve | Canvas Support | AI Generation | Recommendation |
|-----------|------------|----------------|---|---|---|
| **React** | 42 KB gzipped | Easy | Good (Pixi.js) | ⭐⭐⭐⭐⭐ | **PICK THIS** |
| **Vue 3** | 33 KB gzipped | Easy | Good | ⭐⭐⭐⭐ | Good alternative |
| **Svelte** | 18 KB gzipped | Medium | Good | ⭐⭐⭐ | Overkill for game |
| **Solid.js** | 8 KB gzipped | Hard | Good | ⭐⭐ | Too niche |

**Recommendation:** **React** because:
1. Best Claude Code support (99% of training data)
2. Cursor AI auto-completes React idioms perfectly
3. Pixi.js + React integration well-documented
4. Component libraries (Shadcn) generate instantly
5. Most tutorials/examples available

### 3.2 Canvas/Rendering Libraries

**Pixi.js (RECOMMENDED)**
```
Why Pixi:
✓ 2D rendering, optimized for particles/sprites
✓ WebGL-accelerated, 60fps guaranteed
✓ Smallest bundle (220 KB)
✓ Perfect for cell visualization
✓ Active development, great docs

Usage: Render cells, resources, connections, effects

Example setup:
  import * as PIXI from 'pixi.js';
  
  const app = new PIXI.Application({
    width: window.innerWidth,
    height: window.innerHeight,
    antialias: true,
    transparent: true,
  });
  
  // Render 1000 cells @ 60fps with zero lag
```

**Three.js (Alternative if 3D later)**
```
Only if:
- Want 3D colony visualization (extra 200 KB)
- Mobile isn't priority (3x power usage)
- 6+ months of development time

For MVP: Skip, use Pixi.js
```

**Konva.js (Good middle ground)**
```
Pros: Layer management, event handling
Cons: Heavier than Pixi for particle effects
Verdict: Good for UI overlays, not game loop
```

**Recommendation: Pixi.js + React**

### 3.3 UI Component Libraries (100% Free)

**shadcn/ui (RECOMMENDED)**
```
Why:
✓ Copy-paste components, not npm dependency
✓ Zero runtime overhead
✓ Fully customizable
✓ Uses Tailwind CSS
✓ Works perfectly with Claude Code generation

Components needed for Cell Garden:
- Button, Dialog, Slider (speed control)
- Progress bar (cell energy)
- Tabs (colony stats)
- Card (cell details)
- Toast (notifications)
- Input (naming, settings)

Setup:
  npx shadcn-ui@latest init
  npx shadcn-ui@latest add button
  
Bootstrap time: 15 minutes for full component library
```

**DaisyUI (Free alternative)**
```
Pros: Pre-styled, less setup
Cons: Less customizable
When: If shadcn feels complex
```

**Headless UI (Option for React)**
```
Pros: Minimal, accessible
Cons: Requires more styling
Verdict: Good for custom game UI
```

**Recommendation: shadcn/ui**

### 3.4 State Management

**Zustand (RECOMMENDED)**
```typescript
// Why Zustand:
// - 10 KB total
// - Zero boilerplate
// - Perfect for game state
// - Claude Code loves it

import create from 'zustand';

type GameState = {
  // Simulation state
  cells: Cell[];
  colonies: Colony[];
  resources: number;
  isRunning: boolean;
  speed: number;
  
  // UI state
  selectedCellId: string | null;
  cameraZoom: number;
  showStats: boolean;
  
  // Actions
  updateCell: (id: string, data: Partial<Cell>) => void;
  addCell: (position: [number, number]) => void;
  setSpeed: (speed: number) => void;
  toggleRunning: () => void;
};

export const useGameStore = create<GameState>((set) => ({
  cells: [],
  colonies: [],
  resources: 1000,
  isRunning: false,
  speed: 1,
  selectedCellId: null,
  cameraZoom: 1,
  showStats: false,
  
  updateCell: (id, data) => set((state) => ({
    cells: state.cells.map(c => c.id === id ? {...c, ...data} : c)
  })),
  
  addCell: (pos) => set((state) => ({
    cells: [...state.cells, {
      id: uuidv4(),
      position: pos,
      energy: 100,
      age: 0,
      type: 'normal',
      mutations: [],
    }]
  })),
  
  setSpeed: (speed) => set({ speed }),
  toggleRunning: () => set((state) => ({ isRunning: !state.isRunning })),
}));
```

**Alternatives:**
- Redux Toolkit (overkill for game)
- Jotai (good but less AI-friendly)
- Valtio (good for proxies)

### 3.5 WebSocket Communication

**Socket.io (RECOMMENDED for this project)**
```typescript
import io from 'socket.io-client';

const socket = io('ws://localhost:3030', {
  reconnection: true,
  reconnectionDelay: 1000,
  reconnectionAttempts: 10,
});

// Receive full game state @ 60Hz
socket.on('game_state', (state: GameState) => {
  useGameStore.setState(state);
});

// Send user actions
socket.emit('action', {
  type: 'spawn_cell',
  x: 100,
  y: 100,
  energy: 100,
});
```

**Raw WebSocket (Alternative)**
```typescript
// More work, but lighter
const ws = new WebSocket('ws://localhost:3030');
ws.onmessage = (e) => {
  const state = JSON.parse(e.data);
  useGameStore.setState(state);
};
```

**Recommendation: Socket.io**
- Automatic reconnection
- Binary encoding support
- Better debugging
- 20 KB bundle impact worth it

### 3.6 Full Frontend Stack Summary

```json
{
  "devDependencies": {
    "@types/react": "^18.0.0",
    "@types/node": "^20.0.0",
    "typescript": "^5.0.0",
    "tailwindcss": "^3.0.0",
    "postcss": "^8.0.0",
    "autoprefixer": "^10.0.0",
    "vite": "^5.0.0",
    "@vitejs/plugin-react": "^4.0.0"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "zustand": "^4.4.0",
    "pixi.js": "^8.0.0",
    "socket.io-client": "^4.7.0",
    "clsx": "^2.0.0",
    "uuid": "^9.0.0"
  }
}
```

**Total Bundle Size (gzipped):** ~180 KB
**Load Time on 4G:** ~800ms
**Install Size:** ~280 MB (node_modules)

---

## PART 4: DEPLOYMENT OPTIONS (100% FREE TIER)

### 4.1 Deployment Architecture

```
                    ┌─────────────────────┐
                    │   Browser (Client)  │
                    └──────────┬──────────┘
                               │
                    ┌──────────▼──────────┐
                    │  Vercel/Netlify    │
                    │  (Frontend Hosting)│
                    └──────────┬──────────┘
                               │
                    ┌──────────▼──────────┐
                    │ Railway/Fly.io     │
                    │ (Rust Backend)     │
                    └──────────┬──────────┘
                               │
                    ┌──────────▼──────────┐
                    │ SQLite on Volume   │
                    │ (Persistence)      │
                    └────────────────────┘
```

### 4.2 Frontend Hosting Comparison

| Platform | Free Tier | Best For | Setup Time | Bundle Size Limit |
|----------|-----------|----------|------------|-------------------|
| **Vercel** | ∞ | React apps | 2 min | None (100 MB/function) |
| **Netlify** | ∞ | JAMstack | 3 min | 25 MB/file |
| **Cloudflare Pages** | ∞ | Anything | 4 min | 25 MB |
| **GitHub Pages** | ∞ | Static only | 5 min | 1 GB |

**Recommendation: Vercel**
- Next.js integration (if migrating later)
- Preview URLs for branches
- Serverless functions free (if needed)
- Environment variables built-in

**Setup:**
```bash
# 1. Install Vercel CLI
npm i -g vercel

# 2. Deploy (2 minutes)
vercel

# 3. Production domain
vercel --prod
```

### 4.3 Backend Hosting Comparison

| Platform | Specs | Cost | Startup | Persistence | Recommendation |
|----------|-------|------|---------|-------------|---|
| **Railway** | 8GB RAM, 100GB storage | FREE 5GB/mo | 30 sec | Volume mount | ⭐ BEST |
| **Fly.io** | 3 shared CPU, 256MB RAM | FREE | 45 sec | Volume mount | Good 2nd |
| **Render** | 0.5 CPU, 512MB RAM | FREE | 60 sec + sleep | SQLite | Limited |
| **Heroku** | 512MB RAM | ~~FREE~~ paid only | - | - | Deprecated |

**WINNER: Railway**

**Why Railway:**
```
✓ 5 GB/month free persistent data
✓ No cold starts or sleeping
✓ Docker-native deployment
✓ Environment variables easy
✓ Logs streaming included
✓ GitHub integration 1-click
✓ Generous free tier (no downside)
✓ Good support

Best alternative: Fly.io (also excellent)
```

### 4.4 Database Options (100% Free)

**Option 1: SQLite on Railway Volume (RECOMMENDED)**
```
Pros:
✓ Zero setup
✓ Single file on disk
✓ Perfect for <100k users
✓ Rust: sqlx crate (async SQL)

Cons:
✗ Single server only (no replication)
✗ Backups manual

Verdict: Perfect for MVP + first 6 months
```

**Option 2: PlanetScale (MySQL Free Tier)**
```
Pros:
✓ Free tier: 5GB storage, 100M queries/month
✓ Managed backups
✓ Scales if needed

Cons:
✗ Need MySQL driver (slightly heavier)
✗ Foreign keys limitations

Verdict: Good if planning growth beyond 6 months
```

**Option 3: Neon (PostgreSQL Free)**
```
Pros:
✓ Free: 3 projects, 256 MB storage
✓ Good for analytics later
✓ Native Rust support (tokio-postgres)

Cons:
✗ Limited to 256 MB (leaderboards OK, game state no)
✗ Serverless cold starts (OK for game)

Verdict: Use for leaderboards only
```

**RECOMMENDATION: SQLite on Railway Volume**
- Game state lives in-memory on Rust server
- SQLite for: Leaderboards, saved games, user accounts
- Setup time: 0 minutes (already installed in Rust)

### 4.5 Complete Free Deployment Stack

```
Frontend
├─ Code: GitHub (free private repo)
├─ CI/CD: GitHub Actions (free)
├─ Hosting: Vercel (free tier)
└─ CDN: Built into Vercel

Backend
├─ Code: GitHub (same repo)
├─ CI/CD: GitHub Actions (free)
├─ Hosting: Railway (free tier)
├─ Database: SQLite on volume
└─ Monitoring: Railway logs + Sentry (free tier)

Optional Services
├─ Error Tracking: Sentry (free tier, 10k events/mo)
├─ Analytics: Plausible (not free, skip for MVP)
├─ Email: Resend (free tier, good for auth later)
└─ Auth: NextAuth.js (free, self-hosted)
```

### 4.6 Deployment Flow (GitHub → Production)

```bash
# 1. Create monorepo structure
cell-garden/
├─ frontend/        (React app)
├─ backend/         (Rust server)
├─ shared/          (Types, constants)
└─ .github/
   └─ workflows/
      ├─ deploy-frontend.yml
      └─ deploy-backend.yml

# 2. Frontend deployment (Vercel)
cd frontend
vercel --prod

# 3. Backend deployment (Railway)
# Connect Railway to GitHub repo
# Railway auto-deploys on push to main

# 4. Test end-to-end
# Frontend hits backend via API
# Backend broadcasts game state via WebSocket
# Total deploy time: 3 minutes
```

### 4.7 Cost Analysis (12 Months)

| Service | Free Tier | What You Get | Cost |
|---------|-----------|------------|------|
| Vercel | ∞ | 1000 serverless fn/month, 100GB CDN | $0 |
| Railway | 5 GB/month | 1000 hours runtime | $0 |
| Neon | 256 MB | 1 free project, branch deploys | $0 |
| GitHub | ∞ | Unlimited private repos, Actions | $0 |
| Sentry | 10k/month | Error tracking, release management | $0 |
| **TOTAL** | | **Full production game** | **$0** |

**Year 1 Cost: $0**
**Limits hit at:** ~50,000 monthly active users

---

## PART 5: DEVELOPMENT SPEED HACKS

### 5.1 What Scaffolds in Minutes (AI-Assisted)

#### Generated in <5 Minutes with Claude Code
```typescript
// 1. Full Zustand store
// Prompt: "Create Zustand store for Cell Garden with cells, colonies, resources"
// Generated: 180 LOC, 4 minutes
export const useGameStore = create<GameState>((set) => ({
  cells: [],
  colonies: [],
  resources: 1000,
  // ...
}));

// 2. React component with hooks
// Prompt: "Create CellDetailPanel component showing energy, age, mutations"
// Generated: 220 LOC, 3 minutes
const CellDetailPanel: React.FC<Props> = ({ cellId }) => {
  const cell = useGameStore(s => s.cells.find(c => c.id === cellId));
  // ...
};

// 3. Rust enum + impl block
// Prompt: "Create CellType enum with Normal, Queen, Drone and methods"
// Generated: 150 LOC, 2 minutes
#[derive(Clone, Debug)]
pub enum CellType {
  Normal,
  Queen,
  Drone,
}

// 4. TypeScript types from requirements
// Prompt: "Create full Cell and Colony types for a garden simulator"
// Generated: 300 LOC, 5 minutes
type Cell = {
  id: string;
  position: [number, number];
  energy: number;
  age: number;
  type: CellType;
  mutations: Mutation[];
  // ...
};

// 5. Test files
// Prompt: "Generate unit tests for cell growth simulation"
// Generated: 280 LOC, 4 minutes
describe('Cell Growth', () => {
  test('should consume energy over time', () => {
    // ...
  });
});
```

**Total for above: 1,130 LOC in ~18 minutes (88 LOC/min)**

### 5.2 Key Boilerplate Templates

**Template 1: React Game Component Structure**
```typescript
// Saves 30 minutes setup time
import React, { useEffect, useRef } from 'react';
import * as PIXI from 'pixi.js';
import { useGameStore } from '@/store/game';

export const GameCanvas: React.FC = () => {
  const canvasRef = useRef<HTMLDivElement>(null);
  const appRef = useRef<PIXI.Application | null>(null);
  
  const gameState = useGameStore();
  
  useEffect(() => {
    // Initialize PIXI app
    // Create renderer
    // Set up game loop
    // Connect to WebSocket
  }, []);
  
  return <div ref={canvasRef} />;
};
```

**Template 2: Rust WebSocket Handler**
```rust
// Saves 45 minutes of Warp boilerplate
use warp::ws::{WebSocket, Ws};
use futures::{SinkExt, StreamExt};
use tokio::sync::broadcast;

pub async fn handle_connection(
  ws: WebSocket,
  game_state: Arc<Mutex<GameState>>,
) {
  let (mut tx, mut rx) = ws.split();
  
  // Subscribe to game updates
  // Send initial state
  // Handle incoming messages
  // Update game state
}
```

**Template 3: Complete Game Loop**
```rust
// Saves 60 minutes of tick/physics logic
#[tokio::main]
async fn main() {
  let mut game = Game::new();
  let mut interval = tokio::time::interval(Duration::from_millis(33)); // 30 FPS
  
  loop {
    interval.tick().await;
    
    game.update(); // 1ms
    game.apply_physics(); // 5ms
    game.apply_lenia(); // 15ms
    game.broadcast_state(); // 5ms
    
    // Total: 26ms per frame (budget: 33ms)
  }
}
```

### 5.3 Testing Strategy (Don't Slow Down)

**Rule: Test behaviors, not implementation**

```typescript
// Good test - catches real bugs
test('cell dies when energy = 0', () => {
  const cell = createCell({ energy: 0 });
  expect(cell.alive).toBe(false); // Catches if behavior changes
});

// Bad test - tightly coupled to implementation
test('cell calls decreaseEnergy', () => {
  const mock = jest.fn();
  cell.decreaseEnergy = mock;
  cell.tick();
  expect(mock).toBeCalled(); // Breaks if we rename method
});

// Good test - integration
test('colony with 10 cells and 100 resources, after 10 ticks has <100 resources', () => {
  const colony = createColony({ cellCount: 10, resources: 100 });
  for (let i = 0; i < 10; i++) colony.tick();
  expect(colony.resources).toBeLessThan(100);
});
```

**Test Coverage Targets:**
- Core game mechanics: 80%
- UI components: 30% (just happy path)
- WebSocket: 60% (just message types)
- Rust simulation: 90% (critical, catches bugs early)

**Test Framework:**
- Frontend: Vitest (5x faster than Jest)
- Backend: Rust built-in (#[test])

### 5.4 Reusable Code Snippets Library

Create `/snippets` folder in project:

```
snippets/
├─ cell-growth.rs          (copy/paste Lenia logic)
├─ neighbor-detection.rs   (copy/paste spatial queries)
├─ cell-component.tsx      (copy/paste React pattern)
├─ ws-client.ts            (copy/paste WebSocket)
├─ game-loop.rs            (copy/paste simulation tick)
├─ zustand-store.ts        (copy/paste state management)
└─ pixi-renderer.ts        (copy/paste canvas setup)
```

Each snippet:
- 30-100 LOC
- Documented with // comments
- Generic enough to customize
- Tested and verified working

**Usage:** Copy → Paste → Search/Replace variables → Done

**Time savings:** ~10 minutes per snippet (~70 LOC per 10 min vs 20 LOC/min manual)

### 5.5 AI Code Generation Prompts (Copy/Paste Ready)

#### Prompt 1: Full Component Generation
```
Generate a React component that displays a leaderboard for Cell Garden.
Requirements:
- Show top 10 colonies by cell count
- Columns: Rank, Colonyname, Cells, Energy, Created
- Sort by descending cell count
- Update every 10 seconds via WebSocket
- Use Shadcn UI Table component
- TypeScript strict mode
- Include loading state
```

Expected output: 320 LOC, 5 minutes

#### Prompt 2: Game Logic Generation
```
Create Rust functions for cell reproduction in Cell Garden.
Rules:
- Cell reproduces when energy > 150
- Offspring energy = 40, parent energy -= 60
- Offspring gets mutations: 5% chance per gene
- Maximum 4 genes per cell
- Genes: speed (0-1), size (0-1), color (0-6)
- Return new cell and updated parent
- Include error handling for invalid states
```

Expected output: 250 LOC, 4 minutes

#### Prompt 3: State Management
```
Create Zustand store for Game of Life multiplayer.
State:
- 3 independent colonies (id, name, cells[])
- Global resources (water, energy, minerals)
- Game settings (speed, pause, zoom, pan)
- UI state (selected colony, showStats, tooltip)
Actions:
- updateCell(colonyId, cellId, data)
- spawnCell(colonyId, position)
- setGameSpeed(speed)
- togglePause()
- transferResources(from, to, amount)
- resetColony(colonyId)
```

Expected output: 380 LOC, 6 minutes

### 5.6 Development Timeline (4-8 Week Path)

```
WEEK 1: Foundation (80 hours)
├─ Day 1-2: Scaffold repos, set up CI/CD (Claude Code: 40%)
├─ Day 2-3: Create types, contracts (Claude Code: 90%)
├─ Day 4-5: Rust game loop + Lenia physics (Claude Code: 70%, manual: growth logic)
└─ Day 6-7: WebSocket server, basic broadcasting (Claude Code: 80%)
│
├─ DELIVERABLE: Server runs, broadcasts empty game state
└─ Time: 56 hours
│
WEEK 2: MVP Gameplay (80 hours)
├─ Day 1-2: React canvas + Pixi.js renderer (Cursor: 60% incremental)
├─ Day 2-3: Connect to WebSocket, receive state (Claude Code: 85%)
├─ Day 3-4: Cell spawning, energy decay, death (Claude Code: 75%)
├─ Day 5-6: Reproduction & mutation (Claude Code: 70%, manual: genetics)
└─ Day 6-7: Basic UI panels (V0.dev: 70%, manual: layout)
│
├─ DELIVERABLE: Playable game, cells born/die/reproduce
└─ Time: 64 hours
│
WEEK 3-4: Content & Polish (160 hours)
├─ Cell visuals (shaders, animations) - (Cursor: 50%)
├─ Resource system (water, nutrients) - (Claude Code: 75%)
├─ Colony clustering & species tracking - (Claude Code: 80%)
├─ Leaderboards UI - (V0.dev: 80%)
├─ Settings panel - (Shadcn components: 95%)
├─ Sound effects (Howler.js) - (Cursor: 70%)
└─ Performance optimization - (Manual: 40%)
│
├─ DELIVERABLE: Feature-complete game
└─ Time: 128 hours
│
WEEK 5-6: Testing & Hardening (120 hours)
├─ Unit tests (game mechanics) - (Claude Code: 80%)
├─ Integration tests (WS communication) - (Claude Code: 70%)
├─ Performance testing - (Manual: 60%)
├─ Bug fixes & edge cases - (Manual: 70%)
├─ Mobile responsiveness - (Cursor: 65%)
└─ Cross-browser testing - (Manual: 50%)
│
├─ DELIVERABLE: Beta ready
└─ Time: 96 hours
│
WEEK 7-8: Deployment & Launch (80 hours)
├─ Deploy to Railway/Vercel - (Manual: 30%)
├─ Database setup & migrations - (Manual: 40%)
├─ Monitoring & logging - (Claude Code: 75%)
├─ Documentation & README - (Claude Code: 85%)
├─ Marketing site - (Bolt.new: 80%)
└─ Analytics setup - (Manual: 50%)
│
└─ DELIVERABLE: Live game
   Time: 64 hours
```

**Total Hours: 408 hours (~24 weeks part-time @ 17 hrs/week)**
**Actual time with AI tools: ~200 hours (49% reduction)**

---

## PART 6: PRODUCTION CHECKLIST

### Pre-Launch (Week 7-8)

```
DEPLOYMENT
─ [ ] Frontend deployed to Vercel
─ [ ] Backend deployed to Railway
─ [ ] Custom domain configured
─ [ ] SSL certificates auto-renewed
─ [ ] Environment variables set
─ [ ] Secrets not in version control
─ [ ] Build process automated (GitHub Actions)
─ [ ] Rollback procedure documented

TESTING
─ [ ] 80% code coverage (game mechanics)
─ [ ] Performance: <50ms WebSocket latency
─ [ ] Performance: 60fps canvas rendering
─ [ ] Mobile: Works on iOS Safari + Chrome Android
─ [ ] Cross-browser: Chrome, Firefox, Safari
─ [ ] Load test: 100 concurrent users
─ [ ] Chaos test: Kill server, reconnect works
─ [ ] Security: No secrets leaked in network tab

DATABASE
─ [ ] SQLite backups automated
─ [ ] Migrations tested
─ [ ] Scaling plan documented
─ [ ] Data retention policy clear
─ [ ] GDPR compliance (if EU users)

MONITORING
─ [ ] Sentry error tracking active
─ [ ] Server logs accessible
─ [ ] Uptime monitoring enabled
─ [ ] Database size monitored
─ [ ] CDN cache configured

DOCUMENTATION
─ [ ] README with setup instructions
─ [ ] Architecture doc for contributors
─ [ ] API documentation (WebSocket messages)
─ [ ] Deployment guide
─ [ ] Known issues & limitations
```

### Post-Launch (Week 9+)

```
ANALYTICS
─ [ ] Play sessions tracked
─ [ ] User retention monitored
─ [ ] Feature usage analytics
─ [ ] Performance metrics dashboarded

COMMUNITY
─ [ ] Discord/Twitter for feedback
─ [ ] Bug reports template
─ [ ] Feature request process
─ [ ] Regular updates (2x/month)

MONETIZATION (Optional)
─ [ ] Ad integration (if needed)
─ [ ] Premium features identified
─ [ ] Stripe integration (if paid tier)
─ [ ] License key system (if desktop)
```

---

## FINAL RECOMMENDATIONS

### What to Do First (Day 1)

1. Create GitHub repo (private, free tier)
2. Set up Rust backend project: `cargo new cell_garden_backend`
3. Set up React frontend: `npm create vite@latest -- --template react-ts`
4. Create `.cursorrules` file (copy from section 1.3)
5. Deploy empty projects to Vercel + Railway (20 minutes total)
6. Celebrate - you're now 5% through the build!

### Critical Success Factors

1. **Use Claude Code for >70% generation** - It's trained on React/Rust patterns
2. **Keep Rust backend simple** - Game simulation only, no fancy features
3. **Don't overengineer** - MVP first, optimization later
4. **Test as you go** - Catch bugs early when they're cheap to fix
5. **Commit frequently** - Atomic commits every 30-60 minutes
6. **Ask AI for help** - It's literally designed to solve this

### Red Flags to Avoid

```
DON'T:
✗ Add multiplayer before MVP works
✗ Use 3D (Three.js) for MVP
✗ Build custom UI components from scratch
✗ Optimize before testing
✗ Use paid services on free tier
✗ Ignore TypeScript errors
✗ Skip WebSocket testing

DO:
✓ Copy existing patterns (Lenia, neighbor detection)
✓ Use Pixi.js, it's perfect for this
✓ Let Claude Code write 50% of code
✓ Deploy early and often
✓ Get feedback in week 2
✓ Keep it simple until proven necessary
✓ Have fun! Build in public, share progress
```

---

## APPENDIX: Exact File Structure

```
cell-garden/
├─ .github/
│  └─ workflows/
│     ├─ test.yml
│     ├─ deploy-frontend.yml
│     └─ deploy-backend.yml
│
├─ frontend/
│  ├─ src/
│  │  ├─ components/
│  │  │  ├─ GameCanvas.tsx
│  │  │  ├─ CellDetailPanel.tsx
│  │  │  ├─ LeaderboardPanel.tsx
│  │  │  ├─ SettingsPanel.tsx
│  │  │  └─ StatsPanel.tsx
│  │  ├─ hooks/
│  │  │  ├─ useGameLoop.ts
│  │  │  ├─ useWebSocket.ts
│  │  │  └─ usePixiRenderer.ts
│  │  ├─ store/
│  │  │  └─ gameStore.ts
│  │  ├─ types/
│  │  │  └─ index.ts
│  │  ├─ utils/
│  │  │  ├─ colors.ts
│  │  │  └─ math.ts
│  │  ├─ App.tsx
│  │  └─ main.tsx
│  ├─ public/
│  ├─ package.json
│  ├─ tsconfig.json
│  ├─ vite.config.ts
│  └─ .cursorrules
│
├─ backend/
│  ├─ src/
│  │  ├─ api/
│  │  │  └─ mod.rs
│  │  ├─ game/
│  │  │  ├─ cell.rs
│  │  │  ├─ colony.rs
│  │  │  ├─ lenia.rs
│  │  │  └─ mod.rs
│  │  ├─ models/
│  │  │  ├─ types.rs
│  │  │  ├─ constants.rs
│  │  │  └─ mod.rs
│  │  ├─ server/
│  │  │  ├─ ws.rs
│  │  │  └─ mod.rs
│  │  ├─ main.rs
│  │  └─ lib.rs
│  ├─ tests/
│  │  ├─ game_tests.rs
│  │  └─ physics_tests.rs
│  ├─ Cargo.toml
│  ├─ Cargo.lock
│  └─ Dockerfile
│
├─ shared/
│  ├─ types.ts          (TypeScript)
│  └─ types.rs          (Rust, use shared models)
│
├─ .gitignore
├─ README.md
├─ DEPLOYMENT.md
├─ ARCHITECTURE.md
└─ .cursorrules
```

---

## COST SUMMARY

| Category | Cost | How to Minimize |
|----------|------|---|
| Development | $0 (your time) | Use AI tools (80% reduction) |
| Hosting | $0 | Free tiers (Vercel, Railway, Neon) |
| Services | $0 | Sentry free tier |
| Domain | $0 | Free subdomain first month |
| **Total Year 1** | **$0** | ✓ Achieved |

---

## CONCLUSION

Cell Garden is achievable as a solo project in 6-8 weeks with modern AI tools. The key is:

1. **Leverage CREATURE's 5,600 LOC** (save 120 hours)
2. **Use Claude Code for 70% generation** (save 100 hours)
3. **Copy existing design patterns** (save 50 hours)
4. **Deploy to free tier services** (save $10k+ year 1)
5. **Keep scope focused on MVP** (don't overengineer)

**Expected outcome:** Fully playable game with 10k+ lines of code, zero upfront cost, deployable anywhere.

Good luck! 🚀

---

*Document Version: 1.0 | Last Updated: Nov 20, 2025*
*Optimized for: Solo developers + Claude Code + Cursor AI*
*Estimated time to build: 408 hours solo, 200 hours with AI assistance*

