# Research: How Multi-Agent Frameworks Handle Work Management

_Compiled 2026-03-19 by Jarvis for Yam_

---

## Framework Comparison

| Framework | Work Model | Memory/Continuity | Human-in-Loop | Task Tracking | Best For |
|-----------|-----------|-------------------|---------------|---------------|----------|
| **CrewAI** | Role-based crews with tasks | Short-term (per crew run) | Human input mode, delegation | Task objects with expected output | Structured team workflows |
| **AutoGen/AG2** | Conversation-based agents | Session-based state | UserProxyAgent + handoffs | Implicit (conversation flow) | Flexible multi-agent chat |
| **LangGraph** | Graph-based state machines | Checkpoints + persistent state | Interrupt nodes + human approval | State-based (graph nodes) | Complex workflows with control |
| **OpenAI Swarm** | Lightweight handoffs | Stateless (per-call) | None built-in | None | Prototyping, simple routing |
| **Devin** | Autonomous IDE + chat | Long-term planning + learning | Real-time chat + progress reports | Slack/chat-driven sessions | Software engineering tasks |
| **Cursor/Windsurf** | IDE-embedded copilot | Project-level context | Always present (editor) | None (editor-native) | Code editing in-flow |
| **Linear + AI** | Issue tracker as source of truth | Full project history | Assign issues to agents or humans | Native Linear issues/cycles | Team project management |
| **OpenClaw** | File-based workspace + sessions | MEMORY.md + daily logs + vector search | Chat-driven (Telegram/Discord) | File-based (manual) | Personal agent + daily ops |

---

## Common Patterns Across Frameworks

### 1. Task Definition
- **CrewAI**: Explicit Task objects with description, expected output, assigned agent
- **AutoGen**: Tasks embedded in conversation initiation messages
- **LangGraph**: Tasks are graph traversals with typed state
- **Devin**: Natural language task via Slack → autonomous execution
- **OpenClaw**: Natural language via chat → agent decides approach

### 2. Memory & Continuity
- **Short-lived** (Swarm, basic CrewAI): No persistence between runs
- **Session-based** (AutoGen/AG2, LangGraph): State within a session, checkpoints optional
- **File-based** (OpenClaw): MEMORY.md + daily logs survive forever, vector-indexed
- **Long-term** (Devin): Learns from past sessions, remembers project context

### 3. Human Involvement Patterns
- **Always-in-loop**: Cursor/Windsurf (human is editing), OpenClaw chat (human converses)
- **Approval gates**: LangGraph interrupts, AutoGen human_input_mode
- **Async delegation**: Devin (assign → comes back done), Linear (issue → agent picks up)
- **Observe-only**: CrewAI (crew runs, human reviews output)

### 4. Progress Reporting
- **Real-time chat**: Devin (Slack updates), OpenClaw (Telegram messages)
- **Tracing/dashboards**: CrewAI (real-time trace), LangGraph (LangSmith)
- **None**: Swarm, most lightweight frameworks

---

## What's Unique/Innovative in Each

### CrewAI — Role-playing delegation
Agents have roles, goals, and backstory. Tasks can be delegated between agents autonomously. "Flows" add event-driven control on top. Good for repeatable structured work.

### AutoGen/AG2 → Microsoft Agent Framework — Enterprise state management
Merging with Semantic Kernel. Session-based state management for long-running scenarios. Human-in-the-loop via handoff mechanism (agent says "I need human input" → control transfers).

### LangGraph — Explicit control flow
Graph-based: you define exactly when human review happens (interrupt nodes). Persistent checkpoints mean you can resume from any point. Best for workflows that need auditability.

### Devin — Autonomous colleague model
Closest to "give a task, get results back." Reports progress in Slack, accepts feedback mid-task. Teaches itself from examples before tackling migrations at scale. The "junior dev on your team" pattern.

### Linear for Agents — Issue tracker as shared workspace
Agents are first-class workspace members. Assigned to issues, @mentioned in comments, tracked in cycles. The issue itself is the communication channel. Most natural for teams already using project management tools.

