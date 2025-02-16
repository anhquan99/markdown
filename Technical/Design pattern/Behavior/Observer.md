# Observer
- Notify many objects in the system
- publisher subscriber model. The subscriber subscribe to the publisher to get notification.
## Problem
- In weather system, the weather data changes every minute or second that make the prediction changes every time the data change. The user uses the weather system wants to know when the prediction changes. The observer pattern defines one-to-many dependency between objects to that when one object changes its state all the other are notified about the change.
![[Pasted image 20220313010640.png]]
## Code
```java
public interface Subject {
    public void registerObserver(Observer o);
    public void removeObserver(Observer o);
    public void notifyObservers(); // phương thức này được gọi để thông báo cho tất cả các observer một khi trạng thái của Subject được thay đổi.
}

public interface Observer {
    public void update(float temp, float humidity, float pressure);
}

public interface DisplayElement {
    public void display();
}

```

```java
public class WeatherData implements Subject {
    // Chúng ta thêm một ArrayList để lưu danh sách cách Observer, khởi tạo nó ở constructor.
    private ArrayList observers;
    private float temperature;
    private float humidity;
    private float pressure;
    
    public WeatherData() {
        observers = new ArrayList();
    }
    
    public void registerObserver (Observer o) {
    // Khi một observer đăng kí quan sát, chúng ta sẽ thêm nó vào cuối danh sách.
        observer.add(o);
    }
    
    public void removerObserver(Observer o) {
    // Ngược lại, nếu một observer không muốn đăng kí nữa, chúng ta sẽ loại nó ra khỏi danh sách
        int i = observers.indexOf(o);
        if (i >= 0) {
        observers.remove(i);
        }
    }
    
    public void notifyObservers() {
        for (int i = 0; i < observer.size(); ++i) {
            Observer observer = (Observer)observers.get(i);
            observer.udpate(temperature, humidity, pressure);
        }
    }
    
    public void measurementsChanged() {
    // Chúng ta thông báo cho các Observer một khi chúng ta đã cập nhật thành công các thông tin từ Weather Station.
        notifyObservers();
    }
    
    pubic void setMeasurements(float temperature, float humidity, float pressure) {
    // Chúng ta có phương thức này để tạm thời có thể thay đổi trạng thái của WeaterData.
        this.temperature = temperatur;
        this.humidity = humidity;
        this.pressure = pressure;
        
        measurementsChanged();
    }
    
    // other WeatherData methods here
}
```

```java
public class CurrentConditionsDisplay implements Observer, DisplayElement {
    private float temperatur;
    private float humidity;
    private Subject weatherData;
    
    public CurrentConditionsDisplay(Subject weatherData) {
        this.weatherData = weatherData;
        weatherData.registerObserver(this);
    }
    
    public void update(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        display();
    }
    
    public void display() {
        System.out.println("Current conditions: " + temperature + " F degrees and " + humidity + "% humidity");
    }
}
```