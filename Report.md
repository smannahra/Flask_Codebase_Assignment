# REPORT.md — Weekly Assignment Week 1: The Codebase Detective

**Repository Explored:** pallets/flask
**Tool Used:** Claude Code (CLI)
**Assignment:** Claude Code — AI-Augmented Software Engineering

---

## Thinking Questions

---

### Q1. Explain the difference between Claude "understanding" code vs "pattern matching" against training data. How did this distinction show up in your exploration?

No LLM truly understands code the way a human does — but Claude Code's tool use changes the equation significantly. Pure pattern matching would mean Claude generates responses based on what Flask code probably looks like from its training data — plausible but unverified. What Claude Code actually did in this session was different. It consistently used tool calls to read actual files before responding. This grounded its answers in real code rather than training patterns.

---

### Q2. What happened when the context window filled up during your session? How did Claude's responses change? What strategies did you use to manage this?

During this exploration session, Claude Code's responses remained consistent throughout. The response did not visibly degrade during exploration — probably I did not push it too much. However the response changed before and after the /clear for a similar prompt. After the /clear, the response was more detailed and it went through line by line explanation.

I have detailed the experiment in `docs\Contextwindow_Exploration_Log.md`

---

### Q3. Describe a specific hallucination you detected. What caused it, and how could you have prevented it with a better prompt?

Deliberate hallucination tests were done: Claude Code made effective tool calls and did not hallucinate. Claude Code avoided hallucination in every case because it used tool calls to verify claims against actual files before responding. It searched before it spoke. This is fundamentally different from regular Claude which would have had to rely entirely on training data.

More information on the test done: `docs\Prompt_Experiment.md`

---

### Q4. Compare your vague vs. structured prompts side-by-side. What specific elements made the structured version more reliable?

Three vague prompts were tested and then rewritten as structured prompts during this session. Four specific elements made the difference:

1. **File reference** — Telling Claude exactly which file to read eliminated guessing. Claude read one file deeply instead of skimming many files broadly. Example: "In src/flask/app.py" vs nothing.

2. **Line number or method name** — Giving Claude a precise starting point focused its reading immediately on the relevant code. Example: "full_dispatch_request starts at line 992"

3. **Specific question** — Asking a precise yes/no or explain question prevented Claude from generating a generic overview. Example: "Is there a missing error handling case?" vs "fix the bug"

4. **Expected output format** — Telling Claude what to produce — show the code, explain each line, list line numbers — produced consistent structured responses. Example: "Show the exact code and explain if this is a bug or intentional behavior."

More information on the test done: `docs\Prompt_Experiment.md`

---

### Q5. How accurate were Claude's bug findings? What percentage were real vs. hallucinated? What does this tell you about using AI for code review?

I took the help of Claude to do this and verify.

Before presenting results, Claude Code explicitly stated: "Several agent-generated findings were discarded — the __html__() serialization is intentional design, debug error disclosure is intentional, and SESSION_COOKIE_HTTPONLY already defaults to True." This means Claude self-corrected before presenting results. The raw findings contained errors but Claude filtered them against actual code before reporting.

**BUG AUDIT VERIFICATION RESULTS:**

Noticed something important — Claude Code said: "Several agent-generated findings were discarded" in the bug report.

**Finding #1 — SESSION_COOKIE_SECURE = False**
- File: src/flask/app.py, Line 222
- Verified: ✅ REAL
- Code: `"SESSION_COOKIE_SECURE": False`

**Finding #2 — SECRET_KEY = None**
- File: src/flask/app.py, Line 211
- Verified: ✅ REAL
- Code: `"SECRET_KEY": None`

**Finding #3 — SHA-1 HMAC digest**
- File: src/flask/sessions.py, Line 281
- Verified: ✅ REAL
- Code: `return hashlib.sha1(string)`

**Overall accuracy: 3/3 = 100% verified real**
**Hallucinations: 0**

More information on the test done: `docs\Bug_Identify_Verification.md`

---

### Q6. What did your CLAUDE.md contain, and how did it change Claude's behavior when you loaded it? Would you use /init again?

The final CLAUDE.md contained 7 sections:

1. **Commands** — Exact bash commands for running tests, linting, type checking, and building docs — all extracted directly from pyproject.toml and CI workflows. Example: `uv run --locked tox run`

2. **Architecture** — Two-layer model explanation — sansio/ as the WSGI-agnostic base and flask/ as the WSGI implementation. Context system, request lifecycle, ensure_sync pattern, blueprint registration, and the ctx parameter breaking change in Flask 3.2.

