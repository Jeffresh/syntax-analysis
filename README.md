# Equivalent Grammar

# Description

Given the grammar G:

S -> aAc | Ba | SCS | db <br>
A -> aA | ε <br>
B -> Sd | a <br>
C -> aCb <br>
D -> aAb | ace <br>

- Generate the mininum equivalent grammar G2.
- Do the necessary modifications over G2 to obtain a LL(1) G3:
  - Delete the left recursion, apply factorizations and replacements when possible
  - Generate the LL(1) table and decide if G3 is LL(1).
- Generate the SLR(1) table for G2 and check if is SLR(1)

Implement the grammar using SLY and check if the result with the file "debufile" is correct.
