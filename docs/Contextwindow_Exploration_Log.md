## Context Window Experiment — Part 3.2

### Experiment Goal
Compare Claude Code's response quality between:
- A broad question requiring 6+ files to answer
- The same topic narrowed to a single specific file

---

### What is a Context Window?
Claude Code can only hold a limited amount of information 
in memory during a session. This is called the context window. 
As Claude reads more files and generates longer responses, 
the context window fills up. When it is nearly full, Claude 
has less capacity for deep reasoning — answers become broader 
and less precise. Managing the context window is therefore 
a critical skill when using Claude Code effectively.

---

### Experiment Design

Two questions were asked on the same topic — Flask error handling:

Question 1 was intentionally broad, requiring Claude to read 
across the entire codebase.

After /clear was used to reset the context window completely, 
Question 2 was asked on the same topic but scoped to a 
single file only.

---

### Question 1 — Broad

Prompt asked:
"How does Flask handle errors across the entire codebase? 
Include every file involved, all error handler registration, 
propagation, HTTP exceptions, logging, signals, and debug 
mode behavior."

Results:
- Files read: 6 files
- Time taken: 2 minutes 6 seconds

What Claude produced:
- Complete 4-layer error handling architecture
- _find_error_handler lookup algorithm with priority order
- All 4 config flags controlling error behavior
- got_request_exception signal explained
- Debug-mode specific behavior in debughelpers.py
- Complete end-to-end error flow diagram

Strengths:
- Excellent for understanding the big picture
- Showed how all files connect together
- Produced a complete system map

Weaknesses:
- No line-by-line code detail
- Too high level for debugging specific behavior
- High context window usage left less room for deep reasoning

---

### /clear — Context Window Reset

The /clear command was used between both questions.
This completely resets Claude Code's memory — it forgets 
all previous files read, all previous responses, and starts 
the session fresh with an empty context window.

This is important because:
- Claude re-reads files without bias from earlier conversation
- Full context window capacity is restored
- Claude can reason more deeply on a focused topic

---

### Question 2 — Narrow

Prompt asked:
"Look only at src/flask/app.py. How does the 
full_dispatch_request method handle errors? 
Show the exact code and explain each line."

Results:
- Files read: 1 file
- Time taken: Significantly faster
- Context window usage: Low

What Claude produced:
- Exact source code of full_dispatch_request
- Complete line-by-line explanation table
- Clear explanation of the try/except design intent
- Discovered a deprecated should_ignore_error hook 
  that the broad question completely missed

Strengths:
- Highly precise and actionable
- Line-by-line code detail
- Discovered new finding missed by broad question
- Low context window usage enabled deeper reasoning

Weaknesses:
- No visibility into the broader error handling system
- Cannot show how files connect together

---

### Comparison Table

| Criteria              | Broad Question    | Narrow Question     |
|-----------------------|-------------------|---------------------|
| Files read            | 6                 | 1                   |
| Response time         | 2 minutes 6 secs  | Significantly faster|
| Architecture view     | ✅ Complete       | ❌ Limited          |
| Line-by-line detail   | ❌ Not present    | ✅ Complete         |
| New discoveries       | ❌ None           | ✅ Found deprecated hook |
| Context window usage  | High              | Low                 |
| Response actionability| Low               | High                |

---

### Key Learnings

1. Broad questions are best for understanding architecture 
   and mapping how the entire system connects together.

2. Narrow questions produce more precise and actionable 
   answers — and can surface specific details that broad 
   questions miss entirely.

3. The /clear command is a powerful context management tool. 
   Resetting the context window forces Claude to re-read 
   files fresh, restores full reasoning capacity, and 
   eliminates any drift or bias from earlier in the session.

4. As the context window fills up, Claude trades depth for 
   breadth. Response quality does not drop suddenly — it 
   degrades gradually as context usage increases.

5. Low context window usage directly enables deeper reasoning 
   on specific code — as proven by the narrow question 
   discovering a finding the broad question missed.

---

### Recommendation — The Two-Pass Approach

Use Claude Code in two passes for any complex exploration:

PASS 1 — Broad (Map the system)
- Ask broad questions across multiple files
- Save the output immediately before context fills further
- Use this output for architecture diagrams and 
  system-level understanding

/clear — Reset between passes
- Completely clears the context window
- Claude starts fresh with full reasoning capacity

PASS 2 — Narrow (Deep dive)
- Ask focused questions scoped to specific files
- Claude reasons more deeply with lower context usage
- Use this output for code-level understanding and debugging

---

### Conclusion

Neither approach is universally better — they serve 
different purposes.

Broad questions win for: architecture mapping, 
system diagrams, understanding file relationships.

Narrow questions win for: code detail, debugging, 
discovering specific behavior, actionable findings.

Best practice: Always use both. Start broad to understand 
the system, then use /clear and go narrow to understand 
the specific behavior you care about.