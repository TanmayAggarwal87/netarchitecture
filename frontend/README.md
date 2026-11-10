# NetArchitect Frontend: Network Design Studio & Topology Visualizer

**Author**: Tanmay Aggarwal  
**Enrollment No**: 00714802724  
**Course**: CIC-307 Computer Networks  
**Framework**: React 19 + TypeScript + Vite 8 + Tailwind CSS v4  

---

## Overview

The `frontend` application is an interactive single-page application (SPA) built with **React 19**, **TypeScript**, and **Vite**. It provides a dark-themed user interface for designing computer networks, inspecting mathematical VLSM address allocations, visualizing dynamic network topologies via **React Flow**, and downloading hardened Cisco IOS configuration templates.

---

## Key UI Workspaces & Views

1. **Overview Dashboard**:
   - Real-time key performance indicators (Total Sites, Connected Endpoints, VLAN Segments, Subnet Blocks).
   - High-level network architecture snapshot and algorithmic decision highlights.
   - Address Space Utilization Area Chart powered by **Recharts** showing used host demand vs. available subnet addresses.

2. **Interactive Topology Canvas (`src/components/Topology.tsx`)**:
   - Rendered using **React Flow** with custom SVG node representations for routers, switches, cloud gateways, and endpoints.
   - Real-time link status and encryption indicators.
   - Supports interactive canvas panning, zooming, node dragging, and one-click SVG vector graphic export.

3. **VLSM Address Planner**:
   - Interactive table detailing VLAN ID, Subnet Name, Host Demands, CIDR Notation, Dotted-Decimal Subnet Mask, Wildcard Mask, Default Gateway SVI, Usable IP Range, Broadcast Address, and Pool Utilization %.
   - Live non-overlapping address space verification indicator.

4. **Routing & Media Explorer**:
   - In-depth technical explanations of the recommended routing protocol (Single-Area OSPF, Multi-Area OSPF, or eBGP).
   - Comprehensive link-by-link media breakdowns (Cat6a copper, OM4 multimode fiber, OS2 single-mode fiber) with transceivers, wavelengths, and applicable IEEE 802.3 standards.
   - Route summarization candidates and aggregate CIDRs.

5. **Cisco IOS Configurations (`src/components/ConfigPanel.tsx`)**:
   - Split-pane CLI script viewer for:
     - **Distribution Switch** (`*-DIST-01`): SVIs, OSPF Area 0, extended ACLs, and 802.1Q trunking.
     - **Access Switch** (`*-ACCESS-01`): PortFast, BPDU Guard, access ports, and native VLAN blackhole.
     - **WAN Router** (`*-EDGE-01`): Dynamic BGP peering, QoS queuing, and IPsec VPN proposals.
   - Download individual device `.txt` scripts or export a complete bundled `.zip` archive.

6. **Custom Network Builder**:
   - Dynamic parameter controls allowing runtime modification of:
     - Network Type (LAN, MAN, WAN, Hybrid)
     - Site, Building, User, Endpoint, Switch, and Router Counts
     - Latency Targets (ms) and Root IPv4 CIDR Blocks
     - VLAN segments (ID, Name, Host Requirements)
     - Physical Links (Distance in meters, Bandwidth in Gbps, Requested Media)
     - Directional Extended Security ACL Policies (Source VLAN, Destination CIDR, Protocol, Action)
     - Redundant Links, Cloud VPC Integration, IPsec VPN, QoS, and PoE toggles.

7. **AI Code Audit Log**:
   - In-app rendering of formal engineering audit document detailing generative AI defects, mathematical root cause analyses, and RFC-compliant fixes.

---

## Directory Layout

```text
frontend/
├── Dockerfile                 # Container build specification (Node.js & Vite)
├── package.json               # NPM package manifest & script definitions
├── index.html                 # HTML application entrypoint
├── vite.config.ts             # Vite bundler configuration & plugins
├── tsconfig.json              # TypeScript compiler options
└── src/
    ├── main.tsx               # React root renderer
    ├── App.tsx                # Main design studio controller & workspace router
    ├── style.css              # Custom modern dark-theme styles & variables
    ├── components/
    │   ├── ConfigPanel.tsx    # IOS script viewer, syntax highlight, & ZIP export
    │   ├── Topology.tsx       # React Flow canvas & SVG export engine
    │   └── ui/
    │       └── button.tsx     # Reusable accessible button component
    ├── types/
    │   └── network.ts         # TypeScript interfaces & domain data models
    └── utils/
        └── cn.ts              # Tailwind CSS class merging utility
```

---

## Local Setup & Development

### Prerequisites
- Node.js version 20.19+ or 22.12+
- `npm` package manager

### Installation
```bash
# 1. Navigate to the frontend directory
cd frontend

# 2. Install dependencies
npm install

# 3. Create local environment configuration
cp .env.example .env
```

### Environment Variables (`.env`)
```bash
# URL pointing to the FastAPI backend service
VITE_API_URL=http://localhost:8000/api
```

### Running the Development Server
```bash
npm run dev
```
*The development server will launch at `http://localhost:5173` with instant Hot Module Replacement (HMR).*

### Building for Production
```bash
# Type check and build optimized static assets
npm run build

# Preview production build locally
npm run preview
```
