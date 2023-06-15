---
title: "Conway's Game Of Life"
seoTitle: "Conway's game of life"
datePublished: Sat Jun 05 2021 10:02:15 GMT+0000 (Coordinated Universal Time)
cuid: ckpjl7uj307cyw9s1cxkn18v6
slug: conways-game-of-life
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1622885641583/PFhQRh6Pq.jpeg
tags: css, javascript

---

# Introduction
Hello dear readers 👋🏻, hope you all are doing well. So I recently made a project in JavaScript which simulates the **Conway's Game Of Life** and decided to share my knowledge in this community.
 
You can check the simulator here:
https://juju2181.github.io/Game_Of_Life.io/

# WHAT IS CONWAY'S GAME OF LIFE?
Conway's Game Of Life is a cellular automation devised by British mathematician **John Horton Conway**. It is zero player game, meaning that it needs a single input to trigger the start of game and then it requires no further input. The player will have to firstly create an initial configuration and then we observe how the generation will evolve. It is Turing Complete problem and can simulate a universal constructor or any other Turing machines.

## RULES FOR CONWAY'S GAME OF LIFE
- The universe of the Game of life is infinite, two-dimensional orthogonal grid of square cells, each of which can be in either of two states, live or dead. Every cell will interact with its eight neighbors which are the cells that are horizontal, vertical and diagonally adjacent. 
- Any live cell with less than two live neighbors will die due to underpopulation.
- Any live cell with two or three live neighbor will live on to next generation.
- Any live cell with more than three live neighbor dies, as if by overpopulation
- Any dead cell with exactly three live neighbors becomes a live cell as if by reproduction

## PATTERNS
In Conway's game of life, many different types of patterns exist which are classified according to their behavior. These common patterns are mainly classified into three types :

1) **Still Lives** : These don't change their state from one generation to next.

2) **Oscillators** : These return to their initial state after a finite number of generations.

3) **Spaceships** : These translate themselves according to grid.
 
Take a look at some of the patterns


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1622886591338/91pIiUvVa.png)

To know more about these patterns you can check this website:

https://conwaylife.appspot.com/library

### CODE:
You may also be wondering how I implemented the Game of Life in JavaScript. Well look no further You can get the code from the Github Repo here:

https://github.com/JuJu2181/Game_Of_Life.io

## CONCLUSION
So in a nutshell, The Conway's Game Of Life is a simple cellular automation. I had real fun building this project in JavaScript and hope you like this too. Adios 👋🏻