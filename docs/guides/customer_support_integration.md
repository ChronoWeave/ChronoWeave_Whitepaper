# Customer Support Integration Guide

This guide provides instructions for integrating ChronoWeave into customer support applications to enhance agent capabilities with temporal context.

## Table of Contents

- [Overview](#overview)
- [Benefits](#benefits)
- [Integration Steps](#integration-steps)
- [Data Model](#data-model)
- [Implementation Examples](#implementation-examples)
- [Best Practices](#best-practices)

## Overview

Customer support applications can benefit significantly from ChronoWeave's temporal context capabilities. By maintaining a history of customer interactions, support agents can provide more personalized and effective assistance.

## Benefits

- **Conversation Continuity**: Maintain context across multiple interactions
- **Issue Tracking**: Track the evolution of customer issues over time
- **Agent Handoff**: Seamlessly transfer context between agents
- **Personalization**: Provide personalized support based on customer history
- **Efficiency**: Reduce time spent searching for information

## Integration Steps

### 1. Set Up ChronoWeave

```python
from chronoweave.client import ChronoWeaveClient

# Initialize client
client = ChronoWeaveClient(api_key="your_api_key")
```

### 2. Register Customer Entities

```python
# Register a customer
customer_data = {
    "node_type": "Entity",
    "content": {
        "entity_id": "customer_123",
        "entity_type": "Customer",
        "name": "John Doe",
        "attributes": {
            "email": "john@example.com",
            "plan": "premium",
            "signup_date": "2023-01-15"
        }
    }
}

client.observe(customer_data)

# Register consent
client.register_consent(
    user_id="customer_123",
    data_types=["Entity", "Observation", "Event"],
    purposes=["storage", "retrieval", "context_infusion"],
    expiration_days=365
)
```

### 3. Capture Support Interactions

```python
# Store a support interaction
interaction_data = {
    "node_type": "Observation",
    "content": {
        "observation_type": "SupportInteraction",
        "text": "Customer reported issues with account access. Reset password and provided login instructions.",
        "subject_id": "customer_123",
        "observer_id": "agent_456"
    },
    "temporal": {
        "start_time": "2023-06-15T14:30:00Z"
    },
    "metadata": {
        "importance": 0.8,
        "channel": "chat",
        "ticket_id": "T-12345",
        "resolution": "resolved",
        "category": "account_access"
    }
}

client.observe(interaction_data)
```

### 4. Retrieve Customer Context

```python
# Get customer context
context = client.recall({
    "text": "account access issues",
    "reference_time": "2023-06-16T10:00:00Z",
    "entity_ids": ["customer_123"],
    "k": 5
})

# Process results
for result in context["results"]:
    node = result["node"]
    score = result["score"]
    
    print(f"Node Type: {node['node_type']}")
    if node["node_type"] == "Observation":
        print(f"Text: {node['content']['text']}")
        print(f"Date: {node['temporal']['start_time']}")
    print(f"Relevance: {score}")
    print()
```

### 5. Enhance Agent Prompts

```python
# Infuse agent prompt with context
prompt = "How can I help the customer with their current issue?"
infused_prompt = client.infuse_prompt(
    prompt=prompt,
    query={
        "text": "customer issues",
        "entity_ids": ["customer_123"]
    },
    model_type="openai"
)

# Use with LLM
from openai import OpenAI
openai_client = OpenAI(api_key="your_openai_api_key")

response = openai_client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "You are a helpful customer support agent."},
        {"role": "user", "content": infused_prompt}
    ]
)

agent_response = response.choices[0].message.content
```

## Data Model

For customer support integration, consider the following data model:

### Entity Types

- **Customer**: Represents a customer with attributes like name, email, plan, etc.
- **Agent**: Represents a support agent
- **Product**: Represents products or services the customer uses

### Observation Types

- **SupportInteraction**: Records of support conversations
- **CustomerFeedback**: Customer feedback or ratings
- **AccountActivity**: Notable account activities

### Event Types

- **Subscription**: Subscription changes or renewals
- **PurchaseEvent**: Product purchases
- **AccountEvent**: Account status changes

## Implementation Examples

### Support Ticket System Integration

```python
def handle_new_ticket(ticket):
    # Initialize ChronoWeave client
    client = ChronoWeaveClient(api_key="your_api_key")
    
    # Store ticket as an event
    ticket_data = {
        "node_type": "Event",
        "content": {
            "event_type": "SupportTicket",
            "title": ticket.subject,
            "description": ticket.description,
            "participants": [
                {"entity_id": ticket.customer_id, "role": "customer"},
                {"entity_id": ticket.agent_id, "role": "agent"}
            ]
        },
        "temporal": {
            "start_time": ticket.created_at
        },
        "metadata": {
            "ticket_id": ticket.id,
            "priority": ticket.priority,
            "category": ticket.category
        }
    }
    
    client.observe(ticket_data)
    
    # Get customer context for the agent
    context = client.recall({
        "text": ticket.description,
        "entity_ids": [ticket.customer_id],
        "k": 10
    })
    
    return context
```

### Agent Assistant Integration

```python
class AgentAssistant:
    def __init__(self, api_key):
        self.client = ChronoWeaveClient(api_key=api_key)
        self.openai_client = OpenAI(api_key="your_openai_api_key")
    
    def get_response_suggestion(self, customer_id, query):
        # Infuse prompt with customer context
        infused_prompt = self.client.infuse_prompt(
            prompt=query,
            query={
                "text": query,
                "entity_ids": [customer_id]
            }
        )
        
        # Get response suggestion from LLM
        response = self.openai_client.chat.completions.create(
            model="gpt-4",
            messages=[
                {"role": "system", "content": "You are a helpful customer support agent. Use the context provided to assist the customer."},
                {"role": "user", "content": infused_prompt}
            ]
        )
        
        return response.choices[0].message.content
    
    def log_interaction(self, customer_id, agent_id, query, response):
        # Store the interaction
        self.client.observe({
            "node_type": "Observation",
            "content": {
                "observation_type": "SupportInteraction",
                "text": f"Customer: {query}\nAgent: {response}",
                "subject_id": customer_id,
                "observer_id": agent_id
            }
        })
```

## Best Practices

### Data Organization

- **Consistent Entity IDs**: Use consistent IDs for customers across systems
- **Structured Interactions**: Store interactions with clear subject, observer, and content
- **Temporal Metadata**: Include accurate timestamps for all interactions
- **Categorization**: Use metadata to categorize interactions for better filtering

### Privacy and Compliance

- **Explicit Consent**: Always obtain customer consent for data storage
- **Data Retention**: Implement appropriate data retention policies
- **Access Control**: Limit access to customer data to authorized agents
- **Audit Trails**: Maintain logs of data access and usage

### Context Quality

- **Relevance Filtering**: Use appropriate filters to get the most relevant context
- **Recency Bias**: Give more weight to recent interactions
- **Context Summarization**: Summarize lengthy context for agent consumption
- **Feedback Loop**: Allow agents to provide feedback on context quality

### Integration Points

- **Ticket Creation**: Provide context when a new ticket is created
- **Agent Dashboard**: Display relevant customer context in the agent dashboard
- **Response Suggestions**: Use context to suggest responses to common issues
- **Knowledge Base**: Link relevant knowledge base articles based on context
