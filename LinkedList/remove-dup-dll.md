# Remove Duplicates from a Sorted Doubly Linked List

## 💡 Intuition

Because the list is **sorted**, any duplicate values must sit right next to each other — there's no need to search the whole list for repeats. So we only ever need to compare a node with its immediate neighbor, `temp` vs `temp.next`.

When they match, `temp.next` is a duplicate — skip over it by relinking `temp.next` to `temp.next.next`, and fix that new next node's `.prev` to point back to `temp`. Crucially, `temp` does **not** move forward in this case, because there might be a *third* copy of the same value right after — we need to keep comparing `temp` against whatever `temp.next` becomes.

---

## 🔰 Initial State

```
temp = head
```

![Initial sorted doubly linked list with duplicates](/images/remove-dup-dll-01-initial.svg)

---

## 🔍 Step-by-Step Visualization

### Step 1: Duplicate found — skip it, temp stays put

```java
if(temp.data == temp.next.data){
    temp.next = temp.next.next;
    if(temp.next != null){
        temp.next.prev = temp;
    }
}
```

**What happens?** `temp.next` is bypassed by pointing `temp.next` directly to `temp.next.next`. If that new next node exists, its `.prev` is updated to point back to `temp`.
**Why?** This is a standard "unlink a node" operation — same idea as deleting a middle node in a doubly linked list. `temp` deliberately doesn't advance here, so the next loop iteration re-checks `temp` against whatever new node now follows it (handling 3+ consecutive duplicates correctly).

![Duplicate found: skip temp.next, temp stays in place](/images/remove-dup-dll-02-duplicate-skip.svg)

---

### Step 2: No duplicate — move forward

```java
else{
    temp = temp.next;
}
```

**What happens?** When the values differ, there's nothing to remove — `temp` simply advances to the next node.
**Why?** We only skip advancing when we're actively removing something; otherwise normal traversal continues.

![No duplicate: temp advances normally](/images/remove-dup-dll-03-no-duplicate-advance.svg)

---

### Step 3: Repeat — later duplicates handled the same way

The exact same duplicate-check runs again wherever the next repeated value appears in the list.

![Second duplicate later in the list, handled identically](/images/remove-dup-dll-04-duplicate-skip2.svg)

---

## 🎯 Final Result

**Input:** `1 <-> 1 <-> 2 <-> 3 <-> 3 <-> 4`
**Output:** `1 <-> 2 <-> 3 <-> 4`

Every run of consecutive duplicate values was collapsed down to a single node by repeatedly comparing `temp` with `temp.next` and only advancing `temp` once no more duplicates followed it.

![Final deduplicated list](/images/remove-dup-dll-05-final-result.svg)

---

## ⏱️ Complexity Analysis

- **Time Complexity:** O(N) — each node is visited once; `temp` either stays (removing a duplicate) or moves forward, and it can only stay a bounded number of times per position (once per extra duplicate).
- **Space Complexity:** O(1) — pointers adjusted in place, no extra data structures.

---

## 🧠 Key Takeaway

**"Compare with next; skip if equal, advance if not."**
Sorted list ⇒ duplicates are always adjacent. Unlink `temp.next` when it matches `temp` (and don't move `temp`, in case another duplicate follows); otherwise just step forward.

**Pattern:** Doubly Linked List Manipulation + Sorted-list duplicate removal

Your code is correct as-is — no bugs found. Not moving `temp` after a removal is exactly the right call for handling 3+ consecutive duplicates correctly, and the `null` check before updating `temp.next.prev` correctly guards against the case where duplicates extend to the very end of the list.

---

## 💻 Code Implementation

```java
class Solution {
    Node removeDuplicates(Node head) {
        if (head == null || head.next == null) return head;
        Node temp = head;
        while (temp != null && temp.next != null) {
            if (temp.data == temp.next.data) {

                temp.next = temp.next.next;

                if (temp.next != null) {
                    temp.next.prev = temp;
                }
            } else {
                temp = temp.next;
            }
        }
        return head;
    }
}
```
