# We're Using Essay Writers to Solve Multiple-Choice Problems

## Why System 1 AI Could Dramatically Reduce the Cost, Latency, and Environmental Impact of Intelligent Applications

By Sunvir Gujral 
October 14, 2026

Think back to high school.

If your teacher offered a choice between an essay exam and a multiple-choice test, which one would you pick? For most of us, the answer was obvious. Multiple-choice tests felt easier. They were faster, less mentally draining, and required less work to complete. Essay questions were different. To answer them, you had to organize your thoughts, build a narrative, and carefully construct your response one word at a time.

In many ways, today's AI systems face the same dilemma. We're increasingly asking AI to solve what are fundamentally multiple-choice problems, but we're doing it with models designed to write essays.

## The Essay Writer Era of AI

Modern Large Language Models (LLMs) are extraordinary at generating human language. They all work in roughly the same way: they predict the next token, then the next token after that, and then the next token after that until the answer is complete. This architecture is incredibly powerful because it allows the model to generate natural language, write code, summarize documents, and carry on conversations.

The tradeoff is that every token generated requires additional computation. A 100-token response requires roughly 100 sequential prediction steps. A 500-token response requires roughly 500. The model repeatedly performs a large amount of computation simply to keep producing the next token. That's perfectly reasonable when the goal is communicating with a human. But what if the goal isn't to communicate with a human?

## Most AI Applications Don’t Write Essays

When you look at how AI is being used in production today, a pattern emerges. Many of the most valuable AI workloads are not asking the model to create content. They're asking it to make decisions.

Examples include:

- Voice assistants
- Email classification
- Support ticket routing
- Intent detection
- Function calling
- Workflow automation
- Security event classification
- Customer-service routing
- Document categorization

In each of these cases, the AI only needs to answer questions such as:

- Which department should receive this ticket?
- Which action does the user want?
- Which function should be called?
- Which workflow should run next?
- How urgent is this request?

These are decision problems. Yet we often solve them by having a model generate text.

## The Hidden Cost of Human-Readable Output

Many modern AI applications ultimately require structured outputs such as JSON, function calls, database queries, workflow actions, or other machine-readable formats. The challenge is that Large Language Models are designed to generate human language. Left to their own devices, they naturally produce responses that resemble something a person would write or say. Because of this, practitioners spend significant time and effort either parsing the LLM's human-like output into formats that traditional software can consume, or coerce the model to emit JSON, XML, SQL, function calls, or other structured representations.

Consider a simple voice-assistant request:

> It’s getting chilly, turn up the heat.

The application doesn't actually need a paragraph explaining the user's intent. It doesn't even need a sentence. What it needs is something closer to:

```text
Tool: cabin.hvac.station.temperature
Direction: Up
Amount: Small
Zone: Driver
```

For an autoregressive model to produce this result one token at a time, it must be carefully prompted, trained, or fine-tuned to generate the required format directly. Even then, the output is not guaranteed to be correctly formatted or accurate. The AI harness or agent must therefore perform additional validation and apply guardrails before passing the output to the system that executes the requested task. This added processing increases system complexity, latency, and cost.

## Essay Writer vs. Multiple-Choice Test Taker

| Capability | Traditional LLM (System 2 / Autoregressive) | System 1 Decision Model |
|:---: |:---: |:---: |
| Understand user intent | YES | YES |
| Process natural language | YES | YES |
| Generate natural-language output | YES | NO |
| Autoregressive output | YES | NO |
| Produce machine-readable decisions directly | Indirectly, or after fine-tuning | YES |
| Parse generated output | Often required | NO |
| Latency | Higher | Lower |
| Energy consumption | Higher | Lower |
| Infrastructure cost | Higher | Lower |
| Best use case | Human communication | Machine decisions |

An LLM is like a student writing a five-paragraph essay explaining why the answer is **C**. A System 1 model simply selects **C**.

## Enter System 1 Models

Borrowing terminology from Daniel Kahneman's *Thinking, Fast and Slow*, we can think of these systems as System 1 AI. Instead of generating an answer one token at a time, a System 1 model is trained to understand the input and directly select the correct outcome from a predefined set of possibilities.

Examples include:

| User Request | Desired Outcome |
|:---: |:---: |
| *"Turn up the heat"* | Increase cabin temperature |
| *"Warm my seat"* | Increase seat heating |
| *"Route this support ticket"* | Billing department |
| *"Reschedule next week's meeting"* | Calendar action |
| *"What's the sentiment?"* | Positive / Neutral / Negative |
| *“What will the weather be like tomorrow"* | Call weather API |

