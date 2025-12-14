# Top 10 Best Practices for DACA (Dapr Agentic Cloud Ascent)

## Introduction

This guide presents the top 10 best practices for developing scalable, resilient, and efficient agentic AI systems using the DACA (Dapr Agentic Cloud Ascent) design pattern. These practices are based on AI-first and cloud-first principles, ensuring the creation of systems capable of handling millions of concurrent agents.

---

## 1. 🤖 AI-First: Start with OpenAI Agents SDK

**Principle**: Use OpenAI Agents SDK as the main framework for most agent development use cases.

**Why it matters**:
- **Minimal abstraction** with high control
- **Low learning curve** and high simplicity
- **Python-first** approach with core primitives (Agents, Handoffs, Guardrails)
- Balance between power and usability

**How to apply**:
```python
from openai import OpenAI

# Create agent with direct control
agent = OpenAI().agents.create(
    name="TaskAgent",
    instructions="Execute tasks autonomously"
)
```

**Alternatives for special cases**:
- **LangGraph**: For maximum control over complex workflows
- **Dapr Agents**: For enterprise-scale with Kubernetes integration
- **Google ADK**: For multi-level agent hierarchies with Google Cloud

---

## 2. 🐳 Develop Anywhere: Containerization from Day One

**Principle**: Use Docker/OCI containers as the standard for all agentic AI development environments.

**Why it matters**:
- Consistency across different operating systems (macOS, Windows, Linux)
- Eliminates "works on my machine" problems
- Reproducible, isolated development environments
- OS-agnostic, location-agnostic development

**How to apply**:
```dockerfile
FROM python:3.11-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .
CMD ["python", "agent.py"]
```

**Tools**:
- **VS Code Dev Containers** for isolated environments
- **Rancher Desktop** for local development
- **Docker Compose** for multi-container systems

---

## 3. ☁️ Cloud Anywhere: Kubernetes for Orchestration

**Principle**: Use Kubernetes as the standard orchestration layer for AI agent deployment.

**Why it matters**:
- **Deployment portability** across cloud providers
- Avoids vendor lock-in
- Consistency from local development to production
- Scales to planetary level

**DACA Progressive Path**:
1. **Local Development**: Rancher Desktop with local Kubernetes
2. **Prototyping**: Hugging Face Docker Spaces (free)
3. **Medium Scale**: Azure Container Apps (Serverless Containers)
4. **Planetary Scale**: Kubernetes with self-hosted LLMs

**How to apply**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ai-agent
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: agent
        image: myagent:latest
```

---

## 4. 🔄 Stateless Design: No State in Containers

**Principle**: Design agents as stateless containers with external state management through Dapr.

**Why it matters**:
- Efficient scaling
- Simplified failure recovery
- Pure function approach: `agent(events) → new_event`
- Unit-testable with in-memory storage

**Architecture**:
- **Containers**: Stateless, no state retention
- **Dapr Sidecar**: State management, messaging, workflows
- **State Stores**: Redis, CockroachDB for persistence

**How to apply**:
```python
# Stateless agent - state in Dapr
def process_event(event, dapr_client):
    state = dapr_client.get_state(store_name, key)
    result = agent_logic(event, state)
    dapr_client.save_state(store_name, key, new_state)
    return result
```

---

## 5. 🔌 MCP: Standardized Tool Access

**Principle**: Use Model Context Protocol (MCP) for standardized tool calling and resource access.

**Why it matters**:
- **Standardization** of agent-tool interactions
- Context and state management for LLM
- Secure and controlled data access
- JSON schemas for call validation

**MCP Components**:
- **Resources**: Access to external data (/resources)
- **Tools**: Functions the agent can call (/tools)
- **Context Management**: Maintaining relevant context

**How to apply**:
```python
# MCP server provides tools
mcp_tools = {
    "get_data": {...},
    "process_request": {...}
}

# Agent calls through MCP
result = agent.call_tool("get_data", params)
```

---

## 6. 🤝 A2A: Inter-Agent Communication

**Principle**: Use Agent2Agent (A2A) protocol for standardized agent-to-agent interaction.

**Why it matters**:
- **Vendor-agnostic** communication between agents
- Supports intra- and inter-organizational workflows
- Integration of physical and digital agents (robots)
- Foundation for building "Agentia World"

**A2A Capabilities**:
- Agent discovery
- Task coordination
- Secure message exchange
- JSON-RPC and Server-Sent Events (SSE)

**Integration with Kafka**:
- For scalable, event-driven communication
- Decoupled many-to-many interactions
- Durability and auditability

**How to apply**:
```python
# A2A message through Dapr pub/sub
dapr_client.publish_event(
    pubsub_name='kafka',
    topic_name='a2a.tasks',
    data={'agent_id': 'agent1', 'task': 'analyze_data'}
)
```

---

## 7. 🏗️ Open Core, Managed Edges

**Principle**: Use open-source technologies as the system core, with proprietary managed services at the edges.

**Why it matters**:
- **Flexibility and control** through open-source
- Avoids vendor lock-in
- **Operational efficiency** through managed services
- Balance of cost, control, and performance

**Open Core**:
- Kubernetes
- Dapr
- FastAPI
- PostgreSQL

**Managed Edges**:
- CockroachDB Serverless
- Upstash Redis
- OpenAI APIs
- RabbitMQ Cloud

**How to apply**:
```yaml
# Kubernetes (Open Core) + Managed Services (Edges)
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  DB_HOST: "cockroachdb.cloud"  # Managed Edge
  REDIS_HOST: "upstash.redis"   # Managed Edge
