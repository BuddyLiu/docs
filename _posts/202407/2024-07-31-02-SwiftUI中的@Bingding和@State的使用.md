---
title:      SwiftUI中的Binding和State的使用
date:       2024-07-31
tags:
- SwiftUI
- 属性修饰符
---

## 一、@State

### 1.1 基础用法

@State是SwiftUI中用于在视图中声明可变的状态属性，并自动更新视图的属性包装器。它主要用于管理视图的私有状态，如按钮点击次数、文本输入等。当@State修饰的属性值发生变化时，SwiftUI会自动重新渲染视图以反映最新的状态。

### 1.2 示例代码

```swift
struct ContentView: View {
    @State private var count = 0
    var body: some View {
        Button(action: { count += 1 }) {
            Text("Count: \(count)")
        }
    }
}
```

### 1.3 注意点

* @State修饰的属性通常只在视图内部使用，即使未显式标记为private，也应视为私有属性。
* @State变量在视图的构造函数中只能赋值一次，后续的调整需要在视图的body内进行。
* @State变量是线程安全的，可以在非主线程中进行修改，但通常建议在UI更新相关的操作放在主线程执行。

## 二、@Binding

### 2.1 基础用法

@Binding用于在视图之间传递和共享可读写的值，实现双向数据绑定。它创建了一个对属性的引用，以便多个视图可以共享同一份数据，并且对数据的更改会在所有引用的地方生效。

### 2.2 示例代码

```swift
struct ParentView: View {
    @State private var isChildViewVisible = false
    var body: some View {
        VStack {
            Toggle(isOn: $isChildViewVisible) {
                Text("Show Child View")
            }
            if isChildViewVisible {
                ChildView(isVisible: $isChildViewVisible)
            }
        }
    }
}

struct ChildView: View {
    @Binding var isVisible: Bool
    var body: some View {
        Text("Child View")
        Button(action: { isVisible = false }) {
            Text("Hide")
        }
    }
}
```

### 2.3 注意点

* 在传递@Binding属性时，需要使用$符号来修饰，表示传递的是属性的绑定（Binding）而不是属性本身。
* @Binding不直接持有数据，而是提供对数据的读写访问。因此，确保@Binding的数据源是可信的，避免数据不一致或应用崩溃。
* 在复杂的视图层级中，逐级传递@Binding可能导致数据流难以追踪，此时应考虑使用其他状态管理方法，如@EnvironmentObject。

## 三、@Binding和@State的进阶用法和高阶用法

### 3.1 @Binding的进阶用法

#### 3.1.1 自定义Binding

在复杂的应用场景中，可能需要自定义Binding以适应特定的需求。自定义Binding允许开发者封装和控制对数据的读写访问，增加代码的灵活性和可重用性。

**示例代码**

假设有一个自定义的视图，它需要一个Binding来访问和修改一个特定的属性，但这个属性可能不直接存在于视图的父级视图中。这时，可以创建一个自定义的Binding来桥接这个属性。

```swift
class UserData: ObservableObject {
    @Published var name: String = ""
}

struct CustomBindingView: View {
    @Binding var name: String

    var body: some View {
        TextField("Enter name", text: $name)
    }
}

struct ParentView: View {
    @ObservedObject var userData = UserData()

    var body: some View {
        // 自定义Binding，将userData.name包装为Binding<String>
        let nameBinding = Binding<String>(
            get: { self.userData.name },
            set: { self.userData.name = $0 }
        )

        CustomBindingView(name: nameBinding)
    }
}
```

在这个例子中，`CustomBindingView` 需要一个 `Binding<String>` 来接收和修改用户名称。但在 `ParentView` 中，这个数据被封装在 `UserData` 类中。通过自定义Binding，我们可以将 `userData.name` 包装成一个符合 `CustomBindingView` 需求的Binding。

#### 3.1.2 结合@EnvironmentObject和@Binding

当需要在整个应用程序中共享数据，并且这些数据还需要在特定视图之间进行双向绑定时，可以结合使用@EnvironmentObject和@Binding。

**示例场景

一个全局的用户偏好设置对象，在多个视图中需要读取和修改这些设置。

**示例代码**（简化版）：

