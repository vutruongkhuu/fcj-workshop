---
title: "Event 4"
date: 2024-06-27
weight: 4
chapter: false
pre: " <b> 4.4. </b> "
---

# Reflection Report: “FCAJ Community Day”

### Event Purpose

- **Update practical cloud and AI thinking and technology:** bring the latest knowledge from real business practice — from improving AI work habits with context, designing multi-agent systems, to modern infrastructure solutions such as Amazon CloudFront and private connectivity.
- **Promote optimization and modernization thinking:** help developers move from manual configuration to mastering infrastructure as code (Terraform), understanding cost optimization (FinOps), security (SecOps), and applying No-Code/Low-Code tools (Amazon Quick) to democratize data in the enterprise.
- **Build community and shape career direction:** create a high-quality networking space between experts, businesses, and young students/engineers. In doing so, inspire innovation (such as the Hackathon journey) and clarify market reality: remind young people to focus on solid foundational skills (Backend/Core Engineering) combined with AI application skills to avoid being left behind.

### Speakers

- **Steve Tran** - Cloud Thinker
- **Danh Hoang Hieu Nghi** - Renova Cloud
- **Trung** - CEO, Folder Reeve AI
- **Bao & Nguyen Nguyen** - Cloud Engineer (Cloud Kinetics)
- **Truong Tran (Wayne) & Minh Anh (Akie)** - Solution Sales, Noveantiq Vietnam
- **Toan Nguyen** - AWS Security Builder, **Danh Hoang Hieu Nghi** - Renova Cloud

### Key Highlights

#### AgenticOps for your Cloud

- **Market perspective and fear of AI replacing people:** he shared that large companies currently tend to hire very senior staff to work alongside AI tools to accelerate coding and system implementation, which creates significant pressure for younger developers and cloud engineers.
- **Solving complexity problems:** when enterprise systems scale and go global, complexity rises significantly. He emphasized that AI cannot fully replace humans, especially in Cloud Infrastructure support, where incident handling is highly critical and decision-making must be fast and accurate.
- **Applying AI to Cloud:** he shared the development of AI solutions capable of understanding the entire infrastructure—from source code and infrastructure code to business logic—to help companies optimize resources.

#### Building Voice Agent at Scale

- **Optimizing FinOps with AI:** Mr. Nghị delved into FinOps (cloud cost management and optimization). A purely financial or accounting person often struggles to understand cloud technology, but AI—understanding both AWS and financial data—can automatically analyze and provide much better cost optimization recommendations.
- **Controlling data costs:** the discussion covered estimating the cost of moving enterprise data across private cloud infrastructure.

#### From a demo to a production voice agent

This section offered the perspective of a technology product developer, addressing the core challenge of digital transformation. It focused on how to integrate artificial intelligence (AI engine) to handle complex logic layers in large data governance and optimize automated workflows.

#### AWS DevOps Agent: Your Always Available Operations Teammate

- **Current security situation in Vietnam:** the speakers pointed out that security issues are often underestimated in some Vietnamese businesses, leaving system vulnerabilities unresolved for quarters or years without detection.
- **Automated pentesting solutions with AI:** the team shared how they develop AI tools capable of automatically performing pentesting, continuously evaluating infrastructure structure, and reviewing system logs to tightly control security risks.

#### AI-Powered Productivity Workforce Planning for Enterprise

- **Making cost estimates concrete:** they gave a very practical example of calculating the cost of running AI-integrated systems on cloud for enterprises (for example, a company with 500 employees).
- **Hidden costs of private infrastructure:** they analyzed operational costs of network infrastructure (such as Route 53 Resolver around $180, ALB around $32, EC2, AWS Secrets Manager) and pointed out that data transfer costs are often less than the cost of maintaining the hardware infrastructure that supports on-prem AI models.

#### Building secure private MCP for Quick

- **Building a data security wall:** Mr. Toàn focused on the technical details of configuring permissions securely in the cloud, protecting MCP (Model Context Protocol) servers when connecting to AWS secure storage services.
- **Importance of information security:** he concluded with practical solutions that help businesses confidently move sensitive data to the cloud without worrying about information leakage from cyberattacks.

### What I Learned

#### Design Thinking

- **Context-Driven Design:** An AI system or software, no matter how powerful, will fail if it is disconnected from the real context of the business. In design, you should not stop at optimizing prompts or code; you must design mechanisms for the system to absorb environmental data, business processes, and the specific pain points of users.
- **Security-First & Reliability-First:** Put aside the mindset that “if it works, it’s fine.” Experts emphasized that an enterprise-grade system must be designed with security and reliability from the start. Identity management and data encryption (such as AWS Secrets Manager) are not add-ons; they are the backbone of design.
- **Cost-Conscious Design:** Every architectural decision comes with a bill. Designing a tightly secured system (Private Connectivity) or scraping data with AI also requires cost estimation (infrastructure, bandwidth, data transfer). A good architect is someone who finds the balance between technical performance and the company’s financial reality.

#### Technical Architecture

- **Multi-Agent Architecture:** A shift from Single-Agent (one chatbot doing everything) to Multi-Agent systems (multiple specialized AI agents collaborating). This approach is like a “virtual committee,” where each agent plays a role (for example, one checks logic, one pulls data, one scores) and cross-checks each other to solve complex enterprise problems.
- **Handling LLM uncertainty at the Backend layer:** Accept the technical reality that large language models (LLMs) always have some error due to hardware optimization algorithms (even at $Temperature = 0$). From an architectural standpoint, you must build validation logic at the Backend to filter and normalize AI output before returning it to the client or saving it to the database.
- **Private Connectivity Architecture:** Designing architectures with secure, isolated channels that separate systems from the public Internet by combining services such as Route 53 Resolver, Application Load Balancer (ALB), and EC2 to protect the integrity of sensitive data flows.

