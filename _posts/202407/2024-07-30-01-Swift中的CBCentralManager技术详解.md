---
title:      Swift中的CBCentralManager技术详解
date:       2024-07-30
tags:
- Swift
- CBCentralManager
- 蓝牙
---

在iOS开发中，蓝牙通信是一项重要功能，尤其是低功耗蓝牙（BLE）技术，广泛应用于智能家居、健康监测、智能穿戴等多个领域。Swift作为iOS开发的主流编程语言，通过CoreBluetooth框架提供了强大的蓝牙开发支持。其中，CBCentralManager是CoreBluetooth框架中的核心类之一，负责扫描、发现、连接和管理远程外设设备。本文将深入介绍CBCentralManager的使用方法和相关概念，帮助开发者更好地理解和应用这一技术。

## 一、CBCentralManager概述

CBCentralManager是CoreBluetooth框架中的一个关键类，它充当蓝牙中央设备的角色，用于管理发现的或已连接的远程外设设备（由CBPeripheral对象表示）。CBCentralManager能够扫描、发现、连接和断开与蓝牙外设的连接，同时处理与这些外设的通信事件。

### CBCentralManager的主要功能

- **扫描外设**：CBCentralManager可以扫描周围广播的蓝牙外设，并返回这些外设的相关信息。
- **发现服务**：连接到外设后，CBCentralManager可以进一步发现外设提供的服务（CBService）。
- **读写特征值**：通过服务，CBCentralManager可以读写外设的特征值（CBCharacteristic），实现数据的交互。
- **管理连接**：CBCentralManager负责建立和断开与外设的连接，同时处理连接过程中的各种事件。

## 二、 CBCentralManager 的初始化

在使用CBCentralManager之前，首先需要初始化一个CBCentralManager对象。初始化时需要指定一个代理（CBCentralManagerDelegate），用于接收蓝牙状态变化、外设发现等事件通知。

```swift
import CoreBluetooth

class MyCentralManager: NSObject, CBCentralManagerDelegate, CBPeripheralDelegate {
    var centralManager: CBCentralManager!

    override init() {
        super.init()
        centralManager = CBCentralManager(delegate: self, queue: nil)
    }
}
```

## 三、 CBCentralManager API详解

以下是对`CBCentralManager`类中各API的详细翻译和代码示例，展示了每个API的功能和用法。

### 3.1 初始化方法

- **init()**
  - 功能：初始化一个CBCentralManager实例，使用主队列，没有代理。

- **init(delegate: (any CBCentralManagerDelegate)?, queue: dispatch_queue_t?)**
  - 功能：初始化一个CBCentralManager实例，指定代理和事件派发的队列。如果队列为nil，则使用主队列。

- **init(delegate: (any CBCentralManagerDelegate)?, queue: dispatch_queue_t?, options: [String : Any]? = nil)**
  - 功能：iOS 7.0及以上版本可用，初始化一个CBCentralManager实例，支持指定代理、队列和初始化选项。

### 3.2 属性

- **delegate**
  - 类型：`(any CBCentralManagerDelegate)?`
  - 功能：设置CBCentralManager的代理，用于接收中心角色的事件。

- **isScanning**
  - 类型：`Bool`
  - 功能：iOS 9.0及以上版本可用，表示CBCentralManager当前是否在扫描。

### 3.3 方法

- **supports(_ features:)**
  - 功能：iOS 13.0及以上版本可用，检查CBCentralManager是否支持指定的特性。
  -  示例：
```swift
if CBCentralManager.supports(.featureA) { 
    ... 
}
```

- **retrievePeripherals(withIdentifiers:)**
  - 功能：iOS 7.0及以上版本可用，尝试根据标识符列表检索CBPeripheral对象。
  -  示例：
```swift
let peripherals = centralManager.retrievePeripherals(withIdentifiers: [UUID(uuidString: "...")!])
```

