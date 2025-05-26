# SwissTable

[English](README.md) | [简体中文](README.zh-CN.md)


[![Build Status](https://img.shields.io/github/actions/workflow/status/moonbit-community/SwissTable/ci.yml)](https://github.com/moonbit-community/SwissTable/actions)  [![codecov](https://codecov.io/gh/moonbit-community/SwissTable/branch/main/graph/badge.svg)](https://codecov.io/gh/moonbit-community/SwissTable)  


SwissTable is a high-performance hash table implementation based on open addressing, adopting the design principles of Google's SwissTable. It features efficient cache performance, low collision rates, and stable performance characteristics.

## 🚀 Key Features
• 🔍 **High Performance** - Uses open addressing + control byte array layout to increase cache hit rates  
• 🏹 **Robin Hood Insertion Strategy** - Reduces element clustering, improving insertion and lookup performance  
• 🔄 **Automatic Resizing** - Automatically resizes when the load factor exceeds a threshold (7/8), maintaining performance  
• 🧩 **Generic Support** - Supports any hashable type as a key  
• 🛠️ **Rich API** - Provides complete CRUD and traversal operations  

## 📥 Installation
```bash
moon add moonbit-community/SwissTable
```

## 🚀 Usage Guide

### 🔨 Creating a Hash Table
You can create hash tables using the `new()`, `new(capacity)`, `from_array()`, or `of()` methods.

```moonbit
// Create an empty hash table
let map : @SwissTable.T[String, Int] = @SwissTable.new()

// Specify initial capacity
let map_with_capacity = @SwissTable.new(capacity=32)

// Create from an array
let array_map = @SwissTable.from_array([("one", 1), ("two", 2)])

// Create from a fixed-size array
let fixed_map = @SwissTable.of([("one", 1), ("two", 2)])
```

### ➕ CRUD Operations
Use the `set()`, `get()`, `get_or_default()`, `contains()`, `remove()`, and `clear()` methods to manipulate the hash table.

```moonbit
// Insert or update a value
map.set("key", 42)

// Get a value (returns Option type)
let value = map.get("key")  // Some(42)
let none = map.get("nonexistent")  // None

// Get a value or default
let default_value = map.get_or_default("nonexistent", 0)  // 0

// Check if a key exists
let exists = map.contains("key")  // true

// Remove a key
map.remove("key")

// Clear the hash table
map.clear()

// Operator syntax
map["key"] = 42  // equivalent to map.set("key", 42)
let value = map["key"]  // equivalent to map.get("key")
```

### 🔀 Traversal Operations
Use the `each()`, `eachi()`, `iter()`, and `iter2()` methods for traversing the hash table.

```moonbit
// Basic traversal
map.each(fn(key, value) {
  println!("Key: {}, Value: {}", key, value)
})

// Traversal with index
map.eachi(fn(index, key, value) {
  println!("Index: {}, Key: {}, Value: {}", index, key, value)
})

// Using an iterator
for pair in map.iter() {
  let (key, value) = pair
  println!("Key: {}, Value: {}", key, value)
}

// Using iter2 iterator
let iter = map.iter2()
loop {
  match iter.next() {
    IterResult::Value(key, value) => {
      println!("Key: {}, Value: {}", key, value)
    }
    IterResult::End => break
  }
}
```

### 🔢 Utility Methods
Use the `size()`, `capacity()`, `is_empty()`, and `to_array()` methods to get information about the hash table.

```moonbit
// Get the hash table size
let size = map.size()

// Get the hash table capacity
let capacity = map.capacity()

// Check if the hash table is empty
let is_empty = map.is_empty()

// Convert to an array
let arr = map.to_array()
```

### 🔍 Equality and String Representation
Use the `equals()` and `show()` methods to compare hash tables and get their string representation.

```moonbit
// Compare two hash tables
let map1 = @SwissTable.from_array([("one", 1), ("two", 2)])
let map2 = @SwissTable.from_array([("two", 2), ("one", 1)])
let equal = map1.equals(map2)  // true, order doesn't matter

// Get string representation
let str = map1.show()  // e.g., "SwissTable{one: 1, two: 2}"
```

## 🚀 Advanced Usage Examples

### Example: Using SwissTable as a Frequency Counter

```moonbit
// Count word frequencies in a text
fn count_word_frequencies(text: String) -> @SwissTable.T[String, Int] {
  let words = text.split(" ")
  let frequency_map = @SwissTable.new()
  
  for word in words {
    let cleaned_word = word.trim().to_lower()
    if cleaned_word != "" {
      let count = frequency_map.get_or_default(cleaned_word, 0)
      frequency_map[cleaned_word] = count + 1
    }
  }
  
  frequency_map
}

// Usage example
let text = "to be or not to be that is the question"
let frequencies = count_word_frequencies(text)

// Print all frequencies
frequencies.each(fn(word, count) {
  println!("'{}' appears {} times", word, count)
})
```

### Example: Implementing a Simple Cache with SwissTable

```moonbit
struct Cache[K: Hash + Eq, V] {
  map: @SwissTable.T[K, V],
  max_size: Int
}

fn Cache::new[K: Hash + Eq, V](max_size: Int) -> Cache[K, V] {
  Cache { 
    map: @SwissTable.new(), 
    max_size 
  }
}

fn Cache::get(self, key: K) -> Option[V] {
  self.map.get(key)
}

fn Cache::put(self, key: K, value: V) -> Unit {
  // If we're at capacity and this is a new key, we could
  // implement an eviction policy here
  if self.map.size() >= self.max_size && !self.map.contains(key) {
    // For simplicity, we'll just prevent new additions
    return
  }
  
  self.map[key] = value
}

// Usage example
let cache = Cache::new[String, Int](5)
cache.put("key1", 100)
cache.put("key2", 200)

match cache.get("key1") {
  Some(value) => println!("Found: {}", value),
  None => println!("Not found")
}
```

## 📜 License
This project is licensed under the Apache-2.0 License. See LICENSE file for details.

## 📢 Contact & Support
• Moonbit Community: moonbit-community  
• GitHub Issues: [Report an Issue](https://github.com/moonbit-community/SwissTable/issues)

👋 If you like this project, give it a ⭐! Happy coding! 🚀 