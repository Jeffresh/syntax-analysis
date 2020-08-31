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

### 1 - Remove variables that no lead to a sentence (and rules)

Following the algorithm:

Headers:
```
N = {S, A, B ,C ,D}
```
Rules
```
S -> aAc | Ba | SCS | db 
A -> aA | ε 
B -> Sd | a 
C -> aCb 
D -> aAb | ace 
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
S -> aAc | Ba | db 
A -> aA | ε 
B -> Sd | a 
D -> aAb | ace 
````



### 2 - Removing non reachable rules and variables/symbols

Create a new variable *J* and *T* and initialize the variables in that way:

```
J = {S}
N' = {S}
N = {S, A, B ,D}
T = ∅
```

Now while *J* is not empty we will:
- Extract one element of J so extract S and update J:

```
J = ∅
```
- For all rules with this element in the header:
    - Checks every variable in the body of the rule:
        - If this variable is not in *N'* insert in *J* and *N'* sets.
        - Add the terminal symbols in *T*

So for *S* we got the rules:

```
S -> aAc | Ba | db
```

Update and update *J*, *N'* and *T* 

```
N' ={S, A ,B}
J = {A, B}
T = {a, c, d, b}
```

So now extract the new symbol for *J*:
```
J = {B}
```

So for *A* got the rules:

```
A -> aA | ε
```

*A* and *a* are in *N'* and *T* so nothing changes in this iteration, we can include *ϵ*. For me *ϵ* is
more like a character that represent a "concept" than a token but we will include because strictly is part of the alphabet.


```
N' ={S, A ,B}
J = {B}
T = {ϵ, a, c, d, b}
```

So extract the next element of *J*:

```
J = ∅
```

So for *B* got the rules:
```
B -> Sd | a
```

*S* , *d* and *a* are in the sets *N'* and T so nothing changes in this iteration.

```
N' ={S, A ,B}
J = ∅
T = {ϵ, a, c, d, b}
```

Now *J* is empty so for every variable that's not in *N'* and all symbols that's not in *T*
we have to remove it and all rules that including it so:

```
N = {S, A, B ,D}
N' ={S, A ,B}
-------------------------
T = {a, c, d, b}
Σ = {ϵ, a, b, c, d ,e}
```

We have to remove *D* and *e* and the rules where it appears so from  *G'*:

````
S -> aAc | Ba | db 
A -> aA | ε 
B -> Sd | a 
D -> aAb | ace 
````

We get *G2*:

````
S -> aAc | Ba | db 
A -> aA | ε 
B -> Sd | a 
````