- **retrieveConnectedPeripherals(withServices:)**
  - 功能：iOS 7.0及以上版本可用，检索所有已连接到系统的外设，这些外设实现了serviceUUIDs列表中的任意服务。
  -  示例：
```swift
let peripherals = centralManager.retrieveConnectedPeripherals(withServices: [CBUUID(string: "...")!])
```

- **scanForPeripherals(withServices:options:)**
  - 功能：开始扫描具有指定服务的外设。如果CBCentralManager已经在扫描，则新的参数会替换旧的。
  -  示例：
```swift
centralManager.scanForPeripherals(withServices: [CBUUID(string: "...")!], options: nil)
```

- **stopScan()**
  - 功能：停止扫描外设。
  -  示例：
```swift
centralManager.stopScan()
```

- **connect(_:options:)**
  - 功能：尝试连接到指定的CBPeripheral。连接不会超时，成功或失败将通过代理方法通知。
  -  示例：
```swift
centralManager.connect(peripheral, options: nil)
```

- **cancelPeripheralConnection(_ peripheral:)**
  - 功能：取消与指定外设的活跃或挂起的连接。
  -  示例：
```swift
centralManager.cancelPeripheralConnection(peripheral)
```

- **registerForConnectionEvents(options:)**
  - 功能：iOS 13.0及以上版本可用，注册连接事件匹配选项，当发生匹配的连接事件时调用代理方法。
  -  示例：
```swift
centralManager.registerForConnectionEvents(options: [CBConnectionEventMatchingOption.serviceUUIDs: [CBUUID(string: "...")!]])
```

请注意，示例中省略了错误处理和多个外设管理的逻辑，以保持示例的简洁性。在实际应用中，您应该添加适当的错误处理和多外设管理逻辑。
### 3.4 API 原文翻译

