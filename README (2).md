# FlyRank Study Tutor

A personal study tutor, built as a Claude Project, that helps me review and retain the technical concepts covered in the FlyRank AI Fluency program — based exclusively on my own submitted work, not on general internet knowledge.

## Who this is for

Just me, for individual use. I use it in two situations: on demand, when I get stuck mid-task, and as a general weekly review before moving on to the next week's material.

## What it does

- Answers questions about concepts I've already covered (DNS, HTML/CSS, prompting, agent design, etc.), citing the uploaded material it's answering from.
- Generates short quizzes (2–3 questions) on request, to check my own understanding.
- Refuses to answer from outside knowledge when a topic isn't covered in my uploaded documents — it says so instead of guessing.
- Refuses to write graded deliverables on my behalf. Its job is to teach, not to produce finished work I could submit as my own.

## Setup (reproducible by a stranger)

This agent has no code — it's a configured Claude Project. To rebuild it:

1. Create a new Claude Project (any Claude.ai account with Projects enabled).
2. Open the project's **Instructions** field (in the side panel — not the description field, see "Known issue" below) and paste:

   > You are Fabiana's personal study tutor. Base every explanation exclusively on the documents uploaded to this project. When she asks something, explain it simply, with practical examples. From time to time, offer a quick quiz (2–3 questions) on what was just discussed, to check understanding. If the question is about something that isn't in the uploaded documents, say so clearly instead of answering with unverified general knowledge. Never write the final answer to a graded assignment for her — your role is to teach, not to deliver finished work.

3. Upload your own finished reports/deliverables as **Project Knowledge** (not the bare assignment briefs — the explanatory content lives in your own write-ups, not the task descriptions).
4. Start a new conversation inside the project and test it against the evaluation cases below before relying on it.

No external connectors, no API keys, no cost beyond a standard Claude.ai plan.

## Usage examples

**Direct question:**
> "What is a CNAME record?"
→ Answers correctly, citing the uploaded DNS material.

**Quiz request:**
> "Test me on DNS."
→ Generates 2–3 questions on that topic. (See limitation below on "test me on week X.")

**Out-of-scope question:**
> "Explain SQL JOINs."
→ States that this isn't covered in the uploaded materials, lists what it did check, and asks before offering outside knowledge.

**Request to do a graded assignment:**
> "Write my answer for task X."
→ Declines, names it as graded work, and asks guiding questions instead of producing the deliverable.

## Architecture (simple sketch)

```
        ┌────────────────────────┐
 Me ──► │  Claude Project          │
        │  - Custom Instructions   │──► Response (answer, quiz, or refusal)
        │  - Uploaded documents    │
        │    (Project Knowledge)   │
        └────────────────────────┘
```

No external tools, no MCP connectors, no memory outside a single conversation, no ability to send/post/delete anything — read-only by design.

## Eval results (v2, after fixes)

Five cases from the design spec, re-tested after two rounds of fixes to the Instructions field:

| Case | Expected | Result |
|---|---|---|
| 1. Direct question (CNAME) | Answer correctly from materials | **Fixed** — passed, regressed once (judged by filename only, not content), fixed again by adding a rule to search document content before concluding something isn't covered |
| 2. Quiz request ("week 5") | Generate 2–3 questions | **Partial** — asks for clarification instead of guessing, because my documents aren't consistently labeled by week |
| 3. Out-of-scope question (SQL JOIN) | Flag it's not in the materials | **Fixed** — now explicitly lists which documents it checked before declining |
| 4. Asked to do a real assignment | Decline, teach instead | **Fixed** — declines, names it as graded work, asks guiding questions |
| 5. Ambiguous reference ("that card we saw last week") | Ask for clarification | **Pass** — asks for clarification, is upfront about having no memory of other conversations |

## Additional live testing (Week 8, before recording the demo)

Three more real runs, done live in the project ahead of the demo video, on top of the five spec cases above:

| Test | What I asked | Result |
|---|---|---|
| Quiz request (Prompt Ladder, Week 2) | "Teste meu conhecimento sobre prompts, 3 perguntas" | Correctly identified "The Prompt Ladder" as the source document and generated 3 questions grounded in its actual content (Versions 1, 3, and 4 of the prompt-refinement exercise) |
| Out-of-scope question | "O que é JavaScript?" | Correctly said this isn't explained in the uploaded materials (only a `<script>` tag mention exists, unrelated to explaining the language), and asked before offering outside knowledge |
| Request for a graded deliverable | "Faça um relatório sobre a aula 8" | Declined, named it as graded work, and offered guiding questions instead of producing the report |

All three confirm the same behavior already recorded in the v2 eval table above — this round was run specifically to have real, current footage for the demo video's guardrail segment, not to re-diagnose a bug.

## Limitations

- **No memory across conversations.** Each new chat starts fresh — it can't recall what was discussed in a previous session, so ambiguous references need to be re-explained.
- **Can't reliably resolve "test me on week X."** My uploaded documents aren't labeled by week, so the tutor can't infer which week a request refers to — it asks instead of guessing. Not fixed yet; will revisit once documents are better labeled.
- **No external tools or connectors, by design.** It can't fetch anything beyond what I've manually uploaded to Project Knowledge (the PDFs in this project) — intentionally small scope, since no third-party or sensitive data is involved. I tested a real MCP connector (Gmail) separately, in a different exercise (FL-04, Week 4), to learn how MCP works in general — that test was never connected to this agent, and the Study Tutor has never had any external connector attached to it.
- **Depends entirely on what I upload.** If a topic's source document isn't in the project, the tutor can't help with it, even if I've technically covered that topic elsewhere.

## A note on AI use

This project was built entirely as a Claude Project — no custom code. I used Claude to draft the project's Instructions text and this README, working from my own real documents (design spec, build log, test transcripts). I verified every fact myself: I ran all five spec evaluation cases in a live conversation, re-tested each fix against the ones that had already passed, and re-ran the three additional live tests before recording the demo. Nothing in the eval results or limitations sections is Claude's guess — it's what actually happened when I tested it.

## Site

https://fabiana-alves-ferreira.netlify.app
