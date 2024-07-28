- Dynamic programming is optimized solution for a problem.
# Properties
- Optimal substructure: the best solution for subproblem in the problem.
- Overlapping subproblem: break problem into subproblems, and those subproblems are usually can be solved based on the previous subproblem which already solved.
# Types
- Combination
- Optimization
# Framework
1. Define the objective function
2. Identify base cases
3. Write down a recurrence relation for the optimized objective function
4. What's the order of execution?
5. Where to look for the answer?
# Recurrence
## Top down
- Memorization and recursion.
## Bottom up
- Tabular: solve all the sub problems, from the base case to the problem.
### Forward DP
- Knows result of some sub problems, from there calculate the next values.
### Backward DP
- Knows only result in 1 sub problem, from there calculate the next values.