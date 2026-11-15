# Lab 05 — Conclusion

<!--
Read this before you write.

This file is graded by your instructor, by hand, and it is worth 6 of the 15 points.

- Type it yourself. Do not use a chatbot, an AI assistant, an AI autocomplete or a "rewrite my
  text" tool for any part of it, not even for grammar. English that is plain and a little
  clumsy but yours is better than polished text that is not.
- Use your own numbers. Every number you quote must be one your own notebook printed. Copy
  the number exactly as it appeared. A number your notebook did not print is marked as wrong.
- Answer in your own words, about 80-200 words per question. A short answer that uses your
  numbers beats a long answer that could have been written by anyone.
- Delete these comment lines if you like. Keep the three headings and the last line.
-->

## 1. Which layer changed your results the most?

Give the numbers before -> after for that layer (correct / wrong / no answer, out of 20) and say
whether it changed what the agent *can do* or only what it *admits*. If no layer changed the
number of correct answers, say so and explain what the layers did change instead.

In my run, Layer 1 changed the results the most in terms of raw numbers. Before it, the bare loop got 16 correct, 4 wrong, and 4 no answer out of 24. After adding Layer 1, it became 18 correct, 6 wrong, and 0 no answer. Basically, it caught tool errors and fed them back instead of crashing, which helped fix alias questions (q23 and q24 became correct) but also turned some crashes like q21 and q22 into wrong guesses. So it didn't really make the model smarter, it just changed what it attempts, trading program crashes for guesses and getting rid of "no answer" results entirely.

## 2. One failure the full harness did not fix

Pick one question that the full harness (layer 3) still gets wrong or refuses. Give its id
(q01 ... q20). Open its trace in the notebook: at which step did it go wrong, and what did
the model do instead? Name one thing you would add to the harness, and what it would cost
(extra model calls, tokens, time, or a new way to fail).

For question q18 with Layer 3 turned on, it still failed and ended up as no answer (UNVERIFIED). Looking at the trace, the model messed up because at step 2 it divided by 100 in calculate ((16.1 - 10.6)/100 = 0.055), but then at step 3 it wrote ANSWER: 0.55 percentage points, shifting the decimal point. Since 0.55 wasn't in the tool output, Layer 3 rejected it. At step 4, it just repeated the exact same wrong answer. To fix this, we could add a simple Python check in the harness to format percentages right before checking grounding. The downside is extra code complexity and potential false rejections.

## 3. Workflow or agent for the monthly ranking?

Dala Insight wants to publish, every month, "which region is highest and which is lowest on one
indicator in one year". Would you ship the fixed workflow or the agent? Use the accuracy, the
number of model calls, the tokens and the time you measured in Part 1. Then say in one sentence
what kind of question would make you choose the other one.

For monthly regional rankings, I would definitely go with the fixed workflow. From my Part 1 tests on the 15 fixed-shape questions, the workflow got 13 correct, 0 wrong, and 2 no answer, using 15 model calls, 3,852 tokens, and 16.4 seconds. Meanwhile, the bare agent on the same questions only got 9 correct, 2 wrong, and 4 no answer, but took 26 model calls, 19,835 tokens, and 47.6 seconds. The workflow is way faster, uses way fewer tokens, and doesn't get stuck in loops because ranking queries have a fixed structure. I'd only use an agent if the tasks were open-ended and unpredictable.

---

I wrote this file myself, without any AI tool.
