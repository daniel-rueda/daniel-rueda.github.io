---
layout: post
title: About protocol extensions
---

### TLDR;
> IF the inferred type of a variable is the protocol:  
— AND the method is defined in the original protocol  
— — THEN the runtime type’s implementation is called, irrespective of whether there is a default implementation in the extension.  
— AND the method is not defined in the original protocol,  
— — THEN the default implementation is called.  
ELSE IF the inferred type of the variable is the type  
— THEN the type’s implementation is called.

## What?

For my current project I defined a protocol (with a default implementation) to represent a network request
```swift
protocol NetworkRequest {
    var baseURL: URL { get }
    var method: HTTPMethod { get }
    var path: String { get }
    var parameters: [String: String] { get }
    var headers: [String: String] { get }
}

// Default implementation
extension NetworkRequest {
    var method: HTTPMethod { return .get }
    var path: String { return "" }
    var parameters: [String: String] { get }
    var headers: [String: String] { return [:] }
}
```

The protocol is used as a type in my request manager
```swift
extension RequestManager {
    func send(request: NetworkRequest,
              completion: @escaping NetworkResponseHandler) {
        // implementation details
    }
}
```

For a particular feature a coworker created a request defining `parameters` as a dictionary of array of strings
```swift
struct FulfillOrderRequest: NetworkRequest {
    let baseURL: URL
    let parameters: [String: [String]] // instead of `[String: String]`

    init(order: Order) {
        // implementation details
    }
}
```

Xcode is able to compile and build the app but when the request manager tries to create the url `parameters`'s default implementation is returned (meaning parameters' dictionary contains _strings_, not the expected _array of strings_).  

## Why?
Because Swift erases the information about the concrete type when is being pass as parameter to the request manager and the default implementation is used instead.  
I was going to write how Swift's method dispatch works but I remembered I had read about it.

### LINKS:  
- [The Ghost of Swift Bugs Future](https://nomothetis.svbtle.com/the-ghost-of-swift-bugs-future)  
- [Swift protocol extension method dispatch](https://medium.com/ios-os-x-development/swift-protocol-extension-method-dispatch-6a6bf270ba94)  
- [Universal dynamic dispatch for method calls](https://lists.swift.org/pipermail/swift-evolution/Week-of-Mon-20151207/001922.html)  
