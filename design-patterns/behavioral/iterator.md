# Iterator Design Pattern

The **Iterator Pattern** is a behavioral design pattern. It gives you a way to access the elements of a collection (like a list, a stack, or a tree) one by one, without needing to know how that collection is built underneath.

Imagine you have a box of items. Instead of reaching in and grabbing items randomly, or needing to know exactly how the items are arranged inside the box, the Iterator gives you a simple mechanism: "Give me the first item", "Give me the next item", and "Are there any items left?".

## A Simple Real-World Analogy

Imagine you are listening to a **Music Playlist** on your phone.
- The playlist might be stored in a complex way: maybe a mix of downloaded files, cloud links, and cached streams.
- As a listener, you don't care how or where they are stored. You just want to listen to the songs one after another.
- The music player provides an **Iterator** (the "Next" button). You just keep pressing "Next" to get the next song, until the playlist is finished.

The "Next" button hides the complexity of where the songs are actually coming from. It just gives you the next one.

## Why Use the Iterator Pattern?

- **Hides Complexity:** The code using the collection doesn't need to understand the complex data structures (like arrays, trees, or graphs) to go through them. It just uses a standard interface.
- **Multiple Traversals:** You can have multiple iterators going through the same collection at the same time, each keeping its own position without interfering with the others.
- **Standardized Access:** It provides a uniform way to loop through completely different types of collections.

## The 4 Main Components

1. **Iterator Interface:** Declares the operations required for traversing a collection: usually `hasNext()` (are there more elements?) and `next()` (get the current element and move to the next).
2. **Concrete Iterator:** Implements the Iterator interface and keeps track of the current position during the traversal.
3. **Aggregate (Iterable) Interface:** Declares a method for creating an iterator object (e.g., `createIterator()`).
4. **Concrete Aggregate:** Implements the Aggregate interface and returns an instance of the proper Concrete Iterator for its specific data structure.

---

## Code Examples

Let's build the **Music Playlist** example. We have a `Playlist` (Aggregate) that holds `Song` objects. We will create an `Iterator` to go through the songs one by one.

### Python Implementation

In Python, the Iterator pattern is so common that it's built right into the language using "magic methods" (`__iter__` and `__next__`). However, to clearly show how the classic pattern works, we will build it explicitly first using normal methods.

```python
from abc import ABC, abstractmethod

# 1. Iterator Interface
class SongIterator(ABC):
    @abstractmethod
    def has_next(self) -> bool:
        pass

    @abstractmethod
    def next(self):
        pass

# 2. Aggregate Interface
class SongCollection(ABC):
    @abstractmethod
    def create_iterator(self) -> SongIterator:
        pass

# --- Concrete Implementations ---

class Song:
    def __init__(self, title: str, artist: str):
        self.title = title
        self.artist = artist

    def __str__(self):
        return f"'{self.title}' by {self.artist}"

# 3. Concrete Iterator
class PlaylistIterator(SongIterator):
    def __init__(self, songs: list):
        self._songs = songs
        self._position = 0

    def has_next(self) -> bool:
        return self._position < len(self._songs)

    def next(self) -> Song:
        if self.has_next():
            song = self._songs[self._position]
            self._position += 1
            return song
        return None

# 4. Concrete Aggregate
class Playlist(SongCollection):
    def __init__(self):
        self._songs = []

    def add_song(self, song: Song):
        self._songs.append(song)

    def create_iterator(self) -> SongIterator:
        return PlaylistIterator(self._songs)

# Client Code
if __name__ == "__main__":
    my_playlist = Playlist()
    my_playlist.add_song(Song("Shape of You", "Ed Sheeran"))
    my_playlist.add_song(Song("Blinding Lights", "The Weeknd"))
    my_playlist.add_song(Song("Levitating", "Dua Lipa"))

    print("Playing songs in the playlist:")
    
    # We ask the collection to give us an iterator
    iterator = my_playlist.create_iterator()
    
    # We use the iterator to go through the collection
    while iterator.has_next():
        song = iterator.next()
        print(f"Now playing: {song}")
```

*Note: In modern Python, you would typically implement the `__iter__()` and `__next__()` methods so you could simply use a `for song in my_playlist:` loop. But the core concept of separating the "traversal logic" into an iterator object remains exactly the same!*

### Java Implementation

Java has built-in `Iterator` and `Iterable` interfaces in `java.util`, which are perfect for implementing this pattern in a standard way.

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

// Simple Song class
class Song {
    private String title;
    private String artist;

    public Song(String title, String artist) {
        this.title = title;
        this.artist = artist;
    }

    @Override
    public String toString() {
        return "'" + title + "' by " + artist;
    }
}

// 4. Concrete Aggregate 
// We implement Java's built-in Iterable interface so we can use "for-each" loops later.
class Playlist implements Iterable<Song> {
    private List<Song> songs;

    public Playlist() {
        this.songs = new ArrayList<>();
    }

    public void addSong(Song song) {
        songs.add(song);
    }

    // This is the "createIterator" method from the pattern
    @Override
    public Iterator<Song> iterator() {
        return new PlaylistIterator(songs);
    }

    // 3. Concrete Iterator 
    // We implement Java's built-in Iterator interface as an inner class.
    private class PlaylistIterator implements Iterator<Song> {
        private List<Song> playlistSongs;
        private int position = 0;

        public PlaylistIterator(List<Song> songs) {
            this.playlistSongs = songs;
        }

        @Override
        public boolean hasNext() {
            return position < playlistSongs.size();
        }

        @Override
        public Song next() {
            if (this.hasNext()) {
                Song song = playlistSongs.get(position);
                position++;
                return song;
            }
            return null;
        }
    }
}

// Client (The Main Program)
public class Main {
    public static void main(String[] args) {
        Playlist myPlaylist = new Playlist();
        myPlaylist.addSong(new Song("Shape of You", "Ed Sheeran"));
        myPlaylist.addSong(new Song("Blinding Lights", "The Weeknd"));
        myPlaylist.addSong(new Song("Levitating", "Dua Lipa"));

        System.out.println("Playing songs using Iterator explicitly:");
        // Using the Iterator explicitly like the classic pattern
        Iterator<Song> iterator = myPlaylist.iterator();
        while (iterator.hasNext()) {
            Song song = iterator.next();
            System.out.println("Now playing: " + song);
        }

        System.out.println("\nPlaying songs using enhanced for-loop:");
        // Because Playlist implements Iterable, Java automatically uses the Iterator under the hood!
        for (Song song : myPlaylist) {
            System.out.println("Now playing: " + song);
        }
    }
}
```

## Summary

The Iterator Pattern is a powerful way to safely go through the elements of a collection. By moving the logic of "how to loop" out of the collection itself and into a separate **Iterator** object, your collections can stay focused simply on storing data. Meanwhile, your client code can loop through anything—whether it's a list, a complex tree, or a graph—using the exact same simple rules (`hasNext` and `next`). It is so universally useful that most modern programming languages have built it directly into their core syntax!