```

---

## 8. 🔐 Follow 12-Factor Agents Principles

**Principle**: Apply 12-Factor Agents principles for reliable LLM-based systems.

**Key Factors**:

1. **Natural-language → tool calls**: MCP servers as first-class tools
2. **Own your prompts**: Versioned prompts in Git repository
3. **Own your context window**: Context size management through Dapr
4. **Tools are structured outputs**: JSON payloads through MCP
5. **Unify execution & business state**: Dapr Actors store thread + data
6. **Launch/pause/resume via API**: FastAPI endpoints for control
7. **Contact humans with tool calls**: HITL as MCP tool
8. **Own your control flow**: Dapr Workflows for logic
9. **Compact errors into context**: Concise error messages
10. **Small, focused agents**: Separate containers by agent type
11. **Trigger from anywhere**: Kubernetes CronJobs, HTTP, or topics
12. **Stateless reducer design**: Pure functions without internal state

**How to apply**:
- Repository structure: `/prompts`, `/workflows`, `/tests`, `/charts`
- Automate replay-from-events to verify purity
- Enable distributed tracing in Dapr

---

## 9. 📊 Event-Driven Architecture (EDA)

**Principle**: Design the system as event-driven using Dapr pub/sub and workflows.

**Why it matters**:
- **Asynchronicity and reactivity** for real-time performance
- Component decoupling for scalability
- Durability and retry logic through Dapr
- Support for complex multi-agent workflows

**Architecture**:
- **Message Brokers**: RabbitMQ, Kafka
- **Dapr Pub/Sub**: Abstraction over brokers
- **Event Bus**: Central event bus
- **Workflows**: Event-based orchestration

**Patterns**:
- Task chaining
- Fan-out/Fan-in
- Saga patterns for distributed transactions

**How to apply**:
```python
# Publish event
await dapr_client.publish_event(
    pubsub_name='rabbitmq',
    topic_name='agent.tasks',
    data={'event': 'task_completed'}
)

# Subscribe to events
@app.subscribe(pubsub_name='rabbitmq', topic='agent.tasks')
async def handle_task(event):
    process_task(event.data)
```

---

## 10. 👥 Human-in-the-Loop (HITL) Integration

**Principle**: Build human interaction capability as part of the agent architecture.

**Why it matters**:
- Autonomy with **human oversight** for critical decisions
- Regulatory compliance
- Continuous improvement through feedback
- User trust in the system

**HITL Architecture**:
1. Agent determines need for human decision
2. Emit event: `HumanReviewRequired`
3. UI (Chainlit/Streamlit) receives event
4. Human makes decision
5. Emit event: `HumanDecisionMade`
6. Agent continues execution

**How to apply**:
```python
# HITL as MCP tool
if requires_human_review(task):
    # Emit event to UI
    await emit_event('HumanReviewRequired', task_data)
    
    # Pause agent
    agent.pause()
    
    # After receiving human decision
    @app.on_event('HumanDecisionMade')
    async def resume_agent(decision):
        agent.resume(decision)
```

**UI Options**:
- **Next.js**: Full-featured web interface
- **Streamlit**: Quick dashboards
- **Chainlit**: Specialized for agents

---

## Additional Recommendations

### 💰 Cost Optimization
- Start with free-tier services (Hugging Face, Azure Container Apps free tier)
- Use self-hosted LLMs for planet-scale
- Implement Kubecost for financial control

### 🔍 Observability
- OpenTelemetry for distributed tracing
- Prometheus and Grafana for monitoring
- Agent-centric logging in machine-readable format

### 🔒 Security
- mTLS for inter-service communication
- ACLs and RBAC for Kafka topics
- Secrets management through Dapr
- Schema Registry for message validation

### 🧪 Testing
- Unit tests with in-memory state store
- Integration tests with test containers
- Automated prompt evaluation (eval harness)

---

## DACA Path: From Development to Production

```
┌─────────────────┐
│  1. Local       │  Rancher Desktop + Docker
│   Development   │  Open-source stack
└────────┬────────┘
         │
┌────────▼────────┐
│ 2. Prototype    │  Hugging Face Spaces
│    (Free)       │  Free deployment
└────────┬────────┘
         │
┌────────▼────────┐
│ 3. Medium       │  Azure Container Apps
│    Scale        │  Serverless Containers
└────────┬────────┘
         │
┌────────▼────────┐
│ 4. Planetary    │  Kubernetes Cluster
│    Scale        │  Self-hosted LLMs
└─────────────────┘
```

---

## Conclusion

Following these 10 DACA best practices ensures:
- ✅ **Scalability** to millions of concurrent agents
- ✅ **Resilience** through Dapr and Kubernetes
- ✅ **Cost efficiency** through progressive deployment
- ✅ **Flexibility** through open-core approach
- ✅ **Interoperability** through MCP and A2A protocols
- ✅ **Agentia World readiness** — the future of intelligent agents

**Remember**: Start simple with local development and scale gradually as needed. DACA provides a smooth path from prototype to planetary scale.

---

## Additional Resources

- [Comprehensive Guide to DACA](./comprehensive_guide_daca.md)
- [OpenAI Agents SDK Documentation](https://platform.openai.com/docs/agents)
- [Dapr Documentation](https://docs.dapr.io/)
- [Model Context Protocol](https://github.com/modelcontextprotocol/mcp)
- [Agent2Agent Protocol](https://github.com/google/agent2agent)
- [12-Factor Agents](https://github.com/humanlayer/12-factor-agents)

---

**Panaversity Certified Agentic & Robotic AI Engineer Program**
- AI-201: Fundamentals of Agentic AI and DACA
- AI-202: DACA Cloud-First Agentic AI Development
- AI-301: DACA Planet-Scale Distributed AI Agents
