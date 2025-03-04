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

# Future work:
Implement the grammar using SLY and check if the result with the file "debugfile" is correct.


# Grammar elements
First we will describe the notation of the different elements that form
part of the grammar so we have a grammar *G* defined by next the elements:

#### Σ: Alphabet/tokens

```
Σ = {ϵ, a, b, c, d ,e}
```


#### N: not terminal symbols or variables

```
N = {S, A, B ,C, D}
```


#### Initial symbol/variable
```
S
```


#### P: set of rules

```
S -> aAc | Ba | SCS | db 
A -> aA | ε 
B -> Sd | a 
C -> aCb 
D -> aAb | ace 
```

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


### Transform the grammar in one LL(1)
A LL(1) grammar is a type of grammar in witch, for every input symbol exist you can
decide the next step only with one rule. So to achieve that we have to delete the left recursion
first and factorize to try to get a minimum not ambiguous grammar LL(1).

### Preprocessing

First we will try to remove some variables with substitutions if it possible to simplify our grammar *G2*

 *G2*:

````
S -> aAc | Ba | db 
A -> aA | ε 
B -> Sd | a 
````

We notice that *B* rule can be replaced into *S* rule so with this simple 
step we will remove one variable(*B*).

```
Replacement
-------------
S -> Ba
B -> Sd | a
-------------
S -> Sda | aa

```

And update our grammar *G2'*:

````
S -> aAc | Sda | db | aa 
A -> aA | ε 
````



### Deleting left recursion

How you can see we have direct and indirect left recursion in both rules

*G2'*:
````
S -> aAc | Sda | db | aa 
A -> aA | ε 
````

Now we have to delete the left recursion following the algorithm we have to create a new symbol or variable
in this case *S'*, create the right recursion to the rules with *S* header and delete the rules with left recursion. After that
create a new rules with *S'* to do the recursions with the *S* bodies
and another rule that derive in *ϵ* so:

*G2'*:
````
S -> aAcS' | dbS' | aaS' 
S'-> daS' | ε
A -> aA | ε 
````
And now we removed the left recursion.

#### Factorization

The rule S as to bodies with starting with the same symbol so in case
to try to automatize this, the analyzer cannot  decide which rule to apply so
we can have to factorize that rule just applying the algorithm.

In this case we only have to create a new variable in this case *B*, and factorize de part that cause the problem the *a*
moving the following part to a new rule with the same header ( *B*). So doing that we have:

*G2'*:
````
S -> aB | dbS'
S'-> daS' | ε
A -> aA | ε 
B -> AcS' | aS'
````

Now we have another problem we can reach *A* from *B* and this two rules has bodies that
start with the same character so in that case we have one case in which we cannot decide then next
rule to apply, remember that we are trying to get a LL(1) grammar, so we need to try to factorize this. To do that we could do
a replacement of *A* into *B*:

*G2'*:
````
S -> aB | dbS'
S'-> daS' | ε
A -> aA | ε 
B -> aAcS' | aS'| cS'
````

And now do a factorization in *B* in the same way that we do before to the same symbol *a* creating a new variable *C*:

*G2'*:
````
S -> aB | dbS'
S'-> daS' | ε
A -> aA | ε 
B -> aC| cS'
C -> AcS' | S'
````

So now we meet all the main conditions to generate a LL(1) grammar because we dont have
left recursion, direct or indirect, and we have not rules with the same header or with bodies 
that start with the same symbols which could be apply at the same time when we read the next
input character so we get *G3*:

*G3*:
````
S -> aB | dbS'
S'-> daS' | ε
A -> aA | ε 
B -> aC| cS'
C -> AcS' | S'
````

### Checking if G3 is a LL(1) grammar

To know if *G3* is a LL(1) grammar we have to construct the analysis table. If
in this table there is only one rule per value, our grammar is LL(1) because that means
that when you read one input character, you can always decide which rule to apply.

#### Constructing the analysis table to G3

Construct we have to do some steps first. The rows of the table will be
our variables:

```
N = {S, S', A, B, C}
```

And the rows will be the tokens + '$':

```
T = {a, b, c, d}
```

So our table will be some like:

|    	| a 	| b 	| c 	| d 	| $ 	|
|:--:	|:-:	|:-:	|:-:	|:-:	|:-:	|
|  S 	|   	|   	|   	|   	|   	|
|  S' 	|   	|   	|   	|   	|   	|
|  A 	|   	|   	|   	|   	|   	|
|  B 	|   	|   	|   	|   	|   	|
|  C 	|   	|   	|   	|   	|   	|

