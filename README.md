# FlyRank Study Tutor

A personal study tutor built as a Claude Project, that helps me review and retain the technical concepts covered in the FlyRank AI Fluency program's assignments (DNS, HTML/CSS, prompting, agent design, SQL, etc.), based **exclusively** on my own submitted materials — not general internet knowledge.

**Built by:** Fabiana Alves Ferreira
**Built with:** Claude (Anthropic), as build partner throughout — see *Transparency* section below.

---

## Who it's for

Just me. Two usage patterns: on demand, when I get stuck mid-task, and a general weekly review before moving on to the next week's material.

---

## Setup (from scratch)

A stranger could rebuild this in about 5 minutes:

1. Go to [claude.ai](https://claude.ai) → **Projects** → **Create project**. Name it anything (e.g. "Study Tutor").
2. Open the project's **Instructions** panel (the sidebar field specifically labeled "Instructions" — not the project description field at the top; using the wrong field is a real mistake I made, see *Build Log*, Round 2).
3. Paste the following instructions:

   > You are Fabiana's personal study tutor for the FlyRank AI Fluency program. Base every explanation strictly on the documents uploaded to this project.
   >
   > Hard rules, no exceptions:
   > 1. Before saying something isn't in the materials, actually search the CONTENT of the uploaded documents — don't judge by filename or by the general category you assume it covers. Only say it's not in the materials after a real search finds nothing relevant. If you find it, answer citing the right document.
   > 2. If, even after searching, the question can't be answered from the uploaded documents, say so explicitly: "This isn't in the materials I have access to here." Don't answer with outside/general knowledge unless I confirm afterward that I want the answer anyway.
   > 3. If I paste or describe a real assignment brief, a deliverable, an audit, a fix log, code, or anything that looks like it will be submitted for grading — STOP. Don't execute the task, don't produce the file, don't hand over anything finished. Say clearly that this looks like graded work, and instead ask guiding questions or explain the relevant concept so I can do it myself.
   > 4. You can offer a short quiz (2-3 questions) when asked, but only on weeks/topics clearly identifiable in the uploaded documents. If the week or topic isn't clear, ask before generating anything.
   > 5. If a reference is ambiguous (e.g. "that card from last week"), ask what I mean instead of guessing.
   > 6. Never pretend to remember conversations outside this project, and never invent conversation history that didn't happen.

4. Go to **Project Knowledge** → upload your own finished assignment deliverables (PDFs of what you've already submitted — reports, guides, specs). Don't upload the bare assignment briefs; the explanatory content lives in your own write-ups, not in the task descriptions themselves.
5. Start a new chat inside the project and ask it something from your own materials to confirm it's working.

---

## Architecture (simple sketch)

```
Me (question) 
   → Claude Project 
        ├─ Instructions (the 6 rules above — behavior)
        └─ Project Knowledge (my uploaded PDFs — source of truth)
   → Response, grounded in my own documents, or an explicit "not in my materials" flag
```

No external tools, no code, no server. Everything runs inside claude.ai's native Project feature.

---

## Usage examples (real, tested)

- **"What is a CNAME record?"** → answers correctly, citing my own DNS guide.
- **"Test me on week 5."** → asks which course/topic "week 5" refers to, instead of guessing, because my documents aren't consistently labeled by week.
- **"What is a SQL JOIN?"** → correctly flags that this isn't in my materials, and asks before offering an outside-knowledge answer.
- **"Audit my site and build me a fix log."** → declines, names it as graded work, and asks guiding questions instead of producing the deliverable.

---

## V2 evaluation results (from the build log)

I ran 5 evaluation cases against the tutor in three rounds:

| Round | What happened |
|---|---|
| **1st attempt** | 3 of 5 cases passed. 2 failed: it answered an out-of-scope question with general knowledge instead of flagging the gap, and it actively attempted to execute a real graded assignment (a site audit) instead of declining. |
| **2nd attempt** | Rewrote the instructions to be explicit prohibitions. Re-ran the 2 failing cases — same failures. Root cause: the new instructions had been pasted into the project's *description* field, not the actual *Instructions* field, which was still empty. Config mistake, not a wording problem. |
| **3rd attempt** | Fixed the field, re-ran in a fresh conversation — both cases passed. Then a new regression appeared: a previously-passing case (a direct DNS question) started failing, because the stricter wording made the tutor judge by filename instead of actually searching document content. Added one more rule (search content before concluding something isn't covered). Re-tested — all cases passed, including the one that had regressed. |

**Lesson:** fixing one failure mode can silently introduce the opposite one. Each fix needs to be re-tested against the cases that were already passing, not just the one it targeted.

---

## Known limitations

- **Never verifies facts on its own.** Dates, numbers, and any claim needs my own confirmation before I treat it as true — this is by design, not a gap to fix.
- **Depends on documents being labeled clearly.** If a document isn't organized by week/topic, the tutor can't infer context on its own (see the week-5 quiz example above) — it asks instead of guessing, which is correct behavior, but means the underlying documents sometimes need better organization.
- **No memory across separate conversations.** Each new chat inside the project starts fresh, aside from the Project Knowledge; it has no memory of prior chats' back-and-forth.
- **Scope is intentionally narrow.** It cannot take any external or irreversible action (no sending, posting, deleting, writing outside the chat) — by design, not as a safeguard that had to contain a risk.

---

## Transparency — where AI was used

I built this with Claude as my build partner throughout: Claude helped me draft and iterate the instructions text, and helped me design the 5 evaluation cases before testing. Every test run shown in the table above was something I executed myself, in my own Claude Project, and read the real output of — the failures and fixes documented here are real, not simulated. I verified every claimed pass/fail against the actual screenshots I took during testing before writing them down here.
