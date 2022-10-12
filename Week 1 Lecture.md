# Week 1: Intro Materials + Search, Pruning, Recursion, and Backtracking
## Modeling Runtime of Algorithms
### Example: ThreeSum Problem
Consider the ThreeSum Problem: Given a set of arbitrary integers find out how many **distinct** triples sum to exactly zero.

Before we begin, we must ask questions on the problem specifications such as: "*What does **distinct** mean in this problem?*", "*Is the data sorted?*", "*What is the input data size*", etc.

E.g., Example Input: 5, -1, 2, -3, -2, 1, 0.
For this set of inputs, our output should be 4 ((5, -2, -3), (-1, 0, 1,), (2, 0, -2), (-3, 1, 2)).

One possible solution is the **brute-force** solution: We enumerate all possible distinct triples and check their sums.
```PSEUDOCODE
cnt = 0
	for each distinct triple
		if sum of triple equals zero
			increment cnt 
```
Java Implementation:
```Java
public static int count(int[] a){
	int n = a.length;
	int cnt = 0;
	for (int i = 0; i < n; i++){
		for (int j = i+1; j < n; j++){
		// j loop starts from i + 1
			for (int k = j+1; k < n; k++){
				if (a[i] + a[j] + a[k] == 0){
					cnt++;
				}
			}
		}
	} return cnt;
}
```