Now to get the values of each cell of the table, we have to compute
the sets first and follow of each variable of our grammar *G3*.

#### Computing First set

To compute the *First set* of every variable we have to apply this 
three rules, focusin on the headers.

1) If A -> aβ: First(A) = {a}
2) IF A -> ϵ: First(A) = {ϵ}
3) If A -> Xβ:
    3) If X ≠>*ϵ: First(Xβ) = First(X)
    3) iF X =>*ϵ: First(Xβ) = First(X) U First(β)
    3) if X -> α_1 | α_2 | … | α_n:
        - First(X) = First(α_1) U First(α_2) U ... U First(α_n)
        
So given our grammar:

*G3*:
````
S -> aB | dbS'
S'-> daS' | ε
A -> aA | ε 
B -> aC| cS'
C -> AcS' | S'
````

Applying the first rule to *S* variable we get:
```
First(S) = {a, d}
```

Applying the first and second rule to *S'* a we get:

```
First(S') = {d, ε}
```

Applying the first and second rule to *A* a we get:

```
First(A) = {a, ε}
```

Applying the first rule to *B* variable we get:
```
First(B) = {a, c}
```
Applying the last rule to *C* variable we get:
```
First(C) = First(A) U First(S') = {a,ε} U {d,ε} = {ε, a, d}
```

Putting all first sets together we have:

```
First(S) = {a, d}
First(S') = {d, ε}
First(A) = {a, ε}
First(B) = {a, c}
First(C) = {ε, a, d}
```

#### Computing the Follow set

Now focusing on the rules bodies we have to follow the this rules:

1) If B -> αA: Follow(A) = Follow(B)
2) If B -> αAβ and 
    2) β =>* ε: Follow(A) = First(β) - {ε} U Follow(B)
    2) β ≠>* ε: Follow(A) = First(β) - {ε}
3) A = S: Follow(A) = {$}

So given our grammar:

*G3*:
````
S -> aB | dbS'
S'-> daS' | ε
A -> aA | ε 
B -> aC| cS'
C -> AcS' | S'
````

The *S* variable is the initial so we have to include *$* and it isnt in the body of 
other rule so:

```
S = {$} 
```

The *S'* variable is in the end of the the rules with headers *S*, *S'*, *B*, *C* inst followint other variable so applying
the first rule we get:

```
S' = Follow(C) U Follow(B) U Follow(S') U Follow(S) = {$}
```

The *A* variable is *A* and *C* bodies, applying the first rule and in *C* rule has a following *c* so:

```
A = Follow(A) U {c} = {c}
```

The *B* variable is in *S* rules body with no following symbols so apply the first rule:

```
B = Follow(S) = {$}
```

The *C* variable is in the *B* rule in the end so applying the first rule:

```
C = Follow(B) = {$}
```


Putting the Follow sets together we get:

```
Follow(S) = {$}
Follow(S')= {$}
Follow(A) = {c}
Follow(B) = {$}
Follow(C) = {$}
```

### Filling the table

To fill the table we to apply this 3 rules:
1) A -> α ==> A -> α in table[A, First(α) - {ε}]
2) A -> ε ==> A -> ε in table[A, Follow(A)]
3) A -> α  == A =>* ε ==> A -> α in table[A, Follow(A)]

```
First sets:
-------------------------
First(S) = {a, d}
First(S') = {d, ε}
First(A) = {a, ε}
First(B) = {a, c}
First(C) = {ε, a, d}
-------------------------
Follow sets:
-------------------------
Follow(S) = {$}
Follow(S')= {$}
Follow(A) = {c}
Follow(B) = {$}
Follow(C) = {$}
-------------------------
*G3*:
-------------------------
S -> aB | dbS'
S'-> daS' | ε
A -> aA | ε 
B -> aC| cS'
C -> AcS' | S'
-------------------------

```

Starting with *S* :

```
S -> aB

S -> dbS'

```


|    	| a 	| b 	| c 	| d 	| $ 	|
|:--:	|:-:	|:-:	|:-:	|:-:	|:-:	|
|  S 	| S -> aB	| -   	| - 	| S -> dbS' 	| - 	|
|  S' 	| -   	| -   	| -   	| -  	| -   	|
|  A 	| -  	| -  	| -  	| -    	| -   	|
|  B 	| -  	| -   	| -   	| -   	| -   	|
|  C 	| -  	| -   	| -   	| -   	| -   	|