```swift
    /**
     @class CBCentralManager
 @discussion 蓝牙中心角色的入口点。仅当中心管理器的状态为 CBCentralManagerStatePoweredOn 时，才应发出命令。
 */
    @available(iOS 5.0, *)
    open class CBCentralManager : CBManager {
    
    /**
     @property delegate
     @discussion 代理对象，将接收中心角色事件。
     */
    weak open var delegate: (any CBCentralManagerDelegate)?


    /**
     @property isScanning
     @discussion 中心设备当前是否在扫描。
     */
    @available(iOS 9.0, *)
    open var isScanning: Bool { get }


    /**
     @method supports
     @param features	要检查支持的一个或多个特性。
     @discussion     返回一个布尔值，表示是否支持提供的特性。
     */
    @available(iOS 13.0, *)
    open class func supports(_ features: CBCentralManager.Feature) -> Bool


    /**
     @method init
     @discussion 便利初始化方法。
     */
    public convenience init()


    /**
     @method initWithDelegate:queue:
     @param delegate 接收中心角色事件的代理。
     @param queue    事件将分发的dispatch队列。如果为nil，则使用主队列。
     @discussion     初始化调用。中心角色的事件将在提供的队列上分发。
     */
    public convenience init(delegate: (any CBCentralManagerDelegate)?, queue: dispatch_queue_t?)


    /**
     @method initWithDelegate:queue:options:
     @param delegate 接收中心角色事件的代理。
     @param queue    事件将分发的dispatch队列。如果为nil，则使用主队列。
     @param options  一个可选字典，指定管理器的选项。
     @discussion     初始化调用。中心角色的事件将在提供的队列上分发。
     @@seealso		CBCentralManagerOptionShowPowerAlertKey
     @seealso		CBCentralManagerOptionRestoreIdentifierKey
     */
    @available(iOS 7.0, *)
    public init(delegate: (any CBCentralManagerDelegate)?, queue: dispatch_queue_t?, options: [String : Any]? = nil)


    /**
     @method retrievePeripheralsWithIdentifiers:
     @param identifiers	一个 NSUUID 对象的列表。
     @discussion			尝试根据对应的 identifiers 检索 CBPeripheral 对象。
     @@return				一个 CBPeripheral 对象的列表。
     */
    @available(iOS 7.0, *)
    open func retrievePeripherals(withIdentifiers identifiers: [UUID]) -> [CBPeripheral]


    /**
     @method retrieveConnectedPeripheralsWithServices
     @discussion 检索所有已连接到系统并实现 serviceUUIDs 中列出的任何服务的外设。
     				请注意，此集合可能包括由其他应用程序连接的外设，这些外设需要通过{@link connectPeripheral:options:}在本地连接后才能使用。
     @@return		一个 CBPeripheral 对象的列表。
     */
    @available(iOS 7.0, *)
    open func retrieveConnectedPeripherals(withServices serviceUUIDs: [CBUUID]) -> [CBPeripheral]


    /**
     @method scanForPeripheralsWithServices:options:
     @param serviceUUIDs 一个 CBUUID 对象的列表，表示要扫描的服务。
     @param options      一个可选字典，指定扫描选项。
     @discussion         开始扫描广播任何 serviceUUIDs 中列出的服务的外设。虽然强烈不推荐，但如果 serviceUUIDs 为 nil ，将返回所有发现的外设。
                         如果中心设备已经在使用不同的 serviceUUIDs 或 options 进行扫描，则提供的参数将替换它们。
                         已指定 bluetooth-central 后台模式的应用程序允许在后台进行扫描，但有两个限制：扫描必须指定 serviceUUIDs 中的一个或多个服务类型，
                         并且会忽略 CBCentralManagerScanOptionAllowDuplicatesKey 扫描选项。
     @see                centralManager:didDiscoverPeripheral:advertisementData:RSSI:
     @seealso            CBCentralManagerScanOptionAllowDuplicatesKey
     @seealso			CBCentralManagerScanOptionSolicitedServiceUUIDsKey
     */
    open func scanForPeripherals(withServices serviceUUIDs: [CBUUID]?, options: [String : Any]? = nil)


    /**
     @method stopScan
     @discussion 停止扫描外设。
     */
    open func stopScan()


    /**
     @method connectPeripheral:options:
     @param peripheral   要连接的外设。
     @param options      一个可选字典，指定连接行为选项。
     @discussion         发起与 peripheral 的连接。连接尝试永远不会超时，并且根据结果，将调用
                         {@link centralManager:didConnectPeripheral:}或{@link centralManager:didFailToConnectPeripheral:error:}。
                         当 peripheral 被释放时，挂起的尝试将自动取消，也可以通过{@link cancelPeripheralConnection}显式取消。
     @see                centralManager:didConnectPeripheral:
     @see                centralManager:didFailToConnectPeripheral:error:
     @seealso            CBConnectPeripheralOptionNotifyOnConnectionKey
     @seealso            CBConnectPeripheralOptionNotifyOnDisconnectionKey
     @seealso            CBConnectPeripheralOptionNotifyOnNotificationKey
     @seealso            CBConnectPeripheralOptionEnableTransportBridgingKey
     @seealso			CBConnectPeripheralOptionRequiresANCS
     @seealso            CBConnectPeripheralOptionEnableAutoReconnect
     */
    open func connect(_ peripheral: CBPeripheral, options: [String : Any]? = nil)


    /**
     @method cancelPeripheralConnection:
     @param peripheral   一个 CBPeripheral 对象。
     @discussion         取消与 peripheral 的活跃或挂起的连接。请注意，这是非阻塞的，并且仍然挂起到 peripheral 的任何 CBPeripheral 命令可能会或可能不会完成。
     @see                centralManager:didDisconnectPeripheral:error:
     */
    open func cancelPeripheralConnection(_ peripheral: CBPeripheral)


    /**
     @method registerForConnectionEventsWithOptions:
     @param options		一个字典，指定连接事件选项。
     @discussion     	当发生与给定选项匹配的连接事件时，调用{@link centralManager:connectionEventDidOccur:forPeripheral:}。
                         在option参数中传递nil将清除任何先前注册的匹配选项。
     @see				centralManager:connectionEventDidOccur:forPeripheral:
     @seealso        	CBConnectionEventMatchingOptionServiceUUIDs
     @seealso            CBConnectionEventMatchingOptionPeripheralUUIDs
     */
    @available(iOS 13.0, *)
    open func registerForConnectionEvents(options: [CBConnectionEventMatchingOption : Any]? = nil)
}
```

