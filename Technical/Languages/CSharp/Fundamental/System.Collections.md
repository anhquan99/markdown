# Generic collections
- List
- Dictionary
- SortedList
- Queue
- Stack
- Hashset
# Non-generic collections
- ArrayList
- SortedList
- Stack
- Queue
- Hashtable
- BitArray
# IEnumerable and IEnumerator
``````c#
ItemEnumerable items = new ItemEnumerable(data);
foreach(var i in items){
	// iterate items
}
``````
### IEnumerable
- Help user can iterate items using `foreach()`
- It exposes an enumerator, which supports a simple iteration over a non-generic collection.
- When implementing **IEnumerable**, that class must implement `IEnumerator GetEnumerator()` to expose an Enumerator.
``````c#
class ItemEnumerable : IEnumerable{
	private Item[] _items;
	public ItemEnumerable(Item[] items){
		_items = new Item[items.Length];
		for(int i = 0; i < item.Length; i++){
			_items[i] = items[i];
		}
	}
	IEnumerator IEnumerable.GetEnumerator(){
		return (IEnumerator) GetEnumerator();
	}
	public ItemEnum GetEnumerator(){
		return new ItemEnum(_items);
	}
}
``````
### IEnumerator
- Supports a simple iteration over a non-generic collection
- Functions:
	- `MoveNext()`
	- `Reset()`
- Properties:
	- `Current`
``````c#
public ItemEnum : IEnumerator
{
	public Item[] items;
	int position = -1;
	public ItemEnum(Item[] items)
	{
		this.items = items;
	}
	object IEnumerator.Current
	{
		get
		{
			return Current;
		}
	}
	public Item Current
	{
		get
		{
			try
			{
				return items[position];
			}
			catch(IndexOutOfRangeException)
			{
				throw new IndexOutOfRangeException();
			}
		}
	}
	public bool MoveNext()
	{
		position++;
		return position < items.Length;
	}
	public void Reset()
	{
		position = -1;
	}
}
``````
