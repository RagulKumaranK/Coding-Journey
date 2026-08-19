# Sort List

## 💡Intuition

The structure of the linked list (the pointers) never actually needs to change — only the *values* stored inside the nodes need to end up in sorted order.

So: walk the list once and copy every value into an array, sort that array with a standard sort, then walk the list a second time and overwrite each node's value with the sorted array in order. The links (`.next` pointers) are untouched throughout.

---

## 🔰 Initial State

```
temp = head
arr = []
```

![Initial unsorted list](/images/sort-list-01-initial.svg)

---

## 🔍 Step-by-Step Visualization

### Step 1: Extract all values into an array

```java
List<Integer> arr = new ArrayList<>();
ListNode temp = head;
while(temp != null){
    arr.add(temp.val);
    temp = temp.next;
}
```

**What happens?** Traverse the list once, copying each node's value into `arr`, in list order.
**Why?** Once the values are in a plain array, we can use any standard sorting algorithm instead of implementing list-specific sorting logic.

![Extracting values into an ArrayList](/images/sort-list-02-extract-array.svg)

---

### Step 2: Sort the array

```java
Collections.sort(arr);
```

**What happens?** The array `[4, 2, 1, 3]` becomes `[1, 2, 3, 4]`.
**Why?** This is the actual sorting step — everything before and after it is just moving values in and out of the list.

![Sorting the extracted array](/images/sort-list-03-sort-array.svg)

---

### Step 3: Write sorted values back into the nodes

```java
temp = head;
int n = arr.size();
for(int i = 0; i < n; i++){
    temp.val = arr.get(i);
    temp = temp.next;
}
```

**What happens?** Walk the list again from `head`, and overwrite each node's `.val` with the next value from the sorted array.
**Why?** Since the number of nodes equals `arr.size()`, and we walk in the same order the array was built in, this correctly places the smallest value at the first node, the next-smallest at the second, and so on — without ever touching a single `.next` pointer.

![Writing sorted values back into the original nodes](/images/sort-list-04-write-back-final.svg)

---

## 🎯 Final Result

**Input:** `4 -> 2 -> 1 -> 3`
**Output:** `1 -> 2 -> 3 -> 4`

Values were extracted, sorted, then written back into the same nodes in the same structural order — no pointer rewiring needed.

---

## ⏱️ Complexity Analysis

- **Time Complexity:** O(N log N) — dominated by `Collections.sort`; the two list traversals are O(N) each.
- **Space Complexity:** O(N) — the `ArrayList` stores every node's value separately from the list.

---

## 🧠 Key Takeaway

**"Copy out, sort, copy back."**
Extract values → sort the array → overwrite node values in the same order. Simple and correct, but not the space-optimal approach for this problem.

**Pattern:** Array Extraction + Sorting (not a linked-list-native technique)

Your code is **correct**, but it's not the optimal solution for this classic problem — no bugs found, just a space trade-off worth knowing about.

### 📈 Optimization Note

The typical optimal approach for "Sort List" is **Merge Sort directly on the linked list**:
- Use the **slow/fast pointer** technique to split the list into two halves.
- Recursively sort each half.
- Merge the two sorted halves by relinking `.next` pointers (no array involved).

This achieves **O(N log N) time** with **O(log N) space** (recursion stack only), compared to your approach's **O(N)** extra array space. Worth knowing for interviews where O(1)/O(log N) extra space is explicitly asked for — but your current solution is perfectly valid and much simpler to write correctly under time pressure.

---

## 💻 Code Implementation

```java
class Solution {
    public ListNode sortList(ListNode head) {
        List<Integer> arr = new ArrayList<>();
        ListNode temp = head;
        while (temp != null) {
            arr.add(temp.val);
            temp = temp.next;
        }
        temp = head;
        Collections.sort(arr);
        int n = arr.size();
        for (int i = 0; i < n; i++) {
            temp.val = arr.get(i);
            temp = temp.next;
        }
        return head;
    }
}
```
