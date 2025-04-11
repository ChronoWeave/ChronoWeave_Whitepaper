# Personal Memory Integration Guide

This guide provides instructions for integrating ChronoWeave into personal memory applications to help users remember and retrieve their experiences, learnings, and information.

## Table of Contents

- [Overview](#overview)
- [Benefits](#benefits)
- [Integration Steps](#integration-steps)
- [Data Model](#data-model)
- [Implementation Examples](#implementation-examples)
- [Best Practices](#best-practices)

## Overview

Personal memory applications can leverage ChronoWeave to create an external memory system that helps users remember and retrieve their experiences and information. By capturing and organizing memories with temporal context, users can overcome the limitations of human memory.

## Benefits

- **Extended Memory**: Overcome the limitations of human memory
- **Temporal Organization**: Organize memories by time and relevance
- **Contextual Recall**: Retrieve memories based on context, not just keywords
- **Privacy-Aware Storage**: Store personal memories with appropriate privacy controls
- **Memory Augmentation**: Enhance recall of experiences, learnings, and information

## Integration Steps

### 1. Set Up ChronoWeave

```python
from chronoweave.client import ChronoWeaveClient

# Initialize client
client = ChronoWeaveClient(api_key="your_api_key")
```

### 2. Register User

```python
# Register a user
user_data = {
    "node_type": "Entity",
    "content": {
        "entity_id": "user_123",
        "entity_type": "User",
        "name": "John Doe",
        "attributes": {
            "email": "john@example.com"
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
```

### 3. Store Memories

```python
# Store a memory
memory_data = {
    "node_type": "Observation",
    "content": {
        "observation_type": "Memory",
        "text": "Had lunch with Sarah at the Italian restaurant downtown. She mentioned her new job at Google.",
        "subject_id": "user_123",
        "observer_id": "user_123",
        "category": "experience"
    },
    "temporal": {
        "start_time": "2023-06-15T14:30:00Z"
    },
    "metadata": {
        "importance": 0.7,
        "location": "Downtown Italian Restaurant",
        "tags": ["lunch", "Sarah", "meeting", "food"],
        "is_private": False
    }
}

client.observe(memory_data)

# Store a learning
learning_data = {
    "node_type": "Observation",
    "content": {
        "observation_type": "Memory",
        "text": "Learned about the concept of 'spaced repetition' for more effective learning and memory retention.",
        "subject_id": "user_123",
        "observer_id": "user_123",
        "category": "learning"
    },
    "temporal": {
        "start_time": "2023-06-16T10:00:00Z"
    },
    "metadata": {
        "importance": 0.8,
        "tags": ["learning", "memory", "technique"],
        "is_private": False
    }
}

client.observe(learning_data)
```

### 4. Store People and Places

```python
# Store a person
person_data = {
    "node_type": "Entity",
    "content": {
        "entity_id": "person_456",
        "entity_type": "Person",
        "name": "Sarah Johnson",
        "attributes": {
            "relationship": "friend",
            "occupation": "Software Engineer",
            "company": "Google"
        }
    },
    "metadata": {
        "user_id": "user_123"
    }
}

client.observe(person_data)

# Create relationship between user and person
relationship_data = {
    "node_type": "Relationship",
    "content": {
        "type": "KNOWS",
        "source_id": "user_123",
        "target_id": "person_456"
    }
}

client.observe(relationship_data)

# Store a place
place_data = {
    "node_type": "Entity",
    "content": {
        "entity_id": "place_789",
        "entity_type": "Location",
        "name": "Downtown Italian Restaurant",
        "attributes": {
            "address": "123 Main St, Downtown",
            "cuisine": "Italian",
            "rating": 4.5
        }
    },
    "metadata": {
        "user_id": "user_123"
    }
}

client.observe(place_data)
```

### 5. Recall Memories

```python
# Recall memories about Sarah
memories = client.recall({
    "text": "Sarah job",
    "reference_time": "2023-06-20T00:00:00Z",
    "entity_ids": ["user_123"],
    "k": 5
})

# Process results
for result in memories["results"]:
    node = result["node"]
    score = result["score"]
    
    if node["node_type"] == "Observation" and node["content"]["observation_type"] == "Memory":
        print(f"Memory: {node['content']['text']}")
        print(f"Date: {node['temporal']['start_time']}")
        print(f"Category: {node['content']['category']}")
        if node['metadata'].get('location'):
            print(f"Location: {node['metadata']['location']}")
        print(f"Relevance: {score}")
        print()
```

### 6. Ask Memory Assistant

```python
# Infuse prompt with memory context
prompt = "What did Sarah tell me about her job?"
infused_prompt = client.infuse_prompt(
    prompt=prompt,
    user_id="user_123",
    model_type="openai"
)

# Use with LLM
from openai import OpenAI
openai_client = OpenAI(api_key="your_openai_api_key")

response = openai_client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "You are a personal memory assistant that helps users remember their experiences and information."},
        {"role": "user", "content": infused_prompt}
    ]
)

assistant_response = response.choices[0].message.content
```

## Data Model

For personal memory integration, consider the following data model:

### Entity Types

- **User**: The person whose memories are being stored
- **Person**: People the user knows or has interacted with
- **Location**: Places the user has visited or mentioned

### Observation Types

- **Memory**: Personal memories with different categories
  - **Experience**: Personal experiences and events
  - **Learning**: Things learned or studied
  - **Contact**: People met or interacted with
  - **Idea**: Ideas, thoughts, or insights
  - **Task**: Tasks or to-dos
  - **Reflection**: Reflections or journal entries
  - **Media**: Books, movies, articles consumed

### Event Types

- **LifeEvent**: Significant life events
- **Travel**: Travel experiences
- **Meeting**: Meetings or gatherings

## Implementation Examples

### Personal Memory Assistant

```python
class PersonalMemoryAssistant:
    def __init__(self, api_key):
        self.client = ChronoWeaveClient(api_key=api_key)
        self.openai_client = OpenAI(api_key="your_openai_api_key")
        
        self.memory_categories = [
            "experience",    # Personal experiences and events
            "learning",      # Things learned or studied
            "contact",       # People met or interacted with
            "idea",          # Ideas, thoughts, or insights
            "task",          # Tasks or to-dos
            "reflection",    # Reflections or journal entries
            "location",      # Places visited or lived
            "media",         # Books, movies, articles consumed
            "other"          # Miscellaneous memories
        ]
    
    def store_memory(self, user_id, memory_text, category="experience", location=None, 
                    people=None, tags=None, timestamp=None, importance=0.5, is_private=False):
        """Store a new memory."""
        import datetime
        
        # Validate category
        if category not in self.memory_categories:
            category = "other"
        
        # Use current time if not specified
        if not timestamp:
            timestamp = datetime.datetime.now().isoformat()
        
        # Create metadata
        metadata = {
            "importance": importance,
            "user_id": user_id,
            "category": category,
            "is_private": is_private
        }
        
        if tags:
            metadata["tags"] = tags
        
        if location:
            metadata["location"] = location
        
        # Store memory
        memory = self.client.observe({
            "node_type": "Observation",
            "content": {
                "observation_type": "Memory",
                "text": memory_text,
                "subject_id": user_id,
                "observer_id": user_id,
                "category": category
            },
            "temporal": {
                "start_time": timestamp
            },
            "metadata": metadata
        })
        
        # Add relationships to people if specified
        if people:
            for person in people:
                # Check if person entity exists
                person_entity = self._get_or_create_person(user_id, person)
                
                # Create relationship between memory and person
                self.client.observe({
                    "node_type": "Relationship",
                    "content": {
                        "type": "INVOLVES",
                        "source_id": memory["node_id"],
                        "target_id": person_entity["node_id"]
                    }
                })
        
        return memory
    
    def _get_or_create_person(self, user_id, person_name):
        """Get or create a person entity."""
        import uuid
        
        # Query for existing person
        results = self.client.recall({
            "text": person_name,
            "entity_ids": [user_id]
        })
        
        # Check if person exists
        for result in results["results"]:
            node = result["node"]
            if (node["node_type"] == "Entity" and 
                node["content"]["entity_type"] == "Person" and
                node["content"]["name"] == person_name):
                return node
        
        # Create new person entity
        person_id = f"person_{uuid.uuid4().hex[:8]}"
        
        person = self.client.observe({
            "node_type": "Entity",
            "content": {
                "entity_id": person_id,
                "entity_type": "Person",
                "name": person_name,
                "attributes": {}
            },
            "metadata": {
                "user_id": user_id
            }
        })
        
        # Create relationship between user and person
        self.client.observe({
            "node_type": "Relationship",
            "content": {
                "type": "KNOWS",
                "source_id": user_id,
                "target_id": person["node_id"]
            }
        })
        
        return person
    
    def recall_memories(self, user_id, query_text, start_date=None, end_date=None, 
                       category=None, location=None, people=None, tags=None, 
                       include_private=True, limit=10):
        """Recall memories based on query and filters."""
        # Build filters
        filters = {}
        
        if start_date or end_date:
            time_window = {}
            if start_date:
                time_window["start"] = start_date
            if end_date:
                time_window["end"] = end_date
            filters["time_window"] = time_window
        
        # Create query
        query = {
            "text": query_text,
            "entity_ids": [user_id],
            "filters": filters,
            "k": limit * 2  # Get more candidates for filtering
        }
        
        # Get memories
        results = self.client.recall(query)
        
        # Filter and format memories
        memories = []
        for result in results["results"]:
            node = result["node"]
            
            # Skip non-memory observations
            if (node["node_type"] != "Observation" or 
                node["content"]["observation_type"] != "Memory"):
                continue
            
            # Skip private memories if not included
            if not include_private and node["metadata"].get("is_private", False):
                continue
            
            # Apply category filter
            if category and node["metadata"].get("category") != category:
                continue
            
            # Apply location filter
            if location and node["metadata"].get("location") != location:
                continue
            
            # Apply tags filter
            if tags:
                node_tags = node["metadata"].get("tags", [])
                if not any(tag in node_tags for tag in tags):
                    continue
            
            # Format memory
            memory = {
                "id": node["node_id"],
                "text": node["content"]["text"],
                "category": node["metadata"].get("category", "other"),
                "timestamp": node["temporal"]["start_time"],
                "importance": node["metadata"].get("importance", 0.5),
                "is_private": node["metadata"].get("is_private", False),
                "location": node["metadata"].get("location"),
                "tags": node["metadata"].get("tags", []),
                "score": result["score"]
            }
            
            memories.append(memory)
        
        # Apply people filter if needed
        if people:
            # This is a simplified approach - in a real implementation,
            # you would query the relationships to filter by people
            filtered_memories = []
            for memory in memories:
                # For demo purposes, we'll just check if people names are in the text
                if any(person.lower() in memory["text"].lower() for person in people):
                    filtered_memories.append(memory)
            memories = filtered_memories
        
        # Sort by score and limit
        memories.sort(key=lambda x: x["score"], reverse=True)
        return memories[:limit]
    
    def ask_memory_assistant(self, user_id, query_text):
        """Ask the memory assistant a question."""
        # Infuse prompt with memory context
        infused_prompt = self.client.infuse_prompt(
            prompt=query_text,
            user_id=user_id
        )
        
        # Get response from LLM
        response = self.openai_client.chat.completions.create(
            model="gpt-4",
            messages=[
                {"role": "system", "content": "You are a personal memory assistant that helps users remember their experiences and information. Use the context provided to help the user recall their memories."},
                {"role": "user", "content": infused_prompt}
            ]
        )
        
        answer = response.choices[0].message.content
        
        # Store the interaction
        self.client.observe({
            "node_type": "Observation",
            "content": {
                "observation_type": "Interaction",
                "text": f"Query: {query_text}\nResponse: {answer}",
                "subject_id": user_id,
                "observer_id": "memory_assistant"
            }
        })
        
        return answer
    
    def get_memory_timeline(self, user_id, start_date=None, end_date=None, category=None):
        """Get a timeline of memories."""
        import datetime
        
        # Default to last 30 days if not specified
        if not end_date:
            end_date = datetime.datetime.now().isoformat()
        if not start_date:
            start_date = (datetime.datetime.now() - datetime.timedelta(days=30)).isoformat()
        
        # Build filters
        filters = {
            "time_window": {
                "start": start_date,
                "end": end_date
            }
        }
        
        # Create query
        query = {
            "text": category or "memory",
            "entity_ids": [user_id],
            "filters": filters,
            "k": 100  # Get a large number for timeline
        }
        
        # Get memories
        results = self.client.recall(query)
        
        # Format timeline
        timeline = []
        for result in results["results"]:
            node = result["node"]
            
            # Skip non-memory observations
            if (node["node_type"] != "Observation" or 
                node["content"]["observation_type"] != "Memory"):
                continue
            
            # Apply category filter
            if category and node["metadata"].get("category") != category:
                continue
            
            # Format memory
            memory = {
                "id": node["node_id"],
                "text": node["content"]["text"],
                "category": node["metadata"].get("category", "other"),
                "timestamp": node["temporal"]["start_time"],
                "importance": node["metadata"].get("importance", 0.5),
                "location": node["metadata"].get("location"),
                "tags": node["metadata"].get("tags", [])
            }
            
            timeline.append(memory)
        
        # Sort by timestamp
        timeline.sort(key=lambda x: x["timestamp"])
        
        return timeline
```

### Journal Integration

```python
class JournalApp:
    def __init__(self, api_key):
        self.memory_assistant = PersonalMemoryAssistant(api_key)
    
    def add_journal_entry(self, user_id, entry_text, tags=None):
        """Add a journal entry as a reflection memory."""
        return self.memory_assistant.store_memory(
            user_id=user_id,
            memory_text=entry_text,
            category="reflection",
            tags=tags or ["journal"],
            importance=0.8
        )
    
    def get_journal_entries(self, user_id, start_date=None, end_date=None, tags=None, limit=10):
        """Get journal entries."""
        return self.memory_assistant.recall_memories(
            user_id=user_id,
            query_text="journal",
            start_date=start_date,
            end_date=end_date,
            category="reflection",
            tags=tags,
            limit=limit
        )
    
    def get_entry_suggestions(self, user_id):
        """Get suggestions for journal entries based on recent memories."""
        import datetime
        
        # Get recent memories
        one_week_ago = (datetime.datetime.now() - datetime.timedelta(days=7)).isoformat()
        recent_memories = self.memory_assistant.recall_memories(
            user_id=user_id,
            query_text="",
            start_date=one_week_ago,
            category="experience",
            limit=5
        )
        
        # Create prompt for suggestions
        memory_texts = [f"- {memory['text']}" for memory in recent_memories]
        memory_context = "\n".join(memory_texts)
        
        prompt = f"""Based on these recent experiences, suggest some journal prompts for reflection:

{memory_context}

Suggest 3 journal prompts:"""
        
        # Get suggestions from LLM
        infused_prompt = self.memory_assistant.client.infuse_prompt(
            prompt=prompt,
            user_id=user_id
        )
        
        response = self.memory_assistant.openai_client.chat.completions.create(
            model="gpt-4",
            messages=[
                {"role": "system", "content": "You are a journaling assistant that helps users reflect on their experiences."},
                {"role": "user", "content": infused_prompt}
            ]
        )
        
        return response.choices[0].message.content
```

## Best Practices

### Privacy and Security

- **Explicit Consent**: Always obtain user consent for memory storage
- **Privacy Controls**: Allow users to mark memories as private
- **Data Encryption**: Encrypt sensitive personal information
- **User Control**: Give users full control over their memory data
- **Deletion Options**: Provide easy ways to delete memories

### Memory Quality

- **Structured Capture**: Use consistent categories and metadata
- **Rich Context**: Capture location, people, and other context
- **Temporal Accuracy**: Ensure accurate timestamps for all memories
- **Importance Ranking**: Allow users to indicate memory importance
- **Memory Connections**: Create relationships between related memories

### Retrieval Effectiveness

- **Natural Language Queries**: Support natural language for memory recall
- **Multi-faceted Search**: Allow filtering by time, people, location, etc.
- **Semantic Matching**: Use semantic search for concept-based retrieval
- **Temporal Relevance**: Consider recency in memory ranking
- **Contextual Recall**: Provide surrounding context for recalled memories

### User Experience

- **Effortless Capture**: Make memory capture as frictionless as possible
- **Privacy Indicators**: Clearly show privacy status of memories
- **Memory Timeline**: Provide chronological views of memories
- **Memory Insights**: Offer insights and patterns from memory data
- **Adaptive Assistance**: Tailor memory assistance to user needs
