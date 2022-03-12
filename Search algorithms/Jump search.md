# Idea
- Like [[Binary search]], [[Jump search]] is a searching algorithm for sorted arrays.
- Check fewer elements than [[Linear search]] by jumping ahead by fixed steps.
- The best step size is $\sqrt{n}$
# Advantages
- In some situation, jump search is better than binary search because we can traverse back only once.
# Time complexity
- $O(\sqrt{n})$
# Coding
![[Pasted image 20220312103116.png]]