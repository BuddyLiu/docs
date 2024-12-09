---
title:      iOS技术调研与实现：基于 WebKit 的长网页生成截图与PDF实现
date:       2024-10-28
tags:
- Swift
- PDF
---

## 1. 引言

### 1.1 背景

为实现H5页面报告生成，原生需要支持将H5页面的内容生成长截图或PDF文件，因此有了此次技术调研与实现。

### 1.2 目标

本文的目标是调研和实现一种基于 WebKit 框架的长网页截图与PDF生成工具。通过调研和代码实现，理解其工作原理，并探讨可能的优化和扩展方案。

## 2. 技术概述

### 2.1 WebKit 框架

WebKit 是一个开源的浏览器引擎，广泛应用于 iOS 和 macOS 平台。它提供了强大的网页渲染和浏览功能。WebKit 框架的核心组件包括 WebCore（负责 HTML 和 CSS 的解析和渲染）和 JavaScriptCore（负责 JavaScript 的执行）。

### 2.2 WKWebView

WKWebView 是 WebKit 框架在 iOS 和 macOS 中提供的一个用于网页渲染的视图组件。它具有较高的性能和灵活性，支持现代的 Web 标准和特性。与 UIWebView 相比，WKWebView 在性能、稳定性和功能上都有显著的提升。

### 2.3 截图原理

截取长网页的关键在于如何获取整个网页的内容，并将其转换为长图或PDF。基本思路是：

1. 使用 WKWebView 加载网页。
2. 调整 WKWebView 的 frame 以适应网页的全部内容。
3. 如果要生成截图，使用 CoreGraphics 框架将 scrollView 的内容渲染为图片。
4. 如果要生成PDF文件，需要使用 WKWebView 的 UIPrintPageRenderer 功能实现 PDF 生成。

## 3. 实现细节

### 3.1 类设计

为了实现长网页截图功能，我们设计了一个名为 `WebViewCaptureUtil` 的工具类。这个类采用了单例模式，确保全局只有一个实例，方便调用和管理。

### 3.2 捕获流程

图片捕获流程如下：

1. 调用 `capturePicShare(with:success:failure:)` 方法，传入网页的 URL ，输出类型为 image 以及成功和失败的回调函数。
2. 初始化一个 WKWebView，并设置其 frame 为屏幕外的一个区域。
3. 加载网页请求。
4. 当网页加载完成时，调整 WKWebView 的 frame 以适应网页内容的高度。
5. 滚动到内容的底部，截取 scrollView 的内容为图片。
6. 调用成功回调，返回截取的图片。
7. 清理资源，移除 WKWebView。

PDF生成流程如下：

1. 调用 `capturePicShare(with:success:failure:)` 方法，传入网页的 URL ，输出类型为 pdf 以及成功和失败的回调函数。
2. 初始化一个 WKWebView，并设置其 frame 为屏幕外的一个区域。
3. 加载网页请求。
4. 使用 UIPrintPageRenderer 设置打印纸张大小和可打印区域大小，从而生成PDF文件，存储到沙盒路径下。
6. 调用成功回调，返回PDF文件的路径。
7. 清理资源，移除 WKWebView。

### 3.3 回调处理

为了处理截图或PDF的成功和失败的情况，我们定义了两个回调类型：`CapSuccessBlock` 和 `CapFailureBlock`。在捕获方法中，我们将这两个回调块保存为类的属性，以便在适当的时候调用。

### 3.4 截图和PDF生成方法

截图方法 `screenshot(scrollView:size:)` 使用了 CoreGraphics 框架的功能。具体步骤如下：

1. 开启一个图形上下文，指定大小和分辨率。
2. 保存当前的 scrollView 状态（contentOffset 和 frame）。
3. 调整 scrollView 的 contentOffset 和 frame，以便能够截取整个内容。
4. 渲染 scrollView 的层到图形上下文中。
5. 从图形上下文中获取图片。
6. 恢复 scrollView 的状态。
7. 返回截取的图片。

生成PDF方法`createPDF(paperRect:completion:)` 使用了 UIPrintPageRenderer 和 UIKit 框架中的 UIGraphics 中的功能。具体步骤如下：

1. 创建一个 UIPrintPageRenderer 实例，用于渲染和打印网页内容
2. 将 WKWebView 的内容添加到打印格式器中，以便可以将其打印或导出为 PDF。
3. 设置可打印区域，将其设置为与 A4 纸张大小相同，没有边距。
4. 开始 PDF 上下文，将数据对象、纸张大小和页面信息传递给 UIGraphicsBeginPDFContextToData 函数。
5. 准备绘制页面，指定要绘制的页面范围。
6. 获取 PDF 上下文的边界，这通常与设置的纸张大小相同。
7. 遍历所有页面，并逐一绘制。
8. 结束 PDF 上下文，此时所有页面都已绘制完成，PDF 数据已存储在 pdfData 中。
9. 返回生成的 PDF 文件路径。

