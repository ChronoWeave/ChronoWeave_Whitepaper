# Knowledge Work Integration Guide

This guide provides instructions for integrating ChronoWeave into knowledge work applications to help maintain project context and enhance productivity.

## Table of Contents

- [Overview](#overview)
- [Benefits](#benefits)
- [Integration Steps](#integration-steps)
- [Data Model](#data-model)
- [Implementation Examples](#implementation-examples)
- [Best Practices](#best-practices)

## Overview

Knowledge work applications can leverage ChronoWeave to maintain context across research, writing, and project management tasks. By capturing and retrieving temporal information, knowledge workers can reduce context loss and improve productivity.

## Benefits

- **Project Memory**: Maintain context across work sessions
- **Decision Tracking**: Record and retrieve decision rationales
- **Research Continuity**: Connect research findings over time
- **Collaborative Context**: Share context across team members
- **Temporal Organization**: Organize information by time and relevance

## Integration Steps

### 1. Set Up ChronoWeave

```python
from chronoweave.client import ChronoWeaveClient

# Initialize client
client = ChronoWeaveClient(api_key="your_api_key")
```

### 2. Register Users and Projects

```python
# Register a user
user_data = {
    "node_type": "Entity",
    "content": {
        "entity_id": "user_123",
        "entity_type": "User",
        "name": "Jane Smith",
        "attributes": {
            "email": "jane@example.com",
            "role": "Researcher",
            "department": "R&D"
        }
    }
}

client.observe(user_data)

# Register consent
client.register_consent(
    user_id="user_123",
    data_types=["Entity", "Observation", "Event"],
    purposes=["storage", "retrieval", "context_infusion"],
    expiration_days=365
)

# Create a project
project_data = {
    "node_type": "Entity",
    "content": {
        "entity_id": "project_456",
        "entity_type": "Project",
        "name": "Research Initiative Alpha",
        "attributes": {
            "description": "Research on advanced materials for energy storage",
            "status": "active",
            "tags": ["research", "energy", "materials"]
        }
    },
    "temporal": {
        "start_time": "2023-05-01T00:00:00Z",
        "end_time": "2023-12-31T23:59:59Z"
    }
}

client.observe(project_data)

# Create relationship between user and project
relationship_data = {
    "node_type": "Relationship",
    "content": {
        "type": "OWNS",
        "source_id": "user_123",
        "target_id": "project_456"
    }
}

client.observe(relationship_data)
```

### 3. Capture Documents and Notes

```python
# Store a document
document_data = {
    "node_type": "Entity",
    "content": {
        "entity_id": "doc_789",
        "entity_type": "Document",
        "name": "Literature Review: Energy Storage Materials",
        "document_type": "research",
        "attributes": {
            "tags": ["literature", "review", "energy", "storage"]
        }
    },
    "temporal": {
        "start_time": "2023-06-15T14:30:00Z"
    },
    "metadata": {
        "importance": 0.8,
        "user_id": "user_123",
        "project_id": "project_456"
    }
}

client.observe(document_data)

# Store document content
content_data = {
    "node_type": "Observation",
    "content": {
        "observation_type": "DocumentContent",
        "text": "This literature review examines recent advances in materials for energy storage...",
        "subject_id": "doc_789",
        "observer_id": "user_123"
    },
    "temporal": {
        "start_time": "2023-06-15T14:30:00Z"
    },
    "metadata": {
        "importance": 0.8,
        "user_id": "user_123",
        "project_id": "project_456"
    }
}

client.observe(content_data)
```

### 4. Record Decisions and Findings

```python
# Record a decision
decision_data = {
    "node_type": "Event",
    "content": {
        "event_type": "Decision",
        "title": "Focus on Lithium-Sulfur Battery Materials",
        "description": "Decision to focus research efforts on lithium-sulfur battery materials",
        "rationale": "Based on literature review and preliminary experiments, lithium-sulfur shows the most promise for high energy density storage.",
        "participants": [
            {"entity_id": "user_123", "role": "decision_maker"}
        ]
    },
    "temporal": {
        "start_time": "2023-06-20T10:00:00Z"
    },
    "metadata": {
        "importance": 0.9,
        "user_id": "user_123",
        "project_id": "project_456"
    }
}

client.observe(decision_data)
```

### 5. Retrieve Project Context

```python
# Get project context
context = client.recall({
    "text": "lithium-sulfur battery research",
    "reference_time": "2023-06-25T10:00:00Z",
    "entity_ids": ["project_456"],
    "k": 10
})

# Process results
for result in context["results"]:
    node = result["node"]
    score = result["score"]
    
    print(f"Node Type: {node['node_type']}")
    if node["node_type"] == "Event" and node["content"]["event_type"] == "Decision":
        print(f"Decision: {node['content']['title']}")
        print(f"Rationale: {node['content']['rationale']}")
    elif node["node_type"] == "Observation":
        print(f"Content: {node['content']['text'][:100]}...")
    print(f"Date: {node['temporal']['start_time']}")
    print(f"Relevance: {score}")
    print()
```

### 6. Enhance Research Queries

```python
# Infuse research query with project context
prompt = "What are the key challenges in lithium-sulfur battery development?"
infused_prompt = client.infuse_prompt(
    prompt=prompt,
    query={
        "text": "lithium-sulfur battery challenges",
        "entity_ids": ["project_456"]
    },
    user_id="user_123",
    model_type="openai"
)

# Use with LLM
from openai import OpenAI
openai_client = OpenAI(api_key="your_openai_api_key")

response = openai_client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "You are a research assistant with expertise in materials science and energy storage."},
        {"role": "user", "content": infused_prompt}
    ]
)

research_response = response.choices[0].message.content
```

## Data Model

For knowledge work integration, consider the following data model:

### Entity Types

- **User**: Represents a knowledge worker
- **Project**: Represents a research or work project
- **Document**: Represents documents, notes, or other work products

### Observation Types

- **DocumentContent**: Content of documents
- **ResearchFinding**: Research observations or findings
- **Reflection**: User reflections or journal entries

### Event Types

- **Decision**: Project decisions with rationales
- **Meeting**: Meeting notes and outcomes
- **Milestone**: Project milestones or achievements

## Implementation Examples

### Research Assistant Integration

```python
class ResearchAssistant:
    def __init__(self, api_key):
        self.client = ChronoWeaveClient(api_key=api_key)
        self.openai_client = OpenAI(api_key="your_openai_api_key")
    
    def ask_research_question(self, user_id, project_id, question):
        # Infuse question with project context
        infused_question = self.client.infuse_prompt(
            prompt=question,
            query={
                "text": question,
                "entity_ids": [project_id]
            },
            user_id=user_id
        )
        
        # Get response from LLM
        response = self.openai_client.chat.completions.create(
            model="gpt-4",
            messages=[
                {"role": "system", "content": "You are a research assistant with expertise in various scientific domains."},
                {"role": "user", "content": infused_question}
            ]
        )
        
        answer = response.choices[0].message.content
        
        # Store the interaction
        self.client.observe({
            "node_type": "Observation",
            "content": {
                "observation_type": "ResearchQuery",
                "text": f"Question: {question}\nAnswer: {answer}",
                "subject_id": project_id,
                "observer_id": user_id
            }
        })
        
        return answer
    
    def get_project_timeline(self, user_id, project_id, days=30):
        from datetime import datetime, timedelta
        
        # Calculate date range
        end_date = datetime.now().isoformat()
        start_date = (datetime.now() - timedelta(days=days)).isoformat()
        
        # Query for project events
        results = self.client.recall({
            "text": "",
            "reference_time": end_date,
            "entity_ids": [project_id],
            "filters": {
                "time_window": {
                    "start": start_date,
                    "end": end_date
                }
            }
        })
        
        # Format timeline
        timeline = []
        for result in results["results"]:
            node = result["node"]
            
            event = {
                "id": node.get("node_id"),
                "type": node.get("node_type"),
                "title": self._get_title(node),
                "description": self._get_description(node),
                "timestamp": node.get("temporal", {}).get("start_time"),
                "importance": node.get("metadata", {}).get("importance", 0.5)
            }
            
            timeline.append(event)
        
        # Sort by timestamp
        timeline.sort(key=lambda x: x["timestamp"], reverse=True)
        
        return timeline
    
    def _get_title(self, node):
        if node["node_type"] == "Event":
            return node.get("content", {}).get("title", "Untitled Event")
        elif node["node_type"] == "Entity" and node["content"].get("entity_type") == "Document":
            return f"Document: {node.get('content', {}).get('name', 'Untitled')}"
        elif node["node_type"] == "Observation":
            return f"Observation: {node.get('content', {}).get('observation_type', 'General')}"
        else:
            return f"{node['node_type']}: {node.get('node_id')}"
    
    def _get_description(self, node):
        if node["node_type"] == "Event":
            return node.get("content", {}).get("description", "")
        elif node["node_type"] == "Observation":
            text = node.get("content", {}).get("text", "")
            return text[:100] + "..." if len(text) > 100 else text
        else:
            return ""
```

### Document Management Integration

```python
class DocumentManager:
    def __init__(self, api_key):
        self.client = ChronoWeaveClient(api_key=api_key)
    
    def add_document(self, user_id, project_id, title, content, doc_type="note", tags=None):
        # Generate document ID
        import uuid
        doc_id = f"doc_{uuid.uuid4().hex[:8]}"
        
        # Store document entity
        self.client.observe({
            "node_type": "Entity",
            "content": {
                "entity_id": doc_id,
                "entity_type": "Document",
                "name": title,
                "document_type": doc_type,
                "attributes": {
                    "tags": tags or []
                }
            },
            "metadata": {
                "user_id": user_id,
                "project_id": project_id
            }
        })
        
        # Store document content
        self.client.observe({
            "node_type": "Observation",
            "content": {
                "observation_type": "DocumentContent",
                "text": content,
                "subject_id": doc_id,
                "observer_id": user_id
            },
            "metadata": {
                "user_id": user_id,
                "project_id": project_id
            }
        })
        
        # Create relationship between project and document
        self.client.observe({
            "node_type": "Relationship",
            "content": {
                "type": "CONTAINS",
                "source_id": project_id,
                "target_id": doc_id
            }
        })
        
        return doc_id
    
    def find_related_documents(self, doc_id, limit=5):
        # Get the document
        doc = self.client.recall_by_id(doc_id)
        
        if not doc:
            return []
        
        # Extract document content
        content = ""
        project_id = doc.get("metadata", {}).get("project_id")
        
        # Get document content
        content_results = self.client.recall({
            "text": "",
            "entity_ids": [doc_id]
        })
        
        for result in content_results["results"]:
            node = result["node"]
            if node["node_type"] == "Observation" and node["content"]["observation_type"] == "DocumentContent":
                content = node["content"]["text"]
                break
        
        # Find related documents
        related_results = self.client.recall({
            "text": content[:1000],  # Use first 1000 chars for query
            "entity_ids": [project_id] if project_id else [],
            "k": limit + 1  # +1 to account for the document itself
        })
        
        # Filter and format results
        related_docs = []
        for result in related_results["results"]:
            node = result["node"]
            
            # Skip the original document
            if node["node_id"] == doc_id:
                continue
                
            # Only include documents
            if node["node_type"] == "Entity" and node["content"].get("entity_type") == "Document":
                related_docs.append({
                    "id": node["node_id"],
                    "title": node["content"]["name"],
                    "type": node["content"].get("document_type", "document"),
                    "tags": node["content"].get("attributes", {}).get("tags", []),
                    "score": result["score"]
                })
        
        # Sort by relevance
        related_docs.sort(key=lambda x: x["score"], reverse=True)
        
        return related_docs[:limit]
```

## Best Practices

### Data Organization

- **Project Structure**: Organize information by projects and sub-projects
- **Document Relationships**: Establish clear relationships between documents
- **Decision Tracking**: Record decisions with detailed rationales
- **Temporal Metadata**: Include accurate timestamps for all information

### Knowledge Management

- **Consistent Tagging**: Use consistent tags across documents and observations
- **Cross-References**: Create explicit relationships between related items
- **Version Tracking**: Track document versions and changes
- **Importance Ranking**: Assign importance scores to prioritize information

### Context Quality

- **Relevance Filtering**: Use appropriate filters to get the most relevant context
- **Project Scope**: Limit context to the current project when appropriate
- **Temporal Relevance**: Consider recency when retrieving information
- **Semantic Relevance**: Use specific queries to improve semantic matching

### Integration Points

- **Document Creation**: Capture context when creating new documents
- **Research Queries**: Enhance research queries with project context
- **Decision Support**: Provide relevant context for decision-making
- **Project Reviews**: Generate project timelines and summaries
