# Idea
- Pick the best option based on a best option available at the moment without considering future consequence.
- The algorithm neveer reverses the earlier decision even if the choice is wrong. It works in a top-down approach.
- This algorithm may not produce the best result for all the problems.
- The algorithm can be used with any problem has the properties:
    - Greedy choice property: If an optimal solution to the problem can be found by choosing the best choice at each step without reconsidering the previous steps once chosen, the problem can be solved using a greedy approach. This property is called greedy choice property.
    - Optimal substructure: If the optimal overall solution to the problem corresponds to the optimal solution to its subproblems, then the problem can be solved using a greedy approach. This property is called optimal substructure.
# Advantages
- The algorithm is fast and efficient with time complexity of `O(n log n)` or `O(n)`. Therefore applied in solving large-scale problems.
- The search for optimal solution is done without repetition – the algorithm runs once.
- The algorithm is easier to describe.
# Disadvantages
- Does not always produce the optimal solution.