The output is a decision, not a paragraph. And since the model isn't generating prose, each decision can be reached in a single forward pass through the model.

## A Real-World Voice Assistant Example

I've been working on a voice-assistant architecture that uses System 1 models to make a sequence of decisions rather than generating free-form text.

Consider the request:

> Turn up the heat.

The system doesn't generate an explanation of what it thinks the user meant. Instead, it makes a series of decisions:

| Step | Decision |
|:---: |:---: |
| 1 | `Domain = Vehicle Control` |
| 2 | `Intent = Climate` |
| 3 | `Tool = Temperature Adjustment` |
| 4 | `Parameter Extraction = Driver Zone` |
| 5 | `Relative Change = Increase Small Amount` |

Once these decisions are complete, traditional software executes the action.

When each decision is made sequentially, the entire workflow completes in approximately **390–430 ms** on an Apple M5 GPU, with each decision taking about 75–88 ms. Parallelizing independent decisions, making them speculatively, and batching model operations could reduce total latency further, potentially to 100–150 ms.

A comparable task using a System 2 LLM with 8 billion parameters could take 2–3 seconds on similar hardware when generating about 50 tokens per second. It will also need as much as 16GB of RAM just to fit the model (fp16) compared to under 1GB for a very capable System 1 model.

The key advantage is not just lower latency. These workflows require human-language understanding at the input but machine-readable output. The model’s role is to understand the request and make decisions. LLMs, by contrast, spend compute not only interpreting the request but also generating a human-readable response that must then be converted back into a machine-readable format. System 1 models understand the language and proceed directly to the decision.

In many real-world applications, understanding provides the value; generation adds overhead. Eliminating unnecessary generation can reduce the latency, cost, and energy required to build intelligent systems.

### Measured Performance

Here are several examples from a production-oriented System 1 voice-assistant architecture:

| Request | S1 Calls | M5 GPU Time |
|:---: |:---: |:---: |
| *Turn up the heat* | 5 | 390-430 ms |
| *Warm my seat* | 6 | 460-500 ms |
| *Navigate home and text Sam I'm running late* | 7 | ~470 ms |
| *What is my front-left tire pressure* | 5 | 360-400 ms |
| *What is the wheelbase for my car* | 4 | ~290 ms |
| *Find me a table near the airport\** | 3-4 | 180-215 ms |
| *If it's raining, close the windows* | 11 | 790-870 ms |

\*time to agentic handoff

## Why This Changes the Economics

The key insight is that understanding and generation are different workloads. Many applications need understanding. Generation is comparatively expensive.

| Question | LLM Approach | System 1 Approach |
|:---: |:---: |:---: |
| Understand language? | Yes | Yes |
| Understand intent? | Yes | Yes |
| Perform repeated decoding steps? | Yes | No |
| Cost grows with output length? | Yes | No |
| Single-pass decisions possible? | No | Yes |

For many production workloads, the most expensive part of the process may not be understanding the user. It may be describing the answer.

## Why This Matters for the Environment

The AI industry often focuses on larger models, larger clusters, and larger training runs. But efficiency matters just as much as capability. If millions or billions of AI requests are fundamentally routing, classification, scoring, or function-selection tasks, reducing the computation required per decision has direct environmental implications.

Less computation means:

- Less energy consumed
- Less cooling required
- Less accelerator time
- Lower infrastructure requirements
- Reduced carbon impact

The greenest token is the one that never needs to be generated.

## The Tradeoff

Of course, there is no free lunch. System 1 models achieve their efficiency through specialization.

They require:

- Purpose-specific training
- Domain-specific fine-tuning
- Carefully defined outputs
- Well-designed decision spaces

You wouldn't ask one to write a novel, create a powerpoint or draft a legal brief. Those remain excellent use cases for large generative models.

But if all you need is to understand what a human wants and turn it into a machine action then a specialized decision model can be dramatically more efficient.

## What's Next

The challenge isn't proving that System 1 models work. The challenge is making them accessible. Today, developers can create a prompt in minutes and connect it to an LLM. Building, training, and deploying decision models remains significantly more specialized as much of the tooling and infrastructure for decision models doesn’t yet exist to the same extent as it does for Large Language Models.

That creates a gap between what's theoretically possible and what developers can practically ship. That's the problem I've become increasingly interested in solving. We need tools that ease the building of specialized decision model and accompanying SDKs for creating AI applications using such System 1 models.

What if developers could define the decisions they need, provide examples, and automatically generate highly optimized System 1 models for production workloads?


