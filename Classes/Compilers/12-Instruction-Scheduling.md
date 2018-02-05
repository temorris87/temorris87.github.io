---
layout: default
title: Compilers - Instruction Scheduling
---

## 12.1 Introduction

The order in which operations are executed on a processor matters. Different
operations take different amounts of time. As an example, addition and
subtraction take less time that integer division on a typical processor.

The task of ordering the operations in a block or a procedure to make effective
use of processor resources is called **instruction scheduling**. The input to
an instruction scheduler is a partially ordered list of operations in the
target machine's assembly language and produces an ordered version of the same
list. This is under the assumption that optimization has already occored.

### Conceptual Roadmap

Reordering of operations takes into account:

1. flow of data
2. delays associated with individual operations
3. capabilities of the target processor

There are multiple techniques for both scheduling basic blocks and larger areas
of code, but the dominant technique used is a greedy heuristic called list
scheduling.

### Overview

Processors will overlap the execution of operations, issuing successive
operations as quickly as possible given the finite (and small) set of functional
units. A difficulty arises with this strategy when an operation issues before
its operands are ready. This situation is handled in one of two ways:

1. **stall**: a delay caused by a hardware interlock that prevents a value from
   being read until its defining operation completes. An interlock is the
   mechanism that detects the premature issue and creates the actual delay.
2. Execute the operation with incorrect operands.

For the first case, the scheduler reorders operations to minimize these stalls.
For case two nops are inserted to fill the gap.

**Statically scheduled** - A processor that relies on compiler insertion of
nops for correctness is a statically scheduled processor.

**Dynamically scheduled** - A processor that provides interlocks to ensure
correctness is a dynamically scheduled processor.

For some instructions, the number of cycles used is constant while for others
it is variable. Additionally, some processors have the ability to execute more
than one operation each cycle. Such processors are called superscalar
processors. The instruction dispatch hardware has a limited amount of lookahead
so the instruction scheduler needs to pay attention to both the cycle of a given
instruction and the relative ordering of operations within each cycle.

The instruction scheduler has three goals:

1. Preserve the meaning of the code it receives as input.
2. Minimize execution time by avoiding stalls and nops.
3. Avoid increasing value lifetimes past the point where additional register
   spills are necessary.

---

## 12.2 The Instruction-Scheduling Problem

((Copy example on cycles from chapter 1))

Examples have all targeted machines which use a single operation each cycle.
Almost all processors, however, can perform multiple operations each cycle. The
algorithm to follow will work on single operation machines, followed by a
discussion of changes required to make the algorighm work on multioperation
machines.

**Dependence graph** - For a block \\(b\\), its dependence graph
\\(\mathcal{D} = (N, E)\\) has a node for each operation in \\(b\\). An edge
\\(\mathcal{D}\\) connects two nodes \\(n_1\\) and \\(n_2\\) if \\(n_2\\) uses
the result of \\(n\\).

The instruction scheduling problem is defined over the dependence graph
\\(\mathcal{D}\\) of a basic block. Edges in \\(\mathcal{D}\\) represent
the flow of values in the block. Each node has two attributes, an operation
type and a delay.

We use the following code to produce the example dependence graph:

~~~
a:  loadAI  rarp, @a  =>  r1
b:  add     r1,   r1  =>  r1
c:  loadAI  rarp, @b  =>  r2
d:  mult    r1,   r2  =>  r1
e:  loadAI  rarp, @c  =>  r3
f:  mult    r1,   r2  =>  r1
g:  loadAI  rarp, @d  =>  r2
h:  mult    r1,   r2  =>  r1
i:  storeAI r1        =>  rarp, @a
~~~

((Need to put graph image here))

The leaves of the tree in \\(\mathcal{D}\\) (a, c, e, g) can be scheduled
immediately since nothing else depends on them.

Given a dependence graph \\(\mathcal{D}\\) for a code fragment, a schedule
\\(S\\) maps each node \\(n \in N\\) to a nonnegative integer that denotes the
cydle in which it should be issued. A schedule must meed the following three
constraints:

1. \\(S(n) \geq 1\\) for each \\(n \in N\\). Additinally, there must be one
   operation \\(n'\\) with \\(S(n') = 1\\).
2. If \\(n_1, n_2 \in E\\) then \\(S(n_1) + \text{delay}(n_1) \leq S(n_2)\\).
   This constraint ensures that an operation \\(n_2\\) is not performed before
   its operands are ready.
3. Each instruction contains no more operations of each type \\(t\\) than the
   target machine can issue in a cycle. This constraint enforces feasibility.

If a schedule follows these rules, the length of the schedule is the cycle
number in which the last operation completes. Length can be computed:

\\[L(S) = \max_{n \in N} (S(n) + \text{delay}(n))\\]

We can annotate our dependence graph with cumulative latencies to obtain the
following dependence graph:  (Graph to be added, for now see page 646 in text.)

The annotations show that the longest path of the program is abdfhi, utilizing
13 cycles. This particular path is called the **critical path**, which
determines the overall execution time for this example.

To schedule this computation, we first look at candidates that have no
predecessors. In this case those are a, c, e, and g. Since a lies on the
critical path, it gets scheduled first. Next should be c. Then there's a tie at
10 cycles between e and b. Here though, b's operand won't be available until the
next cycle so e gets scheduled. Following this logic all the way through you get
the schedule: acebdgfhi.

