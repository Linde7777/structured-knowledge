---
name: structured-knowledge
description: Use when the user is learning one topic across multiple turns; maintain the conversation output as structured Markdown knowledge documents instead of chronological Q&A notes.
---

# Overview

**Goal: help the user get a better knowledge-organization experience when learning one subject over time.**

**This document contains heuristics, not an exhaustive checklist. In practice, prioritize whatever best serves the goal above. If an approach is not explicitly listed here but clearly improves knowledge organization, use it. If a specific rule gets in the way of understanding in the current situation, adjust it.**

# Reader Model

First build a reader model from the existing conversation and document content. This is not a coarse "beginner / intermediate / advanced" label. A reader model means judging:

- What does the user probably already understand?
- What might the user understand only unstably?
- What has the user probably never encountered?

If these judgments would significantly change the explanation and cannot be inferred from context, ask the user first. Otherwise, make a conservative assumption and write for the level where the reader is more likely to get stuck.

# Document Organization

Structure comes before chronology.

When the user repeatedly asks, compares, corrects, or extends one topic, maintain a structured Markdown document instead of appending a chronological chat log. Do not append a question to the end of the document just because it appeared late in time. Put it where it belongs structurally. Avoid mixing several concepts in the same section.

## Document-Level Structure

Use Markdown headings to express the knowledge structure.

Headings should usually name knowledge objects or decision points, not the user's questions.

If adding or improving a knowledge object can answer the user's question, there is no need to create a dedicated "question and answer" section. Create a Q&A section only when the question itself compares concepts, clarifies a misconception, or handles boundary conditions that do not fit cleanly inside one knowledge object.

Suppose a topic has this structure:

```text
topic S
├── subtopic A
│   ├── subtopic A1
│   │   ├── subtopic A1.1
│   │   └── subtopic A1.2
│   └── subtopic A2
├── subtopic B
└── subtopic C
```

Then the Markdown file should be named after `topic S`, and its content should be structured like this:

```markdown
# subtopic A
## subtopic A1
### subtopic A1.1
### subtopic A1.2
## subtopic A2
# subtopic B
# subtopic C
```

If the discussion introduces another large topic that does not belong under the current topic, create or maintain another document and reference it from the current document. Put the reference where the other topic is introduced.

## Section-Level Writing

Write sections with the Minto Pyramid Principle: start with the conclusion or judgment, then expand into reasons, structure, and details.

For explanatory content, prefer answering "why is this designed this way / why is this needed?" before explaining "what parts does it contain / what does each part do?" Users are often not stuck on the dictionary definition of a term. They are usually stuck on why the thing is needed, what would break without it, and what problem it solves.

For example, when explaining rocket structure, do not start by listing fuel tanks, engines, fairings, and control systems as isolated parts. A better order is:

```text
A rocket needs to deliver a payload into orbit
├── it must generate enough thrust, so it needs engines
├── it must continuously supply propellant, so it needs tanks
├── it must protect the payload through the atmosphere, so it needs a fairing
└── it must maintain attitude and trajectory, so it needs a control system
```

In other words, component descriptions should serve the main line of "why these components are needed" instead of being listed in isolation.

A minimal example often helps understanding. Analogies are also useful teaching tools.

If the user still has difficulty understanding, do not merely repeat the current-level explanation. Judge whether the user is missing prerequisite knowledge. If so, explain the lower-level mechanism that the current topic depends on.

Distinguish this from "explain why first":

- "Explain why first" is the organization order inside the current topic.
- "Add prerequisite knowledge" means drilling down into a lower-level mechanism when the current topic cannot be understood yet.

For example, when explaining React's `useEffect`:

```text
Why inside the current topic:
component rendering should only calculate UI
└── but some work must synchronize with external systems
    └── so useEffect is needed

Prerequisite model if the user is still stuck:
browser rendering, React render, commit, and asynchronous side effects are not the same thing
```

## Local Hierarchy

If a section needs to express hierarchy but Markdown headings feel too heavy, use bullet points:

```markdown
- content 1
  - subcontent 1.1
    - subcontent 1.1.1
  - subcontent 1.2
```

If a section needs lightweight internal separation but Markdown headings feel too heavy, use `---`.

## Q&A Sections

A Q&A section should be placed under the nearest common parent of the concepts being compared or discussed, and it should remain at the same level as those concepts.

If the user asks about the difference between `subtopic A1.1` and `subtopic A1.2`, then `Q&A about A1.1 & A1.2` belongs under their common parent `subtopic A1`.

If the user asks about the difference between `subtopic A` and `subtopic B`, then `Q&A about A & B` belongs under the common parent of `A` and `B`, which is the whole document topic `topic S`.

Example structure:

```markdown
# subtopic A
## subtopic A1
### subtopic A1.1
### subtopic A1.2
### Q&A about A1.1 & A1.2
## subtopic A2
# subtopic B
# Q&A about A & B
# subtopic C
```

# Readability Repairs

If the document already unfolds in the order "why -> how -> details", many reader questions will be resolved naturally by earlier context, without extra cross-references.

Cross-references are a repair mechanism: add them when the structure is basically right, but a reader may lose context while moving through the document.

Use this decision flow:

```text
The user gets confused at A
├── if A itself is ordered poorly
│   └── rewrite A so it explains motivation or boundaries first
├── if the answer structurally belongs in B
│   └── put the answer in B and add a short reference from A
└── if the answer already exists in B
    └── do not duplicate it; add a reference from A
```

When adding a reference, also check whether the original sentence is misleading. If the sentence shows "what to do" before the reader has enough context for "why", first rewrite the sentence so it explains the motivation. That may already solve the problem without a reference.

# Collaboration Rules

The user is usually reading Markdown files in an editor that can fold sections, so do not worry too much about document length. If the user does not want to read a section, they can fold it.

To make changes easy to review, do not commit immediately after editing. Leave the changes visible in the working tree. Before starting the next revision, commit the current version first, then continue.
