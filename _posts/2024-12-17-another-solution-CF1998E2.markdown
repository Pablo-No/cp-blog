---
layout: post
title:  "A linear time solution for CF1998E2"
---

This article discusses the problem [CF1998E2 Eliminating Balls With Merging (Hard Version)](https://codeforces.com/contest/1998/problem/E2).
I will not describe the official solution in $$\mathcal{O}(n\log n)$$, which I couldn't come up with (if you are interested you can read the editorial).
Instead, I will describe a linear time solution using treaps. The solution doesn't need to actually write the treap, so it is very fast and simple to implement.

I am not the only one that came up with an $$\mathcal{O}(n)$$ solution, see for instance [this comment](https://codeforces.com/blog/entry/130647?#comment-1181745).

The solution idea is very nice because it is a natural generalization of the easy version of the problem.

The [easy version](https://codeforces.com/contest/1998/problem/E1) is as follows:

You have a list of labeled balls in a row with a certain weight, you can merge two adjacent balls adding their weights
but only the label of the greater ball can be assigned to the resulting ball (if they have equal weights you can choose).
We are asked how many labels can be the only remaining label at the end.

There's a natural D&C approach to this problem.
You can process an interval by splitting on the maximum value, then for the subinterval to the left and the right you can 
calculate how many elements can be the last one remaining, combining the results only depends on:

- The answer for the left and right parts.
- The sum of elements in the left and right parts.
- The value of the element in the middle.

The idea is the biggest element can always be the last one remaining and you have to count
answer for each part of the side if the sum is greater than or equal to the biggest element.

This can easily give a $$\mathcal{O}(n\log n)$$ solution for the easy version.

Moving on to the hard version, the only difference is that we need to calculate 
the answer for every prefix of the balls.

Now you might be wondering two things:

1. How the previous solution could be generalized to solve the hard version.
Splitting by the biggest element doesn't seem easily generalizable, since the choices for splitting depend heavily on the next elements we append to the end.

2. How by generalizing a $$\mathcal{O}(n\log n)$$ solution for an easier version
we get an $$\mathcal{O}(n)$$ solution to a harder version, shouldn't we have a worse or equal complexity?

If you want to think about this yourself stop reading, since I will now describe the solution.

If you have studied data structures you might think splitting by the largest element looks very similar to cartesian trees (aka treaps). We can understand the previous algorithm as a dp on subtrees on the cartesian tree of all elements where weight is priority. Thinking about linear complexity and cartesian
trees only reminds me of one algorithm. The linear construction of a cartesian tree.

I don't want to describe treaps and the construction in detail, for that you can read [cp-algo's great explanation](https://cp-algorithms.com/data_structures/treap.html).

The main idea is that you have a "monotonic" stack (I will imagine it as an array where we have the bottommost elements to the left and the topmost to the right)
where you store cartesian trees without the right children
It is monotonic since the priority (in the original problem weight) of the root is decreasing.

The main property of this data structure is that if we set the right subtree to next tree in the array we obtain a valid cartesian tree.

![Diagram of the data structure described](/cp-blog/assets/images/treap-construction.svg)

It is always possible to insert one element to the right by some set of pops from the stack and then one push and it works in total amortized time of $$\mathcal{O}(n)$$.

The cool thing about this is that we need few variables per tree in the stack to simulate the process,
and then you can realize we don't even need to store the trees, we only need those values:

- The weight of the root
- The answer (number of labels that can be the only one remaining) for the left subtree
- The sum of all the weights on the left subtree.

There's only one remaining important thing to consider, the dp on subtrees also
depended on the values on the right subtrees, so it isn't as easy as we might think.

The good news is that if we do the dp top-to-bottom and on one node we have the weight of the root
is greater than the sum of the right subtree we don't need to do more recursion.

Translated to our monotonic stack, we can maintain a pointer to the element where that happens.

That means if we only store two more variables:

- The answer for the whole stack
- The sum of the weights of all the subtrees to the right of our pointer.

We can now describe the full solution to the problem in pseudocode.

I will assume that the weights and sums of weights are different for simplicity, but equal weights are easy to implement.

{% highlight python3 %}
answer := 0
sumright := 0
for i from 1 to n:
    read new weight

    while the weight of the node at the pointer < the new weight:
        move the pointer to the left and update answer and sumright accordingly
    
    create an empty node

    while the weight of the rightmost node < the new weight:
        pop from the stack and recalculate the values of the node using that
        it corresponds to the right subtree of the node we are popping
    
    calculate the values for a subtree where the node we are inserting is the
    root and the previously calculated values correspond to the left subtree

    push that node to the right of the stack

    while the sum to the right of the pointer > the weight at the pointer:
        move the pointer to the right and update answer and sumright accordingly
    
    print answer
{% endhighlight %}

We can see the algorithm is amortized $$O(n)$$ since we only insert a linear number of elements to the stack,
and the pointer only goes down a linear amount of times. Also, it is nice to see it is an online algorithm.

I have implemented the algorithm in [this submission](https://codeforces.com/contest/1998/submission/275736709).

I don't know of any other problem that this idea can solve, but I recall the first application of
linear construction of cartesian trees in a competitive programming problem I have seen is
the 100-point solution to [some IOI problem](https://oj.uz/problem/view/IOI18_meetings) (don't click if you don't want to get spoiled).

I hope you liked this post, I'll try to post more soon.
