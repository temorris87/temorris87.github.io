---
layout: default
title: Formal Languages and Automata - Regular Languages
---

## 1.1 Finite Automata

### Definition of a Deterministic Finite Automaton (DFA)
An automaton is a finite state machine that, given an input, either accepts
or rejects the input. A DFA can be represented by the 5-tuple
\\((\\mathbb{Q}, \Sigma, \delta, q_0, F)\\) or a state diagram. The 5-tuple
is formally described by:

1. \\(\mathbb{Q}\\) is a finite set called the **states**.
2. \\(\Sigma\\) is a finite set called the **alphabet**.
3. \\(\delta : \mathbb{Q} \times \Sigma \rightarrow \mathbb{Q}\\) is the **transition function**.
4. \\(q_0 \in \mathbb{Q}\\) is the **start state**.
5. \\(F \subseteq \mathbb{Q}\\) is the **set of accept states**.

We say that the **language of machine \\(M\\)** is the set of all strings \\(A\\) 
that machine \\(M\\) accepts, written as \\(L(M)=A\\).

### Formal Definition of Computation

Let \\(M = (\mathbb{Q}, \Sigma, \delta, q_0, F)\\) be a finite automaton and let
\\(w = w_1w_2\cdots w_n\\) be a string where each \\(w_i\\) is a member of the
alphabet \\(\Sigma\\). Then \\(M\\) accepts \\(w\\) if a sequence of states
\\(r_0, r_1, \cdots, r_n\\) in \\(\mathbb{Q}\\) exists with three conditions:

1. \\(r_0 = q_0\\)
2. \\(\delta(r_i, w_{i+1}) = r_{i+1}, \\) for \\( i=0, \cdots, n-1, \\) and
3. \\(r_n \in F\\)

We say that \\(M\\) recognizes language \\(A\\) if
\\(A = \\{w\\ |\\ M\\) accepts \\(w\\}\\)

A **regular language** is a language where there exists a finite automaton that
recognizes it.

### The Regular Operations

Let \\(A\\) and \\(B\\) be languages. We define the regular operations **union**,
**concatenation**, and **star** as follows:

* Union: \\(A \cup B = \\{x\\ \vert\\ x \in A\\) or \\(x \in B\\}\\)
* Concatenation: \\(A \cup B = \\{xy\\ \vert\\ x \in A\\) and \\(y \in B\\}\\)
* Star: \\(A^* = \\{x_1x_2\cdots x_k\\ \vert\\ k \geq 0\\) and each \\(x_i \in A\\}\\)

**Theorem**: The class of regular languages is closed under the union,
concatenation, and star operators. (Future link to proof.)

### Example

---

## 1.2 Nondeterminism

### Definition of a Nondeterministic Finite Automaton (NFA)
A NFA can be represented by the 5-tuple
\\((\\mathbb{Q}, \Sigma, \delta, q_0, F)\\) or a state diagram. The 5-tuple
is formally described by:

1. \\(\mathbb{Q}\\) is a finite set called the **states**.
2. \\(\Sigma\\) is a finite **alphabet**.
3. \\(\delta : \mathbb{Q} \times \Sigma_\epsilon \rightarrow \mathcal{P}(\mathbb{Q})\\) is the **transition function**.
4. \\(q_0 \in \mathbb{Q}\\) is the **start state**.
5. \\(F \subseteq \mathbb{Q}\\) is the **set of accept states**.

Here we note that the only difference between a NFA and DFA is in the
\\(\delta\\) function. The alphabet has \\(\epsilon\\) added and we take the
set \\(\mathcal{P}(\mathbb{Q})\\) to be the set of possible transition states.

A NFA accepts a string \\(w = y_1y_0\cdots y_m\\) where each \\(y_i\\) is a
member of \\(\Sigma_\epsilon\\) and a sequence of states
\\(r_0, r_1, \cdots, r_m\\) exists such that the following conditions are
satisfied:

1. \\(r_0 = q_0\\)
2. \\(r_{i+1} \in \delta(r_i, y_{i+1})\\) for \\(i = 0, \cdots, m-1\\) and
3. \\(r_m \in F\\)

### Equivalence of NFA's and DFA's

**Equivalent**: Two machines are equivalent if they recognize the same language.

**Theorem**: Every nondeterministic finite automaton has an equivalent
deterministic finite automaton. (Future link to proof.)

**Corollary**: A language is regular if and only if some nondeterministic finite
automaton recognizes it.

### Closure Under the Regular Operations

This section contains proofs that the regular languages are closed under the
regular operations. Each proof utilizes an NFA in its construction.

**Theorem**: The class of regular languages is closed under the union operation.
(Future link to proof.)

**Theorem**: The class of regular languages is closed under the concatenation
operation. (Future link to proof.)

**Theorem**: The class of regular languages is closed under the star operation.
(Future link to proof.)

### Example

---

## 1.3 Regular Expressions

The regular operations (union, concatenation, star) can be used to build up
expressions describing languages. These are called **regular expressions**.
The value of a regular expression is a language.

Example: \\((0\Sigma^\star)\cup(\Sigma^{\star}1)\\) consists of all strings that start
with a \\(0\\) or end with a \\(1\\).