## 四、 CBCentralManagerDelegate 协议详解

`CBCentralManagerDelegate` 协议定义了当使用 `CBCentralManager` 类进行蓝牙低功耗（BLE）通信时，中心设备（Central Device）应如何响应各种蓝牙事件。该协议包含了一系列的方法，用于处理中心设备状态的变化、外设的发现与连接、以及连接过程中的各种事件。下面是对该协议中各个方法的详细解释：

### 4.1 必须实现的方法

- **centralManagerDidUpdateState(_ central:)**
  - 当中心设备的状态发生变化时调用。开发者应在此方法中检查中心设备的状态，并据此决定是否开始扫描或连接外设。特别是，只有当状态为 `CBCentralManagerStatePoweredOn` 时，才应发出蓝牙命令。

### 4.2 可选实现的方法

- **centralManager(_:willRestoreState:)**
  - 当应用从后台被唤醒以恢复之前的状态时调用。这允许应用根据系统保存的状态信息来同步其内部状态。

- **centralManager(_:didDiscoverPeripheral:advertisementData:RSSI:)**
  - 在扫描过程中发现外设时被调用。通过此方法，应用可以获取外设的广告数据、RSSI值等信息，并决定是否与该外设建立连接。

- **centralManager(_:didConnectPeripheral:)**
  - 当通过 `connectPeripheral(_:options:)` 方法成功连接到外设时被调用。此时，可以开始发现外设的服务和特征。

- **centralManager(_:didFailToConnectPeripheral:error:)**
  - 当尝试连接到外设失败时被调用。错误参数提供了连接失败的原因。

- **centralManager(_:didDisconnectPeripheral:error:)**
  - 当与外设的连接断开时被调用。如果断开不是由 `cancelPeripheralConnection(_:)` 方法引起的，则错误参数会提供断开的原因。

- **centralManager(_:didDisconnectPeripheral:timestamp:isReconnecting:error:)**
  - 这是 `didDisconnectPeripheral(_:error:)` 方法的一个扩展，提供了断开连接的时间戳和是否正在尝试重新连接的信息。当启用自动重连时特别有用。

- **centralManager(_:connectionEventDidOccur:forPeripheral:)**
  - 当连接事件发生时调用，这些事件包括外设的连接或断开。此方法通过 `registerForConnectionEventsWithOptions(_:)` 方法注册感兴趣的事件。

- **centralManager(_:didUpdateANCSAuthorizationForPeripheral:)**
  - 当连接到需要Apple Notification Center Service（ANCS）的外设时，如果授权状态发生变化，则调用此方法。这允许应用根据新的授权状态调整其行为。

### 4.3 API 原文翻译 

