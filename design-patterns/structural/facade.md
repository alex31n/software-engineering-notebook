# Facade Design Pattern

The Facade design pattern is a structural pattern that provides a simplified, higher-level interface to a complex system of classes, a library, or a framework. 

Think of it like the front desk of a hotel. When you want to order room service, get your clothes dry-cleaned, and book a taxi, you don't call the kitchen, the laundry service, and the taxi company separately. You just call the front desk (the Facade). The front desk handles the complex coordination with all those different departments for you.

## Real-world Example

Think about setting up a home theater to watch a movie. To get everything ready, you have to do a lot of things:
1. Turn on the popcorn popper and start popping.
2. Dim the lights.
3. Turn on the projector and set it to widescreen mode.
4. Turn on the amplifier, set it to the projector input, and adjust the volume.

Instead of doing all of this manually every time, you could use a smart remote control. The smart remote acts as a **Facade**. You just press a single "Watch Movie" button, and it hides all the complexity by sending the right signals to the right devices in the right order.

## When to use it?

- When you want to provide a simple, easy-to-use interface to a complex subsystem.
- When you want to reduce the dependencies (coupling) between the client code and the complex subsystem. If the subsystem changes, you only need to update the Facade, not all the client code.
- When you want to organize your code into distinct layers.

---

## Code Implementation

Let's write code for the "Home Theater" example. We will have complex subsystem classes (`Amplifier`, `Projector`, `Lights`, `PopcornPopper`) and a Facade class (`HomeTheaterFacade`) that simplifies using them.

### Python Example

```python
# --- Complex Subsystem Classes ---
# These are the complex parts of the system that we want to hide behind the facade.

class Amplifier:
    def on(self):
        print("Amplifier: Turning on.")

    def set_volume(self, level: int):
        print(f"Amplifier: Setting volume to {level}.")

class Projector:
    def on(self):
        print("Projector: Turning on.")

    def set_widescreen_mode(self):
        print("Projector: Setting to widescreen mode (16:9).")

class Lights:
    def dim(self, level: int):
        print(f"Lights: Dimming lights to {level}%.")

class PopcornPopper:
    def on(self):
        print("Popcorn Popper: Turning on.")

    def pop(self):
        print("Popcorn Popper: Popping popcorn!")

# --- Facade Class ---
# This class provides a simple method `watch_movie()` that handles all the complex steps.

class HomeTheaterFacade:
    def __init__(self):
        self.amp = Amplifier()
        self.projector = Projector()
        self.lights = Lights()
        self.popper = PopcornPopper()

    def watch_movie(self, movie: str):
        print(f"\n--- Get ready to watch a movie: {movie} ---")
        # The facade handles the complex coordination of multiple subsystems
        self.popper.on()
        self.popper.pop()
        self.lights.dim(10)
        self.projector.on()
        self.projector.set_widescreen_mode()
        self.amp.on()
        self.amp.set_volume(5)
        print("--- Movie is starting! ---\n")

# --- Client Code ---

if __name__ == "__main__":
    # Without the facade, the client would need to interact with all the devices individually.
    # With the facade, it's just one simple call.
    
    home_theater = HomeTheaterFacade()
    home_theater.watch_movie("The Matrix")
```

### Java Example

```java
// --- Complex Subsystem Classes ---
// These are the complex parts of the system.

class Amplifier {
    public void on() {
        System.out.println("Amplifier: Turning on.");
    }

    public void setVolume(int level) {
        System.out.println("Amplifier: Setting volume to " + level + ".");
    }
}

class Projector {
    public void on() {
        System.out.println("Projector: Turning on.");
    }

    public void setWidescreenMode() {
        System.out.println("Projector: Setting to widescreen mode (16:9).");
    }
}

class Lights {
    public void dim(int level) {
        System.out.println("Lights: Dimming lights to " + level + "%.");
    }
}

class PopcornPopper {
    public void on() {
        System.out.println("Popcorn Popper: Turning on.");
    }

    public void pop() {
        System.out.println("Popcorn Popper: Popping popcorn!");
    }
}

// --- Facade Class ---
// The facade hides the complexity of setting up the home theater.

class HomeTheaterFacade {
    private Amplifier amp;
    private Projector projector;
    private Lights lights;
    private PopcornPopper popper;

    public HomeTheaterFacade() {
        this.amp = new Amplifier();
        this.projector = new Projector();
        this.lights = new Lights();
        this.popper = new PopcornPopper();
    }

    public void watchMovie(String movie) {
        System.out.println("\n--- Get ready to watch a movie: " + movie + " ---");
        // The complex coordination is done here, hidden from the client
        popper.on();
        popper.pop();
        lights.dim(10);
        projector.on();
        projector.setWidescreenMode();
        amp.on();
        amp.setVolume(5);
        System.out.println("--- Movie is starting! ---\n");
    }
}

// --- Client Code ---

public class FacadeDemo {
    public static void main(String[] args) {
        // Without the facade, the client would need to control all devices manually.
        // With the facade, the client code is incredibly simple.
        
        HomeTheaterFacade homeTheater = new HomeTheaterFacade();
        homeTheater.watchMovie("Inception");
    }
}
```

---

## Advantages of the Facade Pattern

1. **Simplifies usage**: Clients don't need to understand the complex details of the subsystem to use it.
2. **Reduces coupling**: Clients interact with the Facade rather than the subsystem classes directly. If the internal subsystem changes (e.g., you upgrade your projector to a new brand), you only need to update the Facade, not the client code.
3. **Better code organization**: It helps to separate complex logic into distinct layers.

## Disadvantages

1. **Can become a "God Object"**: If a Facade class grows too large and takes on too many responsibilities for too many subsystems, it can become overly complex itself and hard to maintain. In such cases, it might be better to split it into multiple smaller Facades.

## Summary

The Facade pattern is like an easy-to-use control panel for a very complex machine. It doesn't hide the complex machine from someone who *really* needs to interact with its inner workings (you can still use the subsystem classes directly if needed), but it provides a convenient, simple default way to perform the most common tasks.
