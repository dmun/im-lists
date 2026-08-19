On coalesce, the left and right nodes' locations and pointers are swapped, but not size/capacity, meaning that a node could have 6 elements but have size/capacity 4.

If you append this resulting node to an empty list, they don't coalesce and an empty node would remain the head, while still linking to the other node, causing what you see below:

## Before

```scheme
(define (foo x)
  (append '() (reverse (range 0 x))))

;; 5 to 8 fail
(foo 4) ; => '(3 2 1 0)
(foo 5) ; => '()
(foo 6) ; => '()
(foo 7) ; => '()
(foo 8) ; => '()
(foo 9) ; => '(8 7 6 5 4 3 2 1 0)

;; contradicting length and empty?
(length (foo 6)) ; => 6
(empty? (foo 6)) ; => #true
```

```rust
let left: SmallRcList<i32> = SmallRcList::new();
let right: SmallRcList<_> = (0..6).collect::<SmallRcList<_>>().reverse();
let left = left.append(right);

left.node_iter().for_each(|node| {
    println!("{}/{}: {:?}", node.index(), node.size(), node.elements());
});

// Output:
// 0/4: []
// 6/4: [0, 1, 2, 3, 4, 5]
````

## After

```scheme
(foo 4) ; => '(3 2 1 0)
(foo 5) ; => '(4 3 2 1 0)
(foo 6) ; => '(5 4 3 2 1 0)
(foo 7) ; => '(6 5 4 3 2 1 0)
(foo 8) ; => '(7 6 5 4 3 2 1 0)
(foo 9) ; => '(8 7 6 5 4 3 2 1 0)

(length (foo 6)) ; => 6
(empty? (foo 6)) ; => #false
```

```rust
let left: SmallRcList<i32> = SmallRcList::new();
let right: SmallRcList<_> = (0..6).collect::<SmallRcList<_>>().reverse();
let left = left.append(right);

left.node_iter().for_each(|node| {
    println!("{}/{}: {:?}", node.index(), node.size(), node.elements());
});

// Output:
// 6/8: [0, 1, 2, 3, 4, 5]
````
