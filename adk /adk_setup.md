# ADK Quickstart + Debugging Notes (Google Agent Development Kit)

> Personal Notes while setting up my first ADK Agent using Google Agent Development Kit (ADK)

---

# Environment Information

## System

- OS: macOS
- Python: 3.10+
- Package Manager: pip
- Framework: Google ADK
- IDE: VS Code
- Runtime: Local Development

---

# Install ADK

Install the Agent Development Kit:

```bash
pip install google-adk
```

Verify installation:

```bash
adk --version
```

---

# Create Agent

Create a new agent project:

```bash
adk create my_agent
```

Choose:

```text
Model: gemini-2.5-flash
Provider: Google AI
```

This generates:

```text
my_agent/
├── agent.py
├── __init__.py
└── .env
```

---

# Project Structure

## agent.py

Main agent configuration.

Contains:

- Agent definition
- Gemini model configuration
- Tools
- Instructions
- Descriptions

Example:

```python
from google.adk.agents import Agent

def get_current_time(city: str):
    return {
        "status": "success",
        "city": city,
        "time": "10:30 AM"
    }

root_agent = Agent(
    model="gemini-2.5-flash",
    name="root_agent",
    instruction="You are a helpful assistant.",
    tools=[get_current_time]
)
```

---

## __init__.py

Marks directory as a Python package.

Usually empty.

---

## .env

Stores secrets.

Example:

```env
GOOGLE_API_KEY=YOUR_API_KEY
```

Never commit real API keys to GitHub.

---

# API Key Setup

## Generate API Key

Go to:

https://aistudio.google.com

Navigate:

```text
Google AI Studio
→ API Keys
→ Create API Key
```

---

## Store API Key

Inside `.env`

```env
GOOGLE_API_KEY=AIza....
```

---

## Verify Environment Variable

Mac/Linux:

```bash
echo $GOOGLE_API_KEY
```

If empty:

```bash
export GOOGLE_API_KEY="YOUR_API_KEY"
```

---

# Running Agent

## Method 1: CLI

Run from parent directory:

```bash
adk run my_agent
```

Example:

```text
project/
└── my_agent/
```

Execute:

```bash
cd project
adk run my_agent
```

---

## Common Mistake

### Problem

Current location:

```bash
pwd
```

Output:

```text
.../project/my_agent
```

Then running:

```bash
adk run my_agent
```

produces:

```text
Error:
Directory 'my_agent' does not exist
```

---

### Why?

ADK searches for:

```text
my_agent/
└── my_agent/
```

which doesn't exist.

---

### Solution 1

Move to parent folder:

```bash
cd ..
adk run my_agent
```

---

### Solution 2

Stay inside folder:

```bash
adk run .
```

---

# Launch ADK Web UI

Start server:

```bash
adk web --port 8000
```

Open browser:

```text
http://localhost:8000
```

---

# ADK Web Interface Overview

Available tabs:

- Info
- State
- Artifacts
- Evals

Debugging views:

- Events
- Traces

Useful for:

- Tool execution tracking
- Prompt debugging
- Agent state inspection
- Multi-agent visualization

---

# First Tool: Current Time Agent

Tool:

```python
def get_current_time(city: str):
    return {
        "status": "success",
        "city": city,
        "time": "10:30 AM"
    }
```

Capabilities:

- Accept city name
- Return mock time
- Demonstrate tool calling

Example:

```text
User:
What is the current time in Delhi?

Agent:
The current time in Delhi is 10:30 AM.
```

---

# Understanding Agent Graph

ADK visualizes:

```text
root_agent
     │
     ▼
get_current_time
```

Meaning:

1. User talks to root agent
2. Root agent decides whether tool is required
3. Tool executes
4. Result returned to user

---

# Debugging Session

## Initial Error

While testing:

```text
Who is Aalima?
```

and

```text
What is the current time in Delhi?
```

received:

```text
TransportError
HTTPSConnectionPool(host='oauth2.googleapis.com', port=443)

Max retries exceeded with url: /token

SSL:
UNEXPECTED_EOF_WHILE_READING
```

---

# Error Analysis

Source:

```text
oauth2.googleapis.com
```

Meaning:

ADK attempted authentication against Google OAuth endpoint.

Connection terminated before SSL handshake completed.

---

# Possible Causes

## 1. Missing API Key

Check:

```bash
ls -la
```

Verify:

```text
.env
```

exists.

---

Check content:

```bash
cat .env
```

Should contain:

```env
GOOGLE_API_KEY=YOUR_KEY
```

---

## 2. API Key Not Loaded

Check:

```bash
echo $GOOGLE_API_KEY
```

If blank:

```bash
export GOOGLE_API_KEY="YOUR_API_KEY"
```

---

## 3. Vertex AI Mode Enabled

Check `.env`

Bad:

```env
GOOGLE_GENAI_USE_VERTEXAI=true
```

This triggers OAuth authentication.

---

Correct:

```env
GOOGLE_API_KEY=YOUR_API_KEY
GOOGLE_GENAI_USE_VERTEXAI=false
```

---

## 4. SSL Issue

Test connectivity:

```bash
curl https://generativelanguage.googleapis.com
```

Expected:

```text
404 Not Found
```

This confirms SSL connectivity works.

---

## 5. Old ADK Version

Upgrade:

```bash
pip install -U google-adk
```

Check:

```bash
adk --version
```

---

# Interesting Observation

Despite OAuth failures:

```text
TransportError
```

the agent later answered:

```text
The current time in Delhi is 10:30 AM.
```

Reason:

The tool itself was local Python code.

No Gemini call required.

Tool executed successfully.

---

# Tool Calling Flow

```text
User Query
      │
      ▼
Root Agent
      │
      ▼
Tool Selection
      │
      ▼
Tool Execution
      │
      ▼
Tool Output
      │
      ▼
Final Response
```

---

# Useful Commands

## Create Agent

```bash
adk create my_agent
```

---

## Run Agent

```bash
adk run my_agent
```

---

## Run Current Directory

```bash
adk run .
```

---

## Start Web UI

```bash
adk web --port 8000
```

---

## Upgrade ADK

```bash
pip install -U google-adk
```

---

## Check Python Version

```bash
python --version
```

---

## Check ADK Version

```bash
adk --version
```

---

## Check Current Directory

```bash
pwd
```

---

## List Files

```bash
ls -la
```

---

# Lessons Learned

### ADK Directory Rule

If inside:

```text
my_agent/
```

Use:

```bash
adk run .
```

If outside:

```bash
adk run my_agent
```

---

### API Keys Matter

Without a valid API key:

- Gemini calls fail
- Authentication fails
- Agent reasoning fails

---

### Local Tools Still Work

Python functions continue running even when Gemini connectivity has issues.

---

### ADK Web UI Is Extremely Useful

Provides:

- Tool traces
- Execution graphs
- Debugging information
- Event logs
- State inspection

Much easier than debugging from terminal alone.

---

# Next Topics To Learn

- Tool Calling
- Function Calling
- Multi-Agent Systems
- Session Memory
- Long-Term Memory
- RAG
- Google Search Tool
- Structured Outputs
- Pydantic Models
- Agent Evaluation
- Agent Deployment
- Vertex AI Agent Engine
- MCP Integration
- Production ADK Applications

---

# Current Status

✅ ADK Installed

✅ Agent Created

✅ Web UI Running

✅ Tool Calling Working

✅ Agent Graph Visible

✅ Local Tool Execution Working

⚠ Gemini Authentication Needs Verification

⚠ API Key / OAuth Configuration Needs Validation

🚀 Ready to Start Building Real AI Agents
