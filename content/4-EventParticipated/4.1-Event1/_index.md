---
title: "Event 1"
date: 2026-05-09
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Reflection Report: “Prompt Engineering, AI Mindmap & BMAD Methodl”

| Info | Details |
|---|---|
| Date | 09/05/2026 |
| Location | Floor 26, Bitexco Financial Tower, Sài Gòn Ward, Ho Chi Minh City |
| Role | Attendee |

### 1. Introduction

This event brought together content from multiple sources:
- Presentation: **"Automated Prompt Engineering: Enhancing LLM Output Quality"**
- Mindmap: **"Effective AI Interaction & AWS Application Architecture"**
- AI development methodology: **BMAD (Build More Architect Dreams)**

---

### 2. Detailed Content

#### 2.1 Presentation: Automated Prompt Engineering

**Speaker: Nguyễn Tuấn Thịnh**

This presentation explains the importance of prompt engineering when working with Large Language Models (LLMs), and provides principles and tools to improve output quality.

**Why Prompt Engineering Matters:**
- Generic commands produce poor results
- Tokens are wasted
- Vague instructions reduce quality and productivity

**Components of an Effective Prompt:**

| Component | Description |
|---|---|
| Role | Define the AI's persona |
| Instructions | What the AI should do |
| Context | Background information |
| Input Data | Data to process |
| Output Format | Expected format of results |
| Examples | Sample inputs and outputs |
| Constraints | Limitations and rules |

**Prompt Writing Guidelines:**
- Be clear and specific
- Use instructive language
- Describe exactly what should be done
- Allow "I don't know" responses when needed
- Break down long tasks into smaller steps

**Token Economics:**
- Tokens are the processing unit of LLMs
- Usage fees are based on input and output tokens
- Costs vary by language, and Vietnamese usually consumes more tokens than English

**Advanced Techniques:**
- Chain-of-Thought (CoT): step-by-step reasoning
- Tree-of-Thoughts (ToT): branching reasoning paths
- Self-Consistency: compare multiple reasoning paths and choose the best answer
- Retrieval-Augmented Generation (RAG): augment responses with external knowledge
- Role Prompting: assign a specific role to the model

**Tool - Proptimizer:**

A browser extension that automatically optimizes prompts based on an AWS serverless architecture:
CloudFront -> S3 -> Cognito -> API Gateway -> Lambda -> Bedrock -> DynamoDB -> CloudWatch

---

#### 2.2 Mindmap: Effective AI Interaction & AWS Application Architecture

**Optimizing AI Interaction:**
- Use the KFC model (Knowledge, Format, Constraints) to provide clear context, format, and constraints

**Recommended Methods:**
- Break down tasks
- Ask AI for suggestions
- Compare multiple options
- Add structured data

**Advanced AI Techniques:**
- Apply CoT, ToT, Self-Consistency, RAG, and Role Prompting to improve results

**AWS Architecture (Serverless):**
CloudFront, S3, Cognito, API Gateway, Lambda, Bedrock, DynamoDB, CloudWatch

---

#### 2.3 BMAD Method (Build More Architect Dreams)

BMAD is a free, open-source AI-driven development framework that helps teams build solutions through a structured process.

**Key Features:**
- Phases: Analysis -> Planning -> Architecture -> Deployment
- More than 12 specialized agents that guide users through the workflow
- Integration with Agile practices
- AI assistant: `bmad-help`
- Party Mode for multi-agent collaboration

**Main Modules:**

| Module | Description |
|---|---|
| BMM | Core framework - 34 processes |
| BMB | Build custom agents |
| TEA | Testing and automation |
| BMGD | Game development |
| CIS | Innovation and design thinking |

**Quick Install (requires Node.js 20+, Python 3.10+, and `uv`):**
```bash
npx bmad-method install
```

**Community:** Discord, YouTube, X/Twitter

---

### 3. Photo from the Event

Below is a photo captured while attending the event:
![Photo from Event 1](/images/event1.jpeg)
---

### 4. Conclusion

This event clearly presented three separate domains: prompt engineering, AI interaction patterns, and the BMAD development methodology. Each section serves a different purpose and comes from a distinct source, helping readers combine the material without confusion. Together, these tools and methods provide a practical foundation for working effectively with AI systems and AWS cloud architecture.