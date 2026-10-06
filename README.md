# NAME

Perl::Critic::Policy::ValuesAndExpressions::ProhibitLiteralArithmetic - Write a number as the number it is, not as arithmetic on other numbers.

# VERSION

version 0.001

# Perl::Critic::Policy::ValuesAndExpressions::ProhibitLiteralArithmetic

Arithmetic whose operands are all literal numbers computes, every time it runs,
a value that never changes.  To make matters worse, doing this "code as documentation"
builds an unhealthy habit which will eventually lead to a rounding/swamping error.
Write the value, with digit separators where they help:

```perl
my $GB = 1024 * 1024 * 1024;         # reported
my $GB = 1024 ** 3;                  # reported
my $GB = 1_073_741_824;              # what to write
```

## PROHIBITED

```perl
my $day = 60 * 60 * 24;
my $mask = 1 << 20;
use constant TIMEOUT => 5 * 60;
my $x = $y + 2 * 3;                  # 2 * 3 is computed before the +
my $x = 2 * 3 + $y;                  # and so is this one
my $x = $y ** 2 ** 3;                # ** is right associative: 2 ** 3 first
```

## ALLOWED

```perl
my $x = $y * 2 + 3;                  # $y * 2 first, then + 3
my $x = $y - 2 - 3;                  # ($y - 2) - 3
my $x = 2 ** 3 ** $y;                # 2 ** (3 ** $y)
my @r = 1 .. 10;                     # a range, not arithmetic
my $s = '-' x 72;                    # repetition, not arithmetic
my $n = -1;                          # a negative literal
my $v = 1024 * $size;                # a variable is not a literal
```

# WHAT COUNTS

An expression is reported when an operator in it has a literal number on each
side, after perl's precedence and associativity decide what each side is.  The
operators are `**`, `*`, `/`, `%`, `+`, `-`, `<<` and `>>`.
A literal is a number in any notation that PPI reads as one, decimal, hex,
octal, binary or exponent, with or without its sign.  A version string such as
`v5.14` is not a number here.

One expression is one violation, at its first number, however many operators
it has.

# CAVEATS

PPI gives a statement as a list of tokens, not as a tree.  So the policy applies
perl's precedence to that list itself, and it knows the binary operators of
[perlop](https://metacpan.org/pod/perlop) that can sit beside a number.  An operator it does not know is taken
to bind more loosely than any of these, which is true of every one that is
left: the comparisons, the logical operators, the ternary, assignment and the
comma.

Parentheses are a structure of their own in PPI.  `(2 + 3) * 4` is reported at
`2 + 3`, inside them, and not as the whole expression.  Whoever writes out
that value writes out the whole of it anyway.

## METHODS

### supported\_parameters

### default\_severity

### default\_themes

### applies\_to

### violates

# BUGS

Please report any bugs or feature requests on the bugtracker website
[https://github.com/teodesian/perl-critic-policy-prohibitliteralarithmetic/issues](https://github.com/teodesian/perl-critic-policy-prohibitliteralarithmetic/issues)

When submitting a bug or request, please include a test-file or a
patch to an existing test-file that illustrates the bug or desired
feature.

# AUTHORS

Current Maintainers:

- George S. Baugh <george@troglodyne.net>

# COPYRIGHT AND LICENSE

Copyright (c) 2026 Troglodyne LLC

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
