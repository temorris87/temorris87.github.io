---
layout: default
title: Conway's Game of Life
---

## Introduction

This program set out to simulate 
![Conway's Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life). 
A video of the final program running is below:

<br>
<div class="text-center">
<iframe width="560" height="315" src="https://www.youtube.com/embed/47HRGkGEliU" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
</div>
<br>

## Technology Used

This program was created using test driven development. I had heard of the
technique before and felt this was a good project to dive in. As a result,
there are a bunch of test cases for the board state with 100% coverage over
the board. Design of the board was made in a way to be reusable, and in fact I
ended up pushing the code into a 
![Bomberman Clone](https://github.com/brianschillaci/Bomb-Barrage) named
Bomb Barrage, that I worked on with a few friends during the 2018 summer.

In order to help with any Pygame based questions that popped up in the
development process, I did the Game of Life graphics in Pygame. Due to the
nature of Game of Life, there was no need for collision detection, so only a
sub part of the entire API was used.

## Simulation

Each run of the simulation is different. Some runs of the program die out in
a few seconds, where others have run for 20 or so minutes. In the center of the
board there is a space where spaces are randomly made alive. The one in the
video is one of my favorite runs because it was symmetric throughout the whole
run.