# Observer Design Pattern

## What is the Observer Pattern?

The **Observer** is a behavioral design pattern. It lets you define a subscription mechanism to notify multiple objects about any events that happen to the object they're observing.

In simple terms, you have one object that holds some data or state (called the **Subject** or **Publisher**), and you have other objects that want to know when that data changes (called the **Observers** or **Subscribers**). Instead of the observers constantly checking if the data has changed, the subject notifies them automatically whenever a change occurs.

## Real-World Analogy

Imagine you are interested in a specific tech magazine.
Instead of going to the store every day to check if the new issue is out (which is a waste of time), you buy a **subscription**.

- The **Magazine Publisher** is the **Subject**.
- **You (and other subscribers)** are the **Observers**.

Whenever a new issue is printed, the publisher doesn't wait for you to ask; they simply mail the new issue directly to your mailbox. All subscribers get the new issue at the same time.

## Why use the Observer Pattern?

1. **Loose Coupling:** The subject doesn't need to know anything about the observers other than the fact that they implement a specific interface. You can add or remove observers at any time without changing the subject's code.
2. **Automatic Updates:** Observers are automatically notified of state changes, preventing them from having to repeatedly poll the subject for updates.
3. **One-to-Many Relationships:** It perfectly handles scenarios where a single change in one object needs to trigger updates in many other objects.

## Code Examples

Let's build a simple **Weather Station** application.
The Weather Station (Subject) tracks the temperature. Various display elements like a Phone Display and a Window Display (Observers) will update themselves whenever the temperature changes.

### Python Implementation

```python
from abc import ABC, abstractmethod

# --- Observer Interface ---
class Observer(ABC):
    @abstractmethod
    def update(self, temperature: float):
        pass

# --- Subject Interface ---
class Subject(ABC):
    @abstractmethod
    def register_observer(self, observer: Observer):
        pass

    @abstractmethod
    def remove_observer(self, observer: Observer):
        pass

    @abstractmethod
    def notify_observers(self):
        pass

# --- Concrete Subject ---
class WeatherStation(Subject):
    def __init__(self):
        self._observers = []
        self._temperature = 0.0

    def register_observer(self, observer: Observer):
        if observer not in self._observers:
            self._observers.append(observer)

    def remove_observer(self, observer: Observer):
        if observer in self._observers:
            self._observers.remove(observer)

    def notify_observers(self):
        for observer in self._observers:
            observer.update(self._temperature)

    def set_temperature(self, temp: float):
        print(f"\nWeatherStation: New temperature measurement: {temp}°C")
        self._temperature = temp
        # Automatically notify everyone when the temperature changes
        self.notify_observers()

# --- Concrete Observers ---
class PhoneDisplay(Observer):
    def update(self, temperature: float):
        print(f"Phone Display: The temperature is now {temperature}°C")

class WindowDisplay(Observer):
    def update(self, temperature: float):
        print(f"Window Display: It's {temperature}°C outside right now.")

# --- Example Usage ---
if __name__ == '__main__':
    # Create the Subject
    weather_station = WeatherStation()

    # Create Observers
    phone_display = PhoneDisplay()
    window_display = WindowDisplay()

    # Subscribe the observers to the subject
    weather_station.register_observer(phone_display)
    weather_station.register_observer(window_display)

    # Change the state of the subject
    weather_station.set_temperature(25.5)
    weather_station.set_temperature(26.0)

    # Unsubscribe an observer
    print("\n[Removing Phone Display]")
    weather_station.remove_observer(phone_display)

    # Change the state again
    weather_station.set_temperature(24.0)
```

### Java Implementation

```java
import java.util.ArrayList;
import java.util.List;

// --- Observer Interface ---
interface Observer {
    void update(float temperature);
}

// --- Subject Interface ---
interface Subject {
    void registerObserver(Observer o);
    void removeObserver(Observer o);
    void notifyObservers();
}

// --- Concrete Subject ---
class WeatherStation implements Subject {
    private List<Observer> observers;
    private float temperature;

    public WeatherStation() {
        observers = new ArrayList<>();
    }

    @Override
    public void registerObserver(Observer o) {
        observers.add(o);
    }

    @Override
    public void removeObserver(Observer o) {
        observers.remove(o);
    }

    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(temperature);
        }
    }

    public void setTemperature(float temperature) {
        System.out.println("\nWeatherStation: New temperature measurement: " + temperature + "°C");
        this.temperature = temperature;
        // Automatically notify everyone when the temperature changes
        notifyObservers();
    }
}

// --- Concrete Observers ---
class PhoneDisplay implements Observer {
    @Override
    public void update(float temperature) {
        System.out.println("Phone Display: The temperature is now " + temperature + "°C");
    }
}

class WindowDisplay implements Observer {
    @Override
    public void update(float temperature) {
        System.out.println("Window Display: It's " + temperature + "°C outside right now.");
    }
}

// --- Example Usage ---
public class ObserverPatternDemo {
    public static void main(String[] args) {
        // Create the Subject
        WeatherStation weatherStation = new WeatherStation();

        // Create Observers
        PhoneDisplay phoneDisplay = new PhoneDisplay();
        WindowDisplay windowDisplay = new WindowDisplay();

        // Subscribe the observers to the subject
        weatherStation.registerObserver(phoneDisplay);
        weatherStation.registerObserver(windowDisplay);

        // Change the state of the subject
        weatherStation.setTemperature(25.5f);
        weatherStation.setTemperature(26.0f);

        // Unsubscribe an observer
        System.out.println("\n[Removing Phone Display]");
        weatherStation.removeObserver(phoneDisplay);

        // Change the state again
        weatherStation.setTemperature(24.0f);
    }
}
```

## Summary

The Observer pattern is an essential tool for building reactive systems. It allows you to build a system where components react to changes in other components without those components being tightly coupled. It's heavily used in GUI frameworks (like handling button clicks) and Event-Driven architectures.