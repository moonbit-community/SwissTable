# SwissTable

[English](README.md) | [简体中文](README.zh-CN.md)


[![构建状态](https://img.shields.io/github/actions/workflow/status/moonbit-community/SwissTable/ci.yml)](https://github.com/moonbit-community/SwissTable/actions)  [![代码覆盖率](https://codecov.io/gh/moonbit-community/SwissTable/branch/main/graph/badge.svg)](https://codecov.io/gh/moonbit-community/SwissTable)  


SwissTable 是一个基于开放地址法的高性能哈希表实现，采用了谷歌的 SwissTable 设计原理，具有高效的缓存性能、低冲突率和稳定的性能特性。

## 🚀 主要特性
• 🔍 **高性能** - 采用开放地址法 + 控制字节数组布局，提高缓存命中率  
• 🏹 **Robin Hood 插入策略** - 降低元素聚集，提高插入和查找性能  
• 🔄 **自动扩容** - 当负载因子超过阈值(7/8)时自动扩容，保持性能  
• 🧩 **泛型支持** - 支持任意可哈希类型作为键  
• 🛠️ **丰富的 API** - 提供完整的增删改查和遍历操作  

## 📥 安装
```bash
moon add moonbit-community/SwissTable
```

## 🚀 使用指南

### 🔨 创建哈希表
你可以使用 `new()`、`new(capacity)`、`from_array()` 或 `of()` 方法创建哈希表。

```moonbit
// 创建空哈希表
let map : @SwissTable.T[String, Int] = @SwissTable.new()

// 指定初始容量
let map_with_capacity = @SwissTable.new(capacity=32)

// 从数组创建
let array_map = @SwissTable.from_array([("one", 1), ("two", 2)])

// 从固定大小数组创建
let fixed_map = @SwissTable.of([("one", 1), ("two", 2)])
```

### ➕ 增删改查操作
使用 `set()`、`get()`、`get_or_default()`、`contains()`、`remove()` 和 `clear()` 方法操作哈希表。

```moonbit
// 插入或更新值
map.set("key", 42)

// 获取值（返回 Option 类型）
let value = map.get("key")  // Some(42)
let none = map.get("nonexistent")  // None

// 获取值或默认值
let default_value = map.get_or_default("nonexistent", 0)  // 0

// 检查键是否存在
let exists = map.contains("key")  // true

// 删除键
map.remove("key")

// 清空哈希表
map.clear()

// 操作符语法
map["key"] = 42  // 相当于 map.set("key", 42)
let value = map["key"]  // 相当于 map.get("key")
```

### 🔀 遍历操作
使用 `each()`、`eachi()`、`iter()` 和 `iter2()` 方法遍历哈希表。

```moonbit
// 基本遍历
map.each(fn(key, value) {
  println!("键: {}, 值: {}", key, value)
})

// 带索引遍历
map.eachi(fn(index, key, value) {
  println!("索引: {}, 键: {}, 值: {}", index, key, value)
})

// 使用迭代器
for pair in map.iter() {
  let (key, value) = pair
  println!("键: {}, 值: {}", key, value)
}

// 使用 iter2 迭代器
let iter = map.iter2()
loop {
  match iter.next() {
    IterResult::Value(key, value) => {
      println!("键: {}, 值: {}", key, value)
    }
    IterResult::End => break
  }
}
```

### 🔢 工具方法
使用 `size()`、`capacity()`、`is_empty()` 和 `to_array()` 方法获取哈希表信息。

```moonbit
// 获取哈希表大小
let size = map.size()

// 获取哈希表容量
let capacity = map.capacity()

// 检查哈希表是否为空
let is_empty = map.is_empty()

// 转换为数组
let arr = map.to_array()
```

### 🔍 相等性和字符串表示
使用 `equals()` 和 `show()` 方法比较哈希表和获取其字符串表示。

```moonbit
// 比较两个哈希表
let map1 = @SwissTable.from_array([("one", 1), ("two", 2)])
let map2 = @SwissTable.from_array([("two", 2), ("one", 1)])
let equal = map1.equals(map2)  // true，顺序无关紧要

// 获取字符串表示
let str = map1.show()  // 例如: "SwissTable{one: 1, two: 2}"
```

## 🚀 高级使用示例

### 示例：使用 SwissTable 作为频率计数器

```moonbit
// 统计文本中的词频
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

// 使用示例
let text = "to be or not to be that is the question"
let frequencies = count_word_frequencies(text)

// 打印所有频率
frequencies.each(fn(word, count) {
  println!("'{}' 出现了 {} 次", word, count)
})
```

### 示例：使用 SwissTable 实现简单缓存

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
  // 如果我们达到容量并且这是一个新键，我们可以
  // 在这里实现一个淘汰策略
  if self.map.size() >= self.max_size && !self.map.contains(key) {
    // 为简单起见，我们只是阻止新增加
    return
  }
  
  self.map[key] = value
}

// 使用示例
let cache = Cache::new[String, Int](5)
cache.put("key1", 100)
cache.put("key2", 200)

match cache.get("key1") {
  Some(value) => println!("找到: {}", value),
  None => println!("未找到")
}
```

## 📜 许可证
本项目采用 Apache-2.0 许可证。详情请参阅 LICENSE 文件。

## 📢 联系与支持
• Moonbit 社区：moonbit-community  
• GitHub 问题：[报告问题](https://github.com/moonbit-community/SwissTable/issues)

👋 如果你喜欢这个项目，请给它一个 ⭐！祝编码愉快！🚀 