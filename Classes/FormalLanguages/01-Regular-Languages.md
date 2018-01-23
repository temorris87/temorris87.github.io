---
layout: default
title: Formal Languages and Automata - Regular Languages
---

## 1.1 Finite Automata

### Definition of a Deterministic Finite Automaton (DFA)
An automaton is a finite state machine that, given an input, either accepts
or rejects the input. A DFA can be represented by the formal definition to 
follow, or a state diagram (see state diagram in example):

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

The class of regular languages is closed under the union, concatenation, and
star operators. (Future link to proof.)

### Example

## 1.2 Nondeterminism