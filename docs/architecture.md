# Architecture

PromptTranslator is built as a small product system around one core contract: translate messy intent into a clearer prompt while preserving the user's meaning.

## Public System Shape

```text
User
  |
  v
Web UI / CLI concept
  |
  v
Translation API
  |
  +-- interpreter pipeline
  +-- safety preamble
  +-- structured output formatter
  +-- session/history layer
  |
  v
Before/after result + explanation
```

## Design Principles

- **Clarity first, compression second.** A shorter prompt that still misses the task is not useful.
- **Assumptions must be visible.** Anticipation is powerful only when the user can inspect what was inferred.
- **No prompt content in product telemetry.** Product learning can use metadata and buckets without storing private user text.
- **Show before auto.** Users should see the transformation before trusting background automation.
- **Graduation is success.** The product should teach users to need less translation over time.

## Private Implementation Boundaries

The production implementation details remain private because they include:

- the interpreter system prompt and transformation heuristics;
- deployment configuration;
- telemetry wiring;
- user/session state;
- product experiments and activation data.

This repository intentionally publishes the product architecture and learning, not the private runtime.
