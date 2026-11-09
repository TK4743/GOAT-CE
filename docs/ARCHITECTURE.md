# 🏛️ Architecture & System Design — GOAT Code Editor

> **Code Thugs 2K26 National Hackathon Winner (1st Place)**  
> Comprehensive 14-page academic & technical report: [`docs/report/project-report.pdf`](./report/project-report.pdf) | LaTeX Source: [`docs/report/project-report.tex`](./report/project-report.tex)

---

## 1. System Overview

**GOAT Code Editor** (GOAT-CE) is a real-time collaborative cloud development environment that enables distributed software developers to simultaneously write, format, execute, and debug source code with millisecond synchronization, peer cursor awareness, and low-latency peer-to-peer WebRTC voice communication.

```
+-------------------+      WebRTC Audio Mesh     +-------------------+
|  Client Peer A    | <========================> |   Client Peer B   |
| (Monaco + React)  |                            | (Monaco + React)  |
+-------------------+                            +-------------------+
          |                                                |
          | Socket.IO (Edits / Cursors)                    |
          v                                                v
+--------------------------------------------------------------------+
|               Authoritative Node.js Orchestrator                   |
|   - Room Namespace Router         - LWW Conflict Resolver          |
|   - Operational Broadcast Mesh    - Sandboxed Execution Engine     |
+--------------------------------------------------------------------+
          |                                                |
          v                                                v
+-------------------+                            +-------------------+
|   MongoDB Atlas   |                            | OpenRouter Engine |
| (State Snapshots) |                            | (Llama 3.1 70B)   |
+-------------------+                            +-------------------+
```

---

## 2. Real-Time Synchronization Engine

* **Last-Write-Wins (LWW) Policy:** Resolves concurrent edits using authoritative server timestamps $\tau_{\text{server}}$ and incremental monotonically increasing version tags $V_{\text{seq}}$.
* **Debounced Keystroke Streaming:** Frontend keystrokes are batched in 25ms debounce windows to eliminate network congestion.
* **Throttled Cursor Awareness:** Remote cursor position broadcasts are capped at 30 Hz (33.3ms intervals) with custom Monaco CSS decorations.

---

## 3. Role-Based Access Control (RBAC) Governance

| Role / Identity | Permitted Scope | Enforced Constraints | Security Token |
| :--- | :--- | :--- | :--- |
| **Room Owner / Admin** | Room configuration, participant eviction, lock/unlock editing | Full administrative privileges over room state | Cryptographic Room Key |
| **Collaborator / Editor** | Simultaneous buffer editing, code execution, cursor broadcast | Subject to room lock state; cannot alter room settings | Authenticated Session ID |
| **Reviewer / Proctor** | View live code and execution output, participate in audio | Read-only buffer; cannot emit edit operations | Proctor Access Badge |
| **Guest / Spectator** | View-only stream of editor canvas and console output | Zero voice, zero edit permissions | Anonymous Ticket |

---

## 4. Key Performance Benchmarks

* **Bandwidth Usage:** ~18 kbps (Socket deltas) vs. 3,200 kbps for video screen-sharing (**177x lower bandwidth**).
* **Input-to-Display Latency:** 22–38 ms vs. 280–450 ms for screen share (**10x lower latency**).
* **Client Memory Footprint:** ~115 MB RAM vs. 850 MB for screen-share tab (**7.3x lower memory**).
* **WebRTC Voice Mesh Latency:** $< 15$ ms peer-to-peer UDP audio transmission.
