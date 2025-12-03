# 🍽️ Pantry-to-Plate: A Multi-Agent System for Zero-Waste Meal Planning

> **Subtitle:** This sequential multi-agent system extracts user-defined ingredients, uses a web search tool to find recipes, and generates a zero-waste meal plan and shopping list.

---

## 🚀 Overview

The **Pantry-to-Plate Agent** is an intelligent assistant designed to solve two common household problems: **food waste** and **meal planning fatigue**.

Built on a robust multi-agent architecture, it takes a simple, unstructured list of leftover ingredients from a user and processes it through a pipeline of specialized agents to deliver a concrete, actionable 3-day meal plan and an accurate shopping list for missing items. The system leverages external web search to ensure recipe ideas are grounded in real-world feasibility, making the plan practical and sustainable.

### ✨ Key Features

* **Sequential Multi-Agent Workflow:** Utilizes **LangGraph** to orchestrate three specialized agents for clear task separation and reliable execution.
* **Zero-Waste Focus:** Prioritizes recipes that maximize the use of all provided pantry ingredients.
* **Grounded Results:** Employs a **DuckDuckGo Search Tool** to find genuine, external recipe ideas, eliminating LLM hallucination.
* **Accurate Shopping List:** Performs a **Gap Analysis** to list only the items *not* currently in the pantry.
* **Session State Management:** Uses a shared `AgentState` for coherent context transfer across all agent steps.

---

## 🛠️ Technical Stack & Dependencies

| Component | Technology / Framework | Purpose |
| :--- | :--- | :--- |
| **Orchestration** | `langgraph` | Defines the state machine and sequential agent flow. |
| **Core LLM** | `langchain-google-genai` (Gemini 2.5 Flash) | Powers the reasoning and synthesis capabilities of the agents. |
| **Tooling** | `langchain-community` (`DuckDuckGoSearchRun`) | Provides external search capability for recipe grounding. |
| **Environment** | Python 3.10+ | Primary development language. |
| **Memory** | `TypedDict` (`AgentState`) | Manages session state/context across the graph. |

### Installation

1.  **Clone the repository:**
    ```bash
    git clone [YOUR_REPO_URL]
    cd pantry-to-plate-agent
    ```

2.  **Install dependencies:**
    *(Note: The `ddgs` package is required by `duckduckgo-search`)*
    ```bash
    pip install -U -r requirements.txt
    ```
    *If you do not have a `requirements.txt`, use:*
    ```bash
    pip install -U langchain-google-genai langgraph langchain-community duckduckgo-search ddgs
    ```

### Configuration

The project requires a Gemini API Key.

1.  Obtain an API key from Google AI Studio.
2.  Set the key as an environment variable:
    ```bash
    export GOOGLE_API_KEY="YOUR_API_KEY_HERE"
    ```

---

## 🧠 Architecture: The Agent Workflow

The system is a sequential graph where each agent handles a specific step, ensuring modularity and reliability.

### 1. The Shared State (`AgentState`)
The `AgentState` object is the single source of truth passed between all nodes:
```python
class AgentState(TypedDict):
    user_request: str  # Initial unstructured input
    pantry_items: List[str]  # Clean list of ingredients (extracted)
    research_results: str  # Raw output from the Search Tool
    final_plan: str  # Final synthesized output
