---
layout: default
report: true
title: "Best languages to build in with Claude Code"
date: "2026-11-11"
summary: "Which language Claude Code writes best (Python, TypeScript, Go, Rust, C, C++) and whether to build backend then frontend. With current Claude models Python's lead has narrowed to a few points but not disappeared; the check Claude can run matters more than the language, and the right build order is feature by feature, not layer by layer."
---

*Published 2026-11-11. Every factual claim links to the source it came from. [All research](https://pete-builds.github.io/research/)*

## TL;DR

Current Claude models score high across the nine languages Anthropic tests, and Python's lead has narrowed without disappearing: Claude Opus 5 scores 96.0% on the Python-only SWE-bench Verified against 89.5% across nine other languages (a 20-point gap in 2025, 6.5 points now), though non-Python tasks still fail about 2.6 times as often, and Opus 5.5 reaches 93.9% on the nine-language set. What still decides the outcome is whether Claude has a pass/fail check it can run on its own, one of the headline points of Anthropic's Claude Code best-practices page. For a builder who does not read code: keep Python for backends, MCP servers and scripts, TypeScript for anything with a UI, and Go where it already lives; use Rust when speed or small binaries matter, and skip C and C++ unless a platform requires it. Do not build the whole backend and then the whole frontend: write a spec, then build one user-visible feature at a time through both layers, checked in a real browser.

## Current Status

- Current Claude models score 89.5% (Opus 5) to 93.9% (Opus 5.5) on SWE-bench Multilingual, 300 problems across nine programming languages, per Anthropic's July to September 2026 system cards. These are Anthropic's own runs; no independent replication was found (High that Anthropic reports it; Medium that independent runs would match).
- The Python edge has shrunk in points but not in failures: Opus 5 scores 96.0% on Python-only SWE-bench Verified against 89.5% on Multilingual, a 6.5-point gap, where Claude 3.7 Sonnet in 2025 showed 63% against 43%, a 20-point gap. Because Python is near the ceiling, non-Python tasks now fail 2.6 times as often (10.5% against 4.0%), up from 1.5 times (57% against 37%) (Medium: two different benchmarks, different harnesses).
- Anthropic publishes no per-language split for any current model. The newest Claude per-language data found is Sonnet 4.6 on contest problems: solve rates of 92% to 96% across Python, Java, Rust and OCaml, with Rust costing 1.16 times Python's output tokens once difficulty is controlled for (Medium).
- The detailed language rankings (Rust strong in an agent loop, C/C++ and TypeScript weakest) come from older models, Claude 3.5 Sonnet through Opus 4.1 plus some non-Claude models. They explain where "Python first" advice came from, but they are history, not current scores.
- GitHub's October 2026 data shows TypeScript, Python and Rust with the highest share of AI-involved merged PRs (about 40% to 44%), Java, C# and C++ lower (about 21% to 34%), and repos with agent activity skewing toward TypeScript.

## Table of Contents

- [TL;DR](#tldr)
- [Current Status](#current-status)
- [Table of Contents](#table-of-contents)
- [Findings](#findings)
  - [What current Claude models score](#what-current-claude-models-score)
  - [Token cost by language](#token-cost-by-language)
  - [What Anthropic says](#what-anthropic-says)
  - [Existing tooling in Claude Code](#existing-tooling-in-claude-code)
  - [Language by language](#language-by-language)
  - [C and Rust](#c-and-rust)
  - [Backend then frontend?](#backend-then-frontend)
  - [What to do](#what-to-do)
  - [History: per-language results from 2025 models](#history-per-language-results-from-2025-models)
- [Confidence Assessment](#confidence-assessment)
- [Open Questions](#open-questions)
- [Sources](#sources)
- [Update History](#update-history)

## Findings

### What current Claude models score

The Claude 4.8 and 5-family figures below are from Anthropic's own system cards; the Opus 5.5 card describes its runs as "adaptive thinking at max effort, default sampling settings (temperature, top_p), averaged over five trials" ([source](https://www-cdn.anthropic.com/fc1b44717c85dc068bc6ba5024219938094694bd/Claude%20Opus%205.5%20System%20Card.pdf)). The Claude 3.7 Sonnet row is from the SWE-bench site's own SWE-agent run, a different setup.

| Model | Card | SWE-bench Multilingual (9 languages) | SWE-bench Pro | SWE-bench Verified (Python) |
|---|---|---|---|---|
| Claude Opus 5.5 | 2026-09-22 | 93.9 | 89.9 | not reported |
| Claude Sonnet 5.5 | 2026-09-28 | 90.3 | 81.3 | not reported |
| Claude Fable 5.1 | in the Opus 5.5 card | 89.1 | 81.2 | not reported |
| Claude Opus 5 | 2026-07-24 | 89.5 | 79.2 | 96.0 |
| Claude Opus 4.8 | in the Opus 5 card | 84.4 | 69.2 | not reported |
| Claude Sonnet 5 | in the Sonnet 5.5 card | 78.3 | 63.2 | not reported |
| Claude 3.7 Sonnet, for scale (2025) | SWE-bench site | 43 | not reported | 63 |

Sources for the table: the Opus 5.5 card states "SWE-bench Multilingual extends the format to 300 problems across nine programming languages. Opus 5.5 achieved 93.9%" and lists Opus 5 at 89.5 and Fable 5.1 at 89.1 ([source](https://www-cdn.anthropic.com/fc1b44717c85dc068bc6ba5024219938094694bd/Claude%20Opus%205.5%20System%20Card.pdf)). The Sonnet 5.5 card: "Sonnet 5.5 achieved 90.3%", with Sonnet 5 at 78.3 ([source](https://www-cdn.anthropic.com/870c8f525702625d2c62fc6dd04c857e3250bec1/Claude%20Sonnet%205.5%20System%20Card.pdf)). The Opus 5 card: "SWE-bench Verified is a 500-problem subset, each verified by human engineers as solvable. Claude Opus 5 achieved 96.0%" and "Claude Opus 5 achieved 89.5%" on Multilingual, with Opus 4.8 at 84.4 ([source](https://www-cdn.anthropic.com/b514064af1408018e64b1ad24e7d5e75850b4ffd/Claude%20Opus%205%20System%20Card.pdf)). SWE-bench Verified is Python: "The benchmark dataset consists of 500 GitHub issues from 17 different Python projects", and Claude 3.7 Sonnet "achieves a 43% resolution rate on SWE-bench Multilingual, compared to 63% on SWE-bench Verified" ([source](https://www.swebench.com/multilingual.html)).

What this means:

- **The point gap is small now; the failure gap is not.** For Opus 5, Python-only Verified and nine-language Multilingual differ by 6.5 points; for Claude 3.7 Sonnet the same pair differed by 20. But Verified at 96.0 is near its ceiling, so measured as failures, non-Python tasks fail 10.5% of the time against 4.0% for Python, 2.6 times as often, where in 2025 it was 57% against 37%, 1.5 times (arithmetic from the published figures). The two benchmarks also differ in difficulty and the runs used different harnesses, so read this as direction (Medium).
- **At the top, few problems are left.** Opus 5.5's 93.9% on 300 problems leaves about 18 unsolved on average, across all nine languages combined (arithmetic from the published figure). Whether those misses cluster in one language is unknown, because no per-language split is published.
- **These are vendor numbers.** Scale's independent SWE-bench Pro leaderboard, run with the mini-swe-agent harness, tops out at 61.50 (Muse Spark 1.1), shows "claude-opus-4-6 (thinking)" at 51.90, and lists no Claude 5 model ([source](https://labs.scale.com/leaderboard/swe_bench_pro)). Anthropic's 89.9 for Opus 5.5 on SWE-bench Pro and Scale's figures come from different harnesses and settings and are not directly comparable.

### Token cost by language

The newest Claude model with per-language results found is Sonnet 4.6, in a July 2026 study of 2,000 agent runs on contest problems (not real apps). Its main finding is that "the choice of programming language has a significant effect on output tokens produced by the agent, even after carefully controlling for problem difficulty"; on accuracy, "accuracy on MiniLCB is not affected very much by language, with the notable exception that Gemma performs much worse on OCaml" ([source](https://arxiv.org/html/2607.22807)). For Sonnet 4.6, with problem difficulty controlled for, Rust cost 1.16 times Python's output tokens and Java 1.18 times, and Sonnet's ever-solved rate was 92% in Python against 96% in Rust ([source](https://arxiv.org/html/2607.22807)). The raw budget for Sonnet points the other way: total output tokens per solved problem were 6,099 in Python, 4,795 in Java and 4,534 in Rust ([source](https://arxiv.org/html/2607.22807)). Across the five models tested, the Rust and Java penalty "is model-dependent, but not significant for Gemma"; the authors still conclude "Models are most efficient when they work in Python", and note that "agents repeatedly produce noncompiling solutions in unfamiliar languages" ([source](https://arxiv.org/html/2607.22807)). The training-data gap behind this: "The Stack v2 has around 200GB of Java and of Python, but only 12GB of Rust" ([source](https://arxiv.org/html/2607.22807)).

### What Anthropic says

Anthropic's Claude Code best-practices page does not rank languages. Its stance is that Claude needs something that returns pass or fail, whatever the language. "Claude stops when the work looks done. If it has nothing to run that tells it whether the work actually is done, "looks done" is all it has to go on. In practice that makes you the test suite" ([source](https://code.claude.com/docs/en/best-practices)). The checks it names are a test file, a build's exit code, a linter, a script that diffs output against a fixture, and a browser screenshot next to the mockup: "If you give it something that comes back pass or fail, Claude can close that loop itself" ([source](https://code.claude.com/docs/en/best-practices)).

Writing "always typecheck" in CLAUDE.md is not enough. The page says an instruction there "works most of the time, not every time", and recommends a Stop hook instead, where "the turn doesn't end until the typecheck passes or the block limit is reached" ([source](https://code.claude.com/docs/en/best-practices)). Its worked example is a TypeScript check, `pnpm tsc --noEmit 1>&2 || exit 2` ([source](https://code.claude.com/docs/en/best-practices.md)).

The one place the page leans toward typed languages is navigation: "if you work in a typed language, a code intelligence plugin lets Claude jump straight to a definition instead of reading through many files to find it" ([source](https://code.claude.com/docs/en/best-practices)).

### Existing tooling in Claude Code

Nothing needs building here. Two mechanisms already exist.

**Code intelligence (LSP) plugins.** Anthropic publishes them for C/C++ (clangd), C#, Go, Java, Kotlin, Lua, PHP, Python (pyright), Ruby, Rust (rust-analyzer), Swift, and TypeScript/JavaScript; "Anthropic maintains every plugin in the table except `liquid-lsp`, which Shopify maintains" ([source](https://code.claude.com/docs/en/plugins/code-intelligence.md)). With one installed, "Claude catches type errors and missing imports that its own edits introduce before you run your build" ([source](https://code.claude.com/docs/en/plugins/code-intelligence.md)). Two limits: "It doesn't include the language server", which has to be installed separately, and "In cloud sessions, Claude Code doesn't start plugin language servers" ([source](https://code.claude.com/docs/en/plugins/code-intelligence.md)). A practitioner also reports that an agent "just won't run the LSP" at times "because it's also lazy and will skip that step if it doesn't have to" ([source](https://lucumr.pocoo.org/2026/2/9/a-language-for-agents/)), which is why the Stop hook matters more than the plugin.

**Stop hooks.** The enforced check described above. This is the mechanism that holds when nobody reads the diff.

### Language by language

**Python: keep it for backends, MCP servers, scripts and data work (Medium).** It had the clearest lead in 2025 and still leads with current models (Opus 5: 96.0 on Python-only Verified against 89.5 across nine languages, with non-Python tasks failing about 2.6 times as often), and it was the cheapest language once problem difficulty was controlled for, though not in Sonnet 4.6's raw per-solve budget (Token cost, above). The caveat is runtime behavior the code itself doesn't show: "Agents struggle with Python's magic (eg: Pytest's fixture injection) or complex runtime challenges (eg: wrong event loop when working with async)" ([source](https://lucumr.pocoo.org/2025/6/12/agentic-coding/)). A static type checker (pyright has an official plugin) plus tests run by a Stop hook is the guard.

**TypeScript: use it for anything with a UI (Medium).** No current per-language score exists. The 2025 benchmarks ranked it near the bottom (see History). The 2026 evidence points the other way. Practitioner Armin Ronacher writes: "I'm now routinely reaching for JavaScript in places where I would have used Python. Not because I love it or the ecosystem is better, but because the agent does much better with TypeScript" ([source](https://lucumr.pocoo.org/2026/2/9/a-language-for-agents/)). GitHub found TypeScript "was the primary language in 35% of those with an observed agent signal, compared to 24% without one" ([source](https://github.blog/news-insights/octoverse/octoverse-commits-pushes-reach-new-highs-in-2026-as-developers-build-with-agents/)). On LLM-written TypeScript (small open models, HumanEval and MBPP tasks), "on average 94% of compilation errors result from failing type checks" ([source](https://arxiv.org/pdf/2504.09246)). The catch: "in TypeScript you can often run the code even though it fails type checks. That can gaslight the agent" ([source](https://lucumr.pocoo.org/2026/2/9/a-language-for-agents/)). This is exactly the case Anthropic's `tsc --noEmit` Stop hook example covers.

**Go: fine where it already lives, no reason to migrate toward or away (Medium).** One practitioner strongly recommends it: "if you can choose your language, I strongly recommend Go for new backend projects" ([source](https://lucumr.pocoo.org/2025/6/12/agentic-coding/)), and in 2026 the same author still calls Go "really good here: it forbids circular dependencies between packages (import cycles), packages have a clear layout, and test results are cached" ([source](https://lucumr.pocoo.org/2026/2/9/a-language-for-agents/)). The 2025 evidence is mixed: Go was in the bottom half on SWE-bench Multilingual and Multi-SWE-bench, while Scale's SWE-bench Pro notes put Go with Python among the higher resolution rates (see History). No current per-language score exists to settle it.

**Rust: when speed or a small binary is the point (Medium).** Covered in [C and Rust](#c-and-rust) below.

**C++: skip unless the target demands it (Low, thinnest evidence).** No current per-language score exists, and the 2025 scores were mixed (see History). In GitHub's 2026 data, TypeScript, Python and Rust show "about 40% to 44% of merged pull requests" with AI involvement, while "Java, C#, and C++ sit lower, between about 21% and 34%" ([source](https://github.blog/news-insights/octoverse/octoverse-commits-pushes-reach-new-highs-in-2026-as-developers-build-with-agents/)). In general practice, which no opened source measured for C++ agents, the C++ compiler does not catch memory bugs the way Rust's does, so a passing build proves less; the same problem in C is measured in [C and Rust](#c-and-rust).

**Security does not come with the language.** A vendor study of 100+ models (July 2025) found Java "the riskiest language for AI code generation, with a security failure rate over 70 percent", with Python, C# and JavaScript "between 38 percent and 45 percent", and "larger models do not perform significantly better than smaller models" ([source](https://www.veracode.com/press-release/ai-generated-code-poses-major-security-risks-in-nearly-half-of-all-development-tasks-veracode-research-reveals/)) (Medium, single source, older models).

### C and Rust

A common argument holds that systems languages are now the better choice for AI coding: the model writes the hard parts, so you get speed and small binaries without the human cost. One practitioner describes the trend more cautiously: "LLMs make language choice much less consequential than it used to be", and projects that "want to be fast and small" increasingly "pick "hard languages"", naming Rust and Zig, not C ([source](https://lucumr.pocoo.org/2026/8/22/fast-hard-code/)). The evidence splits between the two: Rust's compiler checks more of the agent's work than C's does, though not all of it.

**Rust: the compiler does the reviewing (Medium).**

- **It turns errors into feedback.** Rust refuses to build code with whole classes of memory and type errors, and the agent can read the error and retry. On C-to-Rust translation, Claude Opus 4's build pass rate went from 43 to 78 once compiler errors were fed back, and its borrow-checker error share fell from 29 to 3, but its test pass rate rose only from 22 to 29, so most code that now compiled still failed its tests ([source](https://arxiv.org/html/2504.15254v3)). With two rounds of feedback (no Claude tested), "Rust shows the largest improvement", which the authors attribute to its weak first attempts and because "Rust's compile-time diagnostics are detailed and explicit" ([source](https://arxiv.org/html/2607.13820v1)).
- **In an agent loop it did well; without one it does worst.** In 2025, SWE-agent with Claude 3.7 Sonnet "had the highest resolution rate in Rust" on SWE-bench Multilingual ([source](https://www.swebench.com/multilingual.html)), though Multi-SWE-bench results depended on which agent ran it (see History). In one-shot generation with no feedback (no Claude tested), "Rust emerging as the most challenging language" and "Rust shows the highest proportion of non-compilation outcomes" ([source](https://arxiv.org/html/2607.13820v1)).
- **The newest Claude data shows no accuracy penalty and a modest cost.** Sonnet 4.6's ever-solved rate on contest problems was 96% in Rust against 92% in Python, at 1.16 times Python's output tokens once difficulty is controlled for (see Token cost by language).
- **Less training data, plus a tooling snag.** "The Stack v2 has around 200GB of Java and of Python, but only 12GB of Rust" ([source](https://arxiv.org/html/2607.22807)). A practitioner adds: "In Rust, agents sometimes fail because they misunderstand `cargo test`'s invocation syntax" ([source](https://lucumr.pocoo.org/2025/6/12/agentic-coding/)).
- **Use is climbing.** Rust "grew fastest by percentage among the top languages, doubling in the past year", and sits with TypeScript and Python in GitHub's highest band of AI-involved merged PRs, "about 40% to 44%" ([source](https://github.blog/news-insights/octoverse/octoverse-commits-pushes-reach-new-highs-in-2026-as-developers-build-with-agents/)).

**C: a passing build proves little (Low).**

- **No current score.** C is one of the nine languages inside the current SWE-bench Multilingual averages, but no per-language split is published.
- **The benchmarks disagree.** On SWE-bench Multilingual, Claude 3.7 Sonnet with SWE-agent resolved 8 of 30 C tasks, the lowest of any language group: 5 of 12 in redis, 2 of 9 in jq, 1 of 4 in valkey and 0 of 5 in micropython ([source](https://www.swebench.com/multilingual.html)). Multi-SWE-bench puts C mid-pack: 8.59 with two of the three agents and 2.34 with the third, above JavaScript under all three, and its authors list Claude 3.7 Sonnet among models with "relatively strong performance, particularly in languages like Python, Java, and C" ([source](https://arxiv.org/html/2504.02605v1)). In one-shot generation, "Python consistently yields the highest pass rates, followed by C++ and Java at similar levels, then C, with Rust emerging as the most challenging language" ([source](https://arxiv.org/html/2607.13820v1)).
- **A passing build proves little.** In a 2026 study of AI-written C (no Claude, an education setting), models "achieve high Compilation Success Rates" yet dynamic analysis found "a frequent incidence of 'Definitely Lost' heap memory" ([source](https://proceedings.mlr.press/v318/jarrin26a.html)) (Low). The check loop that rescues Rust has nothing to catch here, because the C compiler accepts the bug.
- **If C is required, make the check catch memory errors.** "AddressSanitizer is a fast memory error detector" that finds out-of-bounds accesses, use-after-free, double-free and, experimentally, memory leaks; "Typical slowdown introduced by AddressSanitizer is 2x". Leak detection "is turned on by default on Linux, and can be enabled using ASAN_OPTIONS=detect_leaks=1 on macOS" ([source](https://clang.llvm.org/docs/AddressSanitizer.html)). Running the test suite under it in a Stop hook gives Claude the failing signal the compiler does not (a recommendation, not a measured result).

**C++ sits between them:** level with Java behind Python on one-shot problems ([source](https://arxiv.org/html/2607.13820v1)), with the same memory-safety gap as C (see Language by language).

**Verdict.** Rust is a reasonable pick whenever speed or a single small binary matters, with `cargo build` and `cargo test` in the Stop hook. C or C++ only when the platform requires it, and then with the tests run under AddressSanitizer, leak detection included.

### Backend then frontend?

No source tests backend-first against frontend-first. Anthropic's harness experiments (its posts name Sonnet 4.5, Opus 4.5 and Opus 4.6) and its current docs (written for the Opus 5.5 era) point to a third option: one feature at a time, through every layer.

- **Unsplit, Claude tries to build everything at once.** "the agent tended to try to do too much at once—essentially to attempt to one-shot the app" ([source](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)).
- **The fix was a list of user-level features, built one at a time.** The example feature crosses UI and backend: "a user can open a new chat, type in a query, press enter, and see an AI response", and the agent was "asked to work on only one feature at a time. This incremental approach turned out to be critical" ([source](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)).
- **Backend-only checks missed broken features.** Claude, after unit tests or curl calls, "would fail recognize that the feature didn't work end-to-end", and it "mostly did well at verifying features end-to-end once explicitly prompted to use browser automation tools and do all testing as a human user would" ([source](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)). That is the specific risk of finishing the backend first and checking it alone.
- **Frontend-first has the opposite risk.** In a 2026 study (Qwen models, no Claude), agents "tend to only generate frontend web pages, masking the lack of real full-stack data processing and storage with fancy visual effects", even "with explicit requests to generate the backend" ([source](https://arxiv.org/pdf/2602.03798)) (Medium).
- **Spec first, but not a detailed technical spec.** Anthropic's later three-agent harness kept the planner on "product context and high level technical design", because "if the planner tried to specify granular technical details upfront and got something wrong, the errors in the spec would cascade" ([source](https://www.anthropic.com/engineering/harness-design-long-running-apps)). Without the planner, "the generator under-scoped" ([source](https://www.anthropic.com/engineering/harness-design-long-running-apps)). The current docs give the concrete version: "Interview me until you could write the spec, then write it to SPEC.md", then "start a fresh session to build from it" ([source](https://code.claude.com/docs/en/best-practices.md)).
- **A mixed stack works.** Anthropic's own showcase app was built "with a React, Vite, FastAPI, and SQLite (later PostgreSQL) stack" ([source](https://www.anthropic.com/engineering/harness-design-long-running-apps)), a Python backend with a JavaScript frontend. That shows it works, not that it beats a single-language stack.
- **Practitioner opinion matches.** "If your tasks are organized by layer, the agent will build horizontally... Structure your tasks as user stories and the agent works vertically by default" ([source](https://www.danvega.dev/blog/stop-letting-ai-build-horizontally)). An article on martinfowler.com agrees "the general principle of spec-first is definitely valuable", but is "very skeptical that lots of up-front spec design is a good idea" ([source](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)).
- **API-contract-first is untested.** A vendor argues frontend and backend "are often generated in separate sessions" with "no automatic mechanism to enforce API consistency" without OpenAPI or contract tests ([source](https://www.augmentcode.com/guides/why-ai-coding-agents-fail-e2e-tests)) (Low to Medium, vendor content, no measurements).

### What to do

These follow from the findings above; they are recommendations, not sourced facts.

1. **Use the mainstream languages you already have.** Python for backends, services and scripts, TypeScript for web front ends, Go where it already runs. With current models the language gap is a few points, still in Python's favor; nothing found justifies a rewrite.
2. **Turn on code intelligence for the languages you use:** for example the pyright, TypeScript and Go plugins, plus their language servers, which the plugins do not install.
3. **Put an enforced check in every app repo** as a Stop hook: `tsc --noEmit` for TypeScript repos, pyright plus the test suite for Python. This is the change that matters most for someone who does not read the diff.
4. **Build in slices, not layers.** Have Claude interview you into a SPEC.md of user-visible features, then build one feature end to end per session and require a browser check before calling it done.
5. **Reach for Rust when there is a reason** (speed, a single small binary); on the newest Claude data (Sonnet 4.6) the cost is modest. Skip C and C++ unless the hardware or platform requires it, and if you use either, run the tests under AddressSanitizer.

### History: per-language results from 2025 models

This section collects per-language results from older models: Claude 3.5 Sonnet through Claude Opus 4.1, plus some non-Claude models, all well behind the current Claude 5 family. It is kept because it is where the "Python first, avoid Rust and C++" advice came from, and because no per-language split has been published for any Claude 5 model. Read it as history, not as current scores.

| Benchmark | Python | TypeScript / JS | Go | Rust | C / C++ | Java |
|---|---|---|---|---|---|---|
| SWE-bench Multilingual, Claude 3.7 Sonnet + SWE-agent, % resolved | not in this set | 34.88 | 30.95 | 58.14 | 28.57 (combined) | 53.49 |
| Multi-SWE-bench, Claude 3.7 Sonnet + MopenHands, % resolved | 52.20 | 2.23 / 5.06 | 7.48 | 15.90 | 8.59 / 14.73 | 21.88 |

Sources for the table: SWE-bench Multilingual rows "| Rust | 25 | 18 | 43 | 58.14% |" and "| C/C++ | 12 | 30 | 42 | 28.57% |" ([source](https://www.swebench.com/multilingual.html)); Multi-SWE-bench row "| Claude-3.7-Sonnet | 52.20 | 21.88 | 2.23 | 5.06 | 7.48 | 15.90 | 8.59 | 14.73 |" in the column order Python, Java, TS, JS, Go, Rust, C, C++ ([source](https://arxiv.org/html/2504.02605v1)).

- **Python led.** The SWE-bench Multilingual maintainers concluded "LLMs are more proficient in Python than other languages" ([source](https://www.swebench.com/multilingual.html)). Multi-SWE-bench agreed, though its authors note the agent methods "are initially optimized for Python" ([source](https://arxiv.org/html/2504.02605v1)). SWE-PolyBench (Claude 3.5 era): "All agents demonstrate their strongest performance in Python", and "Performance in Java () and particularly TypeScript () shows significant room for improvement", with the score values lost in the page's rendering ([source](https://arxiv.org/html/2504.08703v1)). Scale's notes on its SWE-bench Pro runs with GPT-5, Claude Opus 4.1 and older models say "Go and Python tasks generally have higher resolution rates", while JavaScript and TypeScript were "more varied and often lower" ([source](https://labs.scale.com/leaderboard/swe_bench_pro)).
- **Rust could be strong inside an agent loop.** SWE-agent "had the highest resolution rate in Rust" ([source](https://www.swebench.com/multilingual.html)). Multi-SWE-bench was less clear: Rust was third for Claude 3.7 Sonnet with MopenHands (15.90), but scored 6.69 and 5.44 with the paper's other two agents ([source](https://arxiv.org/html/2504.02605v1)). The compiler-feedback evidence is in [C and Rust](#c-and-rust).
- **Training data mattered.** Older work found models "struggle with low-resource languages that have limited training data available" ([source](https://arxiv.org/abs/2308.09895)). Rust's one-shot and compiler-feedback results are in [C and Rust](#c-and-rust).
- **C was weaker than C++ on SWE-bench Multilingual.** Its 28.57% is C and C++ combined; C++ alone resolved 4 of 12 tasks (fmtlib/fmt 3 of 11, nlohmann/json 1 of 1) ([source](https://www.swebench.com/multilingual.html)). C's per-repo results are in [C and Rust](#c-and-rust).
- **Among popular languages, frontier models were already close.** AutoCodeBench found average scores for popular languages "ranging from 50.4 to 53.8" across models, with the gap widening only on low-resource languages, where "Claude Opus 4 outperforms other models significantly" ([source](https://arxiv.org/html/2508.09101v1)).

## Confidence Assessment

| Claim | Confidence | Why |
|---|---|---|
| Anthropic reports 89.5% to 93.9% for current Claude models on nine-language SWE-bench Multilingual | High (documentary) | Verbatim in three Anthropic system cards, which agree on each other's SWE-bench figures |
| Independent runs would reproduce those scores | Medium | Single publisher; no independent leaderboard lists Claude 5 models yet |
| The Python edge has shrunk from about 20 points to about 6.5, while the failure ratio rose from 1.5 to 2.6 times | Medium | Arithmetic from published figures; compares two different benchmarks run on different harnesses, and Verified is near its ceiling |
| Give Claude a pass/fail check it can run itself | High | Anthropic's best-practices page, and independently Ronacher: "In an ideal world the agent has one command, that lints and compiles and it tells the agent if all worked out fine" ([source](https://lucumr.pocoo.org/2026/2/9/a-language-for-agents/)) |
| Enforce the check with a Stop hook rather than CLAUDE.md text | Medium | One source, Anthropic's best-practices page |
| Accuracy barely varies by language for Claude Sonnet 4.6 | Medium | One study, contest problems, not repo-level work |
| Agents spend the fewest tokens in Python, more in Rust and Java | Medium | One controlled study, contest problems only; for Sonnet 4.6 the difficulty-controlled Rust penalty was 1.16 times, but its raw tokens per solved problem were lower in Rust and Java than in Python |
| TypeScript's standing with agents | Medium | No current per-language score; 2025 benchmarks and 2026 practitioner and GitHub data point opposite ways |
| Go is a strong agent language | Medium | Practitioner opinion for, 2025 benchmarks against, no current score |
| Rust's compiler lets agents fix their own errors in a loop | Medium | CRUST-Bench (Claude Opus 4) shows the mechanism, though tests still mostly failed; PROBE (no Claude) found Rust gained most from compiler feedback; SWE-bench Multilingual shows the outcome; Multi-SWE-bench results depended on the agent; no current-model per-language score |
| C scored lowest on SWE-bench Multilingual in 2025, and AI-written C can pass the compiler while leaking memory | Low | One benchmark (30 tasks, one model, one agent), contradicted by Multi-SWE-bench where C was mid-pack; the memory study did not test Claude; no current-model score |
| C++ is a poor fit for agents | Low | No current score; 2025 C++ results were mixed; GitHub's lower AI-share band; no direct C++ agent study |
| 2025 per-language rankings (Python first, Rust strong in a loop, C/C++ and TS weakest) | Medium, historical | Several benchmarks, all on Claude 3.5 to Opus 4, small task counts |
| Anthropic's harness found one feature at a time critical, and backend-only checks missed broken features | High (documentary) | Stated in two Anthropic engineering posts and the current docs; single publisher, scoped to what Anthropic found |
| Feature slices beat backend-first or frontend-first in general | Medium | Anthropic's own experiments plus one practitioner opinion; no comparison study exists |
| Contract-first reduces front/back mismatch | Low | Vendor argument only, no measurements |

## Open Questions

- Anthropic publishes no per-language split of SWE-bench Multilingual for Opus 5, Sonnet 5.5 or Opus 5.5, so whether the remaining misses cluster in one language (C, TypeScript) is unknown.
- No independent leaderboard yet lists Claude 5 models on SWE-bench Pro or Multilingual; Scale's independent figures for older Claude models are far below Anthropic's own, partly from harness differences.
- No study compares backend-first, frontend-first and vertical slices, or a single-language stack against a mixed one, for AI agents.
- Whether the LSP plugins measurably improve Claude's results on these repos, given the report that agents sometimes skip the LSP. A before/after on one repo would answer it.

## Sources

**Anthropic system cards (current models)**
- [Claude Opus 5.5 System Card, September 22, 2026](https://www-cdn.anthropic.com/fc1b44717c85dc068bc6ba5024219938094694bd/Claude%20Opus%205.5%20System%20Card.pdf)
- [Claude Sonnet 5.5 System Card, September 28, 2026](https://www-cdn.anthropic.com/870c8f525702625d2c62fc6dd04c857e3250bec1/Claude%20Sonnet%205.5%20System%20Card.pdf)
- [Claude Opus 5 System Card, July 24, 2026](https://www-cdn.anthropic.com/b514064af1408018e64b1ad24e7d5e75850b4ffd/Claude%20Opus%205%20System%20Card.pdf)

**Anthropic docs and engineering**
- [Claude Code best practices](https://code.claude.com/docs/en/best-practices)
- [Claude Code best practices (markdown version)](https://code.claude.com/docs/en/best-practices.md)
- [Claude Code code intelligence plugins](https://code.claude.com/docs/en/plugins/code-intelligence.md)
- [Anthropic engineering, harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
- [Anthropic engineering, harness design for long-running apps](https://www.anthropic.com/engineering/harness-design-long-running-apps)

**Benchmarks and research**
- [Scale SWE-bench Pro leaderboard](https://labs.scale.com/leaderboard/swe_bench_pro)
- [SWE-bench Multilingual](https://www.swebench.com/multilingual.html)
- [Agent token cost by language (arXiv 2607.22807)](https://arxiv.org/html/2607.22807)
- [Multi-SWE-bench (arXiv 2504.02605)](https://arxiv.org/html/2504.02605v1)
- [SWE-PolyBench (arXiv 2504.08703)](https://arxiv.org/html/2504.08703v1)
- [AutoCodeBench (arXiv 2508.09101)](https://arxiv.org/html/2508.09101v1)
- [PROBE, one-shot generation across languages (arXiv 2607.13820)](https://arxiv.org/html/2607.13820v1)
- [CRUST-Bench, C to safe Rust (arXiv 2504.15254)](https://arxiv.org/html/2504.15254v3)
- [MultiPL-T, low-resource languages (arXiv 2308.09895)](https://arxiv.org/abs/2308.09895)
- [Type errors in LLM-generated TypeScript (arXiv 2504.09246)](https://arxiv.org/pdf/2504.09246)
- [Full-stack agents fall back to frontend-only (arXiv 2602.03798)](https://arxiv.org/pdf/2602.03798)
- [Memory safety of LLM-generated C (PMLR 318)](https://proceedings.mlr.press/v318/jarrin26a.html)
- [Veracode GenAI code security report](https://www.veracode.com/press-release/ai-generated-code-poses-major-security-risks-in-nearly-half-of-all-development-tasks-veracode-research-reveals/)

**Industry data**
- [GitHub Octoverse 2026](https://github.blog/news-insights/octoverse/octoverse-commits-pushes-reach-new-highs-in-2026-as-developers-build-with-agents/)

**Tool documentation**
- [Clang AddressSanitizer documentation](https://clang.llvm.org/docs/AddressSanitizer.html)

**Practitioner analysis**
- [Armin Ronacher, agentic coding recommendations (June 2025)](https://lucumr.pocoo.org/2025/6/12/agentic-coding/)
- [Armin Ronacher, on languages for agents (February 2026)](https://lucumr.pocoo.org/2026/2/9/a-language-for-agents/)
- [Armin Ronacher, on hard languages and LLMs (August 2026)](https://lucumr.pocoo.org/2026/8/22/fast-hard-code/)
- [Dan Vega, Stop letting AI build horizontally](https://www.danvega.dev/blog/stop-letting-ai-build-horizontally)
- [martinfowler.com, exploring spec-driven development tools](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)
- [Augment Code, why AI coding agents fail e2e tests](https://www.augmentcode.com/guides/why-ai-coding-agents-fail-e2e-tests)

## Update History

| Date | Change |
|---|---|
| 2026-11-11 11:02 AM ET | Independent verification of the C and Rust section (10 URLs). Fixed: "C was the weakest language" now says the benchmarks disagree (Multi-SWE-bench put C mid-pack), with the C confidence row narrowed and lowered to Low; added that CRUST-Bench tests rose only 22 to 29 after compiler repair, and PROBE's finding that Rust gained most from feedback; restored the cut-off end of the PROBE quote; "solved" corrected to ever-solved; dropped a retry claim the token study made about OCaml, not Rust; added AddressSanitizer's macOS leak flag; What to do #5 now cites Sonnet 4.6 rather than "current models"; removed History bullets that repeated the new section. |
| 2026-11-11 10:55 AM ET | Added a C and Rust section on request, weighing the argument that systems languages suit AI coding. New sources: Clang's AddressSanitizer documentation and a Rust tooling note from an already-cited practitioner post; new SWE-bench Multilingual per-repo counts for C. The shorter Rust and C notes elsewhere now point to the new section instead of repeating it; TL;DR and What to do now name C alongside C++. |
| 2026-11-11 10:28 AM ET | Second independent verification, scoped to the rewrite (10 URLs, including the three system card PDFs). Fixed: "the gap has mostly closed" overstated it, since the failure ratio rose from 1.5 to 2.6 times even as the point gap shrank; "central advice" narrowed to one of the page's headline points; the History intro no longer claims every result is Claude 3.7 or Opus 4, or that no newer per-language data exists; Scale's harness is mini-swe-agent, not its own; added Sonnet 4.6's raw per-solve token budget, which favors Rust and Java; noted the Go evidence is mixed; added Sonnet 4.5 to the harness models; split the vendor-score confidence row into what Anthropic reports (High) and whether it would replicate (Medium). |
| 2026-11-11 10:17 AM ET | Rewritten to lead with current models, after reader feedback that the first version quoted models that were too old. Added the Opus 5.5, Sonnet 5.5 and Opus 5 system cards (downloaded and text-extracted locally) and Scale's SWE-bench Pro leaderboard. Moved all Claude 3.7 and Opus 4 per-language results into a labelled History section. Recast the TL;DR and Current Status around the shrunken Python gap; recommendations unchanged except a softer stance on Rust. A June 2026 multi-language LiveCodeBench paper was checked and left out: it tested open-weight models only, on early-2025 problems. |
| 2026-11-11 10:14 AM ET | Independent verification read all 24 cited URLs. Fixed: the claim that no newer Claude model had per-language data (the token study has Sonnet 4.6 results, now added); the C++ figure (28.57% is C and C++ combined, C++ alone was 4 of 12); the Rust agent-loop claim (now notes the agent-setup dependence); GitHub's lower AI-share band names C++, not C. Split two overbroad High ratings, lowered C++ to Low, matched the token-claim label to Medium, and removed an unsupported publisher attribution. |
| 2026-11-11 10:05 AM ET | Created from 36 opened sources. |
