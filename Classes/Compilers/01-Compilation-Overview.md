---
layout: default
title: Compilers - Overview of Compilation
---

## 1.1 Introduction

### Conceptual Roadmap

Compilers have:
* Front end - deals with the source language.
* Back end - deals with the target language.

Connecting the front and back ends is a formal structure for representing the
program in an intermediate form.

### Overview

Compilers can take on different tasks depending on the goal at hand. Following
is three common tasks that a compiler is designed for:

1. **source-to-target translator**: A compiler that translates from a programming
language to the assembly code for the target machine.

2. **source-to-source translator**: A compiler that translates from one programming
language to another, avoiding the instruction set. The resulting translation
here will have to be compiled again into an instruction set for a given machine.

3. **interpreter**: Teakes as input an executable and produces as output the result
of executing the specification.

### Fundamental Principles of Compilation

1. The compiler must preserve the meaning of the program being compiled.
2. The compiler must improve the input program in some discernable way.

---

## 1.2 Compiler Structure

The code for a compiler is typically modularized into either a two phase or
three phase compiler. In a three phase compiler, the code is broken down into
the phases below:

(Image of three phase compiler.)

Each phase of the compiler contains multiple passes of the input. Traditionally
there are the following passes:

1. Front End
    1. Scanner
    2. Parser
    3. Elaboration
2. Optimizer
    1. Optimization 1
    2. Optimization 2
    3. ...
    4. Optimization 3
3. Back End
    1. Instruction Selection
    2. Instruction Scheduling
    3. Register Allocation

---

## 1.3 Overview of Translation

The following sections trace the path of the compiler for the following
expressions:

\\[a \leftarrow a \times 2 \times b \times c \times d\\]

### 1.3.1 The Front End

The first step is to understand the syntax and semantics. Once a program has
been validated, IR is created. If a program is not validated then diagnostic
errors messages are printed.

#### Checking Syntax

**Scanner**: The compiler pass that converts a string of characters into a
stream of words.

**Parser**: The compiler pass that determines if the input stream is a sentence
in the source language.

**Type Checking**: The compiler pass that checks for type-consistent uses of
names in the input program.

The source language is an infinite set of strings, called a **grammar**, that
is defined by a set of rules. The first pass over the source is the scanner,
which produces pairs \\((p, s)\\), with \\(p\\) as the part of speech and
\\(s\\) as the spelling.

Example: (noun, "Compilers"), (verb, "are"), (adjective, "engineered"),
(noun, "objects"), (endmark, ".")

Next the compiler tries to match the stream of categorized words against the
rules that specify the syntax. This is done by the parser. If matched, the input
belongs to the grammar.

#### Intermediate Representations

The last phase of the front end is to produce some type of IR. There are many
different types, one example of a low level, sequential IR is:

$$
  \begin{align}
    t_0 & \leftarrow a \times 2 \\
    t_1 & \leftarrow t_0 \times b \\
    t_2 & \leftarrow t_1 \times c \\
    t_3 & \leftarrow t_2 \times d \\
    a   & \leftarrow t_3
  \end{align}
$$

### 1.3.2 The Optimizer

Because the front end handles the statements of the source one at a time, it
doesn't see the bigger picture. One optimization technique that can minimize
the number of multiplications is shown in the following example:

~~~ java
b <- ...                          b <- ...
c <- ...                          c <- ...
a <- ...                          a <- 1
for i = 1 to n              -->   t <- 2 * b * c
  read d                          for i = 1 to n
  a <- a * 2 * b * c * d            read d
  end                               a <- a * d * t
                                    end
~~~

This significantly minimizes the number of multiplications required within the
loop for large values of n.

#### Analysis

**Data Flow Analysis**: A form of compile time reasoning about the runtime flow
of values.

Most optimizations consist of an analysis for safety, followed by a
transformation if found to be safe.

**Dependence Analysis**: A form of compile time reasoning about the values of
subscript expressions which uses number theoretic tests. Useful for arrays.

#### Transformation

There are a myriad of transformations. Some improve space and time requirements.
Some focus on optimizing loops. Others make code more compact.

### 1.3.3 The Back End

The job of the back end is to traverse the IR form of the code and output code
for the target machine. The order in which operations will be performed is
chosen. Determination of what lives in registers and what lives in memory is
also made.

#### Instruction Selection

**Virtual Register**: A symbolic register name that the compiler uses to
indicate that a value can be stored in a register.

