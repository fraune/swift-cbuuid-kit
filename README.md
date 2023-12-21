# CBSafeKit

CBSafeKit is a lightweight helper package, to help Swift programmers safely instantiate various CoreBluetooth objects.

## The problem

Instantiating some CoreBluetooth objects can cause your program to raise an NSInternalInconsistencyException, which cannot be handled in a Swift do-catch. Unfortunately, there is very limited documentation for what is/isn't valid input for these types. As such, you might be tempted to write a lot of code to attempt to validate objects that _could_ be valid.

This package solves that problem, by using Objective-C to bridge dangerous instantiations into safe Swift interfaces. Using this library, you are free to attempt all the invalid instantiations you like, and can easily handle the results in a "Swift" way.

## How to use

### 1. Add this repository as a SPM project/package dependency in Xcode:
```
https://github.com/fraune/CBSafeKit
```

### 2. Import the package and instantiate a CBUUID
Example of using this package to safely create a CBUUID
```swift
import CoreBluetooth
import CBSafeKit

func createMutableService(_ fromUUIDString: String) throws -> CBMutableService {
    guard let cbuuid = fromUUIDString.asCBUUID else {
        throw NSError()
    }
    return CBMutableService(type: cbuuid, primary: true)
}
```

## Documented Dangers

### [CBUUID.init(string: String)](https://developer.apple.com/documentation/corebluetooth/cbuuid/1519025-init)

The following inputs will cause this method to throw a `NSInternalInconsistencyException`:
* If `string` length is 4 or 8:
    * It is not entirely hexadecimal (case insensitive)
* Or, if `string` length is 36:
    * It does not contain dashes at indices: 8, 13, 18, and 23
    * All other characters are not hexadecimal (case insensitive)
* Else, the error will be thrown

### [CBMutableService.init(type: CBUUID, primary: Bool)](https://developer.apple.com/documentation/corebluetooth/cbmutableservice/1434330-init)

The following inputs will cause this method to throw a `NSInternalInconsistencyException`:
* A valid CBUUID with a `data.length` that is not 2 or 16
    * Specifically, a valid CBUUID with `string` length of 8 may be instantiated, but will cause this constructor to throw the error
        * e.g. `CBUUID(string: "ABCD1234")`


## Contributing

This package is simple, but contributer-friendly. I don't have a template or style guide right now, so if you would like to contribute, reach out.

### Scope

Keep the scope of this package limited to CoreBluetooth "safety" helpers.

### Documentation

If you can add to or refine any of the **Documented Dangers**, please open a PR with a documentation update and unit test.

## License

https://github.com/fraune/swift-cbuuid-kit/blob/main/LICENSE

9CD667AB-FD28-43BF-BBDF-70F168736BEB