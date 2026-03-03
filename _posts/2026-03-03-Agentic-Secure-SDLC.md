---
layout: post
title: "Agent-first Secure Software Development Lifecycle"
author: Bárbara Vieira
---

Agent-first development is a software development approach where autonomous AI agents plan, write, test, and modify code with minimal human intervention. As a security engineer who's spent years watching development teams wait on security reviews, this shift both excites and concerns me.


Agent-first development is incompatible with traditional secure software  development practices. Feature development and testing can be done in minutes, thus having a multi-day/weeks approach to threat modeling, security requirements specification, etc., doesn't scale. 

Code iterates so rapidly that threats and security requirements are outdated before they're finalized. So we need to adapt to a new way of working. We must be able to compress these security workflows from weeks-months to hours-days, while maintaining—or even exceeding—security quality. This is what the agent-first secure software development approach entails.

This blog post gives an introduction of this fundamental shift in how security should integrate into software development. I hope this inspires you to rethink how we build secure software today.


## Introduction

Agent-first software development is here to stay. I could list a bunch of references on this topic. But I think the most relevant one is the Anthropic's recent report on [Agentic Coding Trends](https://resources.anthropic.com/hubfs/2026%20Agentic%20Coding%20Trends%20Report.pdf) which describes how coding agents are reshaping software development. The traditional Software Development Lifecycle (SDLC) to deliver new products or features has shifted from weeks to minutes. This means that we also need to re-think how to implement secure software development practices. 


### The Problem: Security Is Slow (And We Know It)

Let's be honest, the current approach to secure software development is slow:

**Security Requirements**: Read through hundreds of pages of security standards, manually map them to your design, write requirement documents, get them reviewed, iterate. Weeks gone.

**Threat Modeling**: Schedule a meeting, gather stakeholders, spend hours whiteboarding, document everything manually, schedule follow-ups. By the time you're done, the architecture has changed.

**Automated Security Testing**: Translate threats and security controls into test objectives, write test cases, implement tests, maintain them. If you're thorough, this takes longer than writing the actual code.

**Code Review**: Wait for security engineers to review your code, address findings, wait again. The feedback loop is measured in days, not minutes.

**Manual Security Testing**: Schedule penetration testing, wait for security testers to be available, run manual tests, document findings, wait for retesting. Each cycle takes weeks, and by the time you're done, the code has evolved.


For years, we've accepted this as the necessary cost of security. The problem is that this approach doesn't align with agent-first software development practices. At this point, our options are limited: we must adapt how we operationalize security and secure development. We have no options. 

### The Solution: Agents That Actually Understand Security

The agent-first approach uses specialized AI agents to automate comprehensive and time-consuming development tasks. Security is simply a non-functional requirement among many, so applying these principles to the secure software development is a natural logical extension.

Security-focused agents combine deep domain expertise, standards knowledge, and artifact generation capabilities. They introduce a set of new competencies:


**Speed**: What takes humans weeks takes agents hours. Threat modeling? Minutes. Security requirements? Minutes. Test generation? Minutes.

**Comprehensiveness**: Agents take on the repetitive work. They analyze every component, consider every threat, generate every test case.

**Consistency**: Agents apply security standards uniformly. Automation introduces consistency.

**Traceability**: Every artifact is linked—requirements to threats, threats to tests, tests to code. Full audit trail, automatically maintained.

**Rapid iteration**: Each design or code change automatically triggers updates to security requirements, threat models, and tests.


Agent-first secure development uses specialized agents to accelerate traditionally manual SDLC tasks.


The shift isn't in the security approach itself, it's in how we operationalize it and iterate through the different phases of the process. Humans remain central: software engineers, security engineers, and program managers review outputs, validate results, and drive refinement. All artifacts live in version control, stay synchronized with code, and evolve with each iteration.


