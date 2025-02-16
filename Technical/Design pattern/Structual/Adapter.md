# Adapter
- Change the interface of a class into the interface that client required
- Adapter is in the middle to connect 2 interface does not compatible to each other
## Problem
- In the first developments your application has created class A. Now you use 3rd party library or something else, your application needs to use a class B. But class A is not compatible with class B. The adapter pattern converts the interface of A class into another interface as expected by the client application here we have B class.  
![[Pasted image 20220313004931.png]]
## Code
```csharp
using System;

namespace Adapter
{
    public interface IGraph
    {
        void Point(double x, double y);
    } 
    
    class PolarGraph
    {
        public void Point(double r, double t)
        {
            Console.WriteLine("Polar Coordinate Point: P(" + r + ", " + t + ")");
        }
    }
    //Adapter triển khai interface mà client sử dụng.
    class PolarGraphAdapter : IGraph 
    {
        private readonly PolarGraph polarGraph;
        public PolarGraphAdapter(PolarGraph polarGraph)
        {
            //Lấy reference đến object cần phải thích ứng.
            this.polarGraph = polarGraph; 
        }
		
        //Implement method Point của interface.
        public void Point(double x, double y)
        {
			//Nhận tung độ và hoành độ x và y, xử lý thành độ dài và góc quay r, t
            double r = Math.Sqrt(x * x + y * y);
            double t = Math.Atan2(y, x);
			//Gọi method Point từ object polarGraph. 
            polarGraph.Point(r, t);
        }   
    }
    
    class Program
    {
        static void Main(string[] args)
        {
            PolarGraph polarGraph = new PolarGraph();
            IGraph graph = new PolarGraphAdapter(polarGraph);
            graph.Point(3, 4);
            //Output: Polar Coordinate Point: P(5, 0.9272952180016122)
        }
    }
}
```