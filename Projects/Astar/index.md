---
layout: default
title: A*
---

## Introduction

This project simply implements the A* search algorithm. All of my big C and C++
projects were done for Systems and Operating Systems classes. The courses reuse
their projects every semester and I didn't want to make code public for others
to cheat with. So to show that I can code in a lower level language this
algorithm is implemented in C++.

## Description and Results

A* expands nodes based on the equation:

$$f(x) = c(x) + h(x)$$

where $c(x)$ is the cost of traveling from one node to another and $h(x)$ is
the heuristic cost that under estimates the distance to the goal node. In the
case of this implementation the heuristic function was taken to be euclidian
distance:

$$h(x) = sqrt{x^2 + y^2}$$

This function is not a strong enough heuristic to yield the shortest path.

In the graph that follows, the algorithm was asked to start in the left hand
corner and move to the right hand corner:

<br>
<div class="text-center">
    <img class="img-fluid" src="./img/astar.svg">
</div>
<br>

The path that was obtained from the output of the program is highlighted in
orange. Note that there is a shorter path that a breadth first search would
have picked up.