# Copy List with Random Pointer

## 💡 Intuition

The tricky part of this problem isn't copying `next` — it's copying `random`, since a node's random pointer can point *anywhere* in the list, including a node we haven't cloned yet.

The trick: use a **HashMap** to remember "original node → its clone." First pass, create every clone (values only, no links yet) and record the mapping. Second pass, revisit the originals and use the map to look up the correct clone for both `next` and `random` — since by now every original has a clone in the map, no matter which direction its pointers go.

---

## 🔰 Initial State

```
map = {}
temp = head
```

![Initial list with next and random pointers](/images/copy-random-list-01-initial.svg)

---

## 🔍 Step-by-Step Visualization

### Step 1: First pass — create clones and build the map

```java
HashMap<Node,Node> map = new HashMap<>();
Node temp = head;
while(temp != null){
    map.put(temp, new Node(temp.val));
    temp = temp.next;
}
```

**What happens?** Walk the original list once. For every node, create a brand-new clone node (copying only `.val`) and store the pair `original → clone` in the map.
**Why?** This guarantees that by the time the second pass starts, *every* original node already has a corresponding clone sitting in the map — ready to be looked up regardless of whether it's referenced by `next` or by a `random` pointer from anywhere else in the list.

![Building the original-to-clone map](/images/copy-random-list-02-build-map.svg)

---

### Step 2: Second pass — wire up next and random

```java
temp = head;
while(temp != null){
    Node copy = map.get(temp);
    copy.next = map.get(temp.next);
    copy.random = map.get(temp.random);
    temp = temp.next;
}
```

**What happens?** Walk the original list again. For each original node, fetch its clone from the map, then set the clone's `.next` and `.random` by looking up *the original's* `next`/`random` node in the map — translating original-list references into clone-list references.
**Why?** `map.get(temp.next)` gives the clone of whatever comes next; `map.get(temp.random)` gives the clone of whatever the random pointer targets. Since `map.get(null)` returns `null`, the last node's `next` correctly resolves to `null` too — no extra edge-case handling needed.

![Wiring next and random pointers on the clones using the map](/images/copy-random-list-03-wire-pointers.svg)

---

## 🎯 Final Result

**Input:** `A(1) → B(2) → C(3)`, with `A.random = C`, `B.random = A`, `C.random = B`
**Output:** A fully independent clone `A'(1) → B'(2) → C'(3)` with `A'.random = C'`, `B'.random = A'`, `C'.random = B'` — same structure and values, but sharing **zero** node references with the original.

`return map.get(head)` hands back the clone corresponding to the original head, i.e. `A'`.

![Final result: original and clone as two independent lists](/images/copy-random-list-04-final-result.svg)

---

## ⏱️ Complexity Analysis

- **Time Complexity:** O(N) — two linear passes over the list; each `HashMap` `get`/`put` is O(1) on average.
- **Space Complexity:** O(N) — the `HashMap` stores one entry per node (original → clone).

---

## 🧠 Key Takeaway

**"Map first, wire second."**
Pass 1 creates every clone and records original→clone in a map. Pass 2 uses that map to translate every `next` and `random` reference from the original list into the correct clone reference.

**Pattern:** Hashing (HashMap) + Linked List

Your code is correct as-is — no bugs found. Clean handling of the `null` case (`map.get(null)` naturally returns `null`), so `next`/`random` pointing to nothing is handled without extra checks.

---

## 💻 Code Implementation

```java
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;
        HashMap<Node, Node> map = new HashMap<>();
        Node temp = head;
        while (temp != null) {
            map.put(temp, new Node(temp.val));
            temp = temp.next;
        }
        temp = head;
        while (temp != null) {
            Node copy = map.get(temp);
            copy.next = map.get(temp.next);
            copy.random = map.get(temp.random);
            temp = temp.next;
        }
        return map.get(head);
    }
}
```