The following sections outline agent-first secure software development steps, specifically targeting application security and velocity of the software development teams. Highly specialized tasks like manual penetration testing or security operations (often carried out by independent teams) are out of scope of this post.

---
---

## The Agent-first Secure SDLC Workflow

Let me walk you through how *Agent-first Secure SDLC* works. The workflow is organized into three main phases, each supported by specialized agents. The focus is on velocity, consistency, comprehensiveness and full traceability.

![]({{ site.baseurl}}/images/agent-first-secure-development/Agentic_SSDLC.png){: height="650px" with="650px"}

### What This Process Delivers

While this process may appear complex, the primary goal is simple: derive the security requirements and controls that coding agents must implement. These requirements enable agent-to-agent communication—security agents produce specifications that coding agents consume to generate secure, validated code.

Two principles keep this practical:
1. **Maintain abstraction**: Development teams and agents get clear, actionable guidance without unnecessary detail
2. **Agent quality matters**: Well-calibrated agents deliver correct, accurate output that teams can trust

The security-related tasks (marked yellow in the diagram) can now be operationalized in minutes rather than weeks. Let's see how.


### Phase 1: Design (Steps 1-3)

The design phase establishes what you're building and how it should be architected securely.

#### Step 1: Specification (Human-Driven)

Agent-first development starts with an intent. Software developers express their intent in natural language and derive a high-level specification of what they want to build:

> "I need an API gateway service that handles authentication, rate limiting, and request routing. It should support IAM authentication and integrate with our existing services."

Agents derive specifications that are version-controlled documents describing system goals, user stories, and requirements. *You review it, provide feedback, and the agent refines it*, until your satisfied with the output.