## 4. 代码解析

### 4.1 WebViewCaptureUtil 类

`WebViewCaptureUtil` 类是一个 NSObject 的子类，遵循了 WKNavigationDelegate 协议。它包含了捕获网页截图所需的方法和属性。

```swift
class WebViewCaptureUtil: NSObject, WKNavigationDelegate {
    // ...
}
```

### 4.2 捕获方法

捕获方法 `capturePicShare(with:success:failure:)` 是类的核心方法。它负责初始化 WKWebView，加载网页请求，并处理成功和失败的回调。

```swift
func capturePicShare(with url: String, success: @escaping CapSuccessBlock, failure: @escaping CapFailureBlock) {
    // ...
}
```

### 4.3 WKNavigationDelegate 回调

`WebViewCaptureUtil` 类实现了 WKNavigationDelegate 协议的两个方法：`webView(_:didFinish:)` 和 `webView(_:didFail:withError:)`。这两个方法分别处理网页加载完成和加载失败的情况。

```swift
func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!) {
    // ...
}

func webView(_ webView: WKWebView, didFail navigation: WKNavigation!, withError error: Error) {
    // ...
}
```

### 4.4 截图函数

截图函数 `screenshot(scrollView:size:)` 是一个私有方法，它负责将 scrollView 的内容渲染为图片。

```swift
private func screenshot(scrollView: UIScrollView, size: CGSize) -> UIImage? {
    // ...
}
```

### 4.5 生成PDF文件函数

生成PDF文件是一个 WKWebView 的扩展函数，负责将 WebView 整页导出为 PDF 文件，默认为 A4 纸大小

```swift
/// 创建PDF文件
/// - Parameters:
///   - paperRect: 单页尺寸大小，例如 A4 纸的大小
///   - completion: 完成回调，当 PDF 创建完成后会调用此回调，并传入生成的 PDF 数据（如果成功）或 nil（如果失败）
func createPDF(paperRect: CGRect, completion: @escaping (Data?) -> Void) {
    // ...
}
```

### 4.6 生成结果

