# SwissTable - 高性能哈希表实现

*注意：更完整的文档请参阅项目根目录的 [README.md (English)](../README.md) 或 [README.zh-CN.md (中文)](../README.zh-CN.md)*

SwissTable是一个基于开放地址法的高性能哈希表实现，采用了谷歌的SwissTable设计原理，具有高效的缓存性能、低冲突率和稳定的性能特性。

## 主要特性

- **高性能**: 采用开放地址法 + 控制字节数组布局，提高缓存命中率
- **Robin Hood插入策略**: 降低元素聚集，提高插入和查找性能
- **自动扩容**: 当负载因子超过阈值(7/8)时自动扩容，保持性能
- **泛型支持**: 支持任意可哈希类型作为键
- **丰富的API**: 提供完整的增删改查和遍历操作

## 基本用法

### 创建哈希表

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

### 增删改查操作

```moonbit
// 插入或更新值
map.set("key", 42)

// 获取值（返回Option类型）
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

### 遍历操作

```moonbit
// 基本遍历
map.each(fn(key, value) {
  println!("Key: {}, Value: {}", key, value)
})

// 带索引遍历
map.eachi(fn(index, key, value) {
  println!("Index: {}, Key: {}, Value: {}", index, key, value)
})

// 使用迭代器
for pair in map.iter() {
  let (key, value) = pair
  println!("Key: {}, Value: {}", key, value)
}

// 使用iter2迭代器
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

### 其他操作

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

## 性能考虑

- SwissTable使用负载因子7/8，在大多数情况下能提供良好的空间效率和性能平衡
- Robin Hood插入策略减少了长探测序列，特别是在高负载因子下表现良好
- 使用控制字节数组分离存储元数据，提高缓存局部性

## 适用场景

- 需要高性能哈希表的场景
- 编译器和语言运行时的核心数据结构
- 性能敏感的应用程序
- 需要频繁查找和插入操作的场景

## 贡献

欢迎提交问题和改进建议。 