Filling *S'* rules we apply first and the second rule

```
S' -> daS'
S' -> ε
Follow(S') = {$}
```


|    	| a 	| b 	| c 	| d 	| $ 	|
|:--:	|:-:	|:-:	|:-:	|:-:	|:-:	|
|  S 	| S -> aB	| -  	| - | S -> dbS'   	| -	|
|  S' 	| -  	| -   	| -   	| S' -> daS'   	| S' -> ε  	|
|  A 	| -  	| -   	| -   	| -   	| -   	|
|  B 	| -  	| -  	| -  	| -  	| -  	|
|  C 	| -  	| -  	| -   	| -  	| -   	|

With *A* variable we apply the first and the second:
 
 ```
A -> aA

A -> ε
Follow(A) = {c}
```

|    	| a 	| b 	| c 	| d 	| $ 	|
|:--:	|:-:	|:-:	|:-:	|:-:	|:-:	|
|  S 	| S -> aB	| -   	| -   	| S -> dbS'   	| -  	|
|  S' 	|  - 	| -   	| -  	| S' -> daS'   	| S' -> ε  	|
|  A 	| A -> aA  	| -   	| A -> ε   	|  -  	| -  	|
|  B 	| -  	|  - 	|  - 	|  - 	|  - 	|
|  C 	| -  	|  - 	|  - 	|  - 	|  - 	|


With *B* variable:
 
 ```
B -> aC

B -> cS'
```

|    	| a 	| b 	| c 	| d 	| $ 	|
|:--:	|:-:	|:-:	|:-:	|:-:	|:-:	|
|  S 	| S -> aB	| -   	| - 	| S -> dbS'   	| -  |
|  S' 	|   -	|   -	|   -	| S' -> daS'   	| S' -> ε  	|
|  A 	| A -> aA  	|   -	| A -> ε   	| -   	| -  	|
|  B 	| B -> aC  	|   -	|B -> cS'    	| 	| -   	|
|  C 	|   -	|   -	| -  	| -  	| -  	|


And to finish with *C* que apply the first and the third rule:

 ```
C -> AcS'
First(A) - {ε} = {a}

C -> S'
First(S') - {ε} = {d}
Follow(C) = {$}

```

|    	| a 	| b 	| c 	| d 	| $ 	|
|:--:	|:-:	|:-:	|:-:	|:-:	|:-:	|
|  S 	| S -> aB	| -	| -	| S -> dbS'   	| -  |
|  S' 	| -   	| -   	| -   	| S' -> daS'   	| S' -> ε  	|
|  A 	| A -> aA  	| -   	| A -> ε   	| -    	| -   	|
|  B 	| B -> aC  	| -   	|B -> cS'    	| - 	| -    	|
|  C 	| C -> AcS'   	| -| -| C -> S'   	| C -> S'  	|



### Is a LLR(1) grammar?
How you can see is a LL(1) grammar because there is only a rule per cell in the table, so we can
decide witch rule apply only reading the next character.


### Checking if G2 grammar is SLR

To check if *G2* grammar is SLR we have to construct the SLR table analysis
and observe if per cell there is only one rule.

*G2* grammar:
``` 
S -> aAc | Ba | db 
A -> aA | ε 
B -> Sd | a 
```

The first step to create the table is add a new state *S'* to our initial state *S* and create the
*DFA*.

*G2'* grammar:
``` 
S' -> S
S -> aAc | Ba | db 
A -> aA | ε 
B -> Sd | a 
```

Now we will generate the items adding the dot(*) and computing the closure of the items. Starting with
this new state *S'*. So create this new state:

```
S' -> *S
```

Now compute the closure of this item, that is the rules of variable that follows the dot in this case *S*, this step is recursive
 so if *S* has rules that start with a variable( in this case *B*) we have to add the rules of *B*
 starting with a dot. So we get:

```
---------------------
_________
S' -> *S
_________
Closure:
S -> *aAc |*Ba | *db 
B -> *Sd | *a
---------------------
```
Now we will generate more nodes with the transitions that generate our
rules. The transitions are created with values the symbol that follow the dot
and in the new states we will storage the same rules that do a a transition with the same 
value moving the dot one place to the right. We will do this step until the dot is in the end
of the rule.


