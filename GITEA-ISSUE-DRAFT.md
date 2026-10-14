# [Bug] Inline math is not parsed when directly followed by text (Gitea 28.1.0; related to #27605)

## Description

On Gitea 28.1.0, inline math in repository Markdown files is not recognized if a character follows the closing delimiter without intervening whitespace. This affects both `\(...\)` and `$...$`. It is not specific to Korean or Unicode: English letters and ASCII digits reproduce it too.

Inline math followed by a space, ASCII punctuation, or the end of a paragraph is recognized. The explicit backtick-wrapped math syntax works before adjacent text.

This is a Markdown **parsing** issue rather than a KaTeX error: the server-generated HTML for failed cases contains ordinary text instead of a `language-math` element. No custom header template or injected JavaScript is involved.

## Version and configuration

- **Gitea:** 28.1.0
- **Deployment:** Docker
- **Math rendering:** Gitea built-in Markdown renderer
- **Custom KaTeX / header template:** none

Relevant `app.ini` settings:

~~~~ini
[markdown]
ENABLE_MATH = true
MATH_CODE_BLOCK_DETECTION = inline-dollar,inline-parentheses,block-dollar,block-square-brackets
~~~~

## Steps to reproduce

1. Enable the four math detection options above and restart Gitea.
2. Commit the following Markdown as a file in a repository:

~~~~markdown
Control 1: \(x^2\) test

Control 2: $x^2$ test

Failure 1 (Korean): \(x^2\)이다

Failure 2 (English): \(x^2\)is

Failure 3 (digits): \(x^2\)123

Failure 4 (Japanese): \(x^2\)です

Failure 5 (Thai): $x^2$เป็น

Alternative syntax: $`x^2`$이다
~~~~

3. Open the file in Gitea's web UI.

## Expected behavior

For the explicitly delimited `\(...\)` syntax, the expression should render as inline math even when immediately followed by ordinary text (e.g. a Korean grammatical particle), without requiring the author to insert a space or rewrite the Markdown. Ideally, other supported inline math syntaxes should be consistent where unambiguous.

## Actual behavior

- `\(x^2\)이다` becomes ordinary text `(x^2)이다`; it does not become a math element.
- `\(x^2\)is` becomes ordinary text `(x^2)is`.
- `$x^2$이다` stays ordinary text, including both dollar signs.
- Adding a space after the closing delimiter works.
- The backtick-wrapped alternative works: ``$`x^2`$이다``.

## Reproduction results

We tested **40 cases** on the same Gitea 28.1.0 instance and inspected its generated HTML:

| Group | Parsed as math | Not parsed as math |
|---|---:|---:|
| Controls (end/space/ASCII punctuation) | 6 | 0 |
| `\(...\)` followed by adjacent characters | 0 | 12 |
| `$...$` followed by adjacent characters | 0 | 12 |
| Backtick-wrapped math followed by text | 4 | 0 |
| Characters immediately preceding math | 2 | 2 |
| Display math controls | 2 | 0 |
| **Total** | **14** | **26** |

The trailing characters in the two 12-case groups were Korean, Japanese, Chinese, Thai, Hindi, Arabic, Russian, English letters, ASCII digits, accented Latin, CJK punctuation, and emoji. Every trailing-character case failed for both conventional inline delimiters.

**Public reproduction fixture:** https://github.com/naltang/markdown-test/blob/main/MATH-BOUNDARY-REPRO.md

**Recorded results:** https://github.com/naltang/markdown-test/blob/main/MATH-BOUNDARY-RESULTS.md

## Related issues / implementation note

This appears closely related to #27605, which was addressed by #30175 and released in Gitea 1.21.11. Similar behavior is observable in 28.1.0, but I have not established whether it is a regression of that precise fix or a later change to the parser.

The likely area to examine is `modules/markup/markdown/math/inline_parser.go`, especially its surrounding-character rules. Since dollar delimiters can be ambiguous in ordinary prose, it may be appropriate to handle `\(...\)` independently from `$...$` rather than indiscriminately removing every boundary check.

**Reproduced on demo.gitea.com:** Not tested.
