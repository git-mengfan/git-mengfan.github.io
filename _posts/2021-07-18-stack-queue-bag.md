---
layout: post
title: 数据结构-栈、队列
categories: 数据结构
description: 数据结构栈和队列
keywords: stack, queue
---

 ## 概述
 栈和队列是基本的数据结构，包也属于基本结构，但使用率相对不高。本文尝试对其进行知识点总结，代码采用普林斯顿大学算法课源码，使用时需适当修改。

 ## 队列
 1. Java实现，基于链表

```
package edu.princeton.cs.algs4;

import java.util.Iterator;
import java.util.NoSuchElementException;

/**
 *  The {@code Queue} class represents a first-in-first-out (FIFO)
 *  queue of generic items.
 *  It supports the usual <em>enqueue</em> and <em>dequeue</em>
 *  operations, along with methods for peeking at the first item,
 *  testing if the queue is empty, and iterating through
 *  the items in FIFO order.
 *  <p>
 *  This implementation uses a singly linked list with a static nested class for
 *  linked-list nodes. See {@link LinkedQueue} for the version from the
 *  textbook that uses a non-static nested class.
 *  See {@link ResizingArrayQueue} for a version that uses a resizing array.
 *  The <em>enqueue</em>, <em>dequeue</em>, <em>peek</em>, <em>size</em>, and <em>is-empty</em>
 *  operations all take constant time in the worst case.
 *  <p>
 *  For additional documentation, see <a href="https://algs4.cs.princeton.edu/13stacks">Section 1.3</a> of
 *  <i>Algorithms, 4th Edition</i> by Robert Sedgewick and Kevin Wayne.
 *
 *  @author Robert Sedgewick
 *  @author Kevin Wayne
 *
 *  @param <Item> the generic type of an item in this queue
 */
public class Queue<Item> implements Iterable<Item> {
    private Node<Item> first;    // beginning of queue
    private Node<Item> last;     // end of queue
    private int n;               // number of elements on queue

    // helper linked list class
    private static class Node<Item> {
        private Item item;
        private Node<Item> next;
    }

    /**
     * Initializes an empty queue.
     */
    public Queue() {
        first = null;
        last  = null;
        n = 0;
    }

    /**
     * Returns true if this queue is empty.
     *
     * @return {@code true} if this queue is empty; {@code false} otherwise
     */
    public boolean isEmpty() {
        return first == null;
    }

    /**
     * Returns the number of items in this queue.
     *
     * @return the number of items in this queue
     */
    public int size() {
        return n;
    }

    /**
     * Returns the item least recently added to this queue.
     *
     * @return the item least recently added to this queue
     * @throws NoSuchElementException if this queue is empty
     */
    public Item peek() {
        if (isEmpty()) throw new NoSuchElementException("Queue underflow");
        return first.item;
    }

    /**
     * Adds the item to this queue.
     *
     * @param  item the item to add
     */
    public void enqueue(Item item) {
        Node<Item> oldlast = last;
        last = new Node<Item>();
        last.item = item;
        last.next = null;
        if (isEmpty()) first = last;
        else           oldlast.next = last;
        n++;
    }

    /**
     * Removes and returns the item on this queue that was least recently added.
     *
     * @return the item on this queue that was least recently added
     * @throws NoSuchElementException if this queue is empty
     */
    public Item dequeue() {
        if (isEmpty()) throw new NoSuchElementException("Queue underflow");
        Item item = first.item;
        first = first.next;
        n--;
        if (isEmpty()) last = null;   // to avoid loitering
        return item;
    }

    /**
     * Returns a string representation of this queue.
     *
     * @return the sequence of items in FIFO order, separated by spaces
     */
    public String toString() {
        StringBuilder s = new StringBuilder();
        for (Item item : this) {
            s.append(item);
            s.append(' ');
        }
        return s.toString();
    }

    /**
     * Returns an iterator that iterates over the items in this queue in FIFO order.
     *
     * @return an iterator that iterates over the items in this queue in FIFO order
     */
    public Iterator<Item> iterator()  {
        return new LinkedIterator(first);  
    }

    // an iterator, doesn't implement remove() since it's optional
    private class LinkedIterator implements Iterator<Item> {
        private Node<Item> current;

        public LinkedIterator(Node<Item> first) {
            current = first;
        }

        public boolean hasNext()  { return current != null;                     }
        public void remove()      { throw new UnsupportedOperationException();  }

        public Item next() {
            if (!hasNext()) throw new NoSuchElementException();
            Item item = current.item;
            current = current.next;
            return item;
        }
    }


    /**
     * Unit tests the {@code Queue} data type.
     *
     * @param args the command-line arguments
     */
    public static void main(String[] args) {
        Queue<String> queue = new Queue<String>();
        while (!StdIn.isEmpty()) {
            String item = StdIn.readString();
            if (!item.equals("-"))
                queue.enqueue(item);
            else if (!queue.isEmpty())
                StdOut.print(queue.dequeue() + " ");
        }
        StdOut.println("(" + queue.size() + " left on queue)");
    }
}
```
- 队列还可使用基于数组的实现，但因为数组容量固定，需在适当的时候修改数组大小