#### Modernization Strategy

- **Infrastructure as Code (IaC) with Terraform:** Modernize operations by stopping manual “point-and-click” configuration in the cloud UI. Instead, define the entire infrastructure as code. This helps companies keep a history of changes (version control), simplify debugging, reduce human error, and reproduce the full system in another cloud region within minutes.
- **Applying AI to FinOps and SecOps:** Modernize system monitoring by leveraging AI. AI can interpret infrastructure logs and compare them with financial data to optimize costs (FinOps) better than humans, while also automatically scanning for vulnerabilities and running penetration testing (SecOps) to improve security.
- **No-Code/Low-Code migration strategy (democratizing data):** Use modern tools (such as Amazon Quick) to modernize how non-technical departments operate. By allowing users to access data, create smart workflows (Quick Flows), and build reports (Quick Sight) entirely through natural language, the enterprise can free up a large amount of operational workload for the IT/Data Engineering team.

### How I Will Apply It

#### 🎨 1. Apply Design Thinking
- **Create a context buffer when using AI for code:** When building a new feature, create a context.md file storing the DB structure, tech stack, and coding conventions of the project. Always feed this file to the AI before requesting code generation so the output aligns better with your system and you avoid too much manual revision.
- **Include cost concerns in the initial design:** When proposing a solution (for example, choosing a database or server configuration for a large workload), make it a habit to estimate the bill using tools like AWS Pricing Calculator. Avoid systems that work technically but become too expensive due to the wrong service choices.

#### 🏗️ 2. Apply Technical Architecture
- **Build a validation layer for AI output:** If your project connects to an LLM API (such as OpenAI or Gemini), never trust the returned format completely (even if you force JSON). Write a validation function (Validation/Pydantic) in the Backend to check data before saving it to the database or displaying it in the UI to prevent app crashes.
- **Break complex problems into an “Agent Committee” model:** Instead of asking one chatbot to do everything, design separate functions/prompts for independent roles: one for analyzing requirements, one for checking logic, and one for formatting results. Let them pass data through a pipeline to improve accuracy.

#### 🚀 3. Apply Modernization Strategy
- **Say no to “manual clicks” — use Terraform:** When working with cloud infrastructure (AWS/Azure), start moving to writing code with Terraform to create EC2, S3, or VPC resources. This makes infrastructure management easier, accelerates project cloning, and creates a highly valuable profile when applying for jobs.
- **Use No-Code tools for reporting:** Instead of writing complex SQL queries or doing manual Excel work every time you need a project report, try integrating data into modern BI tools to query it in natural language, which saves time on repetitive operational tasks.

### Event Experience

Attending the **“FCAJ Community Day”** workshop was a very valuable experience, giving me a comprehensive view of modernizing applications and databases with modern methods and tools. Some highlights included:

#### Learning from highly experienced speakers
- **Practical perspective from experts:** I gained hands-on thinking from engineers and senior managers from major companies (AWS, GoTymeX, Cloud Kinetics, Renova Cloud...).
- **Understanding the real market:** I saw the “AI paradox” clearly: AI makes software cheaper, but it also increases demand for senior staff to operate increasingly complex systems.
- **Guiding advice:** I kept in mind the advice from experts: “No matter how advanced AI becomes, the core value of an engineer remains traditional backend skills, system thinking, and infrastructure management.”

#### Practical technical experience
- **Understanding the true nature of LLMs:** I learned how large language models operate and recognized the “uncertainty” of AI even at Temperature = 0 due to hardware optimization algorithms.
- **Access to Multi-Agent architecture:** I learned the enterprise-style “virtual committee” model—coordinating and cross-checking multiple specialized AI agents to solve complex problems (such as credit scoring).
- **Cost and security thinking:** I directly analyzed infrastructure cost estimation (Route 53 Resolver, ALB, EC2) and private connectivity design solutions to protect data.

#### Using modern tools
- **Infrastructure as Code (IaC) with Terraform:** I realized the importance of using code to manage, version, and replicate cloud infrastructure instead of relying on manual clicks.
- **Amazon CloudFront:** I learned how to optimize website performance, strengthen edge security, and reduce bandwidth costs through intelligent content delivery.
- **Amazon Quick ecosystem:** I explored no-code/low-code trends through tools for data control, workflow automation (Quick Flows), and dashboard creation (Quick Sight) using natural language.

#### Networking and exchange
- **Vibrant tech community:** I had the chance to meet and discuss directly with hundreds of developers and system engineers who shared the same passion.
- **Inspiration from peers:** I learned practical lessons from team UTMorpho (LotusHacks 2026) about time management, teamwork, and surviving high-pressure product development cycles for 36 consecutive hours.

#### Key takeaways
- **Context is King:** To make AI truly useful, you must provide enough project context and always build a validation layer at the Backend to control AI output.
- **Enterprise system standards:** A good system doesn’t stop at “working”; it must ensure three things: Secure, Reliable, and Cost-effective.
- **Be brave to pivot:** When faced with deadline pressure or technical dead-ends, you need to decide to cut features or change direction to deliver the core MVP on time.

#### Some event photos
![Illustration](/images/event4.jpg)

> Overall, the event not only provided technical knowledge but also helped me change the way I think about application design, system modernization, and more effective collaboration across teams.