```
---------------------
_________
S' -> *S
_________
Closure:
S -> *aAc |*Ba | *db 
B -> *Sd | *a
---------------------
```

With *S* we create a new node

```
---------------------
_________
S' -> S*
B -> S*d
_________
---------------------
```

This node with the rule *B* generate another node with transition *d*

```
---------------------
_________
B -> Sd*
_________
---------------------
```

This node only have items wheres the dot is in the end so we cant generate more child with this node. 
Back to the main node:

```
---------------------
_________
S' -> *S
_________
Closure:
S -> *aAc |*Ba | *db 
B -> *Sd | *a
---------------------
```

Now with transition *d*:

```
---------------------
_________
S -> d*b
_________
---------------------
```

And from this node with transition *b*:

```
---------------------
_________
S -> db*
_________
---------------------
```

Back to the main node:

```
---------------------
_________
S' -> *S
_________
Closure:
S -> *aAc |*Ba | *db 
B -> *Sd | *a
---------------------
```

We can transition with *B*:

```
---------------------
_________
S -> B*a
_________
---------------------
```

Now with *a*:

```
---------------------
_________
S -> Ba*
_________
---------------------
```

Back to the main node:

```
---------------------
_________
S' -> *S
_________
Closure:
S -> *aAc |*Ba | *db 
B -> *Sd | *a
---------------------
```

We only can transition from this node with *a*

```
---------------------
_________
S -> a*Ac
B -> a*
_________

---------------------
```

And we have the dot in front of a variable so compute de closure of the item. *A* has a ε so
when this happens we do a derivation to the dot.

```
---------------------
_________
S -> a*Ac
B -> a*
_________
A -> *aA
A -> *
---------------------
```

Now we will continue throw this node as main doing a transition with *A*:

```
---------------------
_________
S -> aA*c
_________
---------------------
```

And now with *C*:

```
---------------------
_________
S -> aAc*
_________
---------------------
```

Back to the new main node:

```
---------------------
_________
S -> a*Ac
B -> a*
_________
A -> *aA
A -> *
---------------------
```
We can do a transition with *a*:

```
---------------------
_________
A -> a*A
_________
A -> *
---------------------
```

Closure the *A*

```
---------------------
_________
A -> a*A
A -> *aA
_________
A -> *
---------------------
```

With *a* we got a transition to the same node. Only left *A*:

```
---------------------
_________
A -> aA*
_________
---------------------
```

Now numbering the states and composing a graph we have:

![dfa_srl](/assets/dfa_SRL.jpg)


Now to start to fill the table *Action* and *GOTO* we need only one thing more
the follow sets of the grammar *G2*.

*G2'* grammar:
``` 
S -> aAc | Ba | db 
S' -> S
A -> aA | ε 
B -> Sd | a 
```

Follow set of *S*, is in the body of *S'* in the last position, and in the body
of *B* before *d* so:

```
S = {d} U Follow(S')
```

Follow sef of *S'* in his body only contains the initial state so:

```
S' = {$}
```

Now we can update de follow set of *S*:

Follow set of *S*, is in the body of *S'* in the last position, and in the body
of *B* before *d* so:

```
Follow(S) = {d} U Follow(S') = {$, d}
```

Follow set of *A* is in the body of S before *c* character and is the last
symbol in the a rule with itself as header so:

```
Follow(A) = {c} U Follow(A) = {c}
```

And to finish *B* variable is only in one rule in *S* before *a* character so:

```
Follow(B) = {a}
```

Putting all Follow sets together:

```
Follow(S) = {$, d}
Follow(S') = {$}
Follow(A) = {c}
Follow(B) = {a}
```

We will use this set to get the values of the items with a dot (*) in the end.


### Constructing the SLR table

We will construct a table with:

- N rows, N = number of states.

And we have two tables, *ACTION* table and *GOTO* table

- *ACTION* table:
    - M columns; M = tokens + $.

- *GOTO* table
    - M' columns: M' = variables without *S'*.
    

We will represent this table as one, because has the same rows and 
are interconnected so we will have some like:

|    	| a 	| b 	| c 	| d 	| $ 	| S 	| A 	| B 	|
|:--:	|:-:	|:-:	|:-:	|:-:	|:-:	|---	|---	|---	|
|  0 	|   	|   	|   	|   	|   	|   	|   	|   	|
|  1 	|   	|   	|   	|   	|   	|   	|   	|   	|
|  2 	|   	|   	|   	|   	|   	|   	|   	|   	|
|  3 	|   	|   	|   	|   	|   	|   	|   	|   	|
|  4 	|   	|   	|   	|   	|   	|   	|   	|   	|
| 5  	|   	|   	|   	|   	|   	|   	|   	|   	|
| 6  	|   	|   	|   	|   	|   	|   	|   	|   	|
| 7  	|   	|   	|   	|   	|   	|   	|   	|   	|
| 8  	|   	|   	|   	|   	|   	|   	|   	|   	|
| 9  	|   	|   	|   	|   	|   	|   	|   	|   	|
| 10 	|   	|   	|   	|   	|   	|   	|   	|   	|
| 11 	|   	|   	|   	|   	|   	|   	|   	|   	|



#### ACTION table

Fist we will fill the action table, following the next algorithm:

1) Select all nodes with transitions with tokens and put the value on the table
being the column the state number where the transition going. The value will be preceded
by *SHIFT*.

So following our graph we can see this transitions

````
0-d: 3
0-a: 7
1-d: 2
3-b: 4
5-a: 6
7-a: 10
8-c: 9
10-a: 10
````

Now filling the table we have:

|    	|     a    	|    b    	|    c    	|    d    	| $ 	| S 	| A 	| B 	|
|:--:	|:--------:	|:-------:	|:-------:	|:-------:	|:-:	|---	|---	|---	|
|  0 	|  SHIFT 7 	|         	|         	| SHIFT 3 	|   	|   	|   	|   	|
|  1 	|          	|         	|         	| SHIFT 2 	|   	|   	|   	|   	|
|  2 	|          	|         	|         	|         	|   	|   	|   	|   	|
|  3 	|          	| SHIFT 4 	|         	|         	|   	|   	|   	|   	|
|  4 	|          	|         	|         	|         	|   	|   	|   	|   	|
| 5  	| SHIFT 6  	|         	|         	|         	|   	|   	|   	|   	|
| 6  	|          	|         	|         	|         	|   	|   	|   	|   	|
| 7  	| SHIFT 10 	|         	|         	|         	|   	|   	|   	|   	|
| 8  	|          	|         	| SHIFT 9 	|         	|   	|   	|   	|   	|
| 9  	|          	|         	|         	|         	|   	|   	|   	|   	|
| 10 	|          	|         	|         	|         	|   	|   	|   	|   	|
| 11 	| SHIFT 10 	|         	|         	|         	|   	|   	|   	|   	|


Step two is about reducing, so we will get the items ending in a dot (*) , and reducing them
using the cell marked with the values of his follow set and the node number if the the body of a rule only have a dot we 
will change de dot by *ε*. We will represent de *REDUCE* using
*R(A -> α) being A, a variable different than S'* so getting this items we get:

```
S -> db*: 4   
S -> Ba*: 6 
S -> aAc*: 9
------------------
Follow(S) = {d, $}
__________________
A -> *: 10
A -> *: 7
A -> aA*: 11
------------------
Follow(A) = {c}
__________________
B-> a*: 7
B-> Sd*: 2
------------------
Follow(B) = {a}
```

Fill the table we get:

|    	|          a         	|    b    	|     c     	|      d      	|      $      	| S 	| A 	| B 	|
|:--:	|:------------------:	|:-------:	|:---------:	|:-----------:	|:-----------:	|---	|---	|---	|
|  0 	|       SHIFT 7      	|         	|           	|   SHIFT 3   	|             	|   	|   	|   	|
|  1 	|      R(B -> 2)     	|         	|           	|   SHIFT 2   	|             	|   	|   	|   	|
|  2 	|                    	|         	|           	|             	|             	|   	|   	|   	|
|  3 	|                    	| SHIFT 4 	|           	|             	|             	|   	|   	|   	|
|  4 	|                    	|         	|           	|  R(S -> db) 	|  R(S -> db) 	|   	|   	|   	|
| 5  	| SHIFT 6            	|         	|           	|             	|             	|   	|   	|   	|
| 6  	|                    	|         	|           	| R(S -> Ba)  	| R(S -> Ba)  	|   	|   	|   	|
| 7  	| SHIFT 10 or R(B -> a) 	|         	| R(A -> ε) 	|             	|             	|   	|   	|   	|
| 8  	|                    	|         	| SHIFT 9   	|             	|             	|   	|   	|   	|
| 9  	|                    	|         	|           	| R(S -> aAc) 	| R(S -> aAc) 	|   	|   	|   	|
| 10 	|                    	|         	| R(A -> ε) 	|             	|             	|   	|   	|   	|
| 11 	| SHIFT 10           	|         	|           	|             	|             	|   	|   	|   	|