Spec-driven development is the approach followed by [Kiro](https://kiro.dev/), but can naturally be extended to other coding agents or platforms. You can learn more about this approach in [Kiro and the future of AI spec-driven software development](https://kiro.dev/blog/kiro-and-the-future-of-software-development/).


#### Step 2: System Design (Agent-Generated)

Deriving the specification from intent is critical to this approach because it drives all subsequent lifecycle phases.

If you're familiar with Secure SDLC (SSDLC), you know that security architects focus on initial design decisions. Working backwards from the specification to derive the high-level design becomes essential. To extract the design, an agent takes your specification and performs comprehensive discovery to generate an accurate security design. The agent searches through:

- **Existing documentation**: README files, architecture docs, design documents
- **Source code**: Implementation details, security controls, API definitions
- **Infrastructure**: Deployment pipelines, configuration files, infrastructure-as-code
- **Dependencies**: External services, libraries, integrations

From this analysis, the agent generates a comprehensive security design containing:

- Architecture overview with components and trust boundaries
- Data flow diagrams showing how data moves through the system
- External dependencies and integration points
- Security controls mapped to each component
- Development methodology and deployment practices
- Defense-in-depth strategies
- Operational security considerations

Information gathering, and summarization become tasks that can be done in minutes rather than hours. Identifying workflows, data flows, and architectural patterns happens much faster and can be done almost independently without relying on the entire team to answer questions. Again, *you review it, provide feedback, and the agent refines it*. Iteration cycles measured in minutes, not days.

#### Step 3: Security Design Review (Agent-Validated)

The role of the security architecture can now be supported by an agent that performs a comprehensive security design review to identify issues that could impact the service's security posture, for instance:

- **Custom authorization implementations**: Reinventing the wheel instead of using proven authorization frameworks
- **Custom cryptography**: Rolling your own crypto instead of using established libraries
- **Insecure authentication patterns**: Weak or improperly implemented authentication mechanisms
- **Missing encryption**: Unencrypted data in transit or at rest
- **Overly permissive access controls**: Excessive permissions that violate least privilege
- **Compliance issues**: Design choices that violate regulatory requirements or industry standards


The agent doesn't just flag issues—it explains why these design decisions are problematic and suggests secure alternatives with refactoring guidance. *You review it, provide feedback, and the agent refines it*. Problematic design decisions are identified and addressed earlier, before they become embedded in code.

### Phase 2: Security Analysis (Steps 4-6)

This phase transforms your design into concrete security requirements, threat models, and test objectives.

#### Step 4: Security Requirements (Agent-Derived)

The output of this phase is the most critical artifact of the entire process. Deriving correct security requirements for a software system is the primary goal of the Secure SDLC. This shifts security from a reactive to a proactive "security-by-design" foundation. By identifying and defining security needs you can prevent 
vulnerabilities, reduce costs, ensure regulatory compliance, etc.

Security requirements are derived from threat modeling, security standards (e.g., OWASP ASVS, NIST SSDF, ISO 27034), risk policies, and misuse/abuse case analysis. For better traceability and separation of concerns, threat modeling is addressed in the next step. This step focuses on specialized agents that analyze the design and automatically derive security requirements from your security standards and policies.

Every requirement is mapped to standards, policies and security frameworks. *You review it, provide feedback, and the agent refines it*.

#### Step 5: Threat Modeling (Agent-Analyzed)

The agent generates a comprehensive threat model by analyzing your design and requirements. What makes this powerful is the flexibility and continuous nature of the approach:

**Adaptive Methodology**: The agent applies the most appropriate threat modeling methodology for your system—STRIDE for data flow analysis, PASTA for risk-centric assessment, attack trees for detailed threat decomposition, or Systems-Theoretic Process Analysis (STPA) for hazard analysis. It adapts based on your system's characteristics.

**Knowledge-Based Analysis**: The agent leverages existing threat catalogs and historical security issues to identify risks. It learns from previous incidents, known vulnerabilities, and industry-specific threats, bringing institutional knowledge to every analysis.

**Living Document**: Unlike traditional point-in-time assessments, the threat model evolves with your system. After each iteration—new features, architecture changes, dependency updates—the agent re-analyzes and updates the threat model. It becomes a living process, not a static document gathering dust.

The agent's analysis includes:

- Identifies potential threats and threat actors
- Analyzes attack vectors and vulnerabilities
- Assesses likelihood and impact based on historical data
- Maps mitigations to threats
- Tracks threat evolution over time

 *You review it, provide feedback, and the agent refines it*. This happens in minutes and remains continuously up-to-date.

Like the previous step, the primary output of threat modeling is the set of mitigations that must be implemented. Some threats may be risk-accepted through a parallel decision process, but for those requiring mitigation, technical or organizational controls must be established and integrated into your implementation.

#### Step 6: Test Objectives (Agent-Generated)

Validating security controls from the threat model or security requirements is mandatory. Test-driven secure development becomes easy and mostly cheap to implement in agent-first development. The agent can generate comprehensive test objectives for all the security controls that your service must implement.

**Adaptive Abstraction Levels**: Test objectives can range from high-level specifications to very detailed test descriptions, depending on the threat and mitigation's level of abstraction. For architectural threats like "decentralized authorization," you get high-level test objectives about authorization consistency. For specific vulnerabilities like "SQL injection in user input," you get detailed test cases with exact payloads and assertions.

This flexibility ensures test objectives are practical and actionable—abstract enough to guide implementation, concrete enough to validate security controls.

Every test case is linked to threats and requirements. You know exactly what you're testing and why.


### Phase 3: Implementation & Validation (Steps 7-11)

With security requirements and tests defined, you now implement, validate, and deploy your system with continuous security monitoring.

#### Step 7: Implementation (Specification-Driven Development)

Now you implement the actual system, but you're not starting from scratch. You have comprehensive specifications that drive your implementation:

**Security Requirements as Implementation Guide**: The security requirements from Step 4 tell you exactly what security controls to implement. Need authentication? REQ-AUTH-001 specifies IAM authentication with specific configuration. Need encryption? REQ-DATA-001 defines encryption standards and key management.

**Threat Model as Design Constraint**: The threat model from Step 5 informs your architectural decisions. You know the threats, attack vectors, and required mitigations. You implement defense-in-depth knowing exactly what you're defending against.

**Test Objectives as Acceptance Criteria**: The test objectives from Step 6 define what "done" means. Your implementation must pass these tests. This is test-driven security development—tests exist before implementation, guiding what you build.

**Agent-Assisted Coding**: As you write code, agents provide real-time guidance:
- Suggest secure coding patterns based on requirements
- Warn about potential vulnerabilities
- Validate against security standards
- Ensure alignment with threat mitigations
- Check test coverage continuously

You're not just coding—you're implementing a security specification with continuous validation. Every line of code maps back to requirements, threats, and tests. *You review it, provide feedback, and the agent refines it*. 

#### Step 8: Secure Code Review (Agent-Validated)

As you implement security controls, the agent continuously reviews your code:

- Validates against secure coding guidelines
- Detects vulnerabilities and anti-patterns
- Ensures compliance with security standards
- Verifies test coverage
- Provides remediation guidance

Ideally, these agents should leverage static analysis tools (like traditional [SASTs](https://en.wikipedia.org/wiki/Static_application_security_testing) or [SCA](https://en.wikipedia.org/wiki/Software_composition_analysis)) to assist in the review of the code. The code review agents' responsibility is then triaging findings and providing actionable outcomes that coding agents can use to refine and fix the code. This provides real-time security feedback during development.


#### Step 9: Security Testing (Agent-Assisted)

Once code artifacts are built and deployed in test environments, security testing agents—such as penetration testing agents—can validate security vulnerabilities through tailored multi-step attack scenarios.

These agents generate reproducible attack paths and ready-to-implement code fixes, accelerating penetration testing from weeks to hours and enabling comprehensive testing across all applications, not just critical ones.

Note that this task is typically conducted by highly specialized security engineers and usually falls outside the scope of software engineering teams. Therefore, while agent-assisted, this step intentionally requires more frequent human involvement and recurring reviews.

The level of human oversight depends on confidence in the pentesting agents, the size of the feature or change being deployed, and other factors. Steps 1 through 8 intentionally prepare the software to minimize security issues found at this phase. AI agents excel at pattern matching and inference based on known data, working particularly well with limited scope and specialized knowledge. 
But eliminating human-assisted pentesting entirely is neither desirable nor necessary. However, agents can significantly accelerate this task, and it may be skipped for certain change types where automated test coverage or monitoring are exceptionally mature.

Determining where and when human-assisted security testing is required is a parallel task that must be addressed by the organization and security team. 

### Phase 4: Deployment, Release and Continuous Monitoring

The last phase covers:

- **Deployment & Release**: Agents provide continuous real time visibility, by monitoring:
  - Security configurations
  - Pipeline stages, jobs, regressions, and deployments
  - Logs and trace analysis
  - Resource utilization 
  - Security alerts 
  - Automated security remediation & rollbacks
- **Security Monitoring & Operations**: Agent-assisted continuous monitoring with proactive threat detection and remediation

This continuous feedback loop ensures security doesn't end at deployment—it evolves with your system in production.

--- 

## Real-World Impact

The real transformation isn't just about speed—it's about enabling short feedback loops and fast iterations. Requirements and threat models become living documents that constantly evolve as your system gets updated, rather than static artifacts that become outdated the moment they're written.

Critically, all these artifacts live next to your code—they're version controlled in the same repository. When you update code, the corresponding security documentation updates alongside it. When you review a pull request, you see both code changes and their security implications. Everything stays synchronized because everything lives together. In practice, this introduces a **Continuous Security Iteration Process**:
- Code changes trigger automatic threat model updates (minutes)
- Requirements stay synchronized with implementation (real-time)
- Test coverage adapts to new code paths (continuous)
- Security feedback during development (immediate)

The overall impact is that implementation of secure software goes from weeks to hours. The key difference: you're not just faster at creating artifacts once—you can iterate on code, requirements, and threat models continuously without the traditional multi-day lag between changes and security validation. Security documentation stays current with your codebase instead of becoming obsolete.

### What This Means for Different Roles

**For Developers**: Security becomes an enabler, not a blocker. You get immediate feedback, comprehensive test coverage, and clear requirements. You can iterate rapidly without waiting for security reviews.

**For Security Engineers**: You focus on high-value decisions—architecture reviews, risk assessments, critical security choices—while agents handle comprehensive analysis and documentation. Your expertise is amplified, not replaced.

**For Engineering Managers**: Faster delivery without compromising security. Better traceability and compliance. Reduced security debt. Happier teams.


### The Human-Agent Partnership

Let me emphasize this again: agents don't replace us, they augment us.

**What Agents Do Well**:
- Comprehensive analysis (easier to process large sets of data)
- Rapid artifact generation (minutes vs. days)
- Consistent application of standards (no human error)
- Maintaining traceability (automatic linking)
- Routine validation (continuous checking)

**What Humans Do Well**:
- Strategic security decisions
- Risk assessment and prioritization
- Creative threat modeling
- Architectural security design
- Final approval and accountability

The key is leveraging each for what they do best. Agents handle the comprehensive, repetitive work. Humans focus on judgment, creativity, and critical decisions.

**Human Validation is Non-Negotiable**: While agents accelerate the process, humans must validate every step. Every line of code, every security requirement, every threat model entry submitted to the repository should be reviewed by a human. Someone must be accountable for what goes into production. Agents are powerful assistants, but they don't replace human responsibility and oversight. The speed gains come from agents doing the heavy lifting, not from skipping validation.

---
---

## Getting Started

If you're interested in trying the agent-first secure SDLC approach, here's how to create a proof-of-concept using [Kiro](https://kiro.dev/). This approach extends to any agentic development environment.

> Kiro is an agentic, AI-native IDE built by AWS to accelerate software development from prototype to production.

### 1. Set Up Security Skills

Create workspace-scoped skills in `.kiro/skills/` for each security phase. Skills activate automatically when you make relevant requests:

```
.kiro/skills/
├── security-design-review/
│   ├── SKILL.md
│   └── references/
│       └── anti-patterns.md
├── security-requirements/
│   ├── SKILL.md
│   └── references/
│       ├── owasp-asvs.md
│       └── nist-ssdf.md
├── threat-modeling/
│   ├── SKILL.md
│   └── references/
│       ├── stride.md
│       └── pasta.md
└── test-objectives/
    └── SKILL.md
```

**Example skill** (`.kiro/skills/threat-modeling/SKILL.md`):

```markdown
---
name: threat-modeling
description: Generate threat models using STRIDE or PASTA. Use when analyzing security threats, identifying attack vectors, or assessing system risks.
---

## Threat Modeling Process

1. Identify assets and trust boundaries from the design
2. Apply appropriate methodology (STRIDE for data flows, PASTA for risk-centric)
3. Enumerate threats for each component
4. Map mitigations to threats with traceability
5. Output structured threat model in `docs/security/threat-model.md`

For detailed methodology guidance, see `references/stride.md`,`references/pasta.md`.
```

Skills and agents live in version control alongside your code, ensuring the entire team uses consistent security workflows.

> Partner with your security team to calibrate agents and validate outputs against your organization's security standards and policies.

### 2. Configure Custom Security Agents

For specialized security tasks, create custom agents with pre-approved tools and security-specific context:

```json
{
  "name": "security-architect",
  "description": "Security design review and architecture analysis",
  "resources": [
    "skill://.kiro/skills/security-design-review/SKILL.md",
    "skill://.kiro/skills/security-standards/SKILL.md"
  ],
  "allowedTools": ["fs_read", "fs_write", "grep", "code"],
  "autoApprove": ["fs_read", "grep"],
  "prompt": "You are a helpful security architect responsible for performing security design reviews.",
  "model": "claude-sonnet-4"
}
```

This eliminates permission prompts during security reviews while maintaining control over write operations.

### 3. Pick a Small Project

Start with a new feature or service. Don't try to transform everything at once.

### 4. Create a Structured Specification

Use Kiro's Specs to formalize your feature:
- **Requirements**: User stories and acceptance criteria
- **Design**: Architecture, data flows, and integration points
- **Tasks**: Discrete implementation steps

This structured approach captures security considerations from the start.

### 5. Generate Security Artifacts

With skills and specialized agents configured, simply ask Kiro:

```
> Review this design for security anti-patterns
[skill: security-design-review activated]

> Generate security requirements based on OWASP ASVS
[skill: security-requirements activated]

> Create a threat model using STRIDE
[skill: threat-modeling activated]

> Generate test objectives for these security controls
[skill: test-objectives activated]
```

Each skill produces version-controlled artifacts in your repository. For complex workflows, use [sub-agents](https://kiro.dev/docs/cli/chat/subagents/) to orchestrate multiple security phases in parallel.

**Alternative**: Instead of custom Kiro agents, you can use existing specialized agents like [AWS Security Agent](https://aws.amazon.com/security-agent/) for design review, code review, and pentesting, or [Claude Code Security](https://www.anthropic.com/news/claude-code-security) for secure code reviews. Non-commercial options are also available as open source projects. 

### 6. Integrate Security Controls

Incorporate security requirements and test objectives into your Kiro Spec:
- Update design to address identified issues
- Add security controls to implementation tasks
- Include test objectives as acceptance criteria

Or reference them in steering files for automatic context loading during development.

### 7. Implement with Continuous Validation

Use Kiro's coding agents to implement tasks. Enable your preferred `secure-code-review` agent or skill using [Kiro hooks](https://kiro.dev/docs/cli/hooks/), to automatically scan code changes and provide real-time security feedback.


Review, validate, and refine before committing to the repository.

### 8. Measure Impact

Track:
- Time saved (hours vs. weeks)
- Security quality (vulnerabilities found/prevented)
- Team satisfaction (developer experience)
- Artifact freshness (how often security docs stay current)


> This proof-of-concept shows how agent-first secure development works in practice. Real organizations will centralize security agents, skills, knowledge bases, and standards under dedicated teams who build specialized tooling and validation workflows. Centralized observability with comprehensive logging and metrics become essential. But here's what shouldn't change: the developer experience. Security engineers should avoid creating processes that disrupt how builders naturally work. We need to make implementing and testing security controls easier, not add friction.   

---
---

## Conclusions

Agent-first development represents a fundamental shift in how software gets built. Agent-first *secure* development adapts security practices to this reality. Agents handle comprehensive analysis and artifact generation. Humans focus on strategic decisions, risk assessment, and validation. The result is faster delivery, better security, and teams that can actually keep pace with modern development velocity.

This post doesn't introduce any novel security process or mechanism. Instead, it provides a structured approach to how we can operationalize secure development today and how we can better scale our capabilities. It's about giving security engineers and software developers tools that match the speed of agent-first development. 

Start small, iterate rapidly, and see for yourself how agents can transform secure development. I hope this inspires you to try it.

*Have you tried using AI agents for security workflows? What challenges are you facing with traditional secure development? I'd love to hear your thoughts and experiences.*

***
*** 

## Small Reflection

Writing this post feels strange. A year ago, I would have dismissed agent-first security as hype. I had reservations (still do), about delegating security tasks to AI. But the reality is more nuanced. Agents don't make security decisions; they accelerate the grunt work so humans can focus on decisions that actually matter. The question is no longer whether to adopt agent-first approaches, but how to implement them responsibly and effectively. 

---

## Acknowledgements 