**Antidependence** - Operation \\(x\\) is antidependent on operation \\(y\\) if
\\(x\\) precedes \\(y\\) and \\(y\\) defines a value used in \\(x\\). Reversing
their order of execution could cause \\(x\\) to compute a different value.

Care in scheduling should be taken in order to avoid scheduling operations in
the wrong order when an antidependence is present. Correct code can be produced
in the case of antidependences two ways:

1. Discover antidependences that are present in the input code and respect them
   in the final schedule.
2. Rename the values to avoid them.

The above example contains four antidependences: \\(e \rightarrow c, e
\rightarrow d, g \rightarrow e, \text{and} g \rightarrow f\\).

Respecting antidependences changes the set of valid paths, and thus limits
different optimizations of the code. Renaming values to avoid antidependences
can potentially lead to spill code, which could drastically change the number of
cycles needed. Therefore, one approach is not necessarily better than the other.

### 12.2.1 Other Measures of Schedule Quality
1. Number of live values.
2. Number of nops (some processors have a variable cycle nop command)
3. Energy

### 12.2.2 What Makes Scheduling Hard?

To look at all possible schedules and find the best one is a np-complete
problem. Additionally, you must also consider all antidependences, since
changing the order of operations in the presence of an antidependence changes
the meaning of the program. Therefore, in practice, most algorithms use a greedy
heuristic to approximate an optimal solution.

---

## 12.3 Local List Scheduling

The list scheduling algorithm is popular but it is more of an approach than a
fixed algorithm. The presented algorithm is a greedy approach that discovers
reasonable schedules, but in practice can be modified easily.

### 12.3.1 The Algorithm

This sections algorithm is for a single block. Cross block scheduling has to
consider even more possibilities, so we start with a simpler case.

List scheduling over a block is done with the following algorithm:

1. Rename to avoid antidependences. This is an optional phase but it opens up
   schedules that wouldn't have normally been available with an added risk for
   additional spill code.
2. Build a dependence graph \\(\mathcal{D}\\). To build the dependence graph,
   the scheduler walks the block from bottom to top. For each operation, it
   constructs a node to represent the newly created value. It adds edges from that
   node to each node that uses the value. Each edge is annotated with the latency
   of the current operation. (If the scheduler does not perform renaming, 
   \\(\mathcal{D})\\) must represent antidependences as well.)
3. Assign priorities to each operation. There are different priority schemes,
   though one obvious one is to use the longest latency path as a metric.
4. Iteratively select an operation and schedule it. To schedule operations, the
   algorithm starts in the block's first cycle and chooses as many operations as
   possible to issue in that cycle. It then increments its cycle counter, updates
   it notion of which operations are ready to execute, and schedules the next
   cycle. It repeats this process until each operation has been scheduled.

The key to this algorothm is the heart of the list scheduler, which is:

~~~
Cycle <- 1
Ready <- leaves of D
Active <- 0
while (Ready and Active != 0)
  for each op in Active
    if S(op) + delay(op) < Cycle then
      remove op from Active
      for each successor s of op in D
        if s is ready
          then add s to Ready
  if Ready != 0 then
    remove an op from Ready
    S(op) <- Cycle
    add op to Active
  Cycle <- Cycle + 1
~~~

### 12.3.2 Scheduling Operations with Variable Delays

In order to minimize cache misses, a technique called balanced scheduilng can be
used. This technique schedules the load based on the operations around it
instead of the hardware on which it operates. The algorithm is as follows:

~~~
for each load operation, l, in the block
  delay(l) = 1

for each operation i in D
  let D_i be the nodes and edges in D independent of i
  for each connected component C of D_i do
    find the maximal number of loads, N, on any path through C
    for each load operation l in C
      delay(l) <- delay(l) + delay(i) / N
~~~

### 12.3.3 Extending the Algorithm

The current algorithm assumes only one operation can be issued per cycle. On
processors where more than one can be issued, the while loop should be expanded
with an operation that assigns each all functional unites every cycle.

There is complexity that arises when some operations can be used on multiple
functional units while others cannot. A heuristic for deciding an order must be
decided upon.

At block boundaries, for the first cycle of the new block, there is no guarantee
that all operands have been prepared by the previous block. Assigning operations
for blocks in reverse order fixes this problem, though it does not help with
loop-closing branches.

### 12.3.4 Tie Breaking in the List Scheduling Algorithm

When two or more items have the same rank for scheduling, the scheduler should
break the tie based on another priority ranking. Some options are:

1. Number of immediate successors in \\(\mathcal{D}\\).
2. Number of descendents a node has in \\(\mathcal{D}\\).
3. The delay of a node.
4. Number of operands for which this operation is the last use.

None of the heuristics are necessarily better than another.

### 12.3.5 Forward vs. Backward List Scheduling

Instruction scheduling is not an expensive part of compilation. Therefore it
isn't a bad idea to additionally run the list scheduler in reverse order over
the dependence graph. In this way, the schedule is build by starting at the root
of the graph and working up to the leaves. Then the better of the two schedules
is kept.

### 12.3.6 Improving the Efficiency of List Scheduling

Replacing the Ready list with a priority queue reduces runtime from 
\\(O(n^2)\\) to \\(O(n\log n)\\). A similar approach with a priority queue on
the Active list can be made.