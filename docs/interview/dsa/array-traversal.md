### 🔍 What is Array Traversal?

**Array Traversal** means **accessing and visiting each element of the array exactly once**, typically from start to end (or in reverse). It's the foundation for most operations on arrays like searching, summing, modifying, etc.

#### ✅ Key Points:
- You usually use a **loop** (like `for` or `while`) to visit each index.
- Time complexity is **O(n)** for traversing an array of size `n`.

#### 👇 Example in Java:
```java
int[] arr = {10, 20, 30, 40, 50};
for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]); // Accessing each element
}
```

---

### ✅ Core Skills You Should Practice:
1. **Forward traversal**
2. **Reverse traversal**
3. **Modifying elements during traversal**
4. **Finding min/max, sum, average**
5. **Using conditionals during traversal**

---

### 🧠 Practice Problems

#### 🟢 Easy (Basics)
1. **Print all elements of an array**
2. **Print array in reverse**
3. **Calculate the sum of all elements**
4. **Find the maximum/minimum element**
5. **Count the number of even and odd elements**

#### 🟡 Medium (Slightly tricky logic)
6. **Find the second largest element**
7. **Check if the array is sorted**
8. **Reverse the array in-place**
9. **Find the frequency of each element (use a `Map`)**
10. **Move all zeros to the end of the array**

#### 🔵 Bonus (Slightly more algorithmic)
11. **Left rotate the array by one position**
12. **Left rotate by `k` positions**
13. **Find the leader elements in the array** (element is greater than all elements to its right)
14. **Kadane's Algorithm** – Maximum sum subarray
15. **Remove duplicates from a sorted array**

---