### OpenClaw — Personal agent with file-based memory
Unique strengths:
- **SOUL.md / AGENTS.md**: Agent has personality, rules, and operating procedures
- **Memory layering**: Daily logs + curated long-term memory + vector search
- **Heartbeats**: Proactive periodic check-ins (not just reactive)
- **Multi-channel**: Same agent across Telegram, Discord, etc.
- **Sub-agents**: Can spawn specialized workers for parallel tasks
- **Cron**: Scheduled autonomous tasks
- **Git-backed**: Workspace is version-controlled

---

## Gaps — What None of Them Solve Well

1. **Unified task board for human+agent**: No framework gives a shared view where both human and agent see "what's in progress, what's next, what's blocked." Linear comes closest but requires external tooling.

2. **Priority negotiation**: Human says "this is urgent" but agent has 3 things in flight. No framework handles graceful priority shifting well.

3. **Long-term strategic planning**: All frameworks are task-level. None help with "what should we focus on this week/month?"

4. **Accountability & audit trail**: Who decided what and when? CrewAI tracing helps. OpenClaw has memory files. But no framework provides clean decision logs.

5. **Workload visibility**: How much is the agent doing? What's queued? What's blocked on me? No good answers anywhere.

6. **Feedback loops that improve over time**: Devin's learning is the closest. Most frameworks treat each run as independent.

---

## Recommendations for Our Setup (Yam + Jarvis via Telegram)

### What We Already Have (OpenClaw native)
- ✅ Chat-driven interface (Telegram)
- ✅ Memory system (MEMORY.md + daily logs)
- ✅ Heartbeats for proactive check-ins
- ✅ Sub-agents for parallel work
- ✅ Cron for scheduled tasks
- ✅ Git-backed workspace

### What's Missing / Could Be Better

1. **A shared "task board"** — We need a simple, visible list of:
   - What's in progress
   - What's next (prioritized)
   - What's blocked/waiting
   - What's done recently

2. **Structured intake** — When Yam says "do X", it should get captured reliably (not lost in chat history)

3. **Progress visibility** — Yam should be able to ask "what are you working on?" and get a real answer at any time

4. **Decision log** — Key decisions should be tracked with context (why, when, alternatives considered)

5. **Weekly/daily rhythm** — A structured check-in cadence beyond ad-hoc heartbeats

---

## Proposed Work Management System for Yam + Jarvis

### Option A: File-based (Pure OpenClaw)
Use workspace files as the task board:
- `BACKLOG.md` — prioritized list of upcoming work
- `ACTIVE.md` — what's currently in progress (updated by Jarvis)
- `DECISIONS.md` — log of key decisions with context
- `memory/YYYY-MM-DD.md` — daily notes (already exists)

**Pros**: Simple, no external tools, git-tracked, agent reads/writes natively
**Cons**: No UI, relies on discipline, no notifications beyond heartbeat

### Option B: GitHub Issues (Lightweight PM)
Use a dedicated GitHub repo (or kernelius-workspace) for task management:
- Issues = tasks
- Labels = priority/status
- Milestones = weekly/monthly goals
- Jarvis creates/closes issues, comments progress

**Pros**: Real UI, notifications, history, labels, assignees
**Cons**: Extra hop from Telegram, more ceremony for small tasks

### Option C: Hybrid (Recommended)
Combine both:
- **Quick tasks**: Chat-driven → Jarvis captures in daily notes
- **Bigger work items**: GitHub issues in kernelius-workspace (or dedicated repo)
- **BACKLOG.md**: High-level priorities synced from GitHub
- **Daily standup** (heartbeat or cron): Jarvis reports what's active, blocked, done
- **Weekly review** (cron): Jarvis summarizes the week, proposes next week's priorities

This mirrors how the best teams work: quick communication (Slack/chat) + persistent tracking (Linear/GitHub) + regular rhythm (standups/retros).

---

_This is the starting point for our discussion. Let's figure out what actually works for us._
