
# Singleton
- Ensure a class only have 1 instance
- Global access to that instance
## Application:
- Setting only create 1 time.
## Code:
- **Eager:** the object may not be used so it waste memory 
- **Lazy:** the object create when call, but it can be used in single thread. In multi thread the object will be created as new thread call 
- **Thread safe:** call synchronized method as there will be 1 instance will be created, but the cost is synchronized function will be slow
- Thread safe upgrade: synchronized only call when the instance is null
```java
//Eager initialization
public class EagerInitializedSingleton {

   private static final EagerInitializedSingleton instance = new EagerInitializedSingleton();

   //private constructor to avoid client applications to use constructor
   private EagerInitializedSingleton(){}

   public static EagerInitializedSingleton getInstance(){
	   return instance;
   }
}
```

```java
//Lazy initialization
public class LazyInitializedSingleton {

	private static LazyInitializedSingleton instance;

	private LazyInitializedSingleton(){}

	public static LazyInitializedSingleton getInstance(){
		if(instance == null){
			instance = new LazyInitializedSingleton();
		}
		return instance;
	}
}

```

```java
public class ThreadSafeSingleton {

	private static ThreadSafeSingleton instance;

	private ThreadSafeSingleton(){}

	public static synchronized ThreadSafeSingleton getInstance(){
		if(instance == null){
			instance = new ThreadSafeSingleton();
		}
		return instance;
	 }
 }
```

```java
public class ThreadSafeSingleton {
	private static ThreadSafeSingleton instance;
	private ThreadSafeSingleton(){}

	public static ThreadSafeSingleton getInstance(){
		if(instance == null){
			synchronized(ThreadSafeSingleton.class){
				if(instance == null){
				   instance = new ThreadSafeSingleton();
				}
			}
		}
		return instance;
	 }
}
```