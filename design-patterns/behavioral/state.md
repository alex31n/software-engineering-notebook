# State Design Pattern

The **State** is a behavioral design pattern that allows an object to change its behavior when its internal state changes. It looks as if the object changed its class.

Imagine an object that can exist in several different "states". The way the object reacts to the exact same request depends entirely on which state it is currently in. Instead of having one massive class with a bunch of `if/else` or `switch` statements checking `if state == X do this, else if state == Y do that`, you create separate classes for each state.

## Real-World Analogy

Think about your smartphone's **Screen Lock**.

When you press the Power button:
1. **If the phone is unlocked (State A):** Pressing the power button turns the screen off.
2. **If the phone is locked (State B):** Pressing the power button turns the screen on to show the lock screen.
3. **If the phone's battery is dead (State C):** Pressing the power button does nothing (or shows a low battery icon).

The "Power Button Press" is the same action, but the phone behaves completely differently depending on its current state.

Another classic example is a **Vending Machine**. Its behavior changes based on whether it has items, is out of stock, is waiting for money, or is currently dispensing an item.

## Why use the State Pattern?

1. **Avoids massive `if/else` blocks:** State-specific behavior is localized into separate state classes, making the main context class much cleaner.
2. **Single Responsibility Principle:** Each state class organizes the code related to a specific state in one place.
3. **Open/Closed Principle:** You can easily add new states without changing the existing state classes or the context class.

## Code Examples

Let's build a simple **Audio Player** that has three states: `Playing`, `Paused`, and `Stopped`. The way the "Play/Pause" button behaves changes based on the current state.

### Python Implementation

```python
from abc import ABC, abstractmethod

# --- State Interface ---
class State(ABC):
    def __init__(self, player):
        self.player = player

    @abstractmethod
    def press_play(self):
        pass

# --- Concrete States ---
class PlayingState(State):
    def press_play(self):
        print("Pausing the music.")
        self.player.set_state(PausedState(self.player))

class PausedState(State):
    def press_play(self):
        print("Resuming the music.")
        self.player.set_state(PlayingState(self.player))

class StoppedState(State):
    def press_play(self):
        print("Starting playback from the beginning.")
        self.player.set_state(PlayingState(self.player))

# --- Context Class ---
class AudioPlayer:
    def __init__(self):
        # Default state is Stopped
        self._state = StoppedState(self)

    def set_state(self, state: State):
        self._state = state

    def press_play_button(self):
        # The player delegates the action to its current state object
        self._state.press_play()

# --- Example Usage ---
if __name__ == '__main__':
    player = AudioPlayer()

    # Pressing play when stopped starts the music
    player.press_play_button()  # Expected: Starting playback from the beginning.
    
    # Pressing play when playing pauses the music
    player.press_play_button()  # Expected: Pausing the music.
    
    # Pressing play when paused resumes the music
    player.press_play_button()  # Expected: Resuming the music.
```

### Java Implementation

```java
// --- State Interface ---
interface State {
    void pressPlay();
}

// --- Concrete States ---
class PlayingState implements State {
    private AudioPlayer player;

    public PlayingState(AudioPlayer player) {
        this.player = player;
    }

    @Override
    public void pressPlay() {
        System.out.println("Pausing the music.");
        player.setState(new PausedState(player));
    }
}

class PausedState implements State {
    private AudioPlayer player;

    public PausedState(AudioPlayer player) {
        this.player = player;
    }

    @Override
    public void pressPlay() {
        System.out.println("Resuming the music.");
        player.setState(new PlayingState(player));
    }
}

class StoppedState implements State {
    private AudioPlayer player;

    public StoppedState(AudioPlayer player) {
        this.player = player;
    }

    @Override
    public void pressPlay() {
        System.out.println("Starting playback from the beginning.");
        player.setState(new PlayingState(player));
    }
}

// --- Context Class ---
class AudioPlayer {
    private State state;

    public AudioPlayer() {
        // Default state is Stopped
        this.state = new StoppedState(this);
    }

    public void setState(State state) {
        this.state = state;
    }

    public void pressPlayButton() {
        // The player delegates the action to its current state object
        state.pressPlay();
    }
}

// --- Example Usage ---
public class StatePatternDemo {
    public static void main(String[] args) {
        AudioPlayer player = new AudioPlayer();

        // Pressing play when stopped starts the music
        player.pressPlayButton();  // Expected: Starting playback from the beginning.
        
        // Pressing play when playing pauses the music
        player.pressPlayButton();  // Expected: Pausing the music.
        
        // Pressing play when paused resumes the music
        player.pressPlayButton();  // Expected: Resuming the music.
    }
}
```

## Summary

The State pattern is a powerful way to implement Finite State Machines (FSMs) in Object-Oriented Programming. By encapsulating state-specific logic into distinct classes, your main "Context" object (like the Audio Player or Vending Machine) stays clean and delegates all the complicated state transitions to the state classes themselves. This makes adding new states in the future incredibly easy.