From the IR representation, ILOC instructions (in the case of this course) are
chosen. One example is the conversion of the following IR:

$$
  \begin{align}
    t_0 & \leftarrow a \times 2 \\
    t_1 & \leftarrow t_0 \times b \\
    t_2 & \leftarrow t_1 \times c \\
    t_3 & \leftarrow t_2 \times d \\
    a   & \leftarrow t_3
  \end{align}
$$

This IR could be converted as follows:

~~~
loadAI  r_arp, @a   => ra          // load 'a'
loadI   2           => r_2         // constant 2 into r_2
loadAI  r_arp, @b   => r_b         // load 'b'
loadAI  r_arp, @c   => r_c         // load 'c'
loadAI  r_arp, @d   => r_d         // load 'd'
mult    r_a  , r_2  => r_a         // r_a <- a*2
mult    r_a  , r_b  => r_a         // r_a <- (a*2) * b
mult    r_a  , r_c  => r_a         // r_a <- (a*2*b) * c
mult    r_a  , r_d  => r_a         // r_a <- (a*2*b*c) * d
storeAI r_a         => r_arp, @a   // write r_a back to 'a'
~~~

The assumption here is that we have an unlimited supply of registers. This will
be fixed by the register allocation phase.

Based on the current target machine, the instruciton selector can choose to swap
some commands for more efficient ones based on the availability of commands on
a given machine.

#### Register Allocation

This phase fixes the choice made in the instruction selection phase to use
infinite registers. Fixing the example from the previous section, one possible
allocation is as follows:

~~~
loadAI  r_arp, @a   => r_1          // load 'a'
add     r_1  , r_1  => r_1          // r_1 <- a*2
loadAI  r_arp, @b   => r_2          // load 'b'
mult    r_1  , r_2  => r_1          // r_1 <- (a*2) * b
loadAI  r_arp, @c   => r_2          // load 'c'
mult    r_1  , r_2  => r_1          // r_1 <- (a*2*b) * c
loadAI  r_arp, @d   => r_2          // load 'd'
mult    r_1  , r_2  => r_1          // r_1 <- (a*2*b*c) * d
storeAI r_1         => r_arp, @a    // write r_a back to 'a'
~~~

Optimization is considered, should a value be left in a register since a
a command coming up uses it, or should it be moved into memory?

#### Instruction Scheduling

Different operations take different amounts of time. Scheduling can be
optimized so that a minimal number of cycles are used for a program. For
example, assume that loadAI and storeAI take 3 cycles, mult requires 2 cycles,
and all other operations require 1 cycle. We can rearrange the code from the
previous section in the following ways:

~~~
Start Cycle   End Cycle   
     1            3       loadAI  r_arp, @a   => r_1          // load 'a'
     4            4       add     r_1  , r_1  => r_1          // r_1 <- a*2
     5            7       loadAI  r_arp, @b   => r_2          // load 'b'
     8            9       mult    r_1  , r_2  => r_1          // r_1 <- (a*2) * b
    10           12       loadAI  r_arp, @c   => r_2          // load 'c'
    13           14       mult    r_1  , r_2  => r_1          // r_1 <- (a*2*b) * c
    15           17       loadAI  r_arp, @d   => r_2          // load 'd'
    18           19       mult    r_1  , r_2  => r_1          // r_1 <- (a*2*b*c) * d
    20           22       storeAI r_1         => r_arp, @a    // write r_a back to 'a'
~~~

~~~
Start Cycle   End Cycle   
     1            3       loadAI  r_arp, @a   => r_1          // load 'a'
     2            4       loadAI  r_arp, @b   => r_2          // load 'b'
     3            5       loadAI  r_arp, @c   => r_3          // load 'c'
     4            4       add     r_1  , r_1  => r_1          // r_1 <- a*2
     5            6       mult    r_1  , r_2  => r_1          // r_1 <- (a*2) * b
     6            8       loadAI  r_arp, @d   => r_2          // load 'd'
     7            8       mult    r_1  , r_3  => r_1          // r_1 <- (a*2*b) * c
     9           10       mult    r_1  , r_2  => r_1          // r_1 <- (a*2*b*c) * d
    11           13       storeAI r_1         => r_arp, @a    // write r_a back to 'a'
~~~

Notice here that reordering the operations changed the number of cycles from
22 to 13, and that both programs do exactly the same thing.