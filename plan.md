ChainReaction: Master Execution Plan

1. Objective

Build an Autonomous Supply Chain Agent ("ChainReaction") that visualizes logistics in real-time and automatically
executes financial arbitrage (e.g., booking relief trucks) when delays occur.

2. Tech Stack & Architecture

A. Frontend (Next.js 14)

Routing: App Router (/, /login, /demo, /dashboard, /track/[id]).

Theme: Strict Dark Mode (bg-slate-950) using next-themes.

Maps: react-leaflet (OpenStreetMap).

Config: Darken tiles using CSS filter: invert(100%) hue-rotate(180deg) brightness(95%) contrast(90%).

State: React Context for global truck data.

UI Library: Tailwind CSS + Framer Motion + Lucide React.

B. Backend (Python 3.11)

Core: pathway (Streaming Engine).

Network: websockets (Real-time push).

AI: openai (GPT-4o) for Contract PDF Parsing.

Simulation: Custom script to generate deterministic GPS paths (Normal -> Critical -> Resolved).

3. Step-by-Step Implementation

Phase 1: Backend (The Data Source)

File: backend/simulation.py

Generate Stream: Create a loop that emits JSON coordinates for "Truck-402" moving from Pune to Mumbai.

Chaos Logic: At T+10 seconds, force speed: 0 and status: CRITICAL.

AI Logic: When CRITICAL, call OpenAI:

Prompt: "Penalty is $2500. Spot truck is $800. Calculate savings."

Result: Add {"savings": 1700, "action": "Switch"} to the stream.

Broadcast: Push JSON to ws://localhost:8080.

Phase 2: Frontend Structure (The Views)

File: src/app/...

Landing Page (/):

Hero: "Supply Chain is Broken. We Automate the Fix."

Action: "Launch Platform" (Redirects to /login), "Book Demo" (Redirects to /demo).

Widget: "Track Order" input (Redirects to /track/ORD-402).

Login Page (/login):

Visual: Centered glass card on deep navy grid.

Action: "Sign In" button -> Spinner (1s) -> Push to /dashboard.

Book Demo (/demo):

Layout: Value Prop (Left) + Calendar Mockup (Right).

Dashboard (/dashboard) - The Core:

Map: Full screen. Green markers for moving trucks.

Right Sidebar: "Agent Stream" (Live logs from WebSocket).

Modal: "Arbitrage Opportunity" (Hidden by default).

Phase 3: Integration Logic

WebSocket Hook:

Connect to ws://localhost:8080.

Update trucks state array.

The Trigger:

Watch: if (truck.status === 'CRITICAL').

Action: Set showModal(true).

The Fix:

User clicks "Execute Fix" in Modal.

Visual: Truck marker turns Purple. "Total Savings" header updates.

4. File Structure

chain-reaction/
├── backend/
│ ├── simulation.py # Generates GPS + Chaos
│ └── server.py # WebSocket Host
├── src/
│ ├── app/
│ │ ├── page.tsx # Landing
│ │ ├── login/page.tsx # Auth Mock
│ │ ├── demo/page.tsx # Calendar
│ │ ├── dashboard/page.tsx # Main App
│ │ └── track/[id]/page.tsx # Customer View
│ ├── components/
│ │ ├── Map/LeafletMap.tsx
│ │ ├── UI/ArbitrageModal.tsx
│ │ └── Layout/Navbar.tsx
│ └── hooks/useSocket.ts
