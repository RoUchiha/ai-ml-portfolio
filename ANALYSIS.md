# Project Deep-Dives (in plain English)

A friendly, jargon-light walkthrough of the six LLM-infrastructure projects in this
portfolio — what problem each one solves, how it actually works, the clever bit, and
where it falls short. No prior AI knowledge needed.

**Quick vocabulary** (used throughout):
- **LLM** = Large Language Model — the AI behind ChatGPT/Claude. You send it text, it sends text back. Each call costs money and takes time.
- **Token** = a chunk of text (~¾ of a word). Models are billed per token.
- **Embedding** = a list of numbers that captures the *meaning* of a piece of text, so a computer can measure how similar two texts are.
- **Prompt** = the text you send the model.

---

## 🛡️ 1. Text-to-SQL with Guardrails

**The problem.** You want non-technical people to ask questions like *"who were our top 5 customers last month?"* and get answers from the company database — without learning SQL (the database query language). The easy way is to let an LLM translate English into SQL and run it. The terrifying part: an LLM can be tricked (or just make a mistake) and write `DROP TABLE customers` — which **deletes your data forever**.

**The analogy.** It's a translator standing between a guest and a bank vault. The translator (LLM) turns requests into vault instructions. But you don't trust the translator blindly — you put a **security guard** at the vault door who physically refuses anything except "look, don't touch," no matter what the translator says.

**How it works, step by step.**
1. The user asks a question in English.
2. The LLM turns it into a SQL query.
3. **The guard inspects the query before it runs.** It doesn't read the SQL as text — it parses it into a *structure* (a tree of what the query actually does) and checks that tree against rules: only reading is allowed, only one command at a time, only real tables/columns, etc.
4. If anything looks destructive, it's **blocked** with a clear reason. If it's safe, it runs in a "read-only" mode that physically can't change data, and only returns a limited number of rows.

**The clever bit.** Most naive versions search the SQL text for bad words like "DROP". Attackers defeat that easily (`DR/**/OP`, hidden second commands, etc.). This project analyzes the *parsed structure* of the query instead — so obfuscation tricks don't work. And the guard runs **completely separately from the LLM**, so even a fully "jailbroken" model can't talk its way past it. It's tested against 26 different attacks and blocks 100% of them; if even one ever slips through, the test suite fails the build.

**Where you'd use it.** Internal "chat with your data" tools, customer-facing analytics, any place an AI touches a real database.

**Honest limits.** It defaults to read-only; enabling writes is possible but adds risk. The "is this a real table/column?" check needs an accurate map of your database.

---

## 💸 2. LLM Cost Router

**The problem.** There are cheap, fast AI models and expensive, smart ones (often 20× the price). Most apps lazily send *everything* to the expensive model — including trivial questions a cheap model handles perfectly. That's like taking a taxi to your mailbox.

**The analogy.** A smart dispatcher at a help desk. Simple questions go to a junior agent (cheap); hard ones go to a senior (expensive). If the junior gives a weak answer, the dispatcher escalates it to the senior — but only then.

**How it works, step by step.**
1. A request comes in. A quick, rule-based **classifier** judges how hard it is (trivial / simple / hard) — by looking at length, whether it asks for reasoning or code, etc.
2. It **routes** to the cheapest model that can handle that difficulty.
3. Optionally it **checks the answer's quality**; if it looks weak, it **escalates once** to a stronger model.
4. It records what was spent versus what the always-expensive approach *would* have cost, and reports the savings.

**The clever bit.** The savings number is **honest**. When it escalates, it counts the cost of *both* calls (the failed cheap one and the retry) — so it never flatters itself by hiding waste. The "what it would have cost" baseline is stated openly, not fudged. It also ships as a drop-in proxy that speaks the standard OpenAI format, so existing apps can use it by changing one URL.

**Where you'd use it.** Any product making lots of LLM calls — the routing typically cuts 40–60% of spend with no quality loss on the easy stuff.

**Honest limits.** The difficulty classifier is a fast heuristic, not perfect; token counts are estimates, not billing-exact. Tuning the tiers to your traffic matters.

---

## 🔁 3. Multi-Agent Critique Loop

**The problem.** An AI's first answer is rarely its best. Asking it once and shipping that is leaving quality on the table.

**The analogy.** A writer with a panel of editors. The writer drafts something; the editors (one checks facts, one checks clarity, one checks safety) mark it up in parallel; the writer revises. Repeat until the panel is satisfied — or you run out of patience.

**How it works, step by step.**
1. A **generator** writes a first draft.
2. Several **critics** review it *at the same time* against a scoring rubric, each returning a score plus specific issues and suggestions.
3. An **aggregator** merges their feedback into one consensus score and a de-duplicated, prioritized issue list.
4. If the score clears the bar, stop. Otherwise the generator **revises** using the feedback, and the loop repeats (up to a max number of rounds).
5. You get the best version, the full history, and the score climbing round over round.

