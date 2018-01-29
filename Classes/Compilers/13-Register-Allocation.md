---
layout: default
title: Compilers - Register Allocation
---

## 13.1 Introduction

The register allocator determines which values live in registers and which
values live in memory.

### Conceptual Roadmap

The register allocator takes as input a program that uses an arbitrary number
of registers and produces a program that fits the finite registers of the
machine. The goal is to minimize the number of loads and stores that the code
must execute. Given that the algorithmic problems that underly register
allocation are hard, a good register allocator is one that computes a good
approximate solution quickly.

### Overview

**Spill Code** - Loads and stores inserted by the register allocator are spill 
code.

Most compilers use an IR to simplify the earlier processing of the code. There
are two methods:

1. memory-to-memory storage model
2. register-to-register storage model

With memory-to-memory, the register allocator promotes memory bound values
into registers in regions that are heavily used. With register-to-register,
the register allocator needs to determine the mapping of virtual registers to
either registers or memory.

In both of the above cases, the register allocator tries to minimize the effect
of spill code on performance.

---

## 13.2 Background Issues

By the time the register allocator runs, the program is almost completely
compiled. Earlier processes may have affected the shape of the code, which will
affect the shape of the register allocator. The following subsections explain
factors that can affect the shape of the register allocator.

### 13.2.1 Memory vs. Registers

Memory model choices include register-to-register and memory-to-memory. With the
register-to-register model, earlier phases encode knowledge of ambiguous
memory into the shape of the IR. Here, allocation is necessary. Virtual
registers must be mapped to machine registers. The allocator will shift some
values into memory where demand of machine registers exceeds supply.

With the memory-to-memory model, allocation is optional since the code is legal
to begin with. Allocation is an optimizer, trying to find the best memory
stored values to keep in registers. The allocator tries to remove as many loads
and stores as possible. Limitations on what a compiler knows tends to favor the
use of the register-to-register model. This comes because earlier phases can
do more computations to help the allocator.

### 13.2.2 Allocation vs. Assignment

The register allocator tries to solve two problems, allocation and assignment,
that in the past was sometimes done seperately.

1. **Allocation** - Maps an unlimited name space onto the register set of the
   target machine.
2. **Assignment** - Maps an allocated name set to the pysical registers of the
   target machine.

Register allocation is a hard problem. For a single basic block, with one size
of data value, optimal allocation can be done in polynomial time. This can only
happen if every value must be stored to memory and at the end of its lifetime
the cost of storing all values is uniform. Adding additional complexity makes it
a np-complete problem.

Register assignment can usually be solved in polynomial time. Assignment can be
produced in linear time using an analog of interval-graph coloring. The entire
procedure can be solved in polynomial time as long as the demand for physical
registers does not exceed the number of physical registers.

### 13.2.3 Register Classes

Most processors have distinct classes of register types. For example, a CPU can
have both general-purpose registers and floating-point registers. The CPU can
have any arbitrary number of distinct classes.

Register allocation must keep this in mind. Better code can be produced if
floating point registers are used before general purpose registers, but it
increases the complexity of the algorithm. Similary, if no floating-point
registers exist, then it becomes a pooling question of how to share the
registers.

---

## 13.3 Local Register Allocation and Assignment

The following conversations (13.3.1 and 13.3.2) on top-down and bottom-up
allocation are simplified and assume an input of a complete program that fits in
one block for simplicity. The target machine supplies a set of \\(k\\)
registers. Any code that can be kept in a register is, so the input code may
use more than \\(k\\) virtual registers.

Also assumed is that the input block contains a series of three-address
operations \\(o_1, o_2, o_3, \cdots, o_N\\). Each \\(o_i\\) has the form 
\\(op_i\\ vr_{i1}\\ vr_{i2} \rightarrow vr_{i3}\\) (\\(vr_i\\) denotes virtual
register \\(i\\) and \\(r_i\\) denotes register \\(i\\)). If the number of
virtual registers exceeds \\(k\\), then code spill occurs and the allocator must
insert loads and stores.

**Top-Down Allocation** - Strategy from book uses frequency counts.
**Bottom-Up Allocation** - Relies on detailed knowledge of the code block,
  determins on the way if a code spill is needed.

### 13.3.1 Top-Down Local Register Allocation

**Key heuristic** - Most heavily used values should reside in registers. The
  number of times each register appears in the block is noted. Allocation of
  virtual registers to physical registers occurs in descending order by
  frequency count.

**\\(\mathcal{F}\\)** - On any given ISA, \\(\mathcal{F}\\) is the number of
registers needed to generate code for values that live in memory. We pronounce
\\(\mathcal{F}\\) as "feasible".

When there are more virtual registers than physical registers, a number of
registers need to be kept unassigned for use in load and store operations. The
exact number will depend on the processor.

When there are less virtual registers than physical registers, assignment is
trivial. All virtual registers are mapped to physical registers and
\\(\mathcal{F}\\) doesn't need to be computed.

