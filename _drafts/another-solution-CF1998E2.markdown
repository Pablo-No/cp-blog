---
layout: post
title:  "Another solution for CF1919F2"
---

This article discusses the problem [CF1919F2 (Hard Version)](https://codeforces.com/contest/1919/problem/F2).

If you want to try to solve it for yourself do not continue reading, because I will now describe the solution.

I will not describe the official solution in $$O(n \log n)$$ which uses a segment tree an the Max-Flow Min-Cut theorem. Although this was my initial Idea I was unable to come up with the crutial observation that there are two types of cuts we can have on each segment and therefore we can consider each pair of ends and combine the answers.

Instead this solutions only uses uses a segment tree, and some graph for each node with residual capacities and also works in $$O(n \log n)$$. Note that I am not the only one that came up with this idea, see for instance [this comment](https://codeforces.com/blog/entry/124220?#comment-1104081).

Although this is not a very powerful idea I thought it was pretty enough to make a blog on it.

So, what are we going to do. If you look at the whole graph we are trying to push flow through it looks that we can decompose it into subgraphs and work with them individually.

PICTURE 1

However, we cannot simply do that, becuase by combining two of these graphs the remaining residual graph has not the same shape.

PICTURE 2

It would seem that this idea cannot work, but if we look more closely we can see that the remaining residual graph of any segment always has this shape or an equivalent one.

PICTURE 3

Now, the only remaining step is to see explicitly how to merge two of these subgraphs. It turns out it's very easy, we only need to classify according to the edges which get saturated and sometimes change it to an equivalent graph that has the same shape as the original ones.

PICTURE 4

Now we can code a simple segment tree and solve a 2800 difficulty problem easily.
