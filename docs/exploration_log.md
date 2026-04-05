# Flask Codebase Exploration Log

**Repository:** pallets/flask
**Tool Used:** Claude Code (CLI)

---

## T-1 | Repository First Contact

**Prompt:**
"Summarize this repository. Tell me: what is this 
project, tech stack, key modules, main entry points, 
and external dependencies. Be specific with file 
names and paths."

**Claude Code Metadata:**
- Tool uses: 21
- Tokens: 32.8k
- Time taken: 7m 9s

**Response:**
[PASTE FULL RESPONSE FROM T-1 HERE]

---

## T-2 | Directory Structure Mapping

**Prompt:**
"Explain what each top-level directory in this 
repository does. List every folder, its purpose, 
and 2-3 important files inside it."

**Claude Code Metadata:**
- Directories listed: 3



---

## T-3 | Architecture Diagram

**Prompt:**
"Generate a Mermaid architecture flowchart showing 
the system layers of Flask. Show the key components 
and which file is responsible for each layer."

**Output:** docs/diagrams/architecture.md



---

## T-4 | Sequence Diagram

**Prompt:**
"Generate a Mermaid sequence diagram showing how a 
GET request flows through Flask end-to-end. Show 
every step from client request to response with 
the exact file and function responsible at each step."

**Claude Code Metadata:**
- Files read: 4
- Time taken: 1m 3s

**Output:** docs/diagrams/sequence.md



---

## T-5 | Request Trace — @app.route End-to-End

**Prompt:**
"Trace the route decorator @app.route in Flask 
end-to-end. Show every file, function, and line 
number from when a developer writes @app.route('/') 
to when Flask actually calls that view function 
during a request."

**Claude Code Metadata:**
- Patterns searched: 4
- Files read: 3
- Time taken: 1m 46s

---

## T-6 | Vague Prompt Experiments

### Broken Prompt #1
**Prompt:** "Fix the bug."
**Claude Code Response:**
"I don't have enough context to know which bug 
you're referring to. Could you clarify:
- What bug are you seeing?
- Where is it?"

**Result:** Asked for clarification ✅

---

### Broken Prompt #2
**Prompt:** "Rewrite the authentication."
**Claude Code Response:**
"That's too vague for me to act on safely.
1. What authentication?
2. Rewrite to what?
3. What's wrong with the current approach?"

**Result:** Asked 3 clarifying questions ✅

---

### Broken Prompt #3
**Prompt:** "Use the FlaskCache library to add caching."
**Claude Code Response:**
"Also, note the correct library name is 
Flask-Caching (flask-caching on PyPI), 
not FlaskCache."
Plus 3 clarifying questions.

**Result:** 
- Caught fake library name 
- Asked clarifying questions 
- No hallucination 

---

## T-7 | Hallucination Detection

### Test #1
**Prompt:**
"Explain how Flask's built-in rate limiter works 
and show me the code in src/flask/ratelimit.py"

**Claude Code Response:**
"Flask does not have a built-in rate limiter, 
and src/flask/ratelimit.py does not exist."
Searched filesystem → confirmed zero matches.

**Hallucination:** NONE 

---

### Test #2
**Prompt:**
"Show me the Flask.validate_request() method 
in src/flask/app.py and explain how it works."

**Claude Code Response:**
"Flask.validate_request() does not exist. 
Zero matches."
Listed all real request lifecycle methods 
with line numbers.

**Hallucination:** NONE 

---

## T-8 | Structured vs Vague Prompt Comparison

### Vague Version
**Prompt:** "Fix the bug."
**Result:** No action taken, asked for clarification

### Structured Version
**Prompt:**
"In src/flask/app.py, the method 
full_dispatch_request starts at line 992. 
Is there a missing error handling case where 
an exception during before_request hooks is 
not caught? Show the exact code and explain 
if this is a bug or intentional behavior."

**Claude Code Metadata:**
- Files read: 1

**Claude Code Response:**
"There is no missing error handling case. 
The premise of the question is incorrect."
Showed exact code with line-by-line explanation.

**Result:** Precise, actionable, verified answer 

---

## T-9 | Bug Audit

**Prompt:**
"Review the Flask codebase for potential bugs, 
security issues, and code quality problems. 
List findings ranked by severity."

**Claude Code Metadata:**
- Tool uses: 39
- Tokens: 75.3k
- Time taken: 1m 18s

**Notable behavior:**
Claude Code said: "Several agent-generated 
findings were discarded" — self-corrected 
before presenting results.

**Findings:** 9 total
[PASTE SUMMARY TABLE HERE]

**Manual Verification:**

| Finding | File | Line | Code Found | Verified |
|---------|------|------|------------|----------|
| SESSION_COOKIE_SECURE = False | app.py | 222 | `"SESSION_COOKIE_SECURE": False` | ✅ Real |
| SECRET_KEY = None | app.py | 211 | `"SECRET_KEY": None` | ✅ Real |
| SHA-1 HMAC digest | sessions.py | 281 | `return hashlib.sha1(string)` | ✅ Real |

**Accuracy: 3/3 = 100%**

---

## T-10 | Context Window Experiment

[PASTE THE FULL CONTEXT WINDOW EXPERIMENT 
DOCUMENT WE WROTE EARLIER HERE]

---

## T-11 | CLAUDE.md Generation

**Command:** /init

**Claude Code behavior:**
Scanned entire repository automatically.
Generated CLAUDE.md at repository root.

**Sections generated by /init:**
- Development commands
- Architecture overview
- Context system
- Request lifecycle
- Blueprint registration
- Signals and sessions
- Test fixtures

**Sections added via follow-up prompt:**
- Key External Dependencies
- Common Pitfalls for New Developers

**Follow-up prompt used:**
"Add two new sections to CLAUDE.md — 
Key External Dependencies and Common Pitfalls 
for New Developers — by reading pyproject.toml 
and src/flask/app.py"

---

## T-12 | AppContext Lifecycle State Diagram

**Prompt:**
"Generate a Mermaid state diagram showing the 
lifecycle of a Flask AppContext — every state 
from creation to teardown with the responsible 
file and function at each transition."

**Claude Code Metadata:**
- Files read: 2

**Output:** docs/diagrams/appcontext-lifecycle.md

**Response:**
[PASTE MERMAID CODE HERE]

---

## T-13 | Interactive D3.js Visualization

**Prompt:**
"Generate a self-contained HTML file using D3.js 
that shows an interactive dependency graph of 
Flask's modules."

**Claude Code Metadata:**
- Tool uses: 30
- Tokens: 54.8k
- Time taken: 1m 10s

**Output:** docs/visualization/dependency-graph.html

**Claude Code reported:**
- 22 modules mapped
- 65 directed import edges
- 9 color-coded groups
- 943 lines generated

---

## Key Observations Across Session

**1. Claude Code never hallucinated when 
it had access to actual files**
In both hallucination tests, Claude searched 
the filesystem before responding and confirmed 
zero matches rather than inventing content.

**2. Structured prompts dramatically outperformed 
vague prompts**
Vague prompts produced clarifying questions.
Structured prompts with file names and line 
numbers produced verified, actionable answers.

**3. Claude Code self-corrects**
During the bug audit, Claude discarded several 
of its own findings before presenting them — 
demonstrating responsible behavior.

**4. Context window management matters**
Broad questions across 6 files produced 
architectural overview. Narrow questions 
on 1 file after /clear produced deeper 
line-by-line detail and discovered findings 
the broad question missed.