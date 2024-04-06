---
title:      深入理解 Swift 中闭包与 Objective-C 中 Block 的外部变量捕获机制
date:       2024-04-06
tags:
- Swift
- 闭包
- block
--- 

# 深入理解 Swift 中闭包与 Objective-C 中 Block 的外部变量捕获机制

在 Swift 中的闭包和 Objective-C 中的 Block 都支持捕获外部变量，使得在闭包或 Block 内部可以访问外部作用域的变量。本文将深入探讨它们在捕获外部变量方面的机制和区别。

## 1. Swift 中闭包的外部变量捕获机制

在 Swift 中，闭包捕获外部变量时会根据情况选择采用值捕获（Capture by Value）或引用捕获（Capture by Reference）的方式。

- **值捕获：** 当闭包捕获一个常量或变量时，会捕获该常量或变量的拷贝，即闭包内部使用的是外部变量的一个副本。这意味着即使外部变量的值发生改变，闭包内部使用的值也不会受到影响。

- **引用捕获：** 当闭包捕获一个引用类型的变量时（比如类实例），会捕获该变量的引用，即闭包内部使用的是外部变量的引用，而不是拷贝。这意味着闭包内部对外部变量的修改会影响外部作用域中的变量。

示例代码如下：

```swift
func makeIncrementer(forIncrement amount: Int) -> () -> Int {
    var total = 0
    return {
        total += amount // 引用捕获
        return total
    }
}

let incrementByTen = makeIncrementer(forIncrement: 10)
print(incrementByTen()) // 输出 10
print(incrementByTen()) // 输出 20
```

在这个例子中，闭包捕获了外部变量 `total` 的引用，因此每次调用 `incrementByTen` 闭包时，都会修改外部作用域中的 `total` 变量。

## 2. Objective-C 中 Block 的外部变量捕获机制

与 Swift 不同，Objective-C 中的 Block 默认是通过引用捕获外部变量的，即闭包内部使用的是外部变量的引用。

Objective-C 中 Block 的捕获外部变量机制与 Swift 中闭包的引用捕获类似，但需要注意的是，在 Objective-C 中使用 Block 时需要手动管理内存，特别是在循环引用的情况下需要特别小心。

示例代码如下：

```objective-c
int base = 100;
int (^addBlock)(int) = ^(int num) {
    return num + base; // 引用捕获
};

int result = addBlock(50); // 输出 150
```

在这个例子中，Block 捕获了外部变量 `base` 的引用，因此在 Block 内部可以访问并修改 `base` 的值。

## 3. 结论

Swift 中的闭包和 Objective-C 中的 Block 在捕获外部变量方面有一些相似之处，但也存在一些不同之处。在 Swift 中，闭包可以根据情况选择值捕获或引用捕获外部变量，而在 Objective-C 中，Block 默认是通过引用捕获外部变量的。理解它们的外部变量捕获机制有助于我们更好地使用闭包和 Block，并避免出现潜在的问题。