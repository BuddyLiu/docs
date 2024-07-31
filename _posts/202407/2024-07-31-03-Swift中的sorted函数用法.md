---
title:      Swift中的sorted函数用法
date:       2024-07-31
tags:
- Swift
- 排序
---

Swift标准库中的sorted()函数是一个非常强大且灵活的排序工具。以下是对其基础用法、进阶用法和高级用法的详细讲解：

## 一、基础用法

`sorted()`函数是Swift标准库中的一个高阶函数，用于返回数组元素排序后的新数组。基础用法非常简单，只需调用数组的`sorted()`方法即可，它会返回一个新的数组，其中包含了原数组中的元素，但这些元素已经按照默认的升序规则进行了排序。

```swift
let numbers = [3, 1, 4, 1, 5, 9, 2, 6]
let sortedNumbers = numbers.sorted()
print(sortedNumbers) // 输出: [1, 1, 2, 3, 4, 5, 6, 9]
```

对于字符串数组，`sorted()`同样适用，它会按照字典序进行排序。

```swift
let fruits = ["banana", "apple", "pear", "orange"]
let sortedFruits = fruits.sorted()
print(sortedFruits) // 输出: ["apple", "banana", "orange", "pear"]
```

## 二、进阶用法

进阶用法允许你自定义排序规则。你可以通过传递一个比较闭包给`sorted()`函数来实现这一点。比较闭包接受两个参数（通常命名为`$0`和`$1`），并返回一个布尔值，指示第一个参数是否应该在排序后的序列中出现在第二个参数之前。

```swift
struct Person {
    var name: String
    var age: Int
}

let people = [Person(name: "Alice", age: 30), Person(name: "Bob", age: 25), Person(name: "Charlie", age: 30)]

// 按年龄升序排序
let sortedPeopleByAge = people.sorted { $0.age < $1.age }
print(sortedPeopleByAge.map(\.name)) // 输出: ["Bob", "Alice", "Charlie"]

// 先按年龄升序排序，年龄相同则按姓名升序排序
let sortedPeopleByAgeAndName = people.sorted { first, second in
    if first.age == second.age {
        return first.name < second.name
    }
    return first.age < second.age
}
print(sortedPeopleByAgeAndName.map(\.name)) // 输出: ["Bob", "Alice", "Charlie"]，但如果有姓名不同的情况会按姓名排序
```

## 三、高级用法

高级用法涉及到对排序算法的更深层次理解和定制。在Swift中，`sorted()`函数底层使用了高效的排序算法（如快速排序或归并排序），但在大多数情况下，你不需要关心这些实现细节。

然而，如果你需要处理大量数据，并且关心排序性能，你可以考虑以下几点：

1. **优化比较操作**：确保你的比较闭包尽可能高效。避免在闭包中进行复杂的计算或访问重资源。

2. **使用稳定的排序算法**：`sorted()`函数在Swift中是稳定的，这意味着相等元素的原始顺序在排序后会被保持。如果你的排序逻辑依赖于稳定性，这一点很重要。

3. **并行排序**：对于非常大的数据集，你可以考虑使用并行排序算法来加速排序过程。这通常需要使用外部库或自己实现并行排序逻辑。

4. **自定义排序算法**：在某些特殊情况下，你可能需要实现自己的排序算法来满足特定的性能或行为需求。

在实际应用中，高级用法相对较少见，因为Swift标准库提供的`sorted()`函数已经足够强大和灵活，能够满足大多数排序需求。然而，了解这些高级用法可以帮助你在需要时优化你的代码和排序性能。