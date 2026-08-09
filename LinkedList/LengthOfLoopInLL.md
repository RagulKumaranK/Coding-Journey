# Length of Loop in a Linked List

## 💡 Intuition

Two pointers, `slow` (1 step) and `fast` (2 steps), race through the list. If there's a cycle, `fast` eventually "laps" `slow` and they land on the same node — that's the meeting point.

Once they meet, freeze `fast` right there as an **anchor**. Walk `slow` forward one step at a time, counting. Since both pointers are now trapped inside the cycle, `slow` is guaranteed to come back to the anchor after exactly one full lap — and that step count is the loop's length.

---

## 🔰 Initial State

```
slow = head
fast = head
```

![Initial state: slow and fast both at head](/images/loop-01-initial-state.svg)

---

## 🔍 Step-by-Step Visualization

### Step 1: Race slow and fast toward the cycle

```java
while(fast!=null && fast.next!=null){
    slow = slow.next;
    fast = fast.next.next;
```

**What happens?** Each loop, `slow` moves 1 node, `fast` moves 2 nodes.
**Why?** If a cycle exists, `fast` enters it first and starts closing the gap with `slow` by 1 node every iteration — guaranteeing a meeting inside the cycle. If there's no cycle, `fast` simply hits `null` and the loop ends.

**Current state (tracked across iterations):**

![Detection iterations table showing slow/fast positions](./images/loop-02-detection-iterations.svg)

---

### Step 2: Detect the meeting point

```java
if(fast==slow){
```

**What happens?** By iteration 3, both pointers land on node `4`.
**Why?** This is the mathematical guarantee of Floyd's algorithm — once inside a cycle, the pointers must meet within one full lap of the loop.

![Meeting point with slow and fast overlapping at node 4](./images/loop-03-meeting-point.svg)

---

### Step 3: Count the loop length

```java
slow = slow.next;
int cot = 1;
while(slow != fast){
    cot++;
    slow = slow.next;
}
return cot;
```

**What happens?** `fast` stays fixed at the meeting node (the anchor). `slow` moves forward one node at a time, incrementing `cot`, until it lands back on `fast`.
**Why?** Since the meeting point is inside the cycle, walking forward from it must return to the same node after exactly `loop_length` steps — that's the definition of a cycle.

![Lap counting: slow walks from the anchor back to itself, counting steps](./images/loop-04-lap-counting.svg)

---

## 🎯 Final Result

**Input:** `1 -> 2 -> 3 -> 4 -> 5 -> (5 points back to 3)`
**Output:** `3`

`fast` and `slow` met at node 4 via Floyd's cycle detection. Fixing `fast` there and walking `slow` one full lap (`4 → 5 → 3 → 4`) counted exactly 3 steps — the loop length.

---

## ⏱️ Complexity Analysis

- **Time Complexity:** O(N) — bounded traversal to find the meeting point, plus one more full lap around the cycle to count it.
- **Space Complexity:** O(1) — only pointers and a counter, no extra data structures.

---

## 🧠 Key Takeaway

**"Catch, then lap it."**
Catch `fast` with `slow` to confirm the cycle → freeze one pointer as an anchor → walk the other one full lap while counting → that count is the loop length.

**Pattern:** Fast & Slow Pointer (Floyd's Cycle Detection)

⚠️ **Bug in the base case:** `if(head==null && head.next==null)` throws an NPE when `head` is `null` (short-circuiting doesn't save you here since `&&` still evaluates `head.next`). Should be `||`. Fixed in the code below.

---

## 💻 Code Implementation

```java
class Solution {
    public int lengthOfLoop(Node head) {
        if (head == null || head.next == null) return 0;

        Node slow = head;
        Node fast = head;

        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;

            if (fast == slow) {
                slow = slow.next;
                int cot = 1;
                while (slow != fast) {
                    cot++;
                    slow = slow.next;
                }
                return cot;
            }
        }
        return 0;
    }
}
```
