# Gitea inline math boundary reproduction

This file tests Gitea's Markdown renderer with the built-in math parser enabled.

Configuration (the installation used for this test):

```ini
[markdown]
ENABLE_MATH = true
MATH_CODE_BLOCK_DETECTION = inline-dollar,inline-parentheses,block-dollar,block-square-brackets
```

Each test is a **separate paragraph**. A successful inline-math parse renders the expression `x^2` as a math element; a failure displays ordinary text or literal parentheses/dollar signs.

## A. Controls

C01 (parentheses, end of paragraph): \(x^2\)

C02 (parentheses, ASCII space): \(x^2\) test

C03 (parentheses, ASCII punctuation): \(x^2\), test

C04 (dollar, end of paragraph): $x^2$

C05 (dollar, ASCII space): $x^2$ test

C06 (dollar, ASCII punctuation): $x^2$, test

## B. \(...\) directly followed by characters

P-KO (Korean): \(x^2\)이다

P-JA (Japanese): \(x^2\)です

P-ZH (Chinese): \(x^2\)是

P-TH (Thai): \(x^2\)เป็น

P-HI (Hindi / Devanagari): \(x^2\)है

P-AR (Arabic): \(x^2\)هو

P-RU (Russian / Cyrillic): \(x^2\)это

P-ASCII (ASCII letters): \(x^2\)is

P-DIGIT (ASCII digits): \(x^2\)123

P-LATIN (Latin letter with accent): \(x^2\)é

P-CJK-PUNCT (CJK punctuation): \(x^2\)。

P-EMOJI (emoji): \(x^2\)🙂

## C. $...$ directly followed by characters

D-KO (Korean): $x^2$이다

D-JA (Japanese): $x^2$です

D-ZH (Chinese): $x^2$是

D-TH (Thai): $x^2$เป็น

D-HI (Hindi / Devanagari): $x^2$है

D-AR (Arabic): $x^2$هو

D-RU (Russian / Cyrillic): $x^2$это

D-ASCII (ASCII letters): $x^2$is

D-DIGIT (ASCII digits): $x^2$123

D-LATIN (Latin letter with accent): $x^2$é

D-CJK-PUNCT (CJK punctuation): $x^2$。

D-EMOJI (emoji): $x^2$🙂

## D. Backtick math syntax (alternative)

B-KO (Korean): $`x^2`$이다

B-ASCII (ASCII letters): $`x^2`$is

B-HI (Hindi): $`x^2`$है

B-EMOJI (emoji): $`x^2`$🙂

## E. Characters immediately preceding math

PRE-P-KO (Korean): 이다\(x^2\)

PRE-P-ASCII (ASCII letters): abc\(x^2\)

PRE-D-KO (Korean): 이다$x^2$

PRE-D-ASCII (ASCII letters): abc$x^2$

## F. Regression controls: math elsewhere

BLOCK-SQUARE (display math):

\[
x^2+y^2=z^2
\]

BLOCK-DOLLAR (display math):

$$
x^2+y^2=z^2
$$

---

The raw Markdown file should be kept unchanged: this is a renderer-only experiment.
