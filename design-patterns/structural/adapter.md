# Adapter Design Pattern

The **Adapter Design Pattern** is a structural design pattern that allows objects with incompatible interfaces to collaborate. 

In simple terms: It is a pattern that acts as a wrapper between two objects, catching calls to one object and transforming them into a format and interface recognizable by the second object.

## Analogy

Imagine you are traveling to a different country, let's say from the US to Europe. The power outlets in Europe have a different shape than the plugs on your US electronic devices. You can't plug your laptop directly into the wall! 

What do you do? You use a **power adapter**. The adapter has a plug that fits into the European wall socket, and it has a socket that accepts your US laptop plug. The adapter acts as a bridge between two incompatible interfaces.

In software engineering, the Adapter pattern does the exact same thing. It lets two incompatible pieces of software talk to each other by placing an adapter in the middle.

## The Problem

Let's say you have an existing software system that works perfectly with data in XML format. Later, you want to integrate a shiny new 3rd-party analytics library. But there's a catch: the new library only understands data in JSON format.

Your existing system speaks XML, but the new library speaks JSON. They are incompatible. You don't want to change your entire existing system just to fit the new library, and you can't change the 3rd-party library because you don't own the code.

## The Solution

You create an **Adapter**. 

The Adapter is a special object that acts as a translator. You wrap the 3rd-party library with an adapter class. Your existing system will talk to the adapter using XML. The adapter will take that XML, translate it into JSON behind the scenes, and pass it to the 3rd-party library. 

When the library responds in JSON, the adapter translates it back to XML before sending it back to your system.

## Key Components

1. **Target**: The existing interface that your system expects and uses.
2. **Adaptee**: The existing, incompatible class or 3rd-party library that needs to be adapted.
3. **Adapter**: The class that implements the Target interface and wraps the Adaptee, providing the translation layer.
4. **Client**: The existing code in your system that interacts with the Target.

---

## Code Examples

### Python Implementation

Let's look at a simple example based on the XML to JSON problem we discussed above. We have our existing system that processes XML, and we want to use a new analytics library that only accepts JSON.

```python
import json

# 1. Target: The interface our existing system expects (XML)
class XMLSystem:
    def process_data(self, xml_data):
        print(f"System: Processing XML data: {xml_data}")

# 2. Adaptee: The new 3rd-party library we want to use (understands JSON)
class JSONAnalyticsLibrary:
    def analyze_json_data(self, json_data):
        # Simulating some analysis
        print(f"Library: Analyzing JSON data: {json_data}")

# 3. Adapter: Translates XML to JSON so the system can use the new library
class XMLToJSONAdapter:
    def __init__(self, analytics_library):
        self.analytics_library = analytics_library

    def process_data(self, xml_data):
        print("Adapter: Translating XML to JSON...")
        
        # Simulating the translation process
        # (In reality, we would parse XML and convert it to a JSON string)
        content = xml_data.replace("<tag>", "").replace("</tag>", "")
        json_data = f'{{"data": "{content}"}}'
        
        # Passing the translated data to the incompatible library
        self.analytics_library.analyze_json_data(json_data)

# 4. Client Code
if __name__ == "__main__":
    # Our existing system data
    xml_data = "<tag>Hello World</tag>"
    
    # Using the old system (works fine)
    print("--- Using Old System ---")
    old_system = XMLSystem()
    old_system.process_data(xml_data)
    
    # Trying to use the new library directly WOULD FAIL
    # new_library = JSONAnalyticsLibrary()
    # new_library.process_data(xml_data)  <-- AttributeError!
    
    # Using the new library via the Adapter
    print("\n--- Using New Library via Adapter ---")
    new_library = JSONAnalyticsLibrary()
    adapter = XMLToJSONAdapter(new_library)
    
    # The client uses the adapter exactly like it used the old XML system
    adapter.process_data(xml_data)
```

### Java Implementation

