---
layout: post
title: "Scaling Up Automations with Agents: Lessons Learned"
author: Bárbara Vieira
---

> **Disclaimer:** I'm a security engineer, not a software developer. I have little to no background in data science, but I'm using AI as a tool to solve security problems. While all examples here use AWS infrastructure, these principles apply to other platforms too.

## Introduction 

During the past months, I've focused on automating security test coverage at scale. While this is an extremely hard problem to automate, given it's importance, I decided it was worth trying.

One of the tools I used was *AI Agents*. I started with proof-of-concepts using general-purpose AI CLIs like Kiro-CLI and Cline, then moved to developing my own agents. While I hit many walls, I learned a lot from this experience.

Through this journey, I identified six fundamental principles about developing automations with AI agents:

1. **Break down the problem into small steps**
2. **Be frugal when using agents**
3. **Less is more (with tools)**
4. **Smaller models don't mean worse results**
5. **Break down the inputs**
6. **Structure your outputs**

This blog post shares some of the lessons I have learned. The examples presented here are written in Python and rely on the Strands Agents framework. 

## Background
This section introduces some basic terminology and frameworks used throughout the post. If you're familiar with them, feel free to jump to the next section.

### Strands Agents 

I started agent development using [Strands Agents](https://strandsagents.com), AWS's recently launched agent development framework. I was astonished by its potential - it's incredibly easy to create an agent with your model provider of choice. For obvious reasons, I chose Bedrock, but Strands supports many other providers. The configuration is extremely straightforward.

Strands makes agent development easier, provides the right level of abstraction, and fully matches fundamental software development principles: modularity, abstraction, encapsulation, and separation of concerns. Writing secure and maintainable code with Strands is incredibly easy, though it all depends on your architecture and hygiene.


### Model Context Protocol (MCP)

[Model Context Protocol (MCP)](https://en.wikipedia.org/wiki/Model_Context_Protocol) is an open standard for connecting large language models (LLMs) to external data and tools, enabling AI agents to access real-time info and perform actions. It was introduced at the end 2024 by Anthropic, with an exponential growth in implementations throughout 2025 and beyond. Tool-based integration is the standard approach for connecting MCP servers to agents.


### Tools 

Tools are the primary mechanism for extending agent capabilities beyond text generation. They enable agents to interact with external systems, access real-time data, execute code, manipulate files, and perform actions in their environment. Tools enable agents to perform powerful automations, with complex workflows, API integrations, and multi-step problem solving. Strands Agents natively supports off-the-shelve [tools](https://strandsagents.com/latest/documentation/docs/user-guide/concepts/tools/), as well as, the capability of creating custom ones.


## Principles for Scaling Up Automation with Agents 

**Core Principle**: Deploy agents strategically to automate known solutions, not to discover them.

Agents are powerful for tackling novel problems when equipped with the right tools and context. However, for automation purposes, they should systematize processes you've already mastered. This is the foundation of any successful AI automation initiative.

While AI excels at research and exploration, automation demands reliability and consistency. To scale manual processes with predictable outcomes, AI is simply a tool, not the solution itself. Thus, if a task requires complete predictability, agents may not be the right choice.

**When are agents needed?** LLMs naturally excel at text synthesis, translation, and pattern recognition. Agents become valuable when you need to:
- Process unstructured inputs
- Handle ambiguous or fuzzy requirements  
- Accept some variability while maintaining consistent overall results

By using agents strategically, you can scale your capacity and enhance your work. The key is following a structured approach to problem-solving with agents.


### 1. Break Down the Problem into Small Steps

Always start by breaking down your problem into small tasks. Understand how a human would address the problem and define all required steps. To better understand this principle let's consider an example.

**Example: Extracting Tests from Source Code**

Say you want to automate the extraction of all source code tests from your repository and identify what each test does. Breaking this into small steps:

1. **List all files** in the source code repository
2. **Scan each file** and identify lines containing the word `test`
3. **Validate** that identified lines are actually test methods/functions
4. **Analyze each test** method/function to identify what it's testing

From these tasks, only task 4. handles a fuzzy input and can accept some variability in the outputs. All the other tasks can be implemented using known and fully deterministic mechanisms. Thus,

- **Tasks 1-3:** can be implemented using CLI commands (`ls`, `grep`)
- **Task 4:** can be implemented using an agent that analyzes the test and identifies its purpose

The following code snippet, encodes these steps in Python (for Java repositories) using Strands and Bedrock with Amazon Nova Lite:

```python
def main(repo_path):
    # Initialize Bedrock model with Nova Lite
    bedrock_model = BedrockModel(
        model_id="amazon.nova-lite-v1:0",
        region_name="us-east-1"
    )
    
    # Initialize Strands agent
    agent = Agent(model=bedrock_model)
    
    print(f"Analyzing repository: {repo_path}")
    
    # Step 1: List files
    files = list_files(repo_path)
    print(f"Found {len(files)} Java files")
    
    all_tests = []
    
    for file_path in files:
        if not os.path.exists(file_path):
            continue
            
        # Step 2: Find test lines
        test_lines = find_test_lines(file_path)
        
        if not test_lines or test_lines == ['']:
            continue
            
        # Step 3: Extract test methods
        test_methods = extract_test_methods(test_lines)
        
        if not test_methods:
            continue
            
        print(f"\nProcessing {file_path} - found {len(test_methods)} test methods")
        
        # Step 4: Analyze each test with AGENT
        for method_line in test_methods[:3]:  # Limit to first 3 for demo
            purpose = analyze_test_with_agent(method_line, file_path, agent)
            all_tests.append({
                'file': file_path,
                'method': method_line,
                'purpose': purpose
            })
```

It will become more evident in the remaining lessons of this post why this pattern is so important. For now, just keep in mind that breaking down the problems into small steps improves reliability and consistency of the outputs, and provides better modularity and separation of concerns of the implementations.


> The full implementation and all other examples presented throughout this post can be found in this [Github repository](https://github.com/barbaraisabelvieira/scaling-up-automations-with-agents-examples). Please note these are just simple illustrative examples and are not meant for production use.

### 2. Be Frugal When Using Agents

While you can use agents for any task, doesn't mean you should. For task automation, **always prefer deterministic mechanisms over agent-based ones.**

Use agents only when there's no deterministic alternative. This is primarily due to:

- **Reliability:** Agents introduce non-determinism in outputs
- **Cost:** AI inference is expensive and often unnecessary

For example, I could have used an agent for all tasks in the previous example (see the prompt below). But I would immediately face issues like token limitations, context overflow and poor control over LLM workflow execution.


```markdown

# Test Extraction Automation Prompt

You are a test extraction agent that automates the discovery and analysis of all tests in a source code repository. Follow these 4 steps exactly:

## Step 1: List Repository Files
Use `find` command to locate all source files:
` bash
find /path/to/repo -type f -name "*.java" -o -name "*.py" -o -name "*.js" -o -name "*.ts"
`

## Step 2: Find Test-Related Lines  
Use `grep` to identify lines containing test indicators:
` bash
grep -n -i "test\|@Test\|it(\|describe(" file_path
`

## Step 3: Extract Test Methods
Use regex patterns to extract actual test methods/functions:
- Java: `(public|private|protected).*test\w+\s*\(|@Test`
- Python: `def test_\w+\(|@pytest`
- JavaScript/TypeScript: `it\(|test\(|describe\(`

## Step 4: Analyze Test Purpose (AI Analysis)
For each extracted test method, analyze the code and provide a concise description in format:
"Tests [specific functionality]"

## Output Format:

Repository: /path/to/repo
Total Files: X
Total Tests Found: Y

=== TEST ANALYSIS ===
File: path/to/test/file
Method: test_method_name
Purpose: Tests [specific functionality]
---

## Instructions:
1. Execute steps 1-3 using CLI commands
2. For step 4, read the test method code and provide intelligent analysis
3. Process maximum 3 test methods per file for efficiency
4. Focus on meaningful test descriptions, not generic ones

Execute this process for the repository path provided by the user.
```

This prompt encodes all the steps described in a **Lesson 1** in a single prompt. Let's call this approach of solving the test extraction problem, *Agent-only prompt*.


What happens when we execute the script of **Lesson 1** and this *Agent-Only prompt* in the same source code repository? Let's look into the outputs:

**Structured Script (Lesson 1):**
- Repository: 122 Java files
- Tests identified: 419 individual test methods

**Agent-Only Prompt Approach:**
- Repository: 122 Java files  
- Tests identified: 213 individual test methods

Essentially, the agent-only approach missed nearly half the tests due to context management problems. 

**Why does this happen?** While the structured script only uses agents to infer what the test is doing, the example shown in this lesson uses an agent to perform all 4 steps of the analysis. 

More specifically, the following code snippet (**Lesson 1**) shows that the agent is iteratively called for each test method to analyze what is being tested. While in the Agent-only prompt, the agent will perform an autonomous decision on the tests to extract based of the resources available.

```python
 # Step 4: Analyze each test with AGENT
for method_line in test_methods[:3]:  # Limit to first 3 for demo
    purpose = analyze_test_with_agent(method_line, file_path, agent)
    all_tests.append({
        'file': file_path,
        'method': method_line,
        'purpose': purpose
    })
```

This demonstrates why breaking down the problem into small steps (**Lesson 1**) and using agents only where they are required (**Lesson 2**) is preferable when automating tasks.

### 3. With Tools, Less is More

Agents use tools to connect with external resources and perform actions. These tools give agents incredible powers, but empowering an agent to do anything is dangerous and unreliable. 

While modern agent frameworks include safety mechanisms, tool validation and execution controls, agents outputs are still non-deterministic. Thus, enabling too many tools may lead to inconsistencies and issues during task automation.

**Example: Implementing custom tools**

As an example, consider the prompt from **Lesson 2** (Agent-only prompt). Let's create a Strands Agent for this prompt that enables the following tools: 

- `file_read`: enables the agent to read the repository files
- `file_write`: enables the agent to write the outputs to a file
- `shell`: used by the agent to execute all shell commands

```python
def create_test_extraction_agent():
    """Create a Strands agent with Nova Lite model and file tools"""
    
    # Initialize Bedrock model with Nova Lite
    bedrock_model = BedrockModel(
        model_id="amazon.nova-lite-v1:0",
        region_name="us-east-1"
    )
    
    # Create agent with file tools
    agent = Agent(
        model=bedrock_model,
        tools=[shell, file_read, file_write],
        system_prompt=        
"""# Test Extraction Automation Prompt

You are a test extraction agent that automates the discovery and analysis of all tests in a source code repository. Follow these 4 steps exactly:

## Step 1: List Repository Files
Use `find` command to locate all source files:
` bash
find /path/to/repo -type f -name "*.java" -o -name "*.py" -o -name "*.js" -o -name "*.ts"
`

## Step 2: Find Test-Related Lines  
Use `grep` to identify lines containing test indicators:
` bash
grep -n -i "test\|@Test\|it(\|describe(" file_path
`

## Step 3: Extract Test Methods
Use regex patterns to extract actual test methods/functions:
- Java: `(public|private|protected).*test\w+\s*\(|@Test`
- Python: `def test_\w+\(|@pytest`
- JavaScript/TypeScript: `it\(|test\(|describe\(`

## Step 4: Analyze Test Purpose (AI Analysis)
For each extracted test method, analyze the code and provide a concise description in format:
"Tests [specific functionality]"

## Output Format:

Repository: /path/to/repo
Total Files: X
Total Tests Found: Y

=== TEST ANALYSIS ===
File: path/to/test/file
Method: test_method_name
Purpose: Tests [specific functionality]
---

## Instructions:
1. Execute steps 1-3 using CLI commands
2. For step 4, read the test method code and provide intelligent analysis
3. Process maximum 3 test methods per file for efficiency
4. Focus on meaningful test descriptions, not generic ones

You have access to shell, file_read, and file_write tools to complete this task."""
    )
    
    return agent
```

While enabling the `shell` tool appears straightforward, it introduces significant security risks by allowing the agent to perform unauthorized actions like deleting files. Instead we can create specific, constrained tools that restrict what an agent can do during runtime.

```python
@tool
def find_files(path: str, name_pattern: str = "*.java") -> str:
    """Find files in a directory matching a pattern.
    
    Args:
        path: Directory path to search in
        name_pattern: File pattern to match (e.g., "*.java", "*.py")
    
    Returns:
        Newline-separated list of file paths
    """
    try:
        # Validate and sanitize path
        safe_path = os.path.abspath(path)
        if not os.path.exists(safe_path) or not os.path.isdir(safe_path):
            return "Error: Invalid or non-existent directory"
        
        # Allowed patterns
        allowed_patterns = ["*.java", "*.py", "*.js", "*.ts", "*.jsx", "*.tsx"]
        if name_pattern not in allowed_patterns:
            return f"Error: Pattern '{name_pattern}' not allowed"
        
        # Use safe subprocess execution
        result = subprocess.run(
            ['find', shlex.quote(safe_path), '-type', 'f', '-name', shlex.quote(name_pattern)],
            capture_output=True, text=True, check=True, shell=False
        )
        return result.stdout.strip()
    except subprocess.CalledProcessError as e:
        return f"Error: {e.stderr}"
    except Exception as e:
        return f"Error: {str(e)}"
```

`find_files` and `grep_pattern` are custom tools that work as a wrapper around the `find` and `grep` command respectively. The agent initialization code now uses these tools instead of the `shell` tool.

```python
    # Create agent with custom and file tools
    agent = Agent(
        model=bedrock_model,
        tools=[find_files, grep_pattern, file_read, file_write],
        system_prompt="""You are a test extraction agent that automates the discovery and analysis of all tests in a source code repository. Follow these 4 steps exactly:
    """
```

This approach is more secure and delivers better and more consistent results. 

>  This pattern also improves modularity and long-term maintainability of the code. By encapsulating shell commands, MCP tools, and other operations within custom tools, you can easily swap out underlying implementations or MCP servers as needed.

### 4. Smaller Models, Doesn't Mean Worse Outputs

This is the era of **Large** Language Models, but when it comes to automation, bigger isn't always better. Everything depends on the task at hand.

Models with smaller number of parameters (or smaller models in short), often perform much better with focused tasks. For small tasks like inferring what code does, larger models tend to:

- Ramble unnecessarily
- Burn more computational cycles
- Provide richer-looking but not necessarily better output

While larger models often provide better reasoning and more nuanced understanding, the cost-benefit of using larger models for simple tasks usually isn't worth it. This is also highlighted in this recent [study](https://arxiv.org/pdf/2512.15943) where the authors conclude that *Small Language Models, when trained with targeted strategies, can achieve better performance when compared to larger counter-parts.*

**Example: Nova Lite vs Nova Pro** 

I extended the script from **Lesson 1** to compare execution times between Amazon Nova Lite (smaller model) and Amazon Nova Pro (model with a larger number of parameters). The script is available in the same [GitHub repository](https://github.com/barbaraisabelvieira/scaling-up-automations-with-agents-examples).

The results were the following ones:
```markdown
===============================
MODEL COMPARISON SUMMARY
===============================
Nova Lite - Total time: 10.86s 
Nova Pro  - Total time: 19.30s
Speed difference: 8.44s
🏆 Nova Lite was faster
```
Nova Lite completes the task in roughly half the time while producing comparable output quality to Nova Pro.

> Note that, I'm not dismissing larger models. Larger models are extremely useful. However, today, smaller models tend to be more effective for small, targeted tasks.

### 5. Break Down the Inputs

After 50+ messages, agents don't "recall" early conversation details. If you ask an agent to process large inputs at once, it will "prioritize" - and this priority can be random, leading to inconsistent results. So break down not just the steps (**Lesson 1**), but also the inputs. Managing the context window is fundamental, since agents' context is finite.

Also,  LLMs have token limits, so iterating over smaller inputs yields more reliable outcomes. This is true even in modern agent frameworks that include memory systems, summarization, and context compression.


To better demonstrate this principle, let's consider the comparison done in **Lesson 2**: structured script (**Lesson 1**) vs the agent-only prompt (**Lesson 2**).

- **Structured script (Lesson 1):** In the example of Lesson 1, the script deterministically identifies all test files and lines of code first, and then agent iterates over each individual test methods.

```python
 # Step 4: Analyze each test with AGENT
for method_line in test_methods[:3]:  # Limit to first 3 for demo
    purpose = analyze_test_with_agent(method_line, file_path, agent)
    all_tests.append({
        'file': file_path,
        'method': method_line,
        'purpose': purpose
    })
```
This guarantees extraction of all tests and their purposes.

- **Agent-only prompt (Lesson 2):** In example from Lesson 2, a single prompt encodes all the analysis steps. The agent *decides* how to extract and iterate through the test methods.

```markdown
# Test Extraction Automation Prompt

... 

## Instructions:
1. Execute steps 1-3 using CLI commands
2. For step 4, read the test method code and provide intelligent analysis
3. Process maximum 3 test methods per file for efficiency
4. Focus on meaningful test descriptions, not generic ones

```

The results, (as already shown in **Lesson 2**) differ significantly: 419 tests extracted in Lesson 1, against 213 tests in Lesson 2.  

The structured script identifies all tests systematically, while the agent-only approach lets the agent choose which tests to extract. This gap occurs because in the agent-only approach, the agent manages too much context, leading to inconsistent outputs.

So breaking down agent analysis across different inputs matters. It provides better output control and introduces consistency in automation workflows.

### 6. Structure Your Outputs

Structured outputs are a blessing. Any structured output can be validated, which enables better consistency. As a security engineer, I can't emphasize enough the importance of validating LLM-based outputs.

The unpredictability of the agents' output isn't good for task automation. While these don't eliminate non-determinism entirely, structured outputs help make outputs more deterministic and predictable. Strands offers structure outputs out-of-the-box through Pydantic models.

**Example: Structured Outputs**

This example shows the use of Strands structured outputs in more detail. The following code snippet, shows how the Pydantic model can be used when calling a Strands Agent to generate a structure output.

```python
# Use agent to analyze test purpose with structured output
    prompt = f"""
    Analyze this Java test method and return structured information about it:
    
    Method name: {method_name}
    Code:
    {method_body}
    
    Provide a concise description of what this test method tests.
    """
    
    class TestPurpose(BaseModel):
        purpose: str = Field(description="What the test method tests, starting with 'Tests'")
    
    try:
        response = agent.structured_output(TestPurpose, prompt)
        purpose = response.purpose
    except Exception as e:
        purpose = f"Analysis failed: {str(e)}"
```

Because the output is now an object `TestPurpose`, it's easier to validate its structure and persist data for further analysis.
This pattern is especially valuable for automations that depend on agent workflows. Validating individual agent structured outputs during execution prevents unexpected workflow failures.

## Conclusions

After months of working with AI agents for security automation, these six lessons have proven invaluable. To recap:

1. **Break problems into small, manageable steps** - Start by breaking down your problem into small tasks
2. **Be frugal with agents** - Most tasks don't need AI
3. **Limit agent tools** - Fewer, focused tools yield better results
4. **Choose appropriate model sizes** - Models with small number of parameters often work better for focused tasks
5. **Manage input size** - Break large inputs into smaller chunks and iterate over them
6. **Structure the outputs** - Validation and consistency are crucial for reliable results

The key insight? **Agents should automate processes you already understand, not figure out the automation process for you.** They're powerful tools for scaling manual work, but they're not magic problem-solvers. This might change in the future as modern agents (2026) show significantly better consistency than earlier versions. But predicting the future development of agents and similar systems remains uncertain.

Nevertheless, today, if you're exploring AI automation in your domain, start small, be methodical, and remember: **the goal isn't to use AI everywhere, but to use it effectively where it adds real value.**


---


If you're working on similar challenges or have questions about agent development, I'd love to hear about your experiences.

**Resources:**
- [Strands Agents Documentation](https://strandsagents.com/)
- [Amazon Bedrock](https://aws.amazon.com/bedrock/)
- [Kiro CLI](https://kiro.dev/cli/)

---

### Acknowledgements
I'd like to thank my colleagues Yakov Shafranovich and Max Rattray for the fruitful feedback during the review of this post.