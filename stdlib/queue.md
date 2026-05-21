---
title: Standard library — Queue
---

# `Nar.Base.Queue`

[← Back to index](../index.html)

`Queue[a]` is an immutable FIFO queue with amortised O(1) `enqueue` and `dequeue`. It is built on
top of two lists (a "front" and a reversed "rear"), so all operations are pure.

```nar
type Queue[a]      // exposed constructor `QueueImpl(front, rear)` is internal in practice
```

## Construction

```nar
def empty: Queue[a]
def singleton(a: a): Queue[a]
def fromList(xs: List[a]): Queue[a]
```

## Inspection

```nar
def isEmpty(q: Queue[a]): Bool
def size(q: Queue[a]): Int
def front(q: Queue[a]): Maybe[a]
def toList(q: Queue[a]): List[a]
```

## Modification

```nar
def enqueue(a: a, q: Queue[a]): Queue[a]
def dequeue(q: Queue[a]): ( Maybe[a], Queue[a] )
def updateFront(f: (Maybe[a]): Maybe[a], q: Queue[a]): Queue[a]
```

`dequeue` returns both the popped element (or `Nothing` if the queue was empty) and the rest of
the queue.

## Higher‑order

```nar
def map(f: (a): b, q: Queue[a]): Queue[b]
def filter(pred: (a): Bool, q: Queue[a]): Queue[a]
```

## Example

```nar
import Nar.Base.Queue as Q

def consume(q: Q.Queue[Int]): List[Int] =
  let go(q, acc) =
    let ( m, q2 ) = Q.dequeue(q)
    in
      select m
        case Just(x) -> go(q2, x | acc)
        case Nothing -> List.reverse(acc)
      end
  in
    go(q, [])
```
