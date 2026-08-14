# Delete All Occurrences of X in a Doubly Linked List

## 💡 Intuition

In a doubly linked list, deleting a node means fixing up to two links: the node before it (`prev.next`) and the node after it (`next.prev`). There are three distinct situations depending on *where* the node being deleted sits:

- It's the **head** (no `prev`) → the list's `head` pointer itself must move.
- It's the **tail** (no `next`) → only the previous node's `next` needs clearing.
- It's a **middle** node → both neighbors need to be stitched to each other, bypassing the deleted node.

Since we might delete several nodes in a row (or delete the current node before deciding where to go next), we save `temp.next` *before* touching any pointers — otherwise we'd lose our place in the traversal.

---

## 🔰 Initial State

```
temp = head
x = 2
```

![Initial doubly linked list with three occurrences of x=2: head, middle, and tail](/images/delete-all-x-dll-01-initial.svg)

---

## 🔍 Step-by-Step Visualization

### Step 1: Save the next pointer before any deletion

```java
Node next = temp.next;
```

**What happens?** Before checking or modifying anything, `next` is captured immediately.
**Why?** Once a node is deleted, its own `.next`/`.prev` may get cleared (as seen in the tail case below) — so we must grab where to go *next in the traversal* first, independent of what happens to `temp`.

---

### Step 2: Case — deleting the head

```java
if (temp.prev == null) {
    head = temp.next;
    if (head != null) head.prev = null;
}
```

**What happens?** Since there's no previous node to patch, the list's `head` reference itself is moved forward to `temp.next`. If that new head exists, its `prev` is cleared to `null` (it's now the first node).
**Why?** The head is the only node whose "previous" is conceptually the `head` variable itself — so this is the one case where we update `head` rather than a node's pointer.

![Case 1: deleting the head node](/images/delete-all-x-dll-02-case-head.svg)

---

### Step 3: Case — deleting a middle node

```java
else {
    temp.prev.next = temp.next;
    temp.next.prev = temp.prev;
}
```

**What happens?** The node before `temp` is linked directly to the node after `temp`, and vice versa — `temp` is bypassed entirely from both directions.
**Why?** This is the general "unlink a node from both sides" operation that doubly linked lists are built around.

![Case 2: deleting a middle node, bypassing it from both directions](/images/delete-all-x-dll-03-case-middle.svg)

---

### Step 4: Case — deleting the tail

```java
else if (temp.next == null) {
    temp.prev.next = null;
    temp.prev = null;
}
```

**What happens?** Since there's no next node to patch, `temp.prev.next` is set to `null`, making the previous node the new tail. Then `temp.prev` itself is cleared, fully isolating the deleted node.
**Why?** Clearing `temp.prev` (and not just the previous node's `.next`) ensures the deleted node has no leftover references, which matters for garbage collection and avoids any dangling access.

![Case 3: deleting the tail node](/images/delete-all-x-dll-04-case-tail.svg)

---

### Step 5: Move to the saved next node

```java
temp = next;
```

**What happens?** Regardless of which case ran (or if nothing was deleted), `temp` advances to the node saved in Step 1.
**Why?** This is what lets traversal continue safely even when the current node was just deleted and its own pointers may have been cleared.

---

## 🎯 Final Result

**Input:** `2 <-> 1 <-> 3 <-> 2 <-> 4 <-> 2`, `x = 2`
**Output:** `1 <-> 3 <-> 4`

All three occurrences of `2` were removed — one at the head, one in the middle, one at the tail — each handled by its matching case, and traversal never lost track thanks to saving `next` upfront.

![Final result: all occurrences of x removed](/images/delete-all-x-dll-05-final-result.svg)

---

## ⏱️ Complexity Analysis

- **Time Complexity:** O(N) — a single pass through the list, constant work per node.
- **Space Complexity:** O(1) — no extra data structures; pointers are adjusted in place.

---

## 🧠 Key Takeaway

**"Save next, then handle head / middle / tail separately."**
Grab `temp.next` before deleting anything, then branch on whether `temp` is the head, tail, or a middle node to fix the right one or two links.

**Pattern:** Doubly Linked List Manipulation

Your code is correct as-is — no bugs found. All three cases (head/middle/tail) are handled correctly, and saving `next` before mutation is exactly right for safe traversal-while-deleting.

---

## 💻 Code Implementation

```java
class Solution {
    static Node deleteAllOccurOfX(Node head, int x) {

        Node temp = head;
        while (temp != null) {
            Node next = temp.next;
            if (temp.data == x) {
                if (temp.prev == null) {
                    head = temp.next;
                    if (head != null) {
                        head.prev = null;
                    }
                } else if (temp.next == null) {
                    temp.prev.next = null;
                    temp.prev = null;
                } else {
                    temp.prev.next = temp.next;
                    temp.next.prev = temp.prev;
                }
            }

            temp = next;
        }
        return head;
    }
}
```
