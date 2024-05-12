# Definition
- Extended version of [[Queue]] where the last element is connected to the first element, to form a circle.
  ![[Pasted image 20240511222839.png]]
- In a normal queue after a bit of insertion and deletion, where will be non-usable empty space.
# Operate
- Circular queue works by circularly increasing the pointer.
- If the pointer reach end of the queue then it will start from the beginning of the queue again.
# Implementation
- 2 pointers, `Front` and `Rear`.
- `Front` is used to track the first element in queue.
- `Rear` is used to track the last element in queue.
- Initially, set value of `Front` and `Rear` to -1.
# Operation
## Enqueue
- Check the queue is full, if not set the value to `Rear` then increase the `Rear`.
- If it is the first item, set `Front` = 1.
## Dequeue
- Check the queue is empty, if not, increase the `Front`.
- If it is the last item, set `Front` and `Rear` to 1.