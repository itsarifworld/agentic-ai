# Building a Multi-Tool Agent with Google ADK

## Overview

This project demonstrates how to build a **Multi-Tool AI Agent** using Google's **Agent Development Kit (ADK)**.

The agent is capable of:

- Answering user questions
- Calling multiple tools
- Returning structured responses
- Using Gemini models for reasoning
- Running through CLI and ADK Web UI

---

# What is a Multi-Tool Agent?

A Multi-Tool Agent is an AI agent that can decide when to use one or more external tools to solve a user request.

Example:

```text
User: What's the weather in New York?

Agent:
→ Calls weather tool
→ Receives result
→ Returns weather report
```

Another example:

```text
User: What time is it in Tokyo?

Agent:
→ Calls time tool
→ Receives result
→ Returns current time
```

Instead of relying only on the language model, the agent can use specialized tools for better accuracy.

---

# Prerequisites

Before starting, ensure the following are installed:

## Python

```bash
python --version
```

Recommended:

```text
Python 3.10+
```

---

## pip

```bash
pip --version
```

---

## Virtual Environment

Create a virtual environment:

```bash
python -m venv .venv
```

Activate:

### macOS/Linux

```bash
source .venv/bin/activate
```

### Windows

```powershell
.venv\Scripts\activate
```

---

# Install ADK

Install Google ADK:

```bash
pip install google-adk
```

Verify installation:

```bash
adk --version
```

---

# Create Agent Project

Create a new agent:

```bash
adk create multi_tool_agent
```

Select:

```text
Model:
gemini-2.5-flash

Provider:
Google AI
```

---

# Project Structure

```text
multi_tool_agent/
│
├── agent.py
├── __init__.py
├── .env
└── .venv/
```

---

# Configure Gemini API Key

Create or edit:

```text
.env
```

Add:

```env
GOOGLE_API_KEY=YOUR_API_KEY
```

Get your key from:

https://aistudio.google.com

---

# Understanding the Agent

The project uses:

## Agent

Responsible for:

- Understanding user requests
- Selecting tools
- Generating final responses

---

## Tools

Two example tools are implemented:

### Weather Tool

Provides weather information for a city.

Example:

```python
def get_weather(city: str) -> dict:
```

Returns:

```json
{
  "status": "success",
  "report": "Sunny, 25°C"
}
```

---

### Time Tool

Provides current time for a city.

Example:

```python
def get_current_time(city: str) -> dict:
```

Returns:

```json
{
  "status": "success",
  "time": "10:30 AM"
}
```

---

# Agent Configuration

Typical structure:

```python
from google.adk.agents import Agent

root_agent = Agent(
    name="multi_tool_agent",
    model="gemini-2.5-flash",
    description="Weather and time assistant",
    instruction="You are a helpful assistant.",
    tools=[
        get_weather,
        get_current_time
    ]
)
```

---

# Important Parameters

## name

```python
name="multi_tool_agent"
```

Unique identifier for the agent.

---

## model

```python
model="gemini-2.5-flash"
```

Specifies which Gemini model is used.

---

## description

```python
description="Weather and time assistant"
```

Provides metadata about the agent.

---

## instruction

```python
instruction="You are a helpful assistant."
```

Acts as the system prompt.

---

## tools

```python
tools=[
    get_weather,
    get_current_time
]
```

List of available tools.

---

# Weather Tool Logic

Example flow:

```text
User:
What's the weather in New York?

Agent
↓
Weather Tool
↓
Weather Result
↓
Final Response
```

Sample response:

```text
The weather in New York is sunny with a temperature of 25°C.
```

---

# Time Tool Logic

Example flow:

```text
User:
What time is it in Delhi?

Agent
↓
Time Tool
↓
Current Time
↓
Final Response
```

Sample response:

```text
The current time in Delhi is 10:30 AM.
```

---

# Running the Agent

## CLI Mode

Run from parent directory:

```bash
adk run multi_tool_agent
```

---

## Run Current Directory

If already inside the folder:

```bash
adk run .
```

---

# Launch Web Interface

Start ADK Web:

```bash
adk web --port 8000
```

Open:

```text
http://localhost:8000
```

---

# ADK Web UI

The web interface provides:

- Chat window
- Tool execution tracking
- Event logs
- State management
- Debugging tools
- Agent graph visualization

---

# Agent Graph

The interface visualizes the relationship between the agent and tools.

Example:

```text
root_agent
    │
 ┌──┴──┐
 │     │
 ▼     ▼
Weather Time
 Tool   Tool
```

This makes debugging significantly easier.

---

# Testing the Agent

## Test 1

Input:

```text
What is the weather in New York?
```

Expected:

```text
The weather in New York is sunny and 25°C.
```

---

## Test 2

Input:

```text
What time is it in Delhi?
```

Expected:

```text
The current time in Delhi is 10:30 AM.
```

---

## Test 3

Input:

```text
What is the weather in London and the time in Tokyo?
```

Expected behavior:

```text
Agent decides which tools to call
↓
Calls weather tool
↓
Calls time tool
↓
Combines responses
↓
Returns final answer
```

---

# Common Errors

## Error: Directory Does Not Exist

Wrong:

```bash
cd multi_tool_agent
adk run multi_tool_agent
```

Error:

```text
Directory 'multi_tool_agent' does not exist
```

Reason:

You are already inside the directory.

---

### Fix

Run:

```bash
adk run .
```

or move to parent:

```bash
cd ..
adk run multi_tool_agent
```

---

## Error: Missing API Key

Error symptoms:

```text
Authentication failed
```

or

```text
TransportError
```

Fix:

Verify:

```env
GOOGLE_API_KEY=YOUR_API_KEY
```

exists inside `.env`.

---

## Error: Tool Not Called

Check:

- Tool included in `tools=[...]`
- Function returns valid dictionary
- Agent instructions are clear

---

# Best Practices

## Keep Tools Focused

Good:

```python
get_weather()
get_current_time()
```

Bad:

```python
get_everything()
```

---

## Return Structured Data

Preferred:

```python
return {
    "status": "success",
    "time": current_time
}
```

Avoid:

```python
return "some random text"
```

---

## Use Clear Instructions

Example:

```python
instruction="""
You are a helpful assistant.
Use tools whenever needed.
Provide concise responses.
"""
```

---

# Key Concepts Learned

- ADK fundamentals
- Agent creation
- Tool registration
- Tool execution
- Gemini integration
- Environment configuration
- Web UI debugging
- Multi-tool architecture
- Agent reasoning flow

---

# Final Architecture

```text
User
 │
 ▼
Multi-Tool Agent
 │
 ├─────────────┐
 │             │
 ▼             ▼
Weather Tool  Time Tool
 │             │
 └──────┬──────┘
        ▼
 Final Response
```

---

# Next Steps

After completing this project, explore:

1. Multi-Agent Systems
2. Long-Term Memory
3. Session State
4. RAG (Retrieval-Augmented Generation)
5. Search Tools
6. Database Integration
7. MCP Servers
8. Vertex AI Agent Engine
9. Structured Outputs
10. Production Deployment

---

## Outcome

✅ Created a Multi-Tool Agent

✅ Connected Multiple Tools

✅ Configured Gemini API

✅ Tested Through ADK Web UI

✅ Learned Tool Calling Workflow

✅ Ready for Advanced ADK Projects
