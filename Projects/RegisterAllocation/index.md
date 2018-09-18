---
layout: default
title: Register Allocation
---

## Introduction

When taking a Compiler class in Spring 2018 at Rutgers University, the class
was asked to implement a Register Allocator for the backend of a compiler.
The compiler was for a small custom language created by the professor. The
input to the Register Allocator was an ILOC program that consisted took
the idea of using as many virtual registers as needed. Using different
allocation algorithms, the goal was to print ILOC back out that used the
physical registers of the machine.

## Report

The implementation was performed in Scheme. Due to fear of making the class
easier for a future student my code is not public on GitHub. However the
results of the different tested algorithms can be seen in the following pdf:

[Register Allocation Report](pdf/RegisterAllocation.pdf)