3. **Test Fixtures** — All 5 fixtures from tests/conftest.py including the leak_detector autouse fixture.

4. **Key External Dependencies** — 8 packages with exact minimum versions and their specific role in Flask — extracted from pyproject.toml.

5. **Common Pitfalls for New Developers** — 7 real pitfalls extracted from reading actual source files — including the lazy session loading, blueprint deferred registration, and ensure_sync wrapping at dispatch time rather than decoration time.

I will definitely start with /init again.

---

### Q7. If you had to onboard a new team member to this codebase, which 3 Claude Code prompts would you give them first? Why?

**Prompt 1 — Repository Overview:**
```
Summarize this repository. Tell me:
1. What is this project?
2. What is the tech stack?
3. What are the key modules?
4. What are the main entry points?
5. What are the external dependencies?
Be specific with file names and paths.
```
*Why this prompt first:* This gives the new team member an accurate mental model of the entire codebase before touching any specific file.

**Prompt 2 — Architecture Diagram:**
```
Generate a Mermaid architecture flowchart showing 
the system layers. Show the key components and 
which file is responsible for each layer.
```
*Why this prompt:* Architecture understanding — an architecture map showing exactly which file owns which responsibility — entry point, context, routing, view, template, serialization, signals — will help a new team member navigate properly without much help.

**Prompt 3 — /init for CLAUDE.md:**
```
/init
```
Then follow up asking specifically for `## Important Notes/Pitfalls for New Developers`.

*Why this prompt:* To understand known pitfalls and limitations.

---

## Tactical Questions

---

### Q8. List the 5 tools Claude Code used most during your exploration. Which was most valuable and why?

Because I used the /clear command to test the context window, I lost the details before that. But with whatever was pulled after that from the Claude session report log, these are the top 5 tools used:

**Read, Grep, Bash, Agent, Write**

Grep and Read tools are the most valuable — they help Claude identify specific files and read the content. As a result the responses are more grounded, detailed, and not prone to hallucination.

---

### Q9. Write the exact prompt that produced your best architecture diagram. Why did this prompt work well?

**The Exact Prompt:**
```
Generate a Mermaid architecture flowchart showing 
the system layers of Flask. Show the key components 
and which file is responsible for each layer.
```

**The Mermaid Output:**

See `docs/diagrams/architecture.md` for the full diagram.

---

### Q10. Show a before/after prompt pair where the "after" was significantly more reliable. Explain what you changed and why.

**BEFORE — Vague Prompt:**
```
Fix the bug.
```

**Claude's Response to Vague Prompt:**
"I don't have enough context to know which bug you're referring to. Could you clarify:
- What bug are you seeing?
- Where is it?"

Result: No action taken. Zero output produced.

---

**AFTER — Structured Prompt:**
```
In src/flask/app.py, the method full_dispatch_request 
starts at line 992. Is there a missing error handling 
case where an exception during before_request hooks 
is not caught? Show the exact code and explain if 
this is a bug or intentional behavior.
```

**Claude's Response to Structured Prompt:**
Claude read src/flask/app.py, found full_dispatch_request at line 992, showed the exact try/except block, and explained: "There is no missing error handling case. The premise of the question is incorrect." It then showed the exact code, and explained line by line why the error handling was intentional — preprocess_request is inside the try block so exceptions from before_request hooks are caught correctly.

Result: Precise, verified, actionable answer with exact code and line references.

---

**What Changed:**

The vague prompt gave Claude 4 unknowns:
- Which bug?
- Which file?
- What does fixed look like?
- What output format is expected?

The structured prompt gave Claude 4 specifics:
- File: src/flask/app.py
- Location: line 992
- Question: is error handling missing?
- Output: show code + explain

The key insight is that prompt quality is not about using special keywords or techniques. It is about giving Claude the same information you would give a human colleague — which file, which function, what you think is wrong, and what kind of answer you need.

---

### Q11. What was the deepest call chain you traced? How many files did Claude need to read to answer?

I asked Claude to trace the @app.route decorator.

Claude helped me summarise this:

- **Total steps in chain:** 8 registration steps + 3 dispatch steps = **11 steps total**
- **Total files crossed:** 4 files
- **Total line references:** 15 exact line numbers

---

### Q12. Describe one case where Claude's answer was correct but not useful, and one case where it was wrong but the error was instructive.

**Case 1 — Correct But Not Useful:**

Prompt: "How does Flask handle errors across the entire codebase?"

The answer was correct but broad. It didn't particularly help as a debugging tool.

**Case 2 — Wrong But Instructive:**

I could not particularly see a case where Claude did something wrong during this session.