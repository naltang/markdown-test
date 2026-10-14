# Inline \(...\) math is not parsed before adjacent ASCII letters or digits

### Gitea Version

28.1.0 (as displayed in the self-hosted instance UI)

### What happened?

The built-in Markdown renderer fails to recognize inline math written with explicit `\(...\)` delimiters when the closing delimiter is immediately followed by ASCII letters or digits.

**Minimal reproduction** in a repository Markdown file:

~~~~markdown
Control (space):       \(x^2\) is
Failure (letters):     \(x^2\)is
Failure (digits):      \(x^2\)123
Control (punctuation): \(x^2\), is
Control (end):         \(x^2\)

Comparison (dollars, space):   $x^2$ is
Comparison (dollars, letters): $x^2$is
Alternative (backtick math):   $`x^2`$is
~~~~

**Expected:** The explicitly delimited `\(x^2\)` should be recognized as inline math without requiring a space before the following ASCII text.

**Actual:** On Gitea 28.1.0, `\(x^2\)is` becomes plain text `(x^2)is` in the server-generated HTML rather than an inline math element (`<code class="language-math">`). Similarly, the numeric example is not recognized. Controls with a following space, punctuation, or paragraph end are parsed correctly.

The `$...$` style also fails adjacent to letters, while the backtick-wrapped alternative works.

**ASCII-only reproduction fixture:** https://github.com/naltang/markdown-test/blob/main/INLINE-MATH-ASCII-REPRO.md

The fixture records these examples and observations from the same self-hosted instance. The failure happens at Markdown parsing time, before the browser's KaTeX rendering.

### Related discussions

Related issues: #27605, #32726, #36109.

I understand from earlier discussions that freely interpreting `$...$` may cause false positives in currency expressions such as `US$10 and US$20`. My request is narrower: could **the explicit `\(...\)` delimiter syntax** permit adjacent ASCII text independently of the currency-sensitive rules for `$...$`?

I am not asserting a regression of a specific prior fix.

### How are you running Gitea?

Self-hosted Docker instance. OS and database not collected for this parser-only reproduction.

~~~~ini
[markdown]
ENABLE_MATH = true
MATH_CODE_BLOCK_DETECTION = inline-dollar,inline-parentheses,block-dollar,block-square-brackets
~~~~

No custom header template or external math-rendering JavaScript was applied.

**Reproduced on demo.gitea.com:** Not tested (the Markdown API required authentication).

