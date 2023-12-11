---
title: "Getting started with SwiftUI (Stanford CS193p)"
date: "2023-12-10"
author: "Komeno"
cover: "images/swiftui-intro-cover.png"
description: "This struct behaves like a view (the struct conforms to the View protocol) The View protocol requires you to have the body variable (of type some View)"
tags: [Swift]
---

This struct behaves like a view (the struct conforms to the `View` protocol)
The `View` protocol requires you to have the body variable (of type 'some View')

```swift
struct ContentView: View {
	var body: some View {
	
	}
}
```

The block (curly brackets) after the variable declaration represents a function (closure) and the value it returns gets assigned to the variable `body`. As long as it is a view (`VStack`, `Text`, `Image`, etc) it is ok to assign it. (note: writing the keyword `return` here is optional!)

```swift
struct ContentView: View {
	var body: some View {
		return Text("Hello World!")
	}
}
```

### Shapes
#### RoundedRectangle()

We can return the `RoundedRectangle()` view specifying its `cornerRadius` and calling modifier functions like `.padding()` (can receive an optional argument like `.horizontal`) and `.stroke()` (contrary to `.fill()`) to outline a shape (can receive an optional argument like `lineWidth: 3`).


```swift
struct ContentView: View {
	var body: some View {
		RoundedRectangle(cornerRadius: 20)
			.stroke()
			.padding()
	}
}
```

```swift
struct ContentView: View {
	var body: some View {
		RoundedRectangle(cornerRadius: 20)
			.stroke(lineWidth: 3)
			.padding(.horizontal)
			.foregroundColor(.red)
	}
}
```

Tip: You can write just `.red` instead of `Color.red` when defining the foreground color.

### View Combiners
#### The ZStack()

The `ZStack()` receives a closure as a parameter. (this closure is called a **View Builder**)

```swift
struct ContentView: View {
	var body: some View {
		ZStack(content: {})
	}
}
```

The **View Builder** allows us to list all the views we want to combine and turns it into another view. You also don't need to write `return` inside the **View Builder**.

```swift
    var body: some View {
        ZStack(content: {
            RoundedRectangle(cornerRadius: 20)
                .stroke()
                .padding()
            Text("Hello World!")
        })
    }
```

#### Modifying the View Combiner

Applying a modifier function to a view combiner will apply the properties to all the view within it.

```swift
    var body: some View {
        ZStack(content: {
            RoundedRectangle(cornerRadius: 20)
                .stroke()
            Text("Hello World!")
        })
        .padding()
        .foregroundColor(.red)
    }
```

Preview:

<img src="https://github.com/riceset/riceset.com/assets/48802655/0b64ae6f-4c52-4f91-ab17-230027e9daa0" width="500" height="1000">

#### Setting the alignment

```swift
    var body: some View {
        ZStack(alignment: .top, content: {
            RoundedRectangle(cornerRadius: 20)
                .stroke()
            Text("Hello World!")
        })
        .padding()
        .foregroundColor(.red)
    }
```

Preview:

<img src="https://github.com/riceset/riceset.com/assets/48802655/714a35d1-26e1-4092-8ca8-29213d99c471" width="500" height="1000">

#### Simplifying the code

When the last argument of a function is also a function you can simplify the code like this:

This code:

```swift
    var body: some View {
        ZStack(content: {
        })
    }
```

becomes:

```swift
    var body: some View {
        ZStack() {
        }
    }
```

You can also remove the parenthesis from the ZStack

```swift
    var body: some View {
        ZStack {
        }
    }
```
