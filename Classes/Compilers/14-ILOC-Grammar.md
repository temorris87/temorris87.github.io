---
layout: default
title: Compilers - ILOC
---

## 14.1 ILOC Grammar

The ILOC language is represented by the following context free grammar:

~~~
  IlocProgram     ->  InstructionList
  InstructionList ->  Instruction
                   |  Instruction
                   |  label : Instruction
                   |  Instruction InstructionList
  Instruction     ->  Operation
                   |  [ OperationList ]
  OperationList   ->  Operation
                   |  Operation ; OperationList
  Operation       ->  NormalOp
                   |  ControlFlowOp
  NormalOp        ->  Opcode OperandList => OperandList
  ControlFlowOp   ->  cbr   register -> label , label
                   |  jumpI          -> label
                   |  jump           -> register
  OperandList     ->  Operand
                   |  Operand , OperandList
  Operand         ->  register
                   |  num
                   |  label
~~~

---

## 14.2 Naming Conventions

The following conventions are used for written ILOC code:

1. Memory offsets for variables are represented symbolically by prefixing
   the variable name with the @ character.
2. The user can assume an unlimited supply of registers.
3. The register \\(r_{arp}\\) is reserved as a pointer to the current activation
   record. 

Comments are handled in ILOC by using two forward slashes: //

---

## 14.3 Individual Operations

### 14.3.1 Opcodes

#### Arithmetic

| Opcode |    Sources     |  Targets  | Meaning                         |
|:-------|:--------------:|:---------:|:--------------------------------|
| nop    |      none      |   none    | Used as a placeholder           |
| add    | \\(r_1, r_2\\) | \\(r_3\\) | \\(r_1 + r_2 \rightarrow r_3\\) |
| sub    | \\(r_1, r_2\\) | \\(r_3\\) | \\(r_1 - r_2 \rightarrow r_3\\) |
| mult   | \\(r_1, r_2\\) | \\(r_3\\) | \\(r_1 * r_2 \rightarrow r_3\\) |
| div    | \\(r_1, r_2\\) | \\(r_3\\) | \\(r_1 / r_2 \rightarrow r_3\\) |
| addI   | \\(r_1, c_2\\) | \\(r_3\\) | \\(r_1 + c_2 \rightarrow r_3\\) |
| subI   | \\(r_1, c_2\\) | \\(r_3\\) | \\(r_1 - c_2 \rightarrow r_3\\) |
| rsubI  | \\(r_1, c_2\\) | \\(r_3\\) | \\(c_2 - r_1 \rightarrow r_3\\) |
| multI  | \\(r_1, c_2\\) | \\(r_3\\) | \\(r_1 * c_2 \rightarrow r_3\\) |
| divI   | \\(r_1, c_2\\) | \\(r_3\\) | \\(r_1 / c_2 \rightarrow r_3\\) |
| rdivI  | \\(r_1, c_2\\) | \\(r_3\\) | \\(c_2 / r_1 \rightarrow r_3\\) |

#### Shifting

| Opcode  |    Sources     |  Targets  | Meaning                           |
|:--------|:--------------:|:---------:|:----------------------------------|
| lshift  | \\(r_1, r_2\\) | \\(r_3\\) | \\(r_1 \ll r_2 \rightarrow r_3\\) |
| lshiftI | \\(r_1, c_2\\) | \\(r_3\\) | \\(r_1 \ll c_2 \rightarrow r_3\\) |
| rshift  | \\(r_1, r_2\\) | \\(r_3\\) | \\(r_1 \gg r_2 \rightarrow r_3\\) |
| rshiftI | \\(r_1, c_2\\) | \\(r_3\\) | \\(r_1 \gg c_2 \rightarrow r_3\\) |

#### Boolean Operators

| Opcode |    Sources     |  Targets  | Meaning                              |
|:-------|:--------------:|:---------:|:-------------------------------------|
| and    | \\(r_1, r_2\\) | \\(r_3\\) | \\(r_1 \land r_2 \rightarrow r_3\\)  |
| andI   | \\(r_1, c_2\\) | \\(r_3\\) | \\(r_1 \land r_2 \rightarrow r_3\\)  |
| or     | \\(r_1, r_2\\) | \\(r_3\\) | \\(r_1 \lor r_2 \rightarrow r_3\\)   |
| orI    | \\(r_1, c_2\\) | \\(r_3\\) | \\(r_1 \lor r_2 \rightarrow r_3\\)   |
| xor    | \\(r_1, r_2\\) | \\(r_3\\) | \\(r_1 \oplus r_2 \rightarrow r_3\\) |
| xorI   | \\(r_1, c_2\\) | \\(r_3\\) | \\(r_1 \oplus r_2 \rightarrow r_3\\) |

#### Loading

| Opcode |    Sources     |  Targets  | Meaning                               |
|:-------|:--------------:|:---------:|:--------------------------------------|
| loadI  |   \\(c_1\\)    | \\(r_2\\) | \\(c_1 \rightarrow r_2\\)             |
| load   |   \\(r_1\\)    | \\(r_2\\) | Memory\\((r_1) \rightarrow r_2\\)     |
| loadAI | \\(r_1, c_2\\) | \\(r_3\\) | Memory\\((r_1+c_2) \rightarrow r_3\\) |
| loadAO | \\(r_1, r_2\\) | \\(r_3\\) | Memory\\((r_1+r_2) \rightarrow r_3\\) |

