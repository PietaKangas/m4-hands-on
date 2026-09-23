# Session 1

## Part A
#### Prompt 1:
"org.opentest4j.AssertionFailedError: 5-year loyalty customer should get 10% off (tier 3) ==> expected: <90.0> but was: <95.0>
Explain this Java stack trace in plain English. What is
the most likely root cause? Do NOT propose a patch yet."

#### answer:
Here's what the stack-trace message means and the most likely root cause.

- Error: org.opentest4j.AssertionFailedError — a unit test assertion failed (JUnit/OpenTest4J reported it).
- What failed: test description "5-year loyalty customer should get 10% off (tier 3)". The assertion expected a final price of 90.0 but the code returned 95.0.
- What the numbers imply: assuming a $100 base price, the code applied a 5% discount (result 95.0) instead of the expected 10% discount (result 90.0).
- Most likely root cause: either the customer was classified into the wrong loyalty tier (treated as tier 2 → 5%) or the discount lookup/mapping for tier 3 is wrong (returns 5% instead of 10%).
- Less likely causes: minor rounding/floating errors (wouldn't produce exactly 5% vs 10%), or the test expectation itself is incorrect (but the test message explicitly expects 10%).

In short: the code applied a 5% discount where the test expects 10% — check loyalty-year-to-tier logic and the tier→discount mapping.

#### notes: 
- I checked the suggestion provided by the AI, and it was correct. The problem was that the customer was classified into the wrong loyalty tier. (in the PriceEngine.java file the line 57: if (years > 5) --> if (years >= 5)).
- The AI ​​also suggested other alternatives because it couldn't be certain, given that I hadn't provided any other files or code. It guessed correctly, even though I had only provided a single assertion error.
- I think the entire stack trace is not necessarily needed for this assessment, only if you want to know exactly where the error occurs (which file(s) or/and line(s))

## Part B
#### Prompt:
"Given root cause X (paste the diagnosis from previous Part),
write the smallest patch that fixes the bug without
changing behaviour on any other test. Show only the
diff."

#### notes: 
- AI fixed the line 57 in the file PriceEngine.java: 
    if (years > 5) -> if (years >= 5)
- After that, all the tests pass, and no other test regressed

## Part C
#### Which of the AI’s replies (explain, patch) was the most useful? Which was the least useful?
- I think the explain answer was more usefull, because AI explained the error clearly and also potential root causes. Patch is less useful for this task, because we wanted to identify the error and its root cause first and not fix it immediately.

#### How much do you think should be given to the AI, in terms of stack trace? What is the consequence of pasting the full stack trace? Or just one/two lines?
- it depends on the task. I should give to AI at least the relevant lines of the error (in this case, a single line was enough). But in some cases (more complex ones) providing the full stack trace may be necessary becuse it can help find the exact location of the error.


# Session 2

## Part A
#### prompt:
The whole PriceEngine.java file +
"Rank the methods in this file by refactoring priority.
For each, give:
(a) current cyclomatic complexity estimate,
(b) the single refactor move you would apply first
(extract-method, flatten-guard-clauses, replace-loop-
with-pipeline, or rename),
(c) one sentence on the expected complexity reduction.
Do not propose code yet."

#### answer:
I’m reviewing the method structure and will rank the refactoring opportunities by likely impact before suggesting the first move for each.

Refactoring priority
This file has only one real method, quote, so the ranking is effectively a single-item list.

quote
(a) Current cyclomatic complexity estimate: about 15–17
(b) First refactor move: extract-method
(c) Expected complexity reduction: Splitting the method into smaller, named steps for subtotal, surcharge, promo logic, loyalty logic, and tax will collapse the main method into a simple orchestration flow and reduce branching pressure dramatically.
The main reason this is the top priority is that the method mixes domain calculations, guard clauses, and multiple conditional discount/tax rules in one long block, which makes it harder to read, test, and modify safely.

## Part B
#### Prompt
"Extract the loyalty-tier discount block into a private
helper method ‘applyLoyaltyDiscount(...)‘. Do NOT change public method signatures. Show the diff."

#### notes
- After extracting the return value type is still Money 
- Parameters type changes, customer.loyaltyYears()is now passed to the new method as an int years. 
- Missing final modifiers, but in the original quote method doesnt have final modifiers either, so it is ok
- "return running;" added to the extracted method, no early returns added

- all tests pass

## Part D
#### By how much did the cyclomatic complexity drop?
- the cyclomatic complexity of the quote method dropped by 4

#### List one refactor the AI proposed but you rejected. Explain why.
- I didnt reject any of the Ai's suggestions because the Ai's responses were concise and focused on what was requested in the prompt. I saw no reason for rejection.

