# Rotate List (Rotate Right by k)

## 💡 Intuition

Rotating a list right by `k` just means: the last `k` nodes move to the front, and everything else stays in the same relative order.

The clean way to do this: find the length and the tail, temporarily join the tail back to the head to form a **circle**, then walk to the node that will become the new tail (at position `len - k`), and cut the circle there. Whatever comes right after that cut becomes the new head.

---

## 🔰 Initial State

```
head = node 1
k = 2
```

![Initial list before rotation](/images/rotate-right-01-initial.svg)

---

## 🔍 Step-by-Step Visualization

### Step 1: Find the length and the tail

```java
ListNode tail = head;
int len = 1;

while(tail.next != null){
    len++;
    tail = tail.next;
}
```

**What happens?** Walk to the end of the list, counting nodes as you go.
**Why?** We need to know the total length to normalize `k` (in case `k > len`) and to know exactly where to cut later.

![Traversing to find tail and length](/images/rotate-right-02-compute-length.svg)

---

### Step 2: Join tail to head to form a circle

```java
if(k % len == 0) return head;

tail.next = head;
k = k % len;
```

**What happens?** If `k` is a multiple of `len`, the rotation brings the list back to itself, so return early. Otherwise, connect `tail.next` to `head`, turning the list into a circular one, and reduce `k` to an equivalent smaller rotation (`k % len`).
**Why?** Making it circular means we can now find the new tail purely by counting forward from `head` — no need to handle wraparound as a special case.

![Making the list circular by linking tail to head](/images/rotate-right-03-make-circular.svg)

---

### Step 3: Find the new tail

```java
ListNode newHead = findKthNode(head, len - k);
```

**What happens?** Count `len - k` nodes from `head`. The node reached is the node that should become the **new tail** — the node right after it is the **new head**.
**Why?** The last `k` nodes need to move to the front, so the split point is exactly `len - k` nodes in from the original head.

![Finding the new tail at position len-k](/images/rotate-right-04-find-new-tail.svg)

---

### Step 4: Cut the circle to get the final list

```java
head = newHead.next;
newHead.next = null;
return head;
```

**What happens?** The node after `newHead` becomes the new `head`. Then `newHead.next` is set to `null`, breaking the circular link so the list is linear again.
**Why?** This physically performs the rotation — the last `k` nodes (now at the front) lead into the rest of the original list, and the circle is broken at exactly the right spot.

![Cutting the circle to produce the final rotated list](/images/rotate-right-05-cut-final.svg)

---

## 🎯 Final Result

**Input:** `1 -> 2 -> 3 -> 4 -> 5`, `k = 2`
**Output:** `4 -> 5 -> 1 -> 2 -> 3`

The list's length (5) and tail were found, joined into a circle, then cut after node 3 (position `len - k = 3`) — leaving node 4 as the new head.

---

## ⏱️ Complexity Analysis

- **Time Complexity:** O(N) — one pass to find length/tail, and one more pass (bounded by `len - k`) to find the new tail.
- **Space Complexity:** O(1) — only a handful of pointers used, list is rotated in place.

---

## 🧠 Key Takeaway

**"Circle it, then cut it at len - k."**
Join tail to head to form a circle, walk `len - k` steps from head to find the new tail, then cut right after it.

**Pattern:** Linked List + Pointer Manipulation (circular trick for rotation)

Your code is correct as-is — no bugs found. The `k % len == 0` early return and the `k = k % len` normalization both correctly handle `k` being larger than the list length.

---

## 💻 Code Implementation

```java
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if (head == null || head.next == null) return head;
        ListNode tail = head;
        int len = 1;

        while (tail.next != null) {
            len++;
            tail = tail.next;
        }

        if (k % len == 0) return head;

        tail.next = head;
        k = k % len;

        ListNode newHead = findKthNode(head, len - k);

        head = newHead.next;
        newHead.next = null;
        return head;
    }

    public static ListNode findKthNode(ListNode head, int k) {
        if (head == null || head.next == null) return head;
        ListNode temp = head;
        for (int i = 1; i < k; i++) {
            if (temp == null) return null;
            temp = temp.next;
        }
        return temp;
    }
}
```