For regular expressions, the order of operations is are: 
\\(* \rightarrow \circ \rightarrow \cup\\)

### Formal Definition of a Regular Expression

We say that \\(R\\) is a **regular expression** if \\(R\\) is:

1. \\(a\\) for some \\(a \in \Sigma\\)
2. \\(\epsilon\\)
3. \\(\emptyset\\)
4. \\((R_1 \cup R_2)\\), where \\(R_1\\) and \\(R_2\\) are regular expressions.
5. \\((R_1 \circ R_2)\\), where \\(R_1\\) and \\(R_2\\) are regular expressions.
6. \\((R_1^\star)\\), where \\(R_1\\) is a regular expression.

This definition is not circular because \\(R_1\\) and \\(R_2\\) are always
smaller than \\(R\\).

The following two shorthands exist:

| Shorthand |              Meaning               |
|:---------:|:----------------------------------:|
| \\(R^+\\) |           \\(RR^\star\\)           |
| \\(R^k\\) | \\(k\ R\\)'s concatenated together |

In order to distinguish a regular expression \\(R\\) and the language that it
describes from each other, we write \\(L(R)\\) to be the language of \\(R\\).

The following key expressions are worth noting:

$$
  \begin{align}
    R &\cup \emptyset = R \\
    R &\circ \epsilon = R \\
    R &\cup \epsilon\ \ \ \text{may not equal } R \\
    R &\circ \emptyset\ \ \ \text{may not equal } R
  \end{align}
$$

### Equivalence with Finite Automata

**Theorem** - A language is regular if and only if some regular expression
describes it. (Future link to proof.)

### Generalized Nondeterministic Finite Automaton (GNFA)

A GNFA is the 5-tuple 
\\((\mathbb{Q}, \Sigma, \delta, q_{\text{start}}, q_{\text{accept}})\\) where:

1. \\(\mathbb{Q}\\) is the finite set of states.
2. \\(\Sigma\\) is the input alphabet.
3. \\(\delta : (\mathbb{Q} - \\{q_{\text{accept}}\\}) \times
   (\mathbb{Q} - \\{q_{\text{start}}\\}) \rightarrow R\\) is the transition.
4. \\(q_{\text{start}}\\) is the start state.
5. \\(q_{\text{accept}}\\) is the accept state.

A GNFA allows transition arrows to be labeled by regular expressions.

For convenience, GNFA's must have a special form that meets the following
conditions:
1. The start state has transition arrows going to every other state but no
   arrows coming in from any other state.
2. There is only a single accept state, and it has arrows coming in from every
   other state but no arrows going to any other state. Furthermore, the accept
   state is not the same as the start state.
3. Except for the start and accept states, one arrow goes from every state to
   every other state and also from each state to itself.

A GNFA accepts a string \\(w\\) in \\(\Sigma^\star\\) if
\\(w = w_1 w_2 \cdots w_k\\), where each \\(w_i\\) is in \\(\Sigma^\star\\) and
a sequence of states \\(q_0, q_2, \cdots, q_k\\) exists such that:

1. \\(q_0 = q_{\text{start}}\\) is the start state
2. \\(q_k = q_{\text{accept}}\\) is the accept state, and
3. For each \\(i\\), we have \\(w_i \in L(R_i)\\), where 
   \\(R_i = \delta(q_{i-1}, q_i)\\); in other words, \\(R_i\\) is the expression
   on the arrow from \\(q_{i-1}\\) to \\(q_i\\).

**Theorem** - For any GNFA \\(G\\), Convert\\(G\\) is equivalent to \\(G\\).
(Future link to proof.)

---

## 1.4 Nonregular Languages

To understand what a finite automaton can do, it helps to understand what they
can't do. For example, given:

\\[C = \\{w | w \text{has an equal number of 0's and 1's}\\}\\]
\\[D = \\{w | w \text{has an equal number of occurrences of 01 and 10
substrings}\\}\\]

The language \\(C\\) is nonregular, but \\(D\\) is regular. A way to determine
whether a language is regular or not is needed, because it's not always obvious
at first sight.

### The Pumping Lemma for Regular Languages

**Theorem** - Pumping Lemma: If \\(A\\) is a regular language, then there is a
number \\(p\\) (the pumping length) where if \\(s \in A\\) of length at least p,
then \\(s\\) may be divided into three pieces, \\(s = xyz\\), satisfying the
following conditions:

1. for each \\(i \geq 0, xy^iz \in A\\)
2. \\(\|y\| > 0\\)
3. \\(\|xy\| \leq p\\)

Use the following steps when using the pumping lemma to prove a language is
nonregular:

1. Assume that \\(B\\) is regular in order to obtain a contradiction.
2. Use the pumping lemma to guarantee the existence of a pumping length \\(p\\)
   such that all strings of length \\(p\\) or greater in \\(B\\) can be pumped.
3. Demonstrate that \\(s\\) cannot be pumped by considering all ways of dividing
\\(s\\) into \\(x, y, \text{and} z\\). For each such division, find a value 
\\(i\\) where \\(xy^iz \not\in B\\).