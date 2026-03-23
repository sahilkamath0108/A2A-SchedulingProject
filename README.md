# A2A Scheduling Project


> 📐 **Architecture Diagram**: See `a2a-project.excalidraw` for the editable architecture diagram

A multi-agent system demonstrating Agent-to-Agent (A2A) communication using Google's A2A SDK. The project simulates a real-world scenario where multiple AI agents coordinate to schedule badminton games.

Created by Sahil.

## 🎯 Project Goal

This project demonstrates **Agent-to-Agent (A2A) communication** where AI agents can:
- Communicate with each other autonomously
- Coordinate tasks across multiple agents
- Share information and make collaborative decisions
- Use tools to check availability and book resources

### Real-World Scenario

**Ada Agent** (Host/Coordinator) wants to organize a badminton game. It needs to:
1. Ask **Grace Agent** and **Linus Agent** about their availability
2. Find a common time slot when both are free
3. Check court availability using tools
4. Book a badminton court for the agreed time

This mimics how human assistants would coordinate - each agent has its own information (calendars, tools) and they communicate to reach a common goal.

---

## 🏗️ Architecture

### Agent Overview

| Agent | Framework | Role | Port | Tools |
|-------|-----------|------|------|-------|
| **Ada Agent** | Google ADK | Host/Coordinator - Orchestrates scheduling | 8000 (ADK Web UI) | `send_message`, `book_badminton_court`, `list_court_availabilities` |
| **Grace Agent** | LangChain + LangGraph | Grace's Scheduling Assistant | 10004 | `get_availability` (checks Grace's calendar) |
| **Linus Agent** | CrewAI | Linus's Scheduling Assistant | 10005 | `AvailabilityTool` (checks Linus's calendar) |

### Technology Stack

- **A2A SDK**: Agent-to-Agent communication protocol
- **Google ADK**: Agent Development Kit for building conversational agents
- **LangChain/LangGraph**: Framework for building LLM applications with memory
- **CrewAI**: Multi-agent orchestration framework
- **Google Gemini**: LLM for agent reasoning
- **UV**: Fast Python package manager

---

## 📋 Prerequisites

- Python 3.11+
- UV package manager
- Google API Key (for Gemini models)

---

## 🚀 Setup Instructions

### 1. Clone and Navigate to Project

```bash
cd path/to/A2A-SchedulingProject
```

### 2. Configure Environment Variables

Create a `.env` file in the project root:

```bash
# .env
GOOGLE_API_KEY=your_google_api_key_here
```

> **Get your Google API Key**: Get it from Google AI Studio.

### 3. Install Dependencies

Each agent has its own `pyproject.toml`. Install dependencies per agent:

```bash
cd ada_agent
uv sync

cd ../grace_agent
uv sync

cd ../linus_agent
uv sync
```

---

## 🎮 Running the Agents

### Grace Agent (Port 10004)

Grace's scheduling assistant runs as an A2A server using LangChain.

```bash
cd grace_agent
uv run python __main__.py
```

**Expected Output:**
```
Starting Grace Agent A2A Server...
Agent card available at: http://localhost:10004/.well-known/agent-card.json
Server running on http://localhost:10004
```

**Test the agent:**
```bash
curl http://localhost:10004/.well-known/agent-card.json
```

---

### Linus Agent (Port 10005)

Linus's scheduling assistant runs as an A2A server using CrewAI.

```bash
cd linus_agent
uv run python __main__.py
```

**Expected Output:**
```
Starting Linus Agent A2A Server...
Agent card available at: http://localhost:10005/.well-known/agent-card.json
Server running on http://localhost:10005
```

**Test the agent:**
```bash
curl http://localhost:10005/.well-known/agent-card.json
```

---

### Ada Agent (ADK Web UI)

The host coordinator agent runs via Google ADK with a web interface.

```bash
cd ada_agent
uv run adk web
```

**Expected Output:**
```
ADK Web Server started
For local testing, access at http://127.0.0.1:8000
```

**Access the UI:**
Open your browser and navigate to:
```
http://127.0.0.1:8000
```

---

## 🧪 Testing the Complete System

### Step 1: Verify All Agents are Running

Check that all three agents are responding:

```bash
# Grace Agent
curl http://localhost:10004/.well-known/agent-card.json

# Linus Agent
curl http://localhost:10005/.well-known/agent-card.json

# Ada Agent (ADK)
curl http://127.0.0.1:8000/list-apps
```

### Step 2: Test via ADK Web UI

1. Open browser to `http://127.0.0.1:8000`
2. Select `ada_agent` agent from the dropdown
3. Start a conversation:

**Example Queries:**

```
"Hi, can you help me organize a badminton game with Grace and Linus?"

"Check if Grace is available on November 14th, 2025"

"Ask Linus about his availability for next week"

"Find a common time when both Grace and Linus are free"

"Check court availability for November 15th at 9:00 AM"

"Book a court for us at that time"
```