In this example, we have a basic `MediaPlayer` interface that can play MP3 files. We want it to be able to play VLC and MP4 files as well, which are supported by an `AdvancedMediaPlayer`. We use a `MediaAdapter` to make them compatible.

```java
// 1. Target Interface: What the client expects
interface MediaPlayer {
    void play(String audioType, String fileName);
}

// Target Implementation
class AudioPlayer implements MediaPlayer {
    MediaAdapter mediaAdapter;

    @Override
    public void play(String audioType, String fileName) {
        // Inbuilt support to play mp3 music files
        if(audioType.equalsIgnoreCase("mp3")){
            System.out.println("Playing mp3 file. Name: " + fileName);
        } 
        // mediaAdapter is providing support to play other file formats
        else if(audioType.equalsIgnoreCase("vlc") || audioType.equalsIgnoreCase("mp4")){
            mediaAdapter = new MediaAdapter(audioType);
            mediaAdapter.play(audioType, fileName);
        } else {
            System.out.println("Invalid media. " + audioType + " format not supported");
        }
    }
}

// 2. Adaptee: The incompatible classes
interface AdvancedMediaPlayer {
    void playVlc(String fileName);
    void playMp4(String fileName);
}

class VlcPlayer implements AdvancedMediaPlayer {
    @Override
    public void playVlc(String fileName) {
        System.out.println("Playing vlc file. Name: " + fileName);
    }

    @Override
    public void playMp4(String fileName) {
        // Do nothing
    }
}

class Mp4Player implements AdvancedMediaPlayer {
    @Override
    public void playVlc(String fileName) {
        // Do nothing
    }

    @Override
    public void playMp4(String fileName) {
        System.out.println("Playing mp4 file. Name: " + fileName);
    }
}

// 3. Adapter: Makes AdvancedMediaPlayer compatible with MediaPlayer
class MediaAdapter implements MediaPlayer {
    AdvancedMediaPlayer advancedMusicPlayer;

    public MediaAdapter(String audioType){
        if(audioType.equalsIgnoreCase("vlc") ){
            advancedMusicPlayer = new VlcPlayer();
        } else if (audioType.equalsIgnoreCase("mp4")){
            advancedMusicPlayer = new Mp4Player();
        }
    }

    @Override
    public void play(String audioType, String fileName) {
        if(audioType.equalsIgnoreCase("vlc")){
            advancedMusicPlayer.playVlc(fileName);
        } else if(audioType.equalsIgnoreCase("mp4")){
            advancedMusicPlayer.playMp4(fileName);
        }
    }
}

// 4. Client Code
public class AdapterPatternDemo {
    public static void main(String[] args) {
        AudioPlayer audioPlayer = new AudioPlayer();

        audioPlayer.play("mp3", "beyond_the_horizon.mp3");
        audioPlayer.play("mp4", "alone.mp4");
        audioPlayer.play("vlc", "far_far_away.vlc");
        audioPlayer.play("avi", "mind_me.avi");
    }
}
```

---

## When to use the Adapter Pattern?

* **Incompatible Interfaces**: When you want to use an existing class, but its interface does not match the one you need.
* **Reusable Classes**: When you are creating a reusable class that cooperates with unrelated or unforeseen classes, that is, classes that don't necessarily have compatible interfaces.
* **Legacy Code / 3rd Party Integrations**: When you need to integrate a 3rd-party library or legacy code into your modern system without breaking your existing architecture.

## Pros and Cons

### Pros
* **Single Responsibility Principle**: You separate the interface or data conversion code from the primary business logic of the program.
* **Open/Closed Principle**: You can introduce new types of adapters into the program without breaking the existing client code, as long as they work with the adapters through the client interface.
* **Reusability**: It allows you to reuse existing, already tested code (the Adaptee) even if its interface is incompatible.

### Cons
* **Complexity**: The overall complexity of the code increases because you need to introduce a set of new interfaces and classes. Sometimes it's simpler to just change the service class so that it matches the rest of your code (if you own the source code and can modify it).