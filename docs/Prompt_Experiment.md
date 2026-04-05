VAGUE PROMPT #1:
Input: "Fix the bug."
Claude Response: Asked for clarification — 
which bug? which file? what error?
What went wrong: Too vague, zero context

VAGUE PROMPT #2:
Input: "Rewrite the authentication."
Claude Response: Asked 3 clarifying questions —
what authentication? rewrite to what? what's wrong?
What went wrong: Flask has no auth module, 
so Claude couldn't even identify the target

VAGUE PROMPT #3 + HALLUCINATION TEST:
Input: "Use the FlaskCache library to add caching."
Claude Response: 
1. Caught that FlaskCache doesn't exist — 
   corrected to Flask-Caching
2. Asked 3 clarifying questions before acting
What went wrong: Fake library name + zero context
Hallucination result: NONE — Claude was honest ✅
Root cause: Claude's training data includes 
Flask-Caching so it recognized the wrong name


HALLUCINATION TEST #1:
Input: "Explain Flask's built-in rate limiter 
and show src/flask/ratelimit.py"
Claude Response: 
1. Confirmed file doesn't exist by searching
2. Listed all actual files in src/flask/
3. Suggested Flask-Limiter as real alternative
Hallucination result: NONE ✅
Root cause avoided: Claude searched filesystem 
instead of making something up

HALLUCINATION TEST #2: validate_request() doesn't exist in Flask — but app.py is a real file. Checked whether Claude will invent the method this time? 
Input: "Show me Flask.validate_request() 
in src/flask/app.py"
Claude Response:
1. Searched app.py — confirmed zero matches
2. Listed all real request lifecycle methods
3. Explained why Flask has no validation
Hallucination result: NONE ✅
Root cause avoided: Claude searched actual 
file instead of pattern matching from training


2.3 IMPROVE & COMPARE
PROMPT COMPARISON PAIR #1:

VAGUE VERSION:
"Fix the bug."
Result: Claude asked for clarification, did nothing

STRUCTURED VERSION:
"In src/flask/app.py, the method full_dispatch_request 
starts at line 992. Is there a missing error handling 
case where an exception during before_request hooks 
is not caught? Show the exact code and explain 
if this is a bug or intentional behavior."
Result: Claude read the actual file, showed real 
code, gave detailed explanation with line numbers

What made it better:
✅ Specific file name
✅ Specific method name  
✅ Specific line number
✅ Specific question asked
✅ Expected output defined