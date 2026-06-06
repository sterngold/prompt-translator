# PromptTranslator

**Say it once. Get it right.**

PromptTranslator is a simultaneous-interpretation-inspired prompt optimizer. It translates natural human prompts into precise, machine-readable instructions using four interpreter techniques: chunking, anticipation, compression, and reformulation.

This public repository is a product case study. The production Worker, UI, interpreter prompt, telemetry, and user data stay private.

## The Problem

Many capable people know what they mean but write prompts like emails: context first, task buried in the middle, constraints implied, output format missing. The AI gives something close but not quite right, and the conversation turns into three or four rounds of “no, I meant...”

The problem is not that people are bad at prompting. The problem is that they speak human, while the model performs best when intent is structured.

## The Product

PromptTranslator acts like an interpreter between the human and the model:

```text
messy human prompt
  -> chunk meaning
  -> anticipate missing instructions
  -> compress without losing meaning
  -> reformulate for machine parsing
  -> precise prompt
```

The product is designed around **show, then trust**:

- Show a before/after transformation first.
- Explain what changed and why.
- Let users switch to faster workflows only after they trust the interpreter.
- Teach users to write clearer prompts over time, rather than making them dependent forever.

## Core Techniques

| Technique | What it does |
|---|---|
| Chunking | Separates context, task, constraints, assumptions, and output format. |
| Anticipation | Adds missing but necessary details and marks assumptions explicitly. |
| Compression | Removes hedging and repetition while preserving intent. |
| Reformulation | Restructures the request into a format the model can parse reliably. |

## Example

**Human prompt**

> I was wondering if you could help me think through this client situation. They keep asking for a strategy deck but I feel like what they actually need is a decision framework. I don't want to sound dismissive, but I also don't want to waste time making slides that won't help. Can you help?

**Translated prompt**

```text
<context>
Client is asking for a strategy deck. The real need may be a decision framework.
</context>

<task>
Help me respond to the client in a way that redirects from "make slides" to "choose the right decision process."
</task>

<constraints>
- Tone: respectful, not dismissive
- Preserve trust with the client
- Avoid over-producing unnecessary deck work
- Make the alternative concrete
</constraints>

<output_format>
1. Recommended framing
2. Short client-facing message
3. Optional meeting agenda
</output_format>
```

## Architecture

The private implementation uses a web dashboard, API backend, structured transformation pipeline, storage for sessions/history, and privacy-filtered telemetry. The public architecture summary is in [docs/architecture.md](docs/architecture.md).

## Validation

PromptTranslator was tested against real-world messy prompts from coaching, product, writing, and technical contexts. The useful signal was not “shorter prompts” alone; it was fewer avoidable follow-up rounds and clearer first responses.

See [docs/validation.md](docs/validation.md) for the public validation summary.

## Why This Matters

Prompt engineering should not become another technical class divide. Good AI tools should make people more capable, not more dependent.

PromptTranslator is built around that principle: translate the prompt, show the transformation, and teach the pattern until the user needs the interpreter less.

## Status

Private product prototype. Public repo maintained as a case study and portfolio artifact.

## Related Work

- [Vlad Sterngold](https://sterngold.nl)
- [WerkAnders](https://werkanders.com)
- [The Symbiotic Mind](https://symbiotic-mind.com)
