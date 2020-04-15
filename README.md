![Swift](http://img.shields.io/badge/swift-5.0-brightgreen.svg)
![CI](https://github.com/michaeldorner/ByteBackpacker/workflows/CI/badge.svg) 
[![codecov](https://codecov.io/gh/michaeldorner/ByteBackpacker/branch/master/graph/badge.svg)](https://codecov.io/gh/michaeldorner/ByteBackpacker)
[![codebeat badge](https://codebeat.co/badges/390a34ec-d7ba-4165-bb38-c338247ec04a)](https://codebeat.co/projects/github-com-michaeldorner-bytebackpacker)
![CocoaPods](https://img.shields.io/cocoapods/p/ByteBackpacker.svg)
![CocoaPods](https://img.shields.io/cocoapods/v/ByteBackpacker.svg)
[![MIT licensed](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)
[![Swift Package Manager compatible](https://img.shields.io/badge/Swift%20Package%20Manager-compatible-brightgreen.svg)](https://github.com/apple/swift-package-manager)
[![Average time to resolve an issue](http://isitmaintained.com/badge/resolution/michaeldorner/bytebackpacker.svg)](http://isitmaintained.com/project/michaeldorner/bytebackpacker "Average time to resolve an issue")
[![Percentage of issues still open](http://isitmaintained.com/badge/open/michaeldorner/bytebackpacker.svg)](http://isitmaintained.com/project/michaeldorner/bytebackpacker "Percentage of issues still open")
<img src="https://raw.githubusercontent.com/michaeldorner/ByteBackpacker/master/icon.png" width="200" align="right">

# ByteBackpacker

> ByteBackpacker is a small utility written in pure Swift to pack value types into a `Byte`¹ array and unpack them back. 

Additionally, there is a [`Data`](https://developer.apple.com/reference/foundation/data) (formerly `NSData`) [extension](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Extensions.html) to convert `Data` objects into a `Byte` array. 


¹ `Byte` is a [`typealias`](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Declarations.html#//apple_ref/doc/uid/TP40014097-CH34-ID361) for `UInt8`.

## Table of Contents

- [Installation](#installation)
- [Usage](#usage)
- [API](#api)
- [Discussion](#discussion)
- [Contributions](#contributions)
- [Versions](#versions)
- [License](#license)


## Installation

You have three options:

1. Use [Swift Package Manager](https://swift.org/getting-started/#using-the-package-manager): 

        import PackageDescription

        let package = Package(
            name: "TestImport",
            dependencies: [
                .Package(url: "https://github.com/michaeldorner/ByteBackpacker.git")
            ]
        )

2. Use [Cocoapods](https://cocoapods.org): `pod 'ByteBackpacker'`
3. Copy the [`ByteBackpacker.swift`](https://github.com/michaeldorner/ByteBackpacker/blob/master/Sources/ByteBackpacker/ByteBackpacker.swift) file (containing the `ByteBackpacker` class) to your  project (not considered as good practice).

Although it can be used in the same way in Objective-C, I had clearly Swift projects in mind. The easiest way for Objective-C users is to embed the `ByteBackpacker.framework`. Of course, Swift users can also do this, but actually I do not see any advantages.


## Usage
Important for a proper usage: **ByteBackpacker does only support value types (e.g. numbers, structs, ...), but no reference types (e.g. classes)!** For further information see [Discussion](#discussion).

All examples can be seen running in the [`ByteBackpackerPlayground.playground`](ByteBackpackerPlayground.playground). Let's have a look on some general use cases:

#### From `Double` to `[Byte]`
```
let aDouble: Double = 1.0
let aByteArray: [Byte] = ByteBackpacker.pack(aDouble)
```

#### From `[Byte]` to `Double`
```
let option_1: Double = ByteBackpacker.unpack(aByteArray)
let option_2 = ByteBackpacker.unpack(aByteArray) as Double
let option_3 = ByteBackpacker.unpack(aByteArray, toType: Double.self)
```

#### From `Double` to `Data` to `[Byte]` to `Double`
```
var anotherDouble: Double = 2.0
let data = Data(bytes: &anotherDouble, count: MemoryLayout<Double>.size)
var byteArrayFromNSData = data.toByteArray()
let doubleFromByteArray = ByteBackpacker.unpack(byteArrayFromNSData, toType: Double.self)
```

#### From `[Byte]` to `Data`
```
let anotherByteArray: [Byte] = [0, 0, 0, 0, 0, 0, 8, 64]
let dataFromByteArray = Data(bytes: anotherByteArray)
```


## API

`Byte` is a [`typealias`](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Declarations.html#//apple_ref/doc/uid/TP40014097-CH34-ID361) for `UInt8`.

`ByteOrder` is an `enum` for [Little Endian and Big Endian](https://en.wikipedia.org/wiki/Endianness). Furthermore, there is the option for asking the platform you are using for the native byte order of the system: `ByteOrder.nativeByteOrder`. By default `.nativeByteOrder` is applied for packing and unpacking. 

For packing value types into a `[Byte]`, use

```open class func pack<T: Any>( _ value: T, byteOrder: ByteOrder = .nativeByteOrder) -> [Byte]```

For unpacking a `[Byte]` into a value type, use either

```open class func unpack<T: Any>(_ valueByteArray: [Byte], byteOrder: ByteOrder = .nativeByteOrder) -> T```

or otherwise, if you want to use type inference

```open class func unpack<T: Any>(_ valueByteArray: [Byte], toType type: T.Type, byteOrder: ByteOrder = .nativeByteOrder) -> T```


## Discussion

### Generics
There is no way for specifying value types in Swift's generics (see [here the discussion on stackoverflow](http://stackoverflow.com/q/28782532/1864294)). It would be awesome to specify our methods like `func (un)pack<T: Any where T: ~AnyClass>(...)` to let the compiler check for value types. So far ByteBackpacker ensures the value type with `assert(...)`. 

An open question is how to test the `assert(...)` for this value type check. 

I would love to improve this project. Tell me your ideas, here in github, via mail or in [codereview.stackexchange.com](http://codereview.stackexchange.com/questions/114730/type-to-byte-array-conversion-in-swift).


## Contributions

### To-Do

- [x] Find a solution for making sure, that `T` is a value type, but not a reference type 
- [x] Add documentation to the source code for a nice Xcode integration
- [ ] Find a solution for testing `assert()`

### Acknowledgements

Many thanks to 
* [Martin R](http://codereview.stackexchange.com/users/35991/martin-r) for [his suggestions in codereview.stackexchange.com](http://codereview.stackexchange.com/a/114738/61640) 
* [iCodist](https://github.com/iCodist/ByteBackpacker) for his update to Swift 3 and Xcode 8 
* [vaggos666](https://github.com/michaeldorner/ByteBackpacker/issues/4) for his remarks to Swift 4


## Versions
(Un)fortunately there is a lot of work going on Swift. This made larger changes to ByteBackpacker needed. The following table shows the compatibility.

| Swift version |  2.0 |  3.0 | 4.0 and later
| :-: | :-: | :-: | :-: |
| ByteBackpacker 1.0 | ✓ | ✗ | ✗
| ByteBackpacker 1.1 | ✗ | ✓ | ✗
| ByteBackpacker 1.2 and later | ✗ | ✓ | ✓

Hopefully the APIs will be stable now.

## License 

ByteBackpacker is released under the MIT license. See [LICENSE](LICENSE) for more details.
