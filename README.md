# Equivalent Grammar

Dada la gramática G:

S -> aAc | Ba | SCS | db
A -> aA | ε
B -> Sd | a
C -> aCb
D -> aAb | ace

Se pide:
- Generar una gramática equivalente mínima, sin símbolos inútiles ni inalcanzables, a la que llamaremos G2.
- Hacer las modificaciones necesarias sobre G2 para tratar de convertirla en una gramática LL(1), llamada G3:
- Eliminar recursividades por la izquierda y aplicar factorizaciones y reemplazos, cuando sea posible.
- Generar la tabla LL(1) y decir si G3 es LL(1) o no.
- Generar la tabla SLR(1) para G2 y comprobar si es SLR(1) o no.

Se recomienda implementar la gramática en SLY y comprobar el resultado con el fichero 'debugfile' que genera, para aprender a interpretarlo.
