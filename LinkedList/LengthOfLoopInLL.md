# Length of Loop in a Linked List

## 📝 Problem Explanation

**Question enna kekkuthu:** Unakku oru linked list kudukuvanga. Andha list la cycle (loop) irukka nu check pannanum, cycle irundha andha loop la eththana nodes irukku nu count panni return pannanum. Cycle illana `0` return pannanum.

**Sample Input:**
```
1 -> 2 -> 3 -> 4 -> 5
          ^         |
          |_________|
(5th node points back to 3rd node)
```

**Sample Output:** `3` (loop nodes: 3 → 4 → 5 → back to 3)

**Simple example:** Nee oru necklace nu nenachikoo, but andha necklace oru rope oda thொடங்கி, middle la irundhu oru loop maadhiri form aagudhu (bracelet maadhiri). Andha bracelet part la eththana beads irukku nu kekkaranga.

---

## 💡 Logic Explanation (Thanglish + Simple English)

Idhu **Fast and Slow Pointer** technique (Floyd's Cycle Detection) nu solluvanga. Race maadhiri nenachikoo:

- `slow` pointer oru step step ah nadakkum (1 node move).
- `fast` pointer rendu step aagaakum (2 nodes move) — adhaan fast!

Ippo, list la cycle irundha, indha rendu pointers ஒரே track la ottam ottுவாங்க (like a circular running track). `fast` speed jaasthi ah irukurathala, adhu `slow`-a **lap adichitu** oru point la meet aagum. Andha meet aagira point thaan namma "cycle irukku" nu confirm pannuvom.

Cycle illana? `fast` pointer `null` ku poyiduvaan (list end aayiduchu), adhுக்கு loop illa nu aritha mudiyum.

**Meet aana pinnadi enna pannanum?**

Ippo `fast`-a andha meeting point la fix pannitu (anchor maadhiri vechitu), `slow`-a mattum move pannuvom, count vechikittu (`cot`). `slow` thirumbi andha same meeting point ku vandhurudhaan matter, adhu eththana steps edukkuthu nu count pannina, adhுthaan namma loop length!

**Why idhu work aagum?** Rendu pointers-um already cycle-ku inside pona pinnadi, avanga eppovum andha cycle-kulla thaan suthi suthi varuvanga. So `slow`-a oru round (one full lap) suthina, thirumbi andha same anchor point ku vandhu serum — adhu exactly cycle oda size.

**One line memory trick:** *"Catch panra, appuram lap edukanum"* — first fast/slow meet aagi cycle confirm pannunga, appuram anchor vechitu one lap suthi count pannunga.

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

**What happens?** Every iteration la `slow` 1 node move aagum, `fast` 2 nodes move aagum.
**Why?** Cycle irundha, `fast` first cycle-kulla poyiduvaan, appuram `slow`-oda gap-a ஒவ்வொரு iteration-um 1 node kammi pannikittu varuvaan — final ah meet aagurathukku guarantee.

![Detection iterations table showing slow/fast positions](/images/loop-02-detection-iterations.svg)

---

### Step 2: Detect the meeting point

```java
if(fast==slow){
```

**What happens?** Iteration 3-la, rendu pointers-um node `4`-la meet aaguthu.
**Why?** Idhu Floyd's algorithm oda guarantee — cycle-kulla ponapinnadi, rendu pointers-um one full lap-ukkulla definitely meet aaguvanga.

![Meeting point with slow and fast overlapping at node 4](/images/loop-03-meeting-point.svg)

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

**What happens?** `fast`-a meeting node-la fix pannitu vechirukom (anchor). `slow` mattum move aaguthu, count `cot` increase aaguthu, `slow` thirumbi `fast` ku equal aagura varaikkum.

**Why?** Meeting point cycle-kulla irukurathala, andha point-la irundhu forward nadandha, exactly `loop_length` steps-la thirumbi andha same node-ku varum.

![Lap counting: slow walks from the anchor back to itself, counting steps](/images/loop-04-lap-counting.svg)

---

## 🎯 Final Result

**Input:** `1 -> 2 -> 3 -> 4 -> 5 -> (5 points back to 3)`
**Output:** `3`

`fast` and `slow` node 4-la meet aachu (Floyd's cycle detection). `fast`-a fix pannitu, `slow`-a one full lap (`4 → 5 → 3 → 4`) suthi vachadhula exactly 3 steps — adhுthaan loop length.

---

## ⏱️ Complexity Analysis

- **Time Complexity:** O(N) — meeting point kandupudikka bounded traversal, plus oru extra full lap loop length count pannurathukku.
- **Space Complexity:** O(1) — pointers and oru counter mattum, extra data structure use pannala.

---

## 🧠 Key Takeaway

**"Catch panra, appuram lap edukanum."**
`fast`-a `slow` catch pannitu cycle confirm pannunga → oru pointer-a anchor ah freeze pannunga → verandha pointer-a one full lap suthi count pannunga → adhே loop length.

**Pattern:** Fast & Slow Pointer (Floyd's Cycle Detection)

⚠️ **Bug in the base case:** `if(head==null && head.next==null)` — `head` `null` na, `&&` short-circuit aagadhu, `head.next` evaluate pannum, adhula NullPointerException varum. `||` use pannanum. Fixed code kீழே kudukapatta irukku.

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