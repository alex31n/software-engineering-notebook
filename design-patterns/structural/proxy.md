# Proxy Design Pattern

The Proxy design pattern is a structural pattern that provides a substitute or placeholder for another object. A proxy controls access to the original object, allowing you to perform something either before or after the request gets through to the original object.

Think of the word "proxy" itself. A proxy is someone who is authorized to act on behalf of someone else. In programming, a Proxy object acts on behalf of a Real object.

## Real-world Example

Imagine a **School Internet Network**. The school wants to provide internet access to students, but they also want to block access to certain inappropriate or distracting websites (like social media or gaming sites during class).

Instead of letting the students' computers connect directly to the raw, unfiltered internet, the school forces all traffic to go through a **Proxy Server**. 

- The **Real Object** is the actual Internet.
- The **Proxy Object** is the School's Proxy Server.

When a student types a website address, the request goes to the Proxy first. The Proxy checks its list of banned sites. If the site is allowed, the Proxy passes the request to the Real Internet and returns the webpage. If the site is banned, the Proxy blocks the request and shows an "Access Denied" message.

## When to use it?

There are several common reasons to use a Proxy, which lead to different "types" of proxies:

1.  **Protection Proxy (Access Control):** Like the school internet example. You use a proxy to check if the user has the right permissions to access the real object.
2.  **Virtual Proxy (Lazy Initialization):** If you have an object that is very heavy and takes a long time to create (like a huge high-resolution image), you don't want to load it until it's actually needed on the screen. The proxy acts as a lightweight placeholder until the real image needs to be rendered.
3.  **Remote Proxy:** When the real object is located on a different server or in a different location (like in a network or cloud). The proxy handles the complex network communication, making it look like a local object to your program.
4.  **Caching Proxy:** The proxy stores the results of expensive operations. If the same request comes in again, the proxy returns the saved result instead of asking the real object to do the hard work all over again.

---

## Code Implementation

Let's implement the **School Internet Filter (Protection Proxy)** example.

We will create an interface called `Internet`. Both our `RealInternet` and our `ProxyInternet` will follow this interface so that the user (client) doesn't know the difference.

### Python Example

```python
from abc import ABC, abstractmethod

# --- Interface ---
# Both the Real Subject and the Proxy must implement this interface.
class Internet(ABC):
    @abstractmethod
    def connect_to(self, server_host: str):
        pass

# --- Real Subject ---
# This is the actual internet that can connect to anything.
class RealInternet(Internet):
    def connect_to(self, server_host: str):
        print(f"Connecting to {server_host}")

# --- Proxy ---
# This proxy checks if the site is banned before allowing the connection.
class ProxyInternet(Internet):
    def __init__(self):
        # We hold a reference to the Real Subject
        self.real_internet = RealInternet()
        self.banned_sites = [
            "facebook.com",
            "instagram.com",
            "gaming.com"
        ]

    def connect_to(self, server_host: str):
        # The Proxy controls access
        if server_host.lower() in self.banned_sites:
            print(f"Access Denied: Cannot connect to {server_host}. Site is banned by school policy.")
        else:
            # If it's allowed, pass the request to the Real Subject
            self.real_internet.connect_to(server_host)

# --- Client Code ---
if __name__ == "__main__":
    # The student uses the internet (which is actually the Proxy)
    internet_access = ProxyInternet()

    print("Student tries to visit Wikipedia:")
    internet_access.connect_to("wikipedia.org")
    
    print("\nStudent tries to visit Facebook:")
    internet_access.connect_to("facebook.com")
```

### Java Example

```java
import java.util.ArrayList;
import java.util.List;

// --- Interface ---
// Both the Real Subject and the Proxy must implement this interface.
interface Internet {
    void connectTo(String serverHost);
}

// --- Real Subject ---
// This is the actual internet that can connect to anything.
class RealInternet implements Internet {
    @Override
    public void connectTo(String serverHost) {
        System.out.println("Connecting to " + serverHost);
    }
}

// --- Proxy ---
// This proxy checks if the site is banned before allowing the connection.
class ProxyInternet implements Internet {
    private RealInternet realInternet = new RealInternet();
    private static List<String> bannedSites;

    static {
        bannedSites = new ArrayList<>();
        bannedSites.add("facebook.com");
        bannedSites.add("instagram.com");
        bannedSites.add("gaming.com");
    }

    @Override
    public void connectTo(String serverHost) {
        // The Proxy controls access
        if (bannedSites.contains(serverHost.toLowerCase())) {
            System.out.println("Access Denied: Cannot connect to " + serverHost + ". Site is banned by school policy.");
        } else {
            // If it's allowed, pass the request to the Real Subject
            realInternet.connectTo(serverHost);
        }
    }
}

// --- Client Code ---
public class ProxyDemo {
    public static void main(String[] args) {
        // The student uses the internet (which is actually the Proxy)
        Internet internetAccess = new ProxyInternet();

        System.out.println("Student tries to visit Wikipedia:");
        internetAccess.connectTo("wikipedia.org");
        
        System.out.println("\nStudent tries to visit Facebook:");
        internetAccess.connectTo("facebook.com");
    }
}
```

---

## Advantages of the Proxy Pattern

1.  **Security and Control (Protection Proxy):** You can control who has access to the real object.
2.  **Performance (Virtual and Caching Proxy):** You can delay creating heavy objects until they are absolutely needed, or cache results to speed up repeated requests.
3.  **Separation of Concerns:** The proxy handles the "housekeeping" tasks (like access control or caching), allowing the Real Object to focus solely on its main job.
4.  **Open/Closed Principle:** You can introduce new proxies without changing the client code or the Real Object.

## Disadvantages

1.  **Increased Complexity:** It adds more classes and layers to your application.
2.  **Potential Delay:** The proxy adds an extra step. In some performance-critical applications, this slight delay might be noticeable.

## Summary

The Proxy pattern is like having a bouncer at the door of an exclusive club. You (the client) want to get into the club (the Real Object). However, you have to talk to the bouncer (the Proxy) first. The bouncer checks your ID, and only if you meet the requirements, they let you inside. It's an excellent way to add control, security, or performance optimizations to an existing object without altering its core code.