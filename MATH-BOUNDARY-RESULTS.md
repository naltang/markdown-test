# Gitea inline-math boundary test results

- Date: 2026-10-14 (KST)
- Instance: https://git.ycomputing.co.kr/
- Version shown in instance UI: 28.1.0 (reported by the owner)
- Fixture: [MATH-BOUNDARY-REPRO.md](MATH-BOUNDARY-REPRO.md)
- Method: fetched the public rendered HTML. Inline math counts as parsed when its paragraph contains `<code class="language-math">`. Display math counts as parsed when its block contains the corresponding math-code node. This measures **server-side parser recognition**, not KaTeX's client-side execution.

## Results

| Group | Syntax or situation | Math parsed | Left as plain text |
|---|---|---:|---:|
| A | Controls: end of line, ASCII space, ASCII punctuation | 6 | 0 |
| B | `\(...\)` immediately followed by 12 suffix types | 0 | 12 |
| C | `$...$` immediately followed by 12 suffix types | 0 | 12 |
| D | Backtick-wrapped inline math before following text | 4 | 0 |
| E | Character immediately before math | 2 | 2 |
| F | Display math (square brackets, double dollars) | 2 | 0 |
| **Total** | **40 cases** | **14** | **26** |

All **12 suffix categories** failed for both B and C: Korean, Japanese, Chinese, Thai, Hindi/Devanagari, Arabic, Russian/Cyrillic, ASCII letters, ASCII digits, accented Latin, CJK punctuation, and emoji.

Representative server-side HTML results:

- `\(x^2\)이다` -> ordinary text `(x^2)이다` (not math).
- `\(x^2\)is` -> ordinary text `(x^2)is` (not math).
- `$x^2$이다` -> ordinary text `$x^2$이다` (not math).
- `$x^2$is` -> ordinary text `$x^2$is` (not math).
- `\(x^2\) test` -> math code node followed by ` test`.
- ``$`x^2`$이다`` -> math code node followed by `이다`.
- `이다\(x^2\)` -> `이다` followed by math code node.
- `abc\(x^2\)` -> ordinary text `abc(x^2)` (not math).

## Conclusion

This is **not Korean-specific** and **not Unicode-specific**: trailing ASCII letters and digits fail too. All tested suffixes fail with both standard inline forms, while explicit backtick-wrapped math succeeds. Preceding Unicode and preceding ASCII behave differently in this fixture.

These observations demonstrate a parser-boundary issue but do not establish whether each restriction is intentional; dollar-style math may need surrounding-character heuristics to avoid false positives. The original Markdown files were not transformed.

Re-run this fixture against the same instance after an upgrade or parser change.