**The clever bit.** The critics run **concurrently** (so three reviews cost roughly one review's worth of waiting). The consensus uses the *average* score, so one harsh critic can't permanently block progress but every critic still counts. There's also an optional **human approval** step before accepting a final answer, and the project is honest that this quality gain costs more tokens (more calls per answer) — worth it for high-stakes output, wasteful for trivial.

**Where you'd use it.** Generating important content — legal summaries, customer comms, code — where one extra round of self-review materially improves the result.

**Honest limits.** More rounds = more cost and latency. Critics are only as good as their rubric.

---

## ⚡ 4. Semantic Cache

**The problem.** Users ask the same thing in slightly different words all day: *"How do I reset my password?"* and *"I forgot my login — how do I get back in?"* A normal cache only recognizes **exact** repeats, so it re-pays the LLM for every reworded duplicate.

**The analogy.** A support rep with a great memory. If someone asks a question that *means the same thing* as one they answered an hour ago, they reuse the answer instead of researching it again from scratch.

**How it works, step by step.**
1. A new prompt comes in. First, a **fast exact check** — if it's word-for-word identical (after tidying spaces/casing) to a stored one, return instantly.
2. If not, turn the prompt into an **embedding** (its meaning as numbers) and find the closest stored prompt.
3. If that closest match is **similar enough** (above a strict threshold), serve its saved answer — no LLM call, no cost.
4. Otherwise it's a miss: call the LLM, save the new answer, and remember it (with an expiry date and a size limit so the cache doesn't grow forever).

**The clever bit.** The danger of a semantic cache is a **wrong** reuse — answering a *different* question with a cached answer because the meanings looked close. So the similarity bar is set deliberately high, and every "almost matched" case is logged so you can tune the threshold using real data instead of guessing. The exact-match path is also proven (by a test) to skip the expensive embedding step entirely — a real speed win.

**Where you'd use it.** Chatbots and Q&A products with repetitive traffic — it can eliminate a large fraction of redundant calls.

**Honest limits.** Set the bar too low and you serve wrong answers; too high and you rarely save. It's a tunable trade-off, not a free lunch — which is exactly why the calibration log exists.

---

## 🔎 5. Hybrid RAG with Verified Citations

**The problem.** "RAG" means giving an LLM your documents so it can answer from them instead of from memory (which it makes up). Two things go wrong: (1) it can't find the right document, and (2) it writes a confident answer with a **fake citation** — pointing to a source that doesn't actually say what it claims.

**The analogy.** A research assistant who must (a) search both by *topic* and by *exact keywords* to find sources, and (b) show you the exact highlighted sentence backing every claim — and if they can't, that claim gets crossed out.

**How it works, step by step.**
1. Your documents are split into chunks, each with a stable ID.
2. A question triggers **two searches at once**: a *meaning* search (embeddings) and a *keyword* search (BM25, the classic search-engine method).
3. The two result lists are **fused** into one ranking (a method called Reciprocal Rank Fusion).
4. The LLM writes an answer, tagging each sentence with the chunk ID(s) it used.
5. A **verifier** checks every citation: does that chunk actually exist in what we retrieved, and does it really support the sentence? Unsupported or invented citations are **stripped out**.
6. If nothing relevant was found, it says *"insufficient evidence"* instead of inventing an answer.

**The clever bit.** Meaning-search and keyword-search fail in *opposite* ways — embeddings miss exact terms like error codes or IDs; keywords miss paraphrases. Combining them catches both. And the citation verifier is the trust layer: it's the difference between an answer that *sounds* sourced and one that *is* sourced. Same philosophy as the SQL guard — verify, don't trust.

**Where you'd use it.** "Chat with your docs," internal knowledge bases, support assistants — anywhere a wrong-but-confident answer is dangerous.

**Honest limits.** Verification quality depends on the similarity threshold; very tricky multi-hop questions (needing several documents combined) are harder.

---

## 🤖 6. Agent Orchestration

**The problem.** A demo "AI agent" that uses tools is easy. A *production* one is hard, because real life intrudes: some actions need human sign-off, the process can crash halfway, and the agent needs to remember things across sessions.

**The analogy.** A capable assistant with a notebook, a manager's approval for risky moves, and the discipline to write down every step so that if they're interrupted, they can pick up exactly where they left off — without redoing what's already done.

**How it works, step by step.**
1. **Plan** — break the task into steps.
2. **Act** — pick a tool (calculator, search, etc.) and use it.
3. **Approve** — if the tool is *sensitive* (e.g., writing a file, sending a message), pause for **human approval**. A "no" means it never runs.
4. **Observe** — record the result into short-term memory; save important facts to long-term memory.
5. **Reflect** — decide whether to continue, try something else, or finish.
6. **Persist** — after every step, save the full state to disk.
7. **Stop** — when the goal is met, or limits (max steps / budget) are hit.

**The clever bit.** Two production-grade details. **Memory has two layers:** a short-term working memory that stays small by summarizing old stuff, and a durable long-term memory that survives restarts and is recalled by meaning. And **resuming is safe:** the agent's next move is determined purely by *how many steps it has already taken*, so restarting a crashed run continues from the exact point of failure — it never re-sends an email or re-writes a file it already did. That "no duplicate side-effects" guarantee is proven by a test.

**Where you'd use it.** Multi-step automation where reliability and oversight matter — anything that takes real actions on a user's behalf.

**Honest limits.** The included planner is intentionally simple; the demo uses safe stand-in tools. The "budget" is a basic call-count proxy (a real token/dollar budget would slot into the same place).

---

## The thread running through all six

Notice the repeated idea: **don't trust the model — verify its output with something the model can't override.** The SQL guard, the citation verifier, the cache's strict threshold, the agent's approval gate — all are independent safety layers around a fundamentally unpredictable component. That's what separates a tech demo from infrastructure you'd actually run in production.

Each project is independently installable, fully tested offline (no API keys needed), and has a [live demo](https://huggingface.co/spaces/rosingh/ai-ml-portfolio-demos).