2. Python实现

```
from typing import Generic, Iterator, Optional, TypeVar


class NoSuchElementException(Exception):
    pass


T = TypeVar("T")


class Node(Generic[T]):
    def __init__(self, item: T, next: Optional["Node[T]"]) -> None:
        self.item: T = item
        self.next: Optional[Node[T]] = next


class Queue(Generic[T]):

    def __init__(self) -> None:
        # Initializes an empty queue.
        self._first: Optional[Node[T]] = None
        self._last: Optional[Node[T]] = None
        self._n: int = 0

    def enqueue(self, item: T) -> None:
        # Adds the item to this queue.
        old_last: Optional[Node[T]] = self._last
        self._last = Node(item, None)
        if self.is_empty():
            self._first = self._last
        else:
            assert old_last is not None
            old_last.next = self._last
        self._n += 1

    def dequeue(self) -> T:
        # Removes and returns the item on this queue that was least recently added.
        if self.is_empty():
            raise NoSuchElementException("Queue underflow")

        assert self._first is not None
        item = self._first.item
        self._first = self._first.next
        self._n -= 1
        if self.is_empty():
            self._last = None
        return item

    def is_empty(self) -> bool:
        # Returns true if this queue is empty.
        return self._first is None

    def size(self) -> int:
        # Returns the number of items in this queue.
        return self._n

    def __len__(self) -> int:
        return self.size()

    def peek(self) -> T:
        # Returns the item least recently added to this queue.
        if self.is_empty():
            raise NoSuchElementException("Queue underflow")

        assert self._first is not None
        return self._first.item

    def __iter__(self) -> Iterator[T]:
        # Iterates over all the items in this queue in FIFO order.
        curr = self._first
        while curr is not None:
            yield curr.item
            curr = curr.next

    def __repr__(self) -> str:
        # Returns a string representation of this queue.
        s = []
        for item in self:
            s.append("{} ".format(item))
        return "".join(s)

```

 ## 包
1. Java实现，基于链表

```
package edu.princeton.cs.algs4;

import java.util.Iterator;
import java.util.NoSuchElementException;

/**
 *  The {@code Bag} class represents a bag (or multiset) of
 *  generic items. It supports insertion and iterating over the
 *  items in arbitrary order.
 *  <p>
 *  This implementation uses a singly linked list with a static nested class Node.
 *  See {@link LinkedBag} for the version from the
 *  textbook that uses a non-static nested class.
 *  See {@link ResizingArrayBag} for a version that uses a resizing array.
 *  The <em>add</em>, <em>isEmpty</em>, and <em>size</em> operations
 *  take constant time. Iteration takes time proportional to the number of items.
 *  <p>
 *  For additional documentation, see <a href="https://algs4.cs.princeton.edu/13stacks">Section 1.3</a> of
 *  <i>Algorithms, 4th Edition</i> by Robert Sedgewick and Kevin Wayne.
 *
 *  @author Robert Sedgewick
 *  @author Kevin Wayne
 *
 *  @param <Item> the generic type of an item in this bag
 */
public class Bag<Item> implements Iterable<Item> {
    private Node<Item> first;    // beginning of bag
    private int n;               // number of elements in bag

    // helper linked list class
    private static class Node<Item> {
        private Item item;
        private Node<Item> next;
    }

    /**
     * Initializes an empty bag.
     */
    public Bag() {
        first = null;
        n = 0;
    }

    /**
     * Returns true if this bag is empty.
     *
     * @return {@code true} if this bag is empty;
     *         {@code false} otherwise
     */
    public boolean isEmpty() {
        return first == null;
    }

    /**
     * Returns the number of items in this bag.
     *
     * @return the number of items in this bag
     */
    public int size() {
        return n;
    }

    /**
     * Adds the item to this bag.
     *
     * @param  item the item to add to this bag
     */
    public void add(Item item) {
        Node<Item> oldfirst = first;
        first = new Node<Item>();
        first.item = item;
        first.next = oldfirst;
        n++;
    }


    /**
     * Returns an iterator that iterates over the items in this bag in arbitrary order.
     *
     * @return an iterator that iterates over the items in this bag in arbitrary order
     */
    public Iterator<Item> iterator()  {
        return new LinkedIterator(first);  
    }

    // an iterator, doesn't implement remove() since it's optional
    private class LinkedIterator implements Iterator<Item> {
        private Node<Item> current;

        public LinkedIterator(Node<Item> first) {
            current = first;
        }

        public boolean hasNext()  { return current != null;                     }
        public void remove()      { throw new UnsupportedOperationException();  }

        public Item next() {
            if (!hasNext()) throw new NoSuchElementException();
            Item item = current.item;
            current = current.next;
            return item;
        }
    }

    /**
     * Unit tests the {@code Bag} data type.
     *
     * @param args the command-line arguments
     */
    public static void main(String[] args) {
        Bag<String> bag = new Bag<String>();
        while (!StdIn.isEmpty()) {
            String item = StdIn.readString();
            bag.add(item);
        }

        StdOut.println("size of bag = " + bag.size());
        for (String s : bag) {
            StdOut.println(s);
        }
    }

}
```
2. Python实现