Now we will continue with non terminals transitions, this values will be in the *GOTO* table and the column is marked
by the value of the non terminal that do the transition and the row is the node where is from and the value of the cell
the node where is going the transition so:


```
0| S' -> S*: 1 
__________________
0| S -> B*a : 5
7| S -> aA*c: 8
__________________
10| A-> aA*: 11
```

So filling the table:

|    	|          a         	|    b    	|     c     	|      d      	|      $      	| S      	| A       	| B      	|
|:--:	|:------------------:	|:-------:	|:---------:	|:-----------:	|:-----------:	|--------	|---------	|--------	|
|  0 	|       SHIFT 7      	|         	|           	|   SHIFT 3   	|             	| GOTO 1 	|         	| GOTO 5 	|
|  1 	|      R(B -> 2)     	|         	|           	|   SHIFT 2   	|             	|        	|         	|        	|
|  2 	|                    	|         	|           	|             	|             	|        	|         	|        	|
|  3 	|                    	| SHIFT 4 	|           	|             	|             	|        	|         	|        	|
|  4 	|                    	|         	|           	|  R(S -> db) 	|  R(S -> db) 	|        	|         	|        	|
| 5  	| SHIFT 6            	|         	|           	|             	|             	|        	|         	|        	|
| 6  	|                    	|         	|           	| R(S -> Ba)  	| R(S -> Ba)  	|        	|         	|        	|
| 7  	| SHIFT 10 or R(B -> a) |         	| R(A -> ε) 	|             	|             	|        	| GOTO 8  	|        	|
| 8  	|                    	|         	| SHIFT 9   	|             	|             	|        	|         	|        	|
| 9  	|                    	|         	|           	| R(S -> aAc) 	| R(S -> aAc) 	|        	|         	|        	|
| 10 	|                    	|         	| R(A -> ε) 	|             	|             	|        	|         	|        	|
| 11 	| SHIFT 10           	|         	|           	|             	|             	|        	| GOTO 11 	|        	|


And now only thing to finish our table the *ACCEPTED* value to known when a word is accepted.
To get the values of the column and row we have to search the number of the node that has the item:

```
S' -> S*
```

The row value will be the number of the node, and de column is *$* that means
EOF. In our case:

```
1| S' -> S*
```

So filling the table we get:

|    	|             a             	|    b    	|     c     	|      d      	|       $      	| S      	| A       	| B      	|
|:--:	|:-------------------------:	|:-------:	|:---------:	|:-----------:	|:------------:	|--------	|---------	|--------	|
|  0 	|          SHIFT 7          	|         	|           	|   SHIFT 3   	|              	| GOTO 1 	|         	| GOTO 5 	|
|  1 	|         R(B -> 2)         	|         	|           	|   SHIFT 2   	| **ACCEPTED** 	|        	|         	|        	|
|  2 	|                           	|         	|           	|             	|              	|        	|         	|        	|
|  3 	|                           	| SHIFT 4 	|           	|             	|              	|        	|         	|        	|
|  4 	|                           	|         	|           	|  R(S -> db) 	|  R(S -> db)  	|        	|         	|        	|
| 5  	| SHIFT 6                   	|         	|           	|             	|              	|        	|         	|        	|
| 6  	|                           	|         	|           	| R(S -> Ba)  	| R(S -> Ba)   	|        	|         	|        	|
| 7  	| SHIFT 10 **or** R(B -> a) 	|         	| R(A -> ε) 	|             	|              	|        	| GOTO 8  	|        	|
| 8  	|                           	|         	| SHIFT 9   	|             	|              	|        	|         	|        	|
| 9  	|                           	|         	|           	| R(S -> aAc) 	| R(S -> aAc)  	|        	|         	|        	|
| 10 	|                           	|         	| R(A -> ε) 	|             	|              	|        	|         	|        	|
| 11 	| SHIFT 10                  	|         	|           	|             	|              	|        	| GOTO 11 	|        	|


And our table is finished!


### Is a SLR(1) grammar?

The answer is no, because in the cell of our SLR(1) analysis table [a, 7] we have
two rules, so we cannot decide which rule apply only reading one character.

