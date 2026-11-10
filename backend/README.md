# NetArchitect Backend: Algorithmic Recommendation & Configuration Engine

**Author**: Tanmay Aggarwal  
**Enrollment No**: 00714802724  
**Course**: CIC-307 Computer Networks  
**Framework**: FastAPI 1.0 (Python 3.12+)  

---

## Overview

The `backend` service is the computational core of NetArchitect. It exposes a high-performance RESTful API powered by **FastAPI** and **Pydantic v2** that validates network designs, performs mathematical subnetting, evaluates physical layer constraints, selects routing architectures, and synthesizes Cisco IOS configuration templates.

---

## Directory Layout

```text
backend/
├── Dockerfile                 # Container specification (Python 3.12-slim)
├── requirements.txt           # Production & test dependencies
├── tests/
│   ├── __init__.py
│   └── test_engines.py        # 30 comprehensive unit & API integration tests
└── app/
    ├── __init__.py
    ├── main.py                # FastAPI application entrypoint & CORS middleware
    ├── api/
    │   ├── __init__.py
    │   └── routes.py          # REST API endpoints & error handlers
    ├── engines/               # Core deterministic algorithmic engines
    │   ├── analyze_engine.py  # Orchestrator combining all engine outputs
    │   ├── config_engine.py   # Cisco IOS script synthesis & ZIP bundling
    │   ├── media_engine.py    # Deterministic IEEE 802.3 media & transceiver engine
    │   ├── routing_engine.py  # Routing protocol scope decision matrix
    │   └── vlsm_engine.py     # RFC 4632 / RFC 3021 IPv4 VLSM address allocator
    ├── profiles/
    │   └── catalog.py         # Static preset topologies (Campus LAN, Metro MAN, Global WAN)
    └── schemas/
        └── network.py         # Strict Pydantic models for request/response validation
```

---

## Core Algorithmic Engines

### 1. VLSM Engine (`app/engines/vlsm_engine.py`)
- **Mathematical Prefix Calculation**: Computes the exact power-of-two address block required for $H$ hosts plus 1 Layer 3 gateway plus 2 overhead addresses (Network ID + Directed Broadcast):
  $$\text{required} = H + 1 + 2$$
  $$\text{bits} = (\text{required} - 1).\text{bit\_length}() \implies \text{prefix} = 32 - \text{bits}$$
- **Descending Sort**: Requests are sorted by required prefix size descending to eliminate external address space fragmentation.
- **Boundary Alignment**: Each allocation is strictly aligned to natural binary boundaries with non-overlap validation.
- **RFC 3021 & RFC 4632 Support**: Handles `/31` point-to-point links (2 usable addresses) and `/32` loopback interfaces (1 usable address).
- **Mask Calculations**: Derives dotted-decimal subnet masks, inverted wildcard masks (e.g. `0.0.1.255`), and gateway addresses.

### 2. Physical Media Engine (`app/engines/media_engine.py`)
- **Deterministic Distance Mapping**:
  - $0 \le d \le 100\text{ m}$: Cat6a copper / 10GBASE-T (IEEE 802.3an)
  - $100 < d \le 400\text{ m}$: OM4 multimode fiber / 10GBASE-SR (850 nm, IEEE 802.3ae)
  - $400 < d \le 10,000\text{ m}$: OS2 single-mode fiber / 10GBASE-LR (1310 nm, IEEE 802.3ae)
  - $10,000 < d \le 40,000\text{ m}$: OS2 single-mode fiber / 10GBASE-ER (1550 nm, IEEE 802.3ae)
  - $d > 40,000\text{ m}$: Carrier Ethernet over engineered OS2 transport / DWDM
- **Strict Physical Rule Enforcement**: Raises `PhysicalDistanceViolationError` if copper cabling is requested for links longer than 100 meters.

### 3. Routing Engine (`app/engines/routing_engine.py`)
- Evaluates network scope, multi-site topology, cloud boundaries, and subnet counts:
  - **Single-Area OSPF (Area 0)**: Applied to single-site topologies with $<50$ subnets.
  - **Hierarchical Multi-Area OSPF**: Applied to multi-site or large domains ($\ge 50$ subnets).
  - **Multi-Area OSPF + eBGP**: Applied when cloud infrastructure (VPC/ExpressRoute) or autonomous system boundaries are present.
  - **Static Routing**: Applied when deterministic fixed paths are explicitly requested.

### 4. Configuration Engine (`app/engines/config_engine.py`)
- Synthesizes modular Cisco IOS configuration files:
  - **Layer 3 Distribution Switch (`*-DIST-01`)**: SVIs, `ip routing`, OSPF Area 0, extended ACLs, and 802.1Q trunking.
  - **Layer 2 Access Switch (`*-ACCESS-01`)**: PortFast, BPDU Guard, access VLAN tagging, and unused port shutdown.
  - **WAN Edge Router (`*-EDGE-01`)**: eBGP neighbor statements, QoS queueing (`class-map`, `policy-map`), and IKEv2 proposals.
- Dynamically assigns an unused high-order VLAN (e.g. VLAN 4094) as a blackholed native VLAN on all 802.1Q trunks to mitigate VLAN hopping attacks.

---

## Local Setup & Development

### Requirements
- Python 3.12 or newer
- Virtual environment tool (`venv` or `virtualenv`)

### Installation
```bash
# 1. Create virtual environment
python3 -m venv .venv

# 2. Activate virtual environment
source .venv/bin/activate  # On Linux/macOS
# or .venv\Scripts\activate on Windows

# 3. Install dependencies
pip install -r requirements.txt
```

### Running the API Server
```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
```
- API Base URL: `http://localhost:8000`
- Interactive OpenAPI / Swagger UI: `http://localhost:8000/docs`
- ReDoc Documentation: `http://localhost:8000/redoc`

---

## Automated Test Suite

The backend includes a comprehensive suite of unit and integration tests executing against FastAPI's `TestClient`:

```bash
# Run pytest
pytest -v

# Run pytest with code coverage
pytest --cov=app.engines --cov-report=term-missing
```

### Key Test Coverage Areas
1. **Media Boundaries**: 100 m, 101 m, 400 m, 401 m, 50,000 m thresholds.
2. **Distance Violations**: Copper $>100\text{ m}$ raises `PhysicalDistanceViolationError`.
3. **VLSM Precision**: Descending sort allocation, non-overlapping subnet blocks, exact wildcard mask computation, and boundary alignment.
4. **Special Addressing**: RFC 3021 `/31` point-to-point and `/32` host loopback calculations.
5. **Cisco IOS Generation**: Valid syntax for L3 distribution, L2 access, and WAN router templates.
6. **ZIP Streaming**: Valid binary archive generation and export headers.
