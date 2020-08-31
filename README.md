# Syntax analysis

# Description

Given the grammar *G*:

S -> aAc | Ba | SCS | db <br>
A -> aA | ε <br>
B -> Sd | a <br>
C -> aCb <br>
D -> aAb | ace <br>

- Generate the minimum equivalent grammar G2.
- Do the necessary modifications over G2 to obtain a LL(1) G3:
  - Delete the left recursion, apply factorizations and replacements when possible
  - Generate the LL(1) table and decide if G3 is LL(1).
- Generate the SLR(1) table for G2 and check if is SLR(1)

Implement the grammar using SLY and check if the result with the file "debugfile" is correct.


# Grammar minimization

To reduce the grammar we have to remove variables that no lead to a sentence and remove non reachable symbols
from initial state *S*, as well the rules where
this variables appears in both cases.

### Remove variables that no lead to a sentence

Following the algorithm:

Headers:
```
N = {S, A, B ,C ,D}
```
Rules
```
S -> aAc | Ba | SCS | db <br>
A -> aA | ε <br>
B -> Sd | a <br>
C -> aCb <br>
D -> aAb | ace <br>
```

First create a new variable: 

```
N′ = ∅
```

For all rules we will include his headers are in *N'* in which his bodies
are composed for non terminal symbols only:

We got this rules that meet that condition: 

```
S -> b 
A -> ε 
B -> a 
D -> ace 
```

So update *N'* with that headers:

```
 N' = {S, A, B, D}
```

Now while *N'* gets updated we have to, for every rule checks if all the variables in
in his bodies are in *N'*, if is the case, include the headers of that rules in *N'*. To speed up the 
work we will ignore the rules with headers in *N'* already, so de only rule with a header
is:

````
C -> aCb
````

Which his body is not composed by variables in *N'*. So *N'* dont get updated and the loop stops.
Now we have to remove all variables which are in N but not in N' and the rules where this variable appears:

````
 N' = {S, A, B, D}

 N = {S, A, B ,C ,D}
````

So we remove *C* and the rules where it appears:

```
S -> SCS
C -> aCb
```

And we update our grammar *G'* with two rules less:

````
S -> aAc | Ba | db <br>
A -> aA | ε <br>
B -> Sd | a <br>
D -> aAb | ace <br>
````