```swift
    /**
     @protocol CBCentralManagerDelegate
     @discussion CBCentralManager对象的代理必须遵循 CBCentralManagerDelegate 协议。
             该协议中的唯一必需方法用于指示中心管理器的可用性，而可选方法则允许发现和连接外设。
    */
    public protocol CBCentralManagerDelegate : NSObjectProtocol {


    /**
     @method centralManagerDidUpdateState:
     @param central  状态已更改的中心管理器。
     @discussion     每当中心管理器的状态更新时调用。仅当状态为 CBCentralManagerStatePoweredOn 时才应发出命令。低于 CBCentralManagerStatePoweredOn 的状态意味着扫描已停止，并且所有已连接的外设都已被断开连接。
                     如果状态降至 CBCentralManagerStatePoweredOff 以下，则从此中心管理器获得的所有 CBPeripheral 对象都将失效，并必须重新检索或发现。
     @see            state
     */
    @available(iOS 5.0, *)
    func centralManagerDidUpdateState(_ central: CBCentralManager)


    /**
     @method centralManager:willRestoreState:
     @param central      提供此信息的中心管理器。
     @param dict			一个字典，包含应用终止时系统保留的关于 central 的信息。
     @discussion			对于选择启用状态保存和恢复的应用程序，当您的应用在后台重新启动以完成某些蓝牙相关任务时，将首先调用此方法。使用此方法将您的应用程序状态与蓝牙系统状态同步。
     @seealso            CBCentralManagerRestoredStatePeripheralsKey;
     @seealso            CBCentralManagerRestoredStateScanServicesKey;
     @seealso            CBCentralManagerRestoredStateScanOptionsKey;
     */
    @available(iOS 5.0, *)
    optional func centralManager(_ central: CBCentralManager, willRestoreState dict: [String : Any])


    /**
     @method centralManager:didDiscoverPeripheral:advertisementData:RSSI:
     @param central              提供此更新的中心管理器。
     @param peripheral           一个 CBPeripheral 对象。
     @param advertisementData    一个包含任何广告和扫描响应数据的字典。
     @param RSSI                  peripheral 的当前RSSI值，单位为dBm。值 127 保留，表示RSSI不可用。
     @discussion                 在扫描过程中，当 central 发现 peripheral 时调用此方法。必须保留发现的外设以便使用它；否则，它将被视为不感兴趣并由中心管理器清理。
                                有关 advertisementData 键的列表，请参阅{@link CBAdvertisementDataLocalNameKey}和其他类似常量。
     @seealso                    CBAdvertisementData.h
     */
    @available(iOS 5.0, *)
    optional func centralManager(_ central: CBCentralManager, didDiscover peripheral: CBPeripheral, advertisementData: [String : Any], rssi RSSI: NSNumber)


    /**
     @method centralManager:didConnectPeripheral:
     @param central      提供此信息的中心管理器。
     @param peripheral   已连接的 CBPeripheral 。
     @discussion         当通过{@link connectPeripheral:options:}发起的连接成功时调用此方法。
     */
    @available(iOS 5.0, *)
    optional func centralManager(_ central: CBCentralManager, didConnect peripheral: CBPeripheral)


    /**
     @method centralManager:didFailToConnectPeripheral:error:
     @param central      提供此信息的中心管理器。
     @param peripheral   连接失败的 CBPeripheral 。
     @param error        失败的原因。
     @discussion         当通过{@link connectPeripheral:options:}发起的连接未能完成时调用此方法。由于连接尝试不会超时，因此连接失败是不典型的，并且通常表明存在瞬态问题。
     */
    @available(iOS 5.0, *)
    optional func centralManager(_ central: CBCentralManager, didFailToConnect peripheral: CBPeripheral, error: (any Error)?)


    /**
     @method centralManager:didDisconnectPeripheral:error:
     @param central      提供此信息的中心管理器。
     @param peripheral   已断开连接的 CBPeripheral 。
     @param error        如果发生错误，则为失败的原因。
     @discussion         当通过{@link connectPeripheral:options:}连接的外设断开连接时调用此方法。如果断开连接不是由{@link cancelPeripheralConnection}发起的，则错误参数将详细说明原因。
                         一旦调用此方法，将不再在 peripheral 的 CBPeripheralDelegate 上调用更多方法。
     */
    @available(iOS 5.0, *)
    optional func centralManager(_ central: CBCentralManager, didDisconnectPeripheral peripheral: CBPeripheral, error: (any Error)?)


    /**
     @method centralManager:didDisconnectPeripheral:timestamp:isReconnecting:error
     @param central      提供此信息的中心管理器。
     @param peripheral   已断开连接的 CBPeripheral 。
     @param timestamp        断开连接的时间戳，可能是现在或几秒钟前。
     @param isReconnecting      如果断开连接时触发了重新连接。
     @param error        如果发生错误，则为失败的原因。
     @discussion         当通过{@link connectPeripheral:options:}连接的外设断开连接时调用此方法。如果外设是使用连接选项{@link CBConnectPeripheralOptionEnableAutoReconnect}连接的，
                         则调用此方法后，系统将自动尝试重新连接到外设。如果之后成功建立了与外设的连接，则可能会调用{@link centralManager:didConnectPeripheral:}。
                         如果外设是在没有选项CBConnectPeripheralOptionEnableAutoReconnect的情况下连接的，则调用此方法后，将不再在 peripheral 的 CBPeripheralDelegate 上调用更多方法。
     */
    @available(iOS 5.0, *)
    optional func centralManager(_ central: CBCentralManager, didDisconnectPeripheral peripheral: CBPeripheral, timestamp: CFAbsoluteTime, isReconnecting: Bool, error: (any Error)?)


    /**
     @method centralManager:connectionEventDidOccur:forPeripheral:
     @param central      提供此信息的中心管理器。
     @param event		已发生的 CBConnectionEvent 。
     @param peripheral   触发事件的 CBPeripheral 。
     @discussion         当发生与通过{@link registerForConnectionEventsWithOptions:}提供的选项之一匹配的外设的连接或断开连接时调用此方法。
     */
    @available(iOS 13.0, *)
    optional func centralManager(_ central: CBCentralManager, connectionEventDidOccur event: CBConnectionEvent, for peripheral: CBPeripheral)


    /**
     @method centralManager:didUpdateANCSAuthorizationForPeripheral:
     @param central      提供此信息的中心管理器。
     @param peripheral   触发事件的 CBPeripheral 。
     @discussion         当使用{@link connectPeripheral:}选项{@link CBConnectPeripheralOptionRequiresANCS}连接的外设的授权状态更改时调用此方法。
     */
    @available(iOS 13.0, *)
    optional func centralManager(_ central: CBCentralManager, didUpdateANCSAuthorizationFor peripheral: CBPeripheral)
}
```

