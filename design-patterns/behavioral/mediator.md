# Mediator Design Pattern

The **Mediator** is a behavioral design pattern that reduces chaotic dependencies between objects. The pattern restricts direct communications between the objects and forces them to collaborate only via a mediator object.

Think of it as a central hub for communication. Instead of objects talking directly to each other (which can lead to a tangled web of dependencies, often called "spaghetti code"), they all talk to the mediator, and the mediator handles routing the messages to the correct destination.

## Real-World Analogy

Imagine an **Air Traffic Control (ATC) Tower** at a busy airport.

Planes approaching the airport do not communicate directly with each other to decide who lands first. If they did, it would be chaotic and dangerous. Instead, all planes communicate with the ATC tower. The tower acts as a **mediator**. It knows the status of all planes and runways, and it coordinates the landing and takeoff schedules, telling each plane what to do and when.

Another simple analogy is a **Chat Room**. Users don't send messages directly to every other user's IP address. They send a message to the Chat Room server (the mediator), and the server broadcasts that message to all other connected users.

## Why use the Mediator Pattern?

1. **Reduces Coupling:** Objects don't need to know about each other, only about the mediator. This makes the system easier to understand and maintain.
2. **Centralized Control:** Complex communication logic is extracted into a single place (the mediator class), making it easier to change or update.
3. **Reusability:** Individual components (colleagues) are easier to reuse in other programs because they aren't tightly coupled to other specific components.

## Code Examples

Let's implement a simple Chat Room example where users can send messages to each other through a central Chat Room mediator.

### Python Implementation

```python
from abc import ABC, abstractmethod

# Mediator Interface
class ChatMediator(ABC):
    @abstractmethod
    def send_message(self, message: str, sender: 'User'):
        pass

    @abstractmethod
    def add_user(self, user: 'User'):
        pass

# Concrete Mediator
class ChatRoom(ChatMediator):
    def __init__(self):
        self.users = []

    def add_user(self, user: 'User'):
        self.users.append(user)

    def send_message(self, message: str, sender: 'User'):
        for user in self.users:
            # Don't send the message back to the sender
            if user != sender:
                user.receive(message)

# Colleague Component
class User(ABC):
    def __init__(self, mediator: ChatMediator, name: str):
        self.mediator = mediator
        self.name = name

    @abstractmethod
    def send(self, message: str):
        pass

    @abstractmethod
    def receive(self, message: str):
        pass

# Concrete Colleague
class ChatUser(User):
    def send(self, message: str):
        print(f"[{self.name} sends]: {message}")
        self.mediator.send_message(message, self)

    def receive(self, message: str):
        print(f"[{self.name} receives]: {message}")

# --- Example Usage ---
if __name__ == '__main__':
    # Create the mediator
    chat_room = ChatRoom()

    # Create users
    john = ChatUser(chat_room, "John")
    jane = ChatUser(chat_room, "Jane")
    bob = ChatUser(chat_room, "Bob")

    # Register users to the chat room
    chat_room.add_user(john)
    chat_room.add_user(jane)
    chat_room.add_user(bob)

    # Users communicate through the mediator
    john.send("Hi everyone!")
    print("-" * 20)
    jane.send("Hey John!")
```

### Java Implementation

```java
import java.util.ArrayList;
import java.util.List;

// Mediator Interface
interface ChatMediator {
    void sendMessage(String msg, User user);
    void addUser(User user);
}

// Concrete Mediator
class ChatRoom implements ChatMediator {
    private List<User> users;

    public ChatRoom() {
        this.users = new ArrayList<>();
    }

    @Override
    public void addUser(User user) {
        this.users.add(user);
    }

    @Override
    public void sendMessage(String msg, User sender) {
        for (User user : users) {
            // message should not be received by the user sending it
            if (user != sender) {
                user.receive(msg);
            }
        }
    }
}

// Colleague Abstract Class
abstract class User {
    protected ChatMediator mediator;
    protected String name;

    public User(ChatMediator med, String name) {
        this.mediator = med;
        this.name = name;
    }

    public abstract void send(String msg);
    public abstract void receive(String msg);
}

// Concrete Colleague
class ChatUser extends User {
    public ChatUser(ChatMediator med, String name) {
        super(med, name);
    }

    @Override
    public void send(String msg) {
        System.out.println("[" + this.name + " sends]: " + msg);
        mediator.sendMessage(msg, this);
    }

    @Override
    public void receive(String msg) {
        System.out.println("[" + this.name + " receives]: " + msg);
    }
}

// --- Example Usage ---
public class MediatorPatternDemo {
    public static void main(String[] args) {
        // Create the mediator
        ChatMediator mediator = new ChatRoom();
        
        // Create users
        User john = new ChatUser(mediator, "John");
        User jane = new ChatUser(mediator, "Jane");
        User bob = new ChatUser(mediator, "Bob");
        
        // Register users to the chat room
        mediator.addUser(john);
        mediator.addUser(jane);
        mediator.addUser(bob);
        
        // Users communicate through the mediator
        john.send("Hi everyone!");
        System.out.println("--------------------");
        jane.send("Hey John!");
    }
}
```

## Summary

The Mediator pattern is excellent for decoupling components that interact heavily. Instead of every component holding references to many other components, they only hold one reference to the Mediator. This transforms many-to-many relationships into one-to-many relationships, making your system much easier to maintain and extend.