### Step 3: Observe Agent-to-Agent Communication

Watch the terminal logs to see:
- **Ada Agent**: Sending messages to Grace and Linus
- **Grace Agent**: Processing availability requests
- **Linus Agent**: Checking calendar and responding
- **Ada Agent**: Aggregating responses and making decisions

---

## 📁 Project Structure

```
a2a-project/
├── .env                          # Environment variables (GOOGLE_API_KEY)
├── README.md                     # This file
├── a2a-project.excalidraw         # Architecture diagram (editable)
├── goal.png                      # Diagram image
├── movies/                        # (Optional) media assets
│
├── ada_agent/                    # Host coordinator agent (Google ADK)
│   ├── .python-version            # Python version specification
│   ├── pyproject.toml             # Dependencies (per agent)
│   ├── uv.lock                    # Dependency lock file
│   └── ada/
│       ├── agent.py               # Main agent logic with A2A client
│       └── tools.py               # Court booking tools
│
├── grace_agent/                  # Grace's scheduling agent (LangChain)
│   ├── .python-version            # Python version specification
│   ├── __main__.py                # A2A server entry point
│   ├── agent.py                   # LangChain agent with memory
│   ├── agent_executor.py          # A2A executor wrapper
│   ├── pyproject.toml             # Dependencies (per agent)
│   ├── tools.py                   # Availability checking tool
│   └── uv.lock                    # Dependency lock file
│
└── linus_agent/                  # Linus's scheduling agent (CrewAI)
    ├── .python-version            # Python version specification
    ├── __main__.py                # A2A server entry point
    ├── agent.py                   # CrewAI agent
    ├── agent_executor.py          # A2A executor wrapper
    ├── pyproject.toml             # Dependencies (per agent)
    ├── tools.py                   # Availability tool
    └── uv.lock                    # Dependency lock file
```

---

## 🔧 How It Works

### 1. Agent Communication Flow

```
User → Ada Agent (ADK)
         ↓
    [Send Message via A2A]
         ↓
    ┌────┴────┐
    ↓         ↓
Grace Agent  Linus Agent
    ↓         ↓
[Check Calendar]
    ↓         ↓
[Return Availability]
    ↓         ↓
    └────┬────┘
         ↓
Ada Agent
         ↓
[Find Common Time]
         ↓
[Check Court Availability]
         ↓
[Book Court]
         ↓
    User ← Response
```

### 2. A2A Protocol

Each agent exposes:
- **Agent Card** (`.well-known/agent-card.json`): Metadata about the agent
- **Message Endpoint**: Accepts A2A-formatted messages
- **Response Format**: Returns structured responses

### 3. Tools & Capabilities

**Grace Agent Tools:**
- `get_availability(date)`: Checks Grace's calendar for the given date

**Linus Agent Tools:**
- `AvailabilityTool`: Checks Linus's calendar (CrewAI tool format)

**Ada Agent Tools:**
- `send_message(agent_name, task)`: Sends A2A messages to other agents
- `list_court_availabilities(date)`: Lists available court time slots
- `book_badminton_court(date, start_time, end_time, name)`: Books a court

---

## 🐛 Troubleshooting

### Common Issues

#### 1. Import Error: `No module named 'a2a'`

**Solution:** Make sure you're running from the correct directory with `uv run`:
```bash
cd grace_agent  # or linus_agent
uv run python __main__.py
```

#### 2. Agent Not Found Error in ADK

**Solution:** Ensure the ADK entry module defines `root_agent`:
```bash
cd ada_agent/ada
# Check that agent.py sets a module-level `root_agent` (created from setup()).
```

#### 3. Port Already in Use

**Solution:** Kill existing processes:
```bash
lsof -ti:10004 | xargs kill -9  # Grace Agent
lsof -ti:10005 | xargs kill -9  # Linus Agent
lsof -ti:8000 | xargs kill -9   # Ada Agent
```

#### 4. GOOGLE_API_KEY Not Found

**Solution:** Ensure `.env` file exists in project root:
```bash
echo "GOOGLE_API_KEY=your_key_here" > .env
```

#### 5. LLM Returns None/Empty

**Solution:** Check API key and model name:
```python
# For CrewAI (Linus Agent)
model="gemini/gemini-2.0-flash"

# For LangChain (Grace Agent)
model="gemini-2.0-flash"
```

---

## 📚 Key Concepts

### Agent-to-Agent (A2A) Communication

- **Decentralized**: Each agent runs independently
- **Protocol-based**: Standard message format for interoperability
- **Asynchronous**: Agents can handle multiple requests concurrently
- **Tool-augmented**: Agents can use tools to access external data

### Why Multiple Frameworks?

This project intentionally uses different frameworks to demonstrate:
- **Interoperability**: A2A works across different agent implementations
- **Framework Comparison**: See how LangChain, CrewAI, and ADK differ
- **Real-world Flexibility**: Organizations may have agents built with different tools

