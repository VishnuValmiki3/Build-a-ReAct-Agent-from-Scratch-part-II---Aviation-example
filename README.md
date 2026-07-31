# Building a ReAct Agent from Scratch Part II- Aviation Example

A lightweight **ReAct (Reasoning + Acting) agent built from scratch using Python and the OpenAI API**, without relying on LangChain, LangGraph, or other agent frameworks.

The goal of this project is to understand the core mechanics behind tool-using AI agents by implementing the reasoning-action-observation loop ourselves.

## What is ReAct?

**ReAct = Reasoning + Acting**

Instead of generating an answer in a single step, the agent can:

```text
Question
   ↓
Thought
   ↓
Action
   ↓
Tool Execution
   ↓
Observation
   ↓
Thought
   ↓
...
   ↓
Final Answer
```

The LLM determines **what action is required**, while Python executes the corresponding tool and returns the result as an **Observation**.

This creates a continuous feedback loop between the LLM and the execution environment.

## Project Objective

This project demonstrates how a simple LLM can be transformed into a tool-using agent capable of solving multi-step aviation operations questions.

Rather than treating the LLM as an isolated chatbot, the implementation gives it access to external tools and allows it to dynamically decide:

* What information it needs
* Which tool to use
* When to perform calculations
* When it has enough information to produce the final answer

##  Aviation Operations Use Case

The project uses a **synthetic flight dataset** containing information such as:

* Flight delays
* Flight distances
* Derived operational metrics

The agent is provided with tools to retrieve flight information and perform calculations.

For example, it can receive a query such as:

> *"Flight FL101 and FL202 were both delayed. Find their combined delay and calculate what percentage of their combined flight distance is covered by FL101."*

The agent can independently determine the sequence of actions required to solve the problem.

##  Tools Available to the Agent

### `get_flight_delay`

Retrieves the delay for a given flight.

```text
get_flight_delay: FL101
```

### `get_flight_distance`

Retrieves the flight distance.

```text
get_flight_distance: FL101
```

### `calculate`

Performs mathematical calculations using Python.

```text
calculate: 45 + 35
```

These tools are registered through a simple action registry:

```python
known_actions = {
    "get_flight_delay": get_flight_delay,
    "get_flight_distance": get_flight_distance,
    "calculate": calculate
}
```

##  How the Agent Works

At a high level:

```text
             User Question
                   │
                   ▼
             ┌───────────┐
             │    LLM    │
             └─────┬─────┘
                   │
               Action
                   │
                   ▼
           ┌───────────────┐
           │ Action Parser │
           └───────┬───────┘
                   │
                   ▼
            Tool Selection
                   │
          ┌────────┼────────┐
          ▼        ▼        ▼
       Delay   Distance  Calculate
          │        │        │
          └────────┼────────┘
                   ▼
              Observation
                   │
                   ▼
                  LLM
                   │
                   ▼
             Final Answer
```

The core implementation consists of:

1. **Agent class** — maintains conversation state and communicates with the LLM.
2. **System prompt** — establishes the ReAct interaction protocol.
3. **Tools** — Python functions available to the agent.
4. **Action registry** — maps model-generated actions to executable functions.
5. **Action parser** — extracts the requested tool and input from the model response.
6. **ReAct loop** — repeatedly executes actions and feeds observations back to the LLM.

##  Example Flow

A simplified execution might look like:

```text
Thought:
I need the delay of FL101.

Action:
get_flight_delay: FL101

Observation:
Flight FL101 was delayed by 45 minutes.

Thought:
I need the delay of FL202.

Action:
get_flight_delay: FL202

Observation:
Flight FL202 was delayed by 35 minutes.

Thought:
I need their combined delay.

Action:
calculate: 45 + 35

Observation:
80

Answer:
The combined delay is 80 minutes.
```

The important part is that **the sequence of tool calls is not hardcoded for the question**. The LLM determines the next action based on the current context and observations.

##  Tech Stack

* **Python**
* **OpenAI API**
* **Regular Expressions**
* **python-dotenv**
* **Jupyter Notebook / VS Code**

No agent framework was used.



##  Important Note

The flight information used in this project is **synthetic data created solely for demonstration purposes**.

It does not represent real airline operational data, flight schedules, or proprietary airline systems.

##  Why Build ReAct From Scratch?

Modern frameworks abstract away much of this complexity.

Building a minimal implementation from scratch makes the underlying architecture explicit:

```text
LLM
 ↓
Decision
 ↓
Tool Request
 ↓
Tool Execution
 ↓
Observation
 ↓
LLM
```

Understanding this primitive loop provides a useful foundation for working with more sophisticated agent architectures and frameworks such as LangGraph.

##  Next Steps

Potential extensions include:

* Structured tool calling instead of regex-based parsing
* Better input validation and error handling
* Multiple tools and parallel tool execution
* Persistent agent state
* More realistic aviation operations data
* Human-in-the-loop workflows
* Implementing the same architecture using LangGraph

##  Author

**Vishnu Vikas Valmiki**

This project was built as part of my exploration of **LLMs, AI Agents, Operations Research, and agentic workflows**.

---

⭐ If you found this useful, feel free to explore the code and experiment with your own tools and use cases.
