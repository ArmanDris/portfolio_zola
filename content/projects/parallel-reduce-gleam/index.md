+++
authors = ["Arman Drismir"]
title = "Parallel computing with Gleam!"
description = "Using gleam's Erlang features to parallelize a reduce function"
date = 2025-03-05
[taxonomies]
tags = ["CSS"]
[extra]
disclaimer = "This is still a work in progress so the data is a little messy in some places"
+++

## Reduce Overview

The goal is to write a `parallel_reduce` function that will use a `combine_fn` to aggregate a list.

Parallel reduce should be able to spawn an appropriate number of threads to aggregate the entire list in **O(log n)** time! Parallel reduce should also be able to run much faster than `sequential_reduce` for n around 1_000_000. So in other words, it's runtime must also have a small constant factor.

Here is an example of using `parallel_reduce` to sum a list:
```gleam
parallel_reduce([40, -200, 600, 5], fn(a, b) { a + b })
// Returns -> 445
```
Another example with multiplication
```gleam
parallel_reduce([40, -200, 600, 5], fn(a, b) { a * b })
// Returns -> -24000000
```


## First attempt

Running the parallel algorithm on a list with **100_000_000** numbers we should be able to blow the sequential implementation out of the water. So lets try it!

```sh
gleam run
   Compiled in 0.35s
    Running star_scan.main
Sequential time taken 626 ms
Par time taken 9876 ms
```

Oh dear, that is dreadful, our multithreaded solution is 15 times slower than the naive single threaded solution 😭.

#### Why is it so bad?

Our algorithm spawns 10_000 threads that each calculate the total of 10_000 numbers. To make this easy I have a function that evenly divides the 100_000_000 into equally sized pieces for each of our threads. Lets find out how long splitting up our input takes.

```sh
gleam run
   Compiled in 0.05s
    Running star_scan.main
Split lists in 8635 ms
```

Splitting our list up for our processes takes way longer than the actual operation would have.

### Why is splitting a list so expensive?

Our split list function is essentially a recursive call to the built in split function:
```gleam
let #(new_part, rest) = list.split(list, partition_size)
```
Gleam represents lists as linked lists under the hood so each call to list_split is O(n). We call `split_list` $sqrt(n)$ times so we will spend $O(n sqrt(n) )$ on splitting the lists alone!

### How can we make it fast?

To make it truly fast we need to stop thinking so sequentially and allow the processes to divide up the work themselves. This will also fit nicely into our desire for **O(log n)** performance.

More specifically we should create a tree, where each process divides itself in two until it has a list of length m. When it has a list of length m then it will perform sequential reduce on its segment m of the list and combine with its twin before sending its result to its parent. Then we can divide the list in **O(log n)** time and reduce in **O(log n)** time, achieving the nice runtime we want!

### Second attempt

With our new strategy lets try with a list of 100_000_000 numbers and a process segment size of 1_000_000.

```sh
gleam run 
   Compiled in 0.28s
    Running star_scan.main
Reduce time taken 633 ms
Par time taken 4982 ms
```

Trying with a list of 100_000_000 numbers and a process segment size of 10_000.

```sh
gleam run
   Compiled in 0.29s
    Running star_scan.main
Reduce time taken 763 ms
Par time taken 5229 ms
```

Trying with a list of 100_000_000 numbers and a process segment size of 100.

```sh
gleam run
   Compiled in 0.29s
    Running star_scan.main
Reduce time taken 691 ms
Par time taken 4412 ms
```

After running a few times and averaging it out, segment size of 1_000_000, 10_000, and 100 do not have much of an impact. I suspect that reduce with addition is so simple that the overhead of parallelization will always dominate the runtime in testing.

With 100_000_000 numbers, a combine fn of `let combine_fn = fn(a, b) { a * b / a / a / a }`, and a segment size of 100_000 it gets a lot closer.

```sh
gleam run
  Compiling star_scan
   Compiled in 0.21s
    Running star_scan.main
Reduce time taken 1666 ms
Par time taken 4825 ms
```

**Integrate this somehow**

If we provide a more expensive combine fn then we can actually get some nice results with par reduce :D

```sh
gleam run
Reduce time taken 9272 ms
Hybrid time taken 3626 ms
```

```sh
gleam run
Reduce time taken 9520 ms
Hybrid time taken 3856 ms
```

```sh
gleam run
Reduce time taken 9520 ms
Hybrid time taken 3856 ms
```

```sh
gleam run
Reduce time taken 9955 ms
Hybrid time taken 4617 ms
```

```sh
gleam run
Reduce time taken 9691 ms
Hybrid time taken 4439 ms
```

Wonderful! We are saving time :D

## Scan overview

Scan provides a running total of a list. Using some clever message passing we can parallelize this. 

Example using addition:
```gleam
parallel_scan([40, -200, 600, 5], fn(a, b) { a + b })
// Returns -> [40, -160, 440, 445]
```

Another example with multiplication
```gleam
parallel_scan([40, -200, 600, 5], fn(a, b) { a * b })
// Returns -> [40, -8000, -480000, -2400000]
```

## Random notes:

Gleam's built in fold is implemented sequentially and not optimized. It is the same as my simple custom implementation 

```sh
src % gleam run
Built in foldl time taken 10818 ms
Reduce time taken 10959 ms
Hybrid time taken 5114 ms
```