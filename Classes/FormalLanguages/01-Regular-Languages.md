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