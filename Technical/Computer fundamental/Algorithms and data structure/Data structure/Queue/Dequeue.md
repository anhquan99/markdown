# Idea
- Dequeue or Double Ended Queue is a type of queue you can insert and remove element in the front or the rear of the queue.
- It does not follow FIFO.
# Type
- Input restricted dequeue: input is restricted at a single end but allows delete at both ends.
- Output restricted dequeue: delete is restricted at a single end but allows insert at both ends.
# Applications
- In undo operations on software.
- To store history in browsers.
# Example
- In `C#` dequeue is implemented in `LinkedList<T>`
```csharp
public class Deque
{
    private Node _front;
    private Node _rear;
    private int _k;
    private int _size;
    public Deque(int k)
    {
        _k = k;
        _size = 0;
    }
    public bool InsertFront(int value)
    {
        if (_size == _k) return false;
        var node = new Node(value);
        if (_front == null)
        {
            _front = node;
        }
        else
        {
            _front.Next = node;
            node.Back = _front;
            _front = _front.Next;
        }
        if (_rear == null)
        {
            _rear = node;
        }
        _size++;
        return true;
    }

    public bool InsertLast(int value)
    {
        if (_size == _k) return false;
        var node = new Node(value);
        if (_front == null)
        {
            _front = node;
        }
        if (_rear == null)
        {
            _rear = node;
        }
        else
        {
            _rear.Back = node;
            node.Next = _rear;
            _rear = _rear.Back;
        }
        _size++;
        return true;
    }

    public bool DeleteFront()
    {
        if (IsEmpty()) return false;
        _front = _front.Back;
        if (_front != null)
        {
            _front.Next = null;
        }
        _size--;
        if (IsEmpty())
        {
            _rear = null;
        }
        return true;
    }

    public bool DeleteLast()
    {
        if (IsEmpty()) return false;
        _rear = _rear.Next;
        if (_rear != null)
        {
            _rear.Back = null;
        }
        _size--;
        if (IsEmpty())
        {
            _front = null;
        }
        return true;
    }

    public int GetFront()
    {
        return IsEmpty() ? -1 : _front.Value;
    }

    public int GetRear()
    {
        return IsEmpty() ? -1 : _rear.Value;
    }

    public bool IsEmpty()
    {
        return _size == 0;
    }

    public bool IsFull()
    {
        return _size == _k;
    }
    private class Node
    {
        public int Value;
        public Node Next;
        public Node Back;
        public Node(int value)
        {
            Value = value;
        }
    }
}
```