# Gitea inline math: ASCII-only reproduction

This minimal Markdown fixture tests whether Gitea recognizes inline math adjacent to ASCII text. No custom header template or external KaTeX script is used.

## Parenthesis delimiters

P1 (control, ASCII space): \(x^2\) is

P2 (failure, ASCII letters): \(x^2\)is

P3 (failure, ASCII digits): \(x^2\)123

P4 (control, ASCII punctuation): \(x^2\), is

P5 (control, end of paragraph): \(x^2\)

## Dollar delimiters

D1 (control, ASCII space): $x^2$ is

D2 (failure, ASCII letters): $x^2$is

D3 (failure, ASCII digits): $x^2$123

## Backtick-wrapped alternative

B1 (control, ASCII letters): $`x^2`$is

## Expected vs observed

On the tested self-hosted Gitea 28.1.0 instance, P2, P3, D2, and D3 were not parsed as math, whereas P1, P4, P5, D1, and B1 were parsed as math. Recognition was determined from the server-rendered HTML (the presence or absence of an inline math code element).

The key request is to handle explicit \(...\) delimiters even when immediately followed by text. Dollar-delimited math may need separate currency-aware rules.
