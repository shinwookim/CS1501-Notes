# Week 2: ADT Tree
## Recap: Boggle Game
Recall the Boggle Game. Given a 4x4 board of letters, we want to find all words with at least 3 adjacent letters.
- Adjacent letters are horizontally, vertically, or diagonally neighboring
- Any cube in the board can only be used once per word, but can be used fo multiple words

We came up with the pseudocode to solve the boggle game:
```Pseudo
void traverse(row and column of current cell, word string so far) {
   for each of the eight directions {
       if neighbor down the direction is a in the board and hasn’t been used {
          append neighbor’s letter to word string and mark neighbor             
          as used
          if word string a word with 3+ letters
             add word string to set of solutions
          if word string is a prefix
             traverse(row and column of neighbor, word string)
           delete last letter of word string and mark neighbor as unused
        }
     }
}
```

We also discussed how the search space could be modeled as a *tree*. In this tree, each node represents one call to `traverse()` except the root node.

Notice that with recursive algorithms, it is difficult to determine the the run-time using the frequency and cost technique due to the recursive calls. 

However, we know that in the worst case, the backtracking algorithm must visit each node in the search space tree. And at each node, the non-recursive part of traverse executes. So, we can use the number of nodes in the search tree as a lower bound for the worst-case runtime: $\text{worst-case runtime}=\text{number of nodes} \times \text{work per node (non-recursive part of traverse)}$

#### Calculating the Number of Nodes
