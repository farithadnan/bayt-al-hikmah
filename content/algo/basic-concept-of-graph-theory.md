---
title: Basic Concept of Graph Theory
draft: false
tags:
  - algorithms
---

This note explains the fundamental terminology and basic concepts of [[graph-theory.md|Graph Theory]].

## Vertex, Vertices

**Vertex** and **Vertices**, also known as **node** and **nodes**, originate from [[graph-theory.md|Graph Theory]]. I still remember when I was confused by these words—it’s actually simple but, my brain tend to overcomplicated things. In simple terms, **Vertex** is a single point or node in graph. Think of it as a dot representing an object such as a car, city, computer or person, while **Vertices** refers to more than one node in a graph.

> [!info] In Short...
>
> - **Vertex**: Single dot, node or object in a graph.
> - **Vertices**: More than one dots, nodes, or object in a graph.
> - **Vertices** is the plural form (multiple dots or nodes) of "vertex".

## Edges or Links

**Edges** are the lines that represent connection between vertices, you can imagine it this way. A vertex is an island, while vertices are multiple islands, and edges are the bridges connecting the island.

### Multiple Edges

**Multiple edges** (or **parallel edges**) mean that a single pair of vertices can have more than one edge (or connection) between them. These kinds of graphs are referred to as **[[Multigraph Graph|multigraphs]]**.

## Loops

A **loop** is a special type of edge that connects a vertex to itself. In other words, it’s an edge that starts and ends at the same vertex.

---

## 🔗 Sources

1. [Fundamentals of Graph Theory - GeeksforGeeks](https://www.geeksforgeeks.org/fundamentals-of-graph-theory/)