The following algorithm is used when the number of virtual registers exceeds
physical registers:

1. Compute a priority for each virtual register. (Frequency count of vr's)
2. Sort the virtual registers into priority order.
3. Assign registers in priority order. Assign the first \\(k - \mathcal{F}\\)
   registers to physical registers.
4. Rewrite the code.
    1. Replace virtual register names with physical register names.
    2. Any reference to a virtual register name with no allocated physical
       register is replaced with a short sequence that uses one of the reserved
       registers and performs the appropriate load or store operation.

The flaw with top-down allocation is that a virtual register gets assigned to
one physical register for the life of the program. If it is used heavily in the
first half of the program but not at all in the second half, the register's
potential is wasted.

### 13.3.2 Bottom-Up Local Register Allocation

Bottom-up allocation considers register allocation command by command. All
registers are initially empty and stored in a stack. The registers are given out
on demand one by one until there are no more. Then, when another value needs
to be used, the register that will be used furthest in the future is stored
into memory and replaced with the new value. The exact algorithm is shown next:

~~~
  struct Class {
    int Size;
    int Name[Size];
    int Next[Size];
    int Free[Size];
    int Stack[Size];
    int StackTop;
  }

  // Bottom-Up Algorithm:
  for each operation, i, in order from 1 to N where i has the form:
        op vri1, vri2 -> vri3
    rx <- Ensure(vri1, class(vri1))
    ry <- Ensure(vri2, class(vri2))
    if vri1 is not needed after i
      Free(rx, class(rx))
    if vri2 is not needed after i
      Free(ry, class(ry))
    r2 <- Allocate(vri3, class(vri3))
    rewrite i as opi rx, ry -> rz
    if vri1 is needed after i
      then class.Next[rx] <- Dist(vri1)
    if vri2 is needed after i
      then class.Next[ry] <- Dist(vri2)
    class.Next[r2] <- Dist(vri3)

  Ensure(vr, class)
    if vr is already in class
      then result <- vr's physical register
    else
      result <- Allocate(vr, class)
      emit code to move vr into result
    return result

  Allocate(vr, class)
    if class.StackTop >= 0
      i <- pop(class)
    else
      i <- j that maximizes class.Next[j]
      store contents of j
    class.Name[i] <- vr
    class.Next[i] <- -1
    class.Free[i] <- false
    return i
~~~

This problem becomes np-hard with the presence of both clean and dirty values.
A clean value is one where a store is unnecessary (ex. a constant), while a
dirty value is one that needs a store. There is a different cost for spilling a
clean value vs. the cost of spilling a dirty value. Finding the optimal
allocation in this situation is np-hard.

### 13.3.3 Moving Beyond Single Blocks

Top-down and bottom-up allocation is local in scope and focuses on a single
block. It is possible for multiple blocks to need values from previous blocks
or that values from a block will be used in later blocks. An efficient way to
compute this is needed.

#### Liveness and Live Ranges

**Live Ranges** - A closed set of related definitions and uses that serves
  as the base name space for register allocation.

Regional and global allocators compute a name space that reflects the actual
patterns of definitions and uses for each value. This technique uses live
ranges.

For example, the following is sample code with its associated live ranges:

~~~
  loadI    ...       -> rarp        Register       Interval
  loadAI  rarp, @a   -> ra            rarp         [ 1,11]
  loadI      2       -> r2             ra          [ 2, 7]
  loadAI  rarp, @b   -> rb             ra          [ 7, 8]
  loadAI  rarp, @c   -> rc             ra          [ 8, 9]
  loadAI  rarp, @d   -> rd             ra          [ 9,10]
  mult      ra, r2   -> ra             ra          [10,11]
  mult      ra, rb   -> ra             r2          [ 3, 7]
  mult      ra, rc   -> ra             rb          [ 4, 8]
  mult      ra, rd   -> ra             rc          [ 5, 9]
  storeAI   ra       -> rarp, @a       rd          [ 6,10]
~~~

To find live ranges in larger regions than one block, the allocator must
know when a value lives past the end of the block it's defined in. This can
be determined by Live Out sets as computed in section 8.6.1.

* This example is taken from the text book for the class. It is worth noting
live ranges were computed slightly differently in class, subtracting one from
the end of the interval compared to the current end in the interval. This is
because, at the last line in the interval you no longer need that given
register and can freely reuse it with no problems.

#### Complications and Block Boundaries

Using the Live Out set, at the end of a block all values that aren't needed
anymore are discarded and values that are needed in a later block are stored in
memory. Because top-down and bottom-up allocation both handle local register
allocation, the geometry of other blocks are not known. Therefore values needed
by other blocks must be stored in memory. There is no way to know at a local
scope which register another block expects values to be in. There are more
complicated global register allocation algorithms that ignore local allocation
to optimize use between blocks.