### 4.4 使用示例

假设你正在开发一个需要连接BLE外设的应用，你可能会这样实现 `CBCentralManagerDelegate`：

```swift
import CoreBluetooth

class MyCentralManager: NSObject, CBCentralManagerDelegate, CBPeripheralDelegate {
    var centralManager: CBCentralManager!
    var discoveredPeripherals: [CBPeripheral] = []
    var connectedPeripheral: CBPeripheral?

    override init() {
        super.init()
        centralManager = CBCentralManager(delegate: self, queue: nil)
    }

    // 省略前面的代码 。。。

    // 发现外设
    func centralManager(_ central: CBCentralManager, didDiscover peripheral: CBPeripheral, advertisementData: [String : Any], rssi RSSI: NSNumber) {
        if !discoveredPeripherals.contains(peripheral) {
            discoveredPeripherals.append(peripheral)
            print("Discovered peripheral: \(peripheral)")
        }
    }

    // 已连接到外设
    func centralManager(_ central: CBCentralManager, didConnect peripheral: CBPeripheral) {
        print("Connected to peripheral: \(peripheral)")
        connectedPeripheral = peripheral
        peripheral.delegate = self
        peripheral.discoverServices(nil)
    }

    // 连接外设失败
    func centralManager(_ central: CBCentralManager, didFailToConnect peripheral: CBPeripheral, error: Error?) {
        print("Failed to connect to peripheral: \(peripheral)")
    }

    // 外设断开连接
    func centralManager(_ central: CBCentralManager, didDisconnectPeripheral peripheral: CBPeripheral, error: Error?) {
        print("Disconnected from peripheral: \(peripheral)")
        if connectedPeripheral == peripheral {
            connectedPeripheral = nil
        }
    }
}
```