```
from typing import Generic, Iterator, Optional, TypeVar
T = TypeVar("T")
S = TypeVar("S")
class Bag(Generic[T]):

    class Node(Generic[S]):
        # helper linked list class
        def __init__(self):
            self.next: Optional[Bag.Node[T]] = None
            self.item: Optional[S] = None

    def __init__(self) -> None:
        # Initializes an empty bag
        self._first: Optional[Bag.Node[T]] = None  # beginning of bag
        self._n = 0  # number of elements in bag

    def is_empty(self) -> bool:
        # Returns true if this bag is empty.
        return self._first is None

    def size(self) -> int:
        # Returns the number of items in this bag.
        return self._n

    def __len__(self) -> int:
        return self.size()

    def add(self, item: T) -> None:
        # Adds the item to this bag.
        oldfirst = self._first
        self._first = Bag.Node()
        self._first.item = item
        self._first.next = oldfirst
        self._n += 1

    def __iter__(self) -> Iterator[T]:
        # Returns an iterator that iterates over the items in this bag in
        current = self._first
        while current is not None:
            assert current.item is not None
            yield current.item
            current = current.next

    def __repr__(self) -> str:
        out = "{"
        for elem in self:
            out += "{}, ".format(elem)
        return out + "}"
```



 ## 栈
1. Java实现，基于链表

```
package edu.princeton.cs.algs4;

import java.util.Iterator;
import java.util.NoSuchElementException;


/**
 *  The {@code Stack} class represents a last-in-first-out (LIFO) stack of generic items.
 *  It supports the usual <em>push</em> and <em>pop</em> operations, along with methods
 *  for peeking at the top item, testing if the stack is empty, and iterating through
 *  the items in LIFO order.
 *  <p>
 *  This implementation uses a singly linked list with a static nested class for
 *  linked-list nodes. See {@link LinkedStack} for the version from the
 *  textbook that uses a non-static nested class.
 *  See {@link ResizingArrayStack} for a version that uses a resizing array.
 *  The <em>push</em>, <em>pop</em>, <em>peek</em>, <em>size</em>, and <em>is-empty</em>
 *  operations all take constant time in the worst case.
 *  <p>
 *  For additional documentation,
 *  see <a href="https://algs4.cs.princeton.edu/13stacks">Section 1.3</a> of
 *  <i>Algorithms, 4th Edition</i> by Robert Sedgewick and Kevin Wayne.
 *
 *  @author Robert Sedgewick
 *  @author Kevin Wayne
 *
 *  @param <Item> the generic type of an item in this stack
 */
public class Stack<Item> implements Iterable<Item> {
    private Node<Item> first;     // top of stack
    private int n;                // size of the stack

    // helper linked list class
    private static class Node<Item> {
        private Item item;
        private Node<Item> next;
    }

    /**
     * Initializes an empty stack.
     */
    public Stack() {
        first = null;
        n = 0;
    }

    /**
     * Returns true if this stack is empty.
     *
     * @return true if this stack is empty; false otherwise
     */
    public boolean isEmpty() {
        return first == null;
    }

    /**
     * Returns the number of items in this stack.
     *
     * @return the number of items in this stack
     */
    public int size() {
        return n;
    }

    /**
     * Adds the item to this stack.
     *
     * @param  item the item to add
     */
    public void push(Item item) {
        Node<Item> oldfirst = first;
        first = new Node<Item>();
        first.item = item;
        first.next = oldfirst;
        n++;
    }

    /**
     * Removes and returns the item most recently added to this stack.
     *
     * @return the item most recently added
     * @throws NoSuchElementException if this stack is empty
     */
    public Item pop() {
        if (isEmpty()) throw new NoSuchElementException("Stack underflow");
        Item item = first.item;        // save item to return
        first = first.next;            // delete first node
        n--;
        return item;                   // return the saved item
    }


    /**
     * Returns (but does not remove) the item most recently added to this stack.
     *
     * @return the item most recently added to this stack
     * @throws NoSuchElementException if this stack is empty
     */
    public Item peek() {
        if (isEmpty()) throw new NoSuchElementException("Stack underflow");
        return first.item;
    }

    /**
     * Returns a string representation of this stack.
     *
     * @return the sequence of items in this stack in LIFO order, separated by spaces
     */
    public String toString() {
        StringBuilder s = new StringBuilder();
        for (Item item : this) {
            s.append(item);
            s.append(' ');
        }
        return s.toString();
    }


    /**
     * Returns an iterator to this stack that iterates through the items in LIFO order.
     *
     * @return an iterator to this stack that iterates through the items in LIFO order
     */
    public Iterator<Item> iterator() {
        return new LinkedIterator(first);
    }

    // an iterator, doesn't implement remove() since it's optional
    private class LinkedIterator implements Iterator<Item> {
        private Node<Item> current;

        public LinkedIterator(Node<Item> first) {
            current = first;
        }

        public boolean hasNext() {
            return current != null;
        }

        public void remove() {
            throw new UnsupportedOperationException();
        }

        public Item next() {
            if (!hasNext()) throw new NoSuchElementException();
            Item item = current.item;
            current = current.next;
            return item;
        }
    }


    /**
     * Unit tests the {@code Stack} data type.
     *
     * @param args the command-line arguments
     */
    public static void main(String[] args) {
        Stack<String> stack = new Stack<String>();
        while (!StdIn.isEmpty()) {
            String item = StdIn.readString();
            if (!item.equals("-"))
                stack.push(item);
            else if (!stack.isEmpty())
                StdOut.print(stack.pop() + " ");
        }
        StdOut.println("(" + stack.size() + " left on stack)");
    }
}
```
2. Python实现