```swift
class UserPreferences: ObservableObject {
    @Published var darkModeEnabled: Bool = false
}

// 在App的入口点设置EnvironmentObject
@main
struct MyApp: App {
    @StateObject var userPreferences = UserPreferences()

    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(userPreferences)
        }
    }
}

struct SettingsView: View {
    @EnvironmentObject var userPreferences: UserPreferences

    // 在需要双向绑定的子视图中，可以通过自定义Binding来使用@EnvironmentObject中的数据
    var darkModeBinding: Binding<Bool> {
        Binding<Bool>(
            get: { userPreferences.darkModeEnabled },
            set: { userPreferences.darkModeEnabled = $0 }
        )
    }

    var body: some View {
        Toggle(isOn: darkModeBinding) {
            Text("Dark Mode")
        }
    }
}
```

在这个例子中，`UserPreferences` 是一个全局的环境对象，它包含了用户的偏好设置。在 `SettingsView` 中，我们创建了一个 `darkModeBinding` 来将 `userPreferences.darkModeEnabled` 包装成一个Binding，以便在Toggle视图中使用双向绑定。

### 3.2 @State的进阶用法

#### 3.2.1 使用@State处理复杂数据

虽然@State通常用于处理简单的数据类型（如Int、String等），但它也可以用于处理更复杂的数据结构，如结构体（Struct）或数组。然而，当处理复杂数据结构时，需要注意数据的不可变性和视图的刷新机制。

**示例代码

```swift
struct Item: Identifiable {
    let id = UUID()
    var name: String
}

struct ContentView: View {
    @State private var items: [Item] = [Item(name: "Apple"), Item(name: "Banana")]

    var body: some View {
        List {
            ForEach(items) { item in
                Text(item.name)
            }
            .onDelete(perform: deleteItem)
        }
        .navigationTitle("Items")
    }

    func deleteItem(at offsets: IndexSet) {
        items.remove(atOffsets: offsets)
    }
}
```

在这个例子中，`ContentView` 使用@State来管理一个`Item`数组。通过List和ForEach，我们可以展示数组中的每个项，并通过`.onDelete`修饰符来处理删除操作。由于`items`是@State属性，每当数组发生变化时，视图都会自动刷新。

#### 3.2.2 结合@State和@Binding处理视图间的复杂交互

在处理复杂的视图交互时，可能需要结合使用@State和@Binding来管理状态和数据流。父视图可以使用@State来声明状态，并通过@Binding将状态传递给子视图。子视图则可以通过修改@Binding属性来反馈状态变化给父视图。

**示例场景

一个表单视图，包含多个子视图（如文本框、选择器等），每个子视图都需要与父视图共享和更新数据。

**示例代码**（简化版）：为了保持示例的简洁性，这里不展示完整的表单视图和子视图代码，但概念相同。

```swift
struct FormView: View {
    @State private var formData: FormData = FormData()

    var body: some View {
        VStack {
            TextField("Name", text: $formData.name)
            // 假设有更多子视图，如DatePicker、Picker等，都使用$formData中的相应属性进行绑定
        }
    }
}

struct FormData: Identifiable {
    let id = UUID()
    var name: String = ""
    // 可能还有其他属性，如dateOfBirth、gender等
}
```

在这个例子中，`FormView` 使用@State来管理一个`FormData`实例，该实例包含了表单的所有数据。通过文本字段（TextField）和其他可能的子视图（如DatePicker、Picker等），我们可以将`formData`中的属性绑定到视图上，实现数据的双向绑定和自动更新。注意，虽然这里没有直接展示子视图的代码，但概念上它们会接收来自`FormView`的@Binding属性，并使用这些属性来与父视图共享和更新数据。

### 3.3 实践中的注意点

* **避免过度使用@Binding虽然@Binding提供了强大的双向数据绑定功能，但过度使用可能导致数据流复杂且难以追踪。在可能的情况下，考虑使用单向数据流（如通过回调或闭包传递数据）来简化视图间的交互。**
* **确保数据源的一致性在使用@Binding时，确保所有引用的数据源都是一致的，避免数据不一致导致的问题。**
* **注意生命周期管理对于使用@ObservedObject或@EnvironmentObject管理的全局状态对象，需要注意其生命周期管理。确保在不再需要时及时销毁这些对象，以避免内存泄漏。**
* **优化性能在使用大量@State或@Binding属性时，注意优化视图的性能。避免在视图的body中执行复杂的计算或数据操作，这些操作应该在视图外部完成并通过状态属性传递给视图。**