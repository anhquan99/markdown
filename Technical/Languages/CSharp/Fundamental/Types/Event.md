# Delegate
- It is a function pointer points to a function with the same return type and parameters.
- Event can be called through delegate.
```c#
// declare a delegate type
public delegate void ShowLog(string message);

// functions that can be assign must be the same signature with the delegate
public static void Info(string s){}

public static void Warning(string s){}

public static void Main()
{
	ShowLog showlog;
	showLog = Info;
	showLog("Info");

	showLog = Warning;
	showLog("Warning");
}
```
- A delegate assign multiple functions, including lambda functions.
```c#
showLog += Warning;
showLog += Info;
showLog += Warning;
```
### Multicast delegates
- The multicast delegate contains a list of the assigned delegates. When the multicast delegate is called, it invokes the delegates in the list in order.
```c#
delegate void CustomCallback(string s);
CustomCallback multicast;

multicast += Hello;
multicast += Goodbye;

multicast("A"); // this invoke Hello and Goodbye

static void Hello(string s) 
{
	Console.WriteLine($" Hello, {s}!"); 
} 
static void Goodbye(string s) 
{ 
	Console.WriteLine($" Goodbye, {s}!"); 
}
```
### Anonymous method
```c#
delegate void NotifyCallback(string str);

// anonymous
NotifyCallback delegate1 = delegate(string s){ Console.WriteLine(s); };

// lambda
NotifyCallback delegate2 = s => { Console.WriteLine(s);}; 
```
## Func
- A pre-defined delegate type in C# where it must have a return type and parameter is optional.
- The last data type declared in the Func is the return type.
```c#
Func<string> func_1; // return string
Func<int, int, string> func_2; // parameters are int return string.
```
## Action
- A pre-defined delegate type in C# where it returns void.
```c#
Action<string> action_1;
Action action_2;
```
# Event
- Event is a communication technique for class where a class wants to notify other class has interest in an event.
- The same with delegate, event only allow user to use `+=` or `-=` operation (Pub/sub).
- The class containing the event raises (trigger) the event, which in turn invokes the delegate associated with the event.
- All the event in .NET is build from a delegate `EventHandler`
```c#
public delegate void EventHandler(object sender, EventArgs e);
public delegate void EventHandler<TEventArgs>(object sender, TEventArgs e);
```
- `EventArgs` can be inherited to add more custom data. 
- Example:
```c#
using System;

namespace ConsoleApplication1
{
    class ProgramOne
    {
        static void Main(string[] args)
        {
            Counter c = new Counter(new Random().Next(10));
            c.ThresholdReached += c_ThresholdReached;

            Console.WriteLine("press 'a' key to increase total");
            while (Console.ReadKey(true).KeyChar == 'a')
            {
                Console.WriteLine("adding one");
                c.Add(1);
            }
        }

        static void c_ThresholdReached(object sender, EventArgs e)
        {
            Console.WriteLine("The threshold was reached.");
            Environment.Exit(0);
        }
    }

    class Counter
    {
        private int threshold;
        private int total;

        public Counter(int passedThreshold)
        {
            threshold = passedThreshold;
        }

        public void Add(int x)
        {
            total += x;
            if (total >= threshold)
            {
                ThresholdReached?.Invoke(this, EventArgs.Empty);
            }
        }

        public event EventHandler ThresholdReached;
    }
}
```