```
from typing import Generic, Iterator, List, Optional, TypeVar

T = TypeVar("T")


class Node(Generic[T]):
    def __init__(self):
        self.item: T = None
        self.next: Optional[Node] = None


class Stack(Generic[T]):

    def __init__(self) -> None:
        # Initializes an empty stack.
        self._first: Optional[Node[T]] = None
        self._n: int = 0

    def is_empty(self) -> bool:
        # Returns true if this stack is empty.
        return self._n == 0

    def size(self) -> int:
        # Returns the number of items in this stack.
        return self._n

    def __len__(self) -> int:
        return self.size()

    def push(self, item: T) -> None:
        # Adds the item to this stack.
        oldfirst = self._first
        self._first = Node()
        self._first.item = item
        self._first.next = oldfirst
        self._n += 1

    def pop(self) -> T:
        # Removes and returns the item most recently added to this stack.
        if self.is_empty():
            raise ValueError("Stack underflow")
        assert self._first is not None
        item = self._first.item
        assert item is not None
        self._first = self._first.next
        self._n -= 1
        return item

    def peek(self) -> T:
        # Returns (but does not remove) the item most recently added to this stack.
        if self.is_empty():
            raise ValueError("Stack underflow")
        assert self._first is not None
        item = self._first.item
        assert item is not None
        return item

    def __repr__(self) -> str:
        # Returns a string representation of this stack.
        s = []
        for item in self:
            s.append(item.__repr__())
        return " ".join(s)

    def __iter__(self) -> Iterator[T]:
        # Returns an iterator to this stack that iterates through the items in
        current = self._first
        while current is not None:
            item = current.item
            assert item is not None
            yield item
            current = current.next


class FixedCapacityStack(Generic[T]):
    def __init__(self, capacity: int):
        self.a: List[Optional[T]] = [None] * capacity
        self.n: int = 0

    def is_empty(self) -> bool:
        return self.n == 0

    def size(self) -> int:
        return self.n

    def __len__(self) -> int:
        return self.size()

    def push(self, item: T):
        self.a[self.n] = item
        self.n += 1

    def pop(self) -> T:
        self.n -= 1
        item = self.a[self.n]
        assert item is not None
        return item


class ResizingArrayStack(Generic[T]):
    def __init__(self) -> None:
        self.a: List[Optional[T]] = [None]
        self.n: int = 0

    def is_empty(self) -> bool:
        return self.n == 0

    def size(self) -> int:
        return self.n

    def __len__(self) -> int:
        return self.size()

    def resize(self, capacity: int) -> None:
        temp: List[Optional[T]] = [None] * capacity
        for i in range(self.n):
            temp[i] = self.a[i]
        self.a = temp

    def push(self, item: T) -> None:
        if self.n == len(self.a):
            self.resize(2 * len(self.a))
        self.a[self.n] = item
        self.n += 1

    def pop(self) -> T:
        self.n -= 1
        item = self.a[self.n]
        self.a[self.n] = None
        if 0 < self.n <= len(self.a) // 4:
            self.resize(len(self.a) // 2)
        assert item is not None
        return item

    def __iter__(self) -> Iterator[T]:
        i = self.n - 1
        while i >= 0:
            item = self.a[i]
            assert item is not None
            yield item
            i -= 1
```
