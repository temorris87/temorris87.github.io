---
layout: default
title: Formal Languages and Automata - Context-Free Languages
---

## 2.1 Context-Free Grammars

An example context-free grammar that recognizes the language
\\(\\{0^n1^n | n \geq 0\\}\\) is:

$$
  \begin{align}
    A &\rightarrow 0A1 \\
    A &\rightarrow B \\
    B &\rightarrow \epsilon
  \end{align}
$$

As shown above, a grammar is a collection of **substitution rules**, also called
**productions**. Each rule consists of a variable (\\(A\\) and \\(B\\) above),
and a string separated by an arrow. Strings are made up of variables and
**terminals**. A terminal is a symbol that can't be reduced by a substitution
rule. One variable is designated as the **start variable**, typically the first
rule in the list.

You can generate a string in a grammar with the following rules:

1. Write down the start variable. It is the variable on the left hand side of
   the top rule, unless specified otherwise.
2. Find a variable that is written down and a rule that starts with that
   variable. Replace the written down variable with the right hand side of that
   rule.
3. Repeat step 2 until no variables  remain.

Example: \\(A \rightarrow 0A1 \rightarrow 00A11 \rightarrow 0011\\)

**Context-free language** - any language that can be generated by a context free
grammar.

### Formal Definition of a Context-Free Grammar

Definition: A context-free grammar is a 4-tuple \\((V, \Sigma, R, S)\\) where
1. \\(V\\) is a finite set called the **variables**
2. \\(\Sigma\\) is a finite set, disjoint from \\(V\\), called the
   **terminals**.
3. \\(R\\) is a finite set of rules, with each rule being a variable and a
   string of variables and terminals.
4. \\(S \in V\\) is the start variable.

The language of the grammar is \\(\\{w \in \Sigma^* \| S \rightarrow^* w \\}\\).

### Designing Context-Free Grammars

1. If a context free language is the union of smaller context free languages,
   create an or rule that branches to the two simpler languages.
2. If a language is regular, construct a DFA first. With the DFA, convert to a
CFG by:
    1. Make a variable \\(R_i\\) for each state \\(q_i\\) of the DFA.
    2. Add the rule \\(R_i \rightarrow aR_j\\) to the CFG if
    \\(\delta(q_i, a) = q_j\\) is a transition in the DFA.
    3. Add the rule \\(R_i \rightarrow \epsilon\\) if \\(q_i\\) is an accept
    state of the DFA.
    4. Make \\(R_0\\) the start variable of the grammar, where \\(q_0\\) is the
    start state of the machine.
3. Utilize idea from \\(\\{0^n1^n \| n \geq 0\\}\\) having equivalent production
of \\(R \rightarrow uRv\\).

### Ambiguity

A derivation of a string \\(w\\) in a grammar \\(G\\) is a 
**leftmost derivation** if at every step the leftmost variable is the one
replaced.

A string \\(w\\) is derived ambiguously in context-free grammar \\(G\\) if it
has two or more different leftmost derivations. Grammar \\(G\\) is ambiguous if
it generates some string ambiguously.

A CFG is inherently ambiguous if it can be generated by an ambiguous grammar.

### Chomsky Normal Form

A context-free grammar is in **Chomsky normal form** if every rule is of the
form:

$$
  \begin{align}
    A &\rightarrow BC \\
    A &\rightarrow a
  \end{align}
$$

where \\(a\\) is any terminal and A, B, and C are any variables - except that B
and C may not be the start variable. In addition, we permit the rule
\\(S \rightarrow \epsilon\\), where \\(S\\) is the start variable.

**Theorem** - Any context-free language is generated by a context-free grammar
in Chomsky normal form.