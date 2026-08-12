# Swap Nodes in Pairs

## 💡 Intuition

Instead of swapping node *values* (which loses the "true" spirit of pointer manipulation problems), swap the nodes themselves by rewiring three links per pair: the previous node's `next`, the first node's `next`, and the second node's `next`.

A **dummy node** placed before `head` makes the very first pair swap uniform with every later pair — there's no special case needed for "what if we're swapping the first two nodes." `prev` always has a real node before the pair it's about to swap.

---

## 🔰 Initial State

```
dummy.next = head
prev = dummy
```

![Initial state with dummy node before head](/images/swap-pairs-01-initial.svg)

---

## 🔍 Step-by-Step Visualization

### Step 1: Identify the pair to swap

```java
ListNode first = prev.next;
ListNode second = prev.next.next;
```

**What happens?** `first` is the next node after `prev`; `second` is the one after that — together they're the pair about to be swapped.
**Why?** These two references are needed before any pointers get overwritten, otherwise we'd lose track of the nodes mid-swap.

![Identifying first and second nodes of the pair](/images/swap-pairs-02-identify-pair1.svg)

---

### Step 2: Swap the pair and advance prev

```java
first.next = second.next;
second.next = first;
prev.next = second;

prev = first;
```

**What happens?**
1. `first.next = second.next` — saves the rest-of-the-list link on `first` *before* it gets overwritten.
2. `second.next = first` — reverses the order: `second` now points to `first`.
3. `prev.next = second` — connects the previous part of the list to the new front of this pair (`second`).
4. `prev = first` — since `first` is now the *tail* of this swapped pair, it becomes the new `prev` for the next pair.

**Why this order matters:** Step 1 must happen before step 2, or `first.next` would be overwritten to point to itself/lost before we could save where it should go next.

![Swapping pair 1 with the three relinks](/images/swap-pairs-03-swap-pair1.svg)

---

### Step 3: Repeat for the next pair

The `while` loop condition (`prev.next != null && prev.next.next != null`) checks that a full pair still exists. If so, the same three-line swap runs again.

![Identifying and swapping pair 2](/images/swap-pairs-04-swap-pair2.svg)

---

## 🎯 Final Result

**Input:** `1 -> 2 -> 3 -> 4`
**Output:** `2 -> 1 -> 4 -> 3`

Each pair was swapped in place using the dummy-node technique, and the loop naturally stops once fewer than 2 nodes remain (or the list is fully processed).

![Final swapped list](/images/swap-pairs-05-final-result.svg)

---

## ⏱️ Complexity Analysis

- **Time Complexity:** O(N) — each node is visited a constant number of times as part of exactly one pair swap.
- **Space Complexity:** O(1) — only a fixed number of pointers (`dummy`, `prev`, `first`, `second`) are used; the swap is done in-place.

---

## 🧠 Key Takeaway

**"Save, flip, connect, advance."**
Save `first.next` before overwriting it → flip `second.next = first` → connect `prev.next = second` → advance `prev = first` for the next pair.

**Pattern:** Linked List + Pointer Manipulation (dummy node technique)

Your code is correct as-is — no bugs found. Order of operations inside the swap is exactly right, and the dummy node cleanly avoids a special case for the first pair. Handles odd-length lists correctly too (the loop condition naturally leaves a trailing single node untouched).

---

## 💻 Code Implementation

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode dummy = new ListNode(-1);
        ListNode prev = dummy;
        dummy.next = head;
        while (prev.next != null && prev.next.next != null) {
            ListNode first = prev.next;
            ListNode second = prev.next.next;

            // swap
            first.next = second.next;
            second.next = first;
            prev.next = second;

            // move
            prev = first;

        }
        return dummy.next;
    }
}
```
