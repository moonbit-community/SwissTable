# SwissTable - High-Performance Hash Table Implementation

*[English](README.md) | [中文](README.zh-CN.md)*

SwissTable is a high-performance hash table implementation based on open addressing, adopting the design principles of Google's SwissTable. It features efficient cache performance, low collision rates, and stable performance characteristics.

## Key Features

- **High Performance**: Uses open addressing + control byte array layout to increase cache hit rates
- **Robin Hood Insertion Strategy**: Reduces element clustering, improving insertion and lookup performance
- **Automatic Resizing**: Automatically resizes when the load factor exceeds a threshold (7/8), maintaining performance
- **Generic Support**: Supports any hashable type as a key
- **Rich API**: Provides complete CRUD and traversal operations

## Basic Usage

### Creating a Hash Table

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

### CRUD Operations

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

### Traversal Operations

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

### Other Operations

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

## Performance Considerations

- SwissTable uses a load factor of 7/8, providing a good balance of space efficiency and performance in most cases
- Robin Hood insertion strategy reduces long probe sequences, especially at high load factors
- Using a separate control byte array to store metadata improves cache locality

## Suitable Scenarios

- Scenarios requiring high-performance hash tables
- Core data structures for compilers and language runtimes
- Performance-sensitive applications
- Scenarios requiring frequent lookup and insertion operations

## Contributions

Issues and suggestions for improvements are welcome. 