在这个示例中，`BluetoothManager` 类实现了 `CBCentralManagerDelegate` 协议，并在 `centralManagerDidUpdateState(_:)` 方法中根据中心设备的状态决定是否开始扫描外设。此外，还实现了 `didDiscoverPeripheral(_:advertisementData:rssi:)` 和 `didConnectPeripheral(_:)` 方法来处理外设发现和连接成功的事件。你可以根据应用的需求实现其他可选方法。

## 五、CBCentralManagerState状态

在iOS开发中，`CBCentralManager` 是CoreBluetooth框架中的一个关键类，它用于管理蓝牙中心设备（即主动扫描和连接其他蓝牙外设的设备）的功能。这里我们详细解析了几个与`CBCentralManager`相关的枚举类型，以及一个结构体，它们共同提供了蓝牙中心设备状态、连接事件和设备特性的描述。

### 5.1 CBCentralManagerState 枚举

`CBCentralManagerState`枚举代表了`CBCentralManager`（即蓝牙中心管理器）的当前状态。这个枚举在iOS 10.0及以后版本中被弃用，推荐使用`CBManagerState`来替代。不过，了解这个枚举仍然有助于理解蓝牙设备的状态管理。

- **CBCentralManagerStateUnknown**：状态未知，即将更新。
- **CBCentralManagerStateResetting**：与系统服务的连接暂时丢失，即将更新。
- **CBCentralManagerStateUnsupported**：该平台不支持蓝牙低功耗中心/客户端角色。
- **CBCentralManagerStateUnauthorized**：该应用未被授权使用蓝牙低功耗中心/客户端角色。
- **CBCentralManagerStatePoweredOff**：蓝牙当前已关闭。
- **CBCentralManagerStatePoweredOn**：蓝牙当前已开启并可用。

### 5.2 CBConnectionEvent 枚举

`CBConnectionEvent`枚举表示对等设备的连接状态。这对于跟踪蓝牙外设的连接和断开连接事件非常有用。

- **CBConnectionEventPeerDisconnected**：对等设备已断开连接。
- **CBConnectionEventPeerConnected**：对等设备已连接。

#### CBCentralManager.Feature 结构体

从iOS 13.0开始，`CBCentralManager`引入了一个`Feature`结构体，用于描述设备特定的蓝牙功能。这个结构体遵循`OptionSet`协议，允许通过位掩码来表示多个特性的组合。

- **extendedScanAndConnect**：表示硬件支持扩展扫描和增强的连接创建。这是一个静态属性，可以通过`CBCentralManager.Feature.extendedScanAndConnect`访问，用于检查设备是否支持这些高级蓝牙功能。

### 5.3 使用示例

以下是一个简单的使用示例，展示了如何在`CBCentralManagerDelegate`的`centralManagerDidUpdateState(_:)`方法中根据蓝牙中心管理器的状态来执行不同的操作：

```swift
import CoreBluetooth

class BluetoothManager: NSObject, CBCentralManagerDelegate {
    var centralManager: CBCentralManager!

    override init() {
        super.init()
        centralManager = CBCentralManager(delegate: self, queue: nil)
    }

    func centralManagerDidUpdateState(_ central: CBCentralManager) {
        switch central.state {
        case .poweredOn:
            print("Bluetooth is powered on and ready to use.")
            // 开始扫描等操作
        case .poweredOff:
            print("Bluetooth is powered off.")
        // 处理其他状态...
        default:
            break
        }
    }

    // 实现其他CBCentralManagerDelegate方法...
}
```

请注意，由于`CBCentralManagerState`在iOS 10.0及以后版本中被弃用，如果你的应用目标版本是iOS 10.0或更高，建议使用`CBManagerState`代替。不过，上面的示例仍然使用了`CBCentralManagerState`来展示其用法。对于新开发的应用，推荐使用`CBManagerState`。