#### Storing

| Opcode  |  Sources  |    Targets     | Meaning                                       |
|:--------|:---------:|:--------------:|:----------------------------------------------|
| store   | \\(r_1\\) |   \\(r_2\\)    | \\(r_1 \rightarrow\\) Memory\\((r_2)\\)       |
| storeAI | \\(r_1\\) | \\(r_2, c_3\\) | \\(r_1 \rightarrow\\) Memory\\((r_2 + c_3)\\) |
| storeAO | \\(r_1\\) | \\(r_2, r_3\\) | \\(r_1 \rightarrow\\) Memory\\((r_2 + r_3)\\) |

### 14.3.2 Control-Flow Operations

#### Standard Control Flow

| Opcode |    Sources     |    Targets     | Meaning                                            |
|:-------|:--------------:|:--------------:|:---------------------------------------------------|
| jump   |       -        |   \\(r_1\\)    | \\(r_1 \rightarrow PC\\)                           |
| jumpI  |       -        |   \\(l_1\\)    | \\(l_1 \rightarrow PC\\)                           |
| cbr    |   \\(r_1\\)    | \\(l_1, l_3\\) | \\(l_2 \rightarrow PC\\)&nbsp; if \\(r_1 =\\) true |
|        |                |                | \\(l_3 \rightarrow PC\\)&nbsp; otherwise           |
| tbl    | \\(r_1, l_2\\) |       -        | \\(r_1\\)&nbsp; might hold \\(l_2\\)               |

#### Comparisons

| Opcode |    Sources     |  Targets  | Meaning                                                |
|:-------|:--------------:|:---------:|:-------------------------------------------------------|
| cmp_LT | \\(r_1, r_2\\) | \\(r_3\\) | true \\(\rightarrow r_3\\)&nbsp; if \\(r_1 < r_2\\)    |
|        |                |           | false \\(\rightarrow r_3\\)&nbsp; otherwise            |
| cmp_LE | \\(r_1, r_2\\) | \\(r_3\\) | true \\(\rightarrow r_3\\)&nbsp; if \\(r_1 \leq r_2\\) |
|        |                |           | false \\(\rightarrow r_3\\)&nbsp; otherwise            |
| cmp_EQ | \\(r_1, r_2\\) | \\(r_3\\) | true \\(\rightarrow r_3\\)&nbsp; if \\(r_1 = r_2\\)    |
|        |                |           | false \\(\rightarrow r_3\\)&nbsp; otherwise            |
| cmp_GE | \\(r_1, r_2\\) | \\(r_3\\) | true \\(\rightarrow r_3\\)&nbsp; if \\(r_1 \geq r_2\\) |
|        |                |           | false \\(\rightarrow r_3\\)&nbsp; otherwise            |
| cmp_GT | \\(r_1, r_2\\) | \\(r_3\\) | true \\(\rightarrow r_3\\)&nbsp; if \\(r_1 > r_2\\)    |
|        |                |           | false \\(\rightarrow r_3\\)&nbsp; otherwise            |
| cmp_NT | \\(r_1, r_2\\) | \\(r_3\\) | true \\(\rightarrow r_3\\)&nbsp; if \\(r_1 \neq r_2\\) |
|        |                |           | false \\(\rightarrow r_3\\)&nbsp; otherwise            |

#### Conditional Branches

| Opcode |    Sources     |    Targets     | Meaning                                           |
|:-------|:--------------:|:--------------:|:--------------------------------------------------|
| comp   | \\(r_1, r_2\\) |   \\(cc_3\\)   | sets \\(cc_3\\)                                   |
| cbr_LT |   \\(cc_1\\)   | \\(l_2, l_3\\) | \\(l_2 \rightarrow PC\\)&nbsp; if \\(cc_3 = LT\\) |
|        |                |                | \\(l_3 \rightarrow PC\\)&nbsp; otherwise          |
| cbr_LE |   \\(cc_1\\)   | \\(l_2, l_3\\) | \\(l_2 \rightarrow PC\\)&nbsp; if \\(cc_3 = LE\\) |
|        |                |                | \\(l_3 \rightarrow PC\\)&nbsp; otherwise          |
| cbr_EQ |   \\(cc_1\\)   | \\(l_2, l_3\\) | \\(l_2 \rightarrow PC\\)&nbsp; if \\(cc_3 = EQ\\) |
|        |                |                | \\(l_3 \rightarrow PC\\)&nbsp; otherwise          |
| cbr_GE |   \\(cc_1\\)   | \\(l_2, l_3\\) | \\(l_2 \rightarrow PC\\)&nbsp; if \\(cc_3 = GE\\) |
|        |                |                | \\(l_3 \rightarrow PC\\)&nbsp; otherwise          |
| cbr_GT |   \\(cc_1\\)   | \\(l_2, l_3\\) | \\(l_2 \rightarrow PC\\)&nbsp; if \\(cc_3 = GT\\) |
|        |                |                | \\(l_3 \rightarrow PC\\)&nbsp; otherwise          |
| cbr_NE |   \\(cc_1\\)   | \\(l_2, l_3\\) | \\(l_2 \rightarrow PC\\)&nbsp; if \\(cc_3 = NE\\) |
|        |                |                | \\(l_3 \rightarrow PC\\)&nbsp; otherwise          |
