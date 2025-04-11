# ChronoWeave API Documentation

Welcome to the ChronoWeave API documentation. This guide provides comprehensive information about ChronoWeave's API, including core concepts, installation instructions, API reference, and usage examples.

## Table of Contents

- [Introduction](#introduction)
- [Core Concepts](#core-concepts)
- [Installation](#installation)
- [API Reference](#api-reference)
- [Client SDK](#client-sdk)
- [Examples](#examples)
- [Troubleshooting](#troubleshooting)

## Introduction

ChronoWeave is a time-layered knowledge graph system designed to provide temporal context for AI applications. It enables applications to maintain and retrieve information based on temporal relevance, enhancing the contextual awareness of AI systems.

### Key Features

- **Time-Layered Knowledge Graph (TLKG)**: Store and retrieve information with temporal context
- **Temporal Retrieval**: Find information based on temporal relevance
- **Context Infusion**: Enhance prompts with time-aware context
- **Privacy Controls**: Manage data access with consent-aware mechanisms
- **Scalable Architecture**: Handle large volumes of temporal data efficiently

## Core Concepts

### Time-Layered Knowledge Graph (TLKG)

The TLKG is the core data structure in ChronoWeave. It stores information as nodes with temporal metadata, allowing for time-aware retrieval and context management.

#### Node Types

- **Entity**: Represents a person, organization, product, or other entity
- **Event**: Represents an occurrence at a specific time
- **Observation**: Represents a piece of information observed at a specific time
- **Pattern**: Represents a pattern or trend identified across multiple nodes

#### Relationships

Relationships connect nodes in the TLKG, creating a rich network of information. Each relationship has a type that defines the semantic meaning of the connection.

### Temporal Context

Temporal context refers to the relevance of information based on time. ChronoWeave uses temporal distance and decay functions to determine the relevance of information relative to a reference time.

### Context Infusion

Context infusion is the process of enhancing prompts with relevant temporal context. ChronoWeave retrieves relevant information based on the query and reference time, then formats it for different LLM providers.

## Installation

### Prerequisites

- Python 3.8+
- PostgreSQL or SQLite
- Vector database (Chroma, Pinecone, or similar)

### Installing the Package

```bash
pip install chronoweave
```

### Setting Up Environment Variables

```bash
export CHRONOWEAVE_API_KEY="your_api_key"
export DATABASE_URL="postgresql://user:password@localhost:5432/chronoweave"
export VECTOR_STORE_PATH="/path/to/vector/store"
```

### Basic Configuration

```python
from chronoweave.config import ChronoWeaveConfig

config = ChronoWeaveConfig(
    database_url="postgresql://user:password@localhost:5432/chronoweave",
    vector_store_path="/path/to/vector/store",
    api_keys=["your_api_key"]
)
```

## API Reference

The ChronoWeave API is organized into several key components:

- [TLKG API](./tlkg.md): Core Time-Layered Knowledge Graph operations
- [Retriever API](./retriever.md): Temporal retrieval functionality
- [Infuser API](./infuser.md): Context infusion for LLMs
- [Storage API](./storage.md): Database and vector store operations
- [API Endpoints](./endpoints.md): REST API endpoints
- [Consent API](./consent.md): Privacy and consent management

## Client SDK

The ChronoWeave Client SDK provides a simple interface for interacting with the ChronoWeave API:

```python
from chronoweave.client import ChronoWeaveClient

# Initialize client
client = ChronoWeaveClient(api_key="your_api_key")

# Store an observation
client.observe({
    "node_type": "Observation",
    "content": {
        "observation_type": "CustomerFeedback",
        "text": "Customer reported issues with account access",
        "subject_id": "customer_123",
        "observer_id": "agent_456"
    }
})

# Recall information
results = client.recall({
    "text": "account access issues",
    "reference_time": "2023-06-15T14:30:00Z"
})

# Infuse a prompt with context
infused_prompt = client.infuse_prompt(
    prompt="What issues has the customer reported?",
    query={
        "text": "customer issues",
        "entity_ids": ["customer_123"]
    }
)
```

See the [Client SDK Reference](./client.md) for detailed information.

## Examples

ChronoWeave includes several example applications that demonstrate its capabilities:

- [Customer Support Agent](../examples/customer_support_agent.md): Enhance customer support with temporal context
- [Knowledge Work Assistant](../examples/knowledge_work_assistant.md): Maintain project context for knowledge workers
- [Personal Memory Assistant](../examples/personal_memory_assistant.md): Help users remember and retrieve personal information
- [Healthcare Example](../examples/healthcare.md): Provide temporal context for healthcare applications

## Troubleshooting

See the [Troubleshooting Guide](../troubleshooting.md) for solutions to common issues.

---

For more information, visit the [ChronoWeave GitHub repository](https://github.com/chronoweave/chronoweave) or contact support at support@chronoweave.ai.
