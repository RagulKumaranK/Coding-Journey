# Reverse Nodes in k-Group

## 💡 Intuition

Instead of reversing the whole list at once, break it into chunks of size `k` and reverse each chunk individually, then stitch the chunks back together in order.

For every chunk: find its `k`th node, temporarily cut the chunk off from the rest of the list, reverse just that chunk, then reconnect it — the previous chunk's tail points into this chunk's new head, and this chunk's new tail points to the next chunk. If a leftover group has fewer than `k` nodes, leave it untouched (that's what `findKthNode` returning `null` signals).
---

## 🔰 Initial State

```
temp = head
prev = null
k = 2
```

![Initial state before any group is processed](/images/reverse-k-group-01-initial.svg)

---

## 🔍 Step-by-Step Visualization

### Step 1: Find the kth node and cut the group off

```java
ListNode kth = findKthNode(temp, k);
if (kth == null) break;

ListNode nextGroup = kth.next;
kth.next = null;
```

**What happens?** Walk `k-1` steps from `temp` to find the `k`th node of the current group. Save what comes after it (`nextGroup`), then sever the link so this group becomes an isolated mini-list.
**Why?** Reversing needs a clean, self-contained sublist — if we didn't cut it off, `revHead` would keep reversing past the group boundary into the rest of the list.

![Find kth node and cut group 1 away from the rest](/images/reverse-k-group-02-find-cut-group1.svg)

---

### Step 2: Reverse the group and relink it

```java
ListNode rev = revHead(temp);

if (prev == null) head = rev;
else prev.next = rev;

prev = temp;
temp.next = nextGroup;
temp = nextGroup;
```

**What happens?** The isolated group gets reversed in place (`revHead`). Since `temp` was the *head* of the group before reversing, it becomes the *tail* after reversing. The new head (`rev`) is linked in — either becomes the overall `head` (first group) or is attached via `prev.next`. Then the tail (`temp`) is linked forward to `nextGroup`, and `prev` is advanced to this tail for the next iteration.
**Why?** This is what stitches each reversed chunk back into a single connected list, in the correct order.

![Reverse group 1 and relink into the list](/images/reverse-k-group-03-reverse-relink-group1.svg)

---

### Step 3: Repeat for the next group

The same find → cut → reverse → relink sequence runs again for the next chunk of `k` nodes, using the updated `prev` and `temp`.

![Group 2 processed: found, cut, reversed, and relinked](/images/reverse-k-group-04-group2-process.svg)

---

### Step 4: Stop when a group is incomplete

```java
if (kth == null) break;
```

**What happens?** When fewer than `k` nodes remain, `findKthNode` can't find a full group and returns `null`, so the loop exits — leaving that remainder untouched.
**Why?** The problem only reverses *complete* groups of size `k`; a partial trailing group stays in its original order.

![Final result: leftover node left unreversed, full list stitched together](/images/reverse-k-group-05-final-result.svg)

---

## 🎯 Final Result

**Input:** `1 -> 2 -> 3 -> 4 -> 5`, `k = 2`
**Output:** `2 -> 1 -> 4 -> 3 -> 5`

Groups `[1,2]` and `[3,4]` were each reversed and relinked in order; node `5` didn't have a full group of 2, so it was left as-is at the end.

---

## ⏱️ Complexity Analysis

- **Time Complexity:** O(N) — every node is visited a constant number of times: once by `findKthNode`, once during reversal, across all groups combined.
- **Space Complexity:** O(1) — reversal and relinking are done in-place with a fixed number of pointers, no recursion or extra data structures.

---

## 🧠 Key Takeaway

**"Find k, cut, flip, stitch."**
For each group: find the `k`th node → cut it off → reverse it → connect `prev` to the new head and the new tail to `nextGroup`. Stop the moment a group can't be completed.

**Pattern:** Linked List + Pointer Manipulation (in-place group reversal)

Your code is correct as-is — no bugs found. Clean handling of the incomplete-trailing-group edge case via the `kth == null` check.

---

## 💻 Code Implementation

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode temp = head;
        ListNode prev = null;

        while (temp != null) {

            ListNode kth = findKthNode(temp, k);

            if (kth == null) {
                break;
            }

            ListNode nextGroup = kth.next;
            kth.next = null;

            ListNode rev = revHead(temp);

            if (prev == null) head = rev;
            else {
                prev.next = rev;
            }
            prev = temp;
            temp.next = nextGroup;
            temp = nextGroup;

        }
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

    public static ListNode revHead(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode curr = head;
        ListNode prev = null;
        while (curr != null) {
            ListNode temp = curr.next;
            curr.next = prev;
            prev = curr;
            curr = temp;
        }
        return prev;
    }
}
```