[截图](http://uimwiki.united-imaging.com/download/thumbnails/95400029/IMG_0070.JPG?version=1&modificationDate=1730103881713&api=v2)

[PDF](http://uimwiki.united-imaging.com/pages/viewpage.action?pageId=95400029&preview=%2F95400029%2F95400119%2F报告单+3.pdf)

### 4.7 完整代码 Demo

- **[完整代码 Demo](http://uimwiki.united-imaging.com/pages/viewpage.action?pageId=95400029&preview=%2F95400029%2F95400037%2FWebViewCapture.zip)**

- **核心代码**：
```
/**
 截取scrollView的内容为图片
 - parameter scrollView: 需要截取的scrollView。
 - parameter size: 截取图片的大小，如果为.zero，则使用scrollView的contentSize。
 - returns: 截取的图片。
 */
private func screenshot(scrollView: UIScrollView, size: CGSize) -> UIImage? {
    print("WebViewCaptureUtil info 正在截取scrollView的截图")
    
    UIGraphicsBeginImageContextWithOptions(size.width == 0 ? scrollView.contentSize : size, false, 0.0)
    defer { UIGraphicsEndImageContext() }
    
    let savedContentOffset = scrollView.contentOffset
    let savedFrame = scrollView.frame
    
    scrollView.contentOffset = .zero
    scrollView.frame = size.width == 0 ? CGRect(origin: .zero, size: scrollView.contentSize) : CGRect(origin: .zero, size: size)
    
    scrollView.layer.render(in: UIGraphicsGetCurrentContext()!)
    let image = UIGraphicsGetImageFromCurrentImageContext()
    
    scrollView.contentOffset = savedContentOffset
    scrollView.frame = savedFrame
    
    return image
}

// WKWebView 扩展：增加创建 PDF 文件的功能
extension WKWebView {
    /// 创建PDF文件
    /// - Parameters:
    ///   - paperRect: 单页尺寸大小，例如 A4 纸的大小
    ///   - margins: 页面的边距，默认 0
    ///   - fileURL: 可选的文件路径，默认保存到 Cache 目录
    ///   - fileName: 可选的文件名，默认使用网页的 title
    ///   - startPage: 可选的开始页码，默认从第一页开始
    ///   - maxPages: 可选的最大页数，默认打印全部页数
    ///   - completion: 完成回调，传入生成的 PDF 数据和文件路径
    ///   - error: 可选的错误回调，如果生成 PDF 过程中发生错误，将返回错误信息
    func createPDF(
        paperRect: CGRect,
        margins: UIEdgeInsets = .zero,
        fileURL: URL? = nil,
        fileName: String? = nil,
        startPage: Int = 0,
        maxPages: Int = Int.max,
        completion: @escaping (Data?, URL?) -> Void,
        error: ((String) -> Void)? = nil
    ) {
        // 计算打印区域，应用边距
        let printableRect = paperRect.inset(by: margins)
        
        // 使用 CustomPrintPageRenderer 来自定义页面尺寸和打印区域
        let printRenderer = PDFPrintPageRenderer(paperRect: paperRect, printableRect: printableRect, startPage: startPage, maxPages: maxPages)
        
        // 获取网页的标题作为默认文件名
        let defaultFileName = fileName ?? (self.title ?? "glocuse_report".localized) + ".PDF"
        
        // 使用 Cache 目录作为默认文件路径
        let defaultFileURL = fileURL ?? FileManager.default.temporaryDirectory.appendingPathComponent(defaultFileName)
        
        // 通过 WKWebView 的 viewPrintFormatter 获取打印格式器
        let printFormatter = self.viewPrintFormatter()
        printRenderer.addPrintFormatter(printFormatter, startingAtPageAt: 0)
        
        // 3. 设置 PDF 输出路径
        let pdfData = NSMutableData()
        UIGraphicsBeginPDFContextToData(pdfData, .zero, nil)
        
        // 4. 渲染 PDF
        for i in 0..<printRenderer.numberOfPages {
            UIGraphicsBeginPDFPage()
            printRenderer.drawPage(at: i, in: paperRect)
        }
        
        UIGraphicsEndPDFContext()
        
        // 5. 如果 PDF 数据有效，尝试将数据保存为文件
        if pdfData.length > 0 {
            do {
                try pdfData.write(to: defaultFileURL)
                // 调用完成回调，返回 PDF 数据和文件路径
                completion(pdfData as Data, defaultFileURL)
            } catch let saveError {
                // 如果保存失败，调用错误回调，传递具体错误信息
                completion(nil, nil)
                error?(saveError.localizedDescription)
            }
        } else {
            // 如果 PDF 数据为空，直接调用完成回调返回 nil
            completion(nil, nil)
        }
    }
}

// 自定义打印页面渲染器
class PDFPrintPageRenderer: UIPrintPageRenderer {
    private var customPaperRect: CGRect
    private var customPrintableRect: CGRect
    private var startPage: Int
    private var maxPages: Int
    
    // 初始化时传入纸张尺寸、打印区域、开始页码和最大页数
    init(paperRect: CGRect, printableRect: CGRect, startPage: Int, maxPages: Int) {
        self.customPaperRect = paperRect
        self.customPrintableRect = printableRect
        self.startPage = startPage
        self.maxPages = maxPages
        super.init()
    }
    
    // 使用计算属性来提供自定义的页面尺寸
    override var paperRect: CGRect {
        return customPaperRect
    }
    
    // 使用计算属性来提供自定义的可打印区域
    override var printableRect: CGRect {
        return customPrintableRect
    }
    
    // 设置打印的页数
    override var numberOfPages: Int {
        return min(super.numberOfPages - startPage, maxPages)
    }
    
    // 设置打印的起始页码
    override func drawPage(at index: Int, in rect: CGRect) {
        super.drawPage(at: index + startPage, in: rect)
    }
}
```

## 5. 风险项

1. 网络风险: 在网络较差的情况下，用户使用该工具截图或导出PDF文件将无法导出完整的页面内容。该工具完全依赖WKWebView所展示的内容，能看见的内容才能导出。
2. 系统和机型适配风险：虽然截图功能最低支持 iOS 2.0 系统，PDF导出功能最低支持 iOS 4.2 系统，但不同设备上导出的效果仍然需要通过兼容性测试一一验证，不保证所有系统所有机型都能导出一致的文件。
3. 系统资源风险：如果 H5 页面很长，不能保证低配机型能够完成耗费较大资源的截图或PDF生成任务。
4. PDF兼容性风险：不保证导出的PDF文件在任何设备的任何PDF阅读器上都能正常展示，需要经过不同系统（iOS、mac、android、windows等）、不同厂商、不同版本的PDF阅读器兼容性测试。

## 6. 总结

通过本次调研和实现，我们成功地开发了一个基于 WebKit 框架的长网页截图和PDF生成工具。该工具能够加载网页、调整视图大小、滚动内容并截取整个网页为一张图片，通过 UIPrintPageRenderer 导出 PDF 文件。