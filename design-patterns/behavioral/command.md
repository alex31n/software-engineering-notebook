# Command Design Pattern

The **Command Pattern** is a behavioral design pattern. It turns a request or an action into a stand-alone object containing all the information about the request. 

Think of it as wrapping a task up into a neat little box. Once the task is in the box, you can pass it around, store it, or execute it whenever you want.

## A Simple Real-World Analogy

Imagine you are at a restaurant:
1. **You (Client):** You look at the menu and tell the waiter what you want.
2. **The Waiter (Invoker):** The waiter writes your order on a piece of paper. The waiter doesn't cook the food; they just take the paper to the kitchen.
3. **The Order (Command):** The piece of paper has all the details of what needs to be cooked.
4. **The Chef (Receiver):** The chef looks at the paper and knows exactly how to cook the meal.

In this scenario, you don't talk directly to the chef. The waiter takes your "Command" (the written order) and passes it to the chef, who actually does the work.

## Why Use the Command Pattern?

- **Decoupling:** The object that *requests* an action doesn't need to know anything about the object that *performs* the action. (The waiter doesn't need to know how to cook).
- **Delayed Execution:** You can store commands in a list or queue and execute them later.
- **Undo/Redo:** Since a command is an object, you can keep a history of commands. If you want to undo an action, you just look at the last command and reverse it.

## The 4 Main Components

1. **Command Interface:** A common interface for all commands, usually containing a single method like `execute()`.
2. **Concrete Command:** Implements the Command interface. It connects an action with the Receiver.
3. **Receiver:** The object that actually performs the real work (like the Chef).
4. **Invoker:** The object that triggers the command to execute (like the Waiter or a Remote Control).

---

## Code Examples

Let's look at a classic example: building a **Smart Home Remote Control**. The remote (Invoker) can turn on a light (Receiver), but it doesn't know *how* the light works. It just presses a button that executes a Command.

### Python Implementation

```python
from abc import ABC, abstractmethod

# 1. Receiver
# This is the object that actually knows how to do the work.
class Light:
    def turn_on(self):
        print("Light is ON")

    def turn_off(self):
        print("Light is OFF")

# 2. Command Interface
# All commands must follow this rule: they need an execute method.
class Command(ABC):
    @abstractmethod
    def execute(self):
        pass

# 3. Concrete Commands
# These wrap the receiver and the action into a single object.
class TurnOnLightCommand(Command):
    def __init__(self, light: Light):
        self.light = light

    def execute(self):
        self.light.turn_on()

class TurnOffLightCommand(Command):
    def __init__(self, light: Light):
        self.light = light

    def execute(self):
        self.light.turn_off()

# 4. Invoker
# This is the Remote Control. It doesn't know anything about the light.
# It only knows how to trigger a command.
class RemoteControl:
    def __init__(self):
        self.command = None

    def set_command(self, command: Command):
        self.command = command

    def press_button(self):
        if self.command:
            self.command.execute()

# Client (The Main Program)
if __name__ == "__main__":
    # Create our receiver (The Light)
    living_room_light = Light()

    # Create our commands (The Orders)
    turn_on = TurnOnLightCommand(living_room_light)
    turn_off = TurnOffLightCommand(living_room_light)

    # Create our invoker (The Remote Control)
    remote = RemoteControl()

    # Turn the light on
    print("Pressing the ON button...")
    remote.set_command(turn_on)
    remote.press_button()

    # Turn the light off
    print("Pressing the OFF button...")
    remote.set_command(turn_off)
    remote.press_button()
```

### Java Implementation

```java
// 1. Receiver
// This is the object that actually knows how to do the work.
class Light {
    public void turnOn() {
        System.out.println("Light is ON");
    }

    public void turnOff() {
        System.out.println("Light is OFF");
    }
}

// 2. Command Interface
// All commands must implement this interface.
interface Command {
    void execute();
}

// 3. Concrete Commands
// These classes wrap the receiver and the specific action.
class TurnOnLightCommand implements Command {
    private Light light;

    public TurnOnLightCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.turnOn();
    }
}

class TurnOffLightCommand implements Command {
    private Light light;

    public TurnOffLightCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.turnOff();
    }
}

// 4. Invoker
// This is the Remote Control. It just knows how to hold and execute a command.
class RemoteControl {
    private Command command;

    public void setCommand(Command command) {
        this.command = command;
    }

    public void pressButton() {
        if (command != null) {
            command.execute();
        }
    }
}

// Client (The Main Program)
public class Main {
    public static void main(String[] args) {
        // Create our receiver (The Light)
        Light livingRoomLight = new Light();

        // Create our commands (The Orders)
        Command turnOn = new TurnOnLightCommand(livingRoomLight);
        Command turnOff = new TurnOffLightCommand(livingRoomLight);

        // Create our invoker (The Remote Control)
        RemoteControl remote = new RemoteControl();

        // Turn the light on
        System.out.println("Pressing the ON button...");
        remote.setCommand(turnOn);
        remote.pressButton();

        // Turn the light off
        System.out.println("Pressing the OFF button...");
        remote.setCommand(turnOff);
        remote.pressButton();
    }
}
```

## Summary

The Command Pattern is excellent when you need to separate the object that asks for something to be done from the object that actually does the work. By wrapping requests into command objects, you open up the possibilities of creating queues, keeping logs of operations, or creating an undo system in your software!
