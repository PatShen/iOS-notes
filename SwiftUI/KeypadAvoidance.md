# Disable Keypad Avoidance

```Swift
var body: some View {
    GeometryReader { _ in
        VStack {
            //content
        }
        .sheet(isPresented: $showModal) {
            ModalWithTextFieldThatPushesUpParentContent()
        }
    }
    .ignoresSafeArea(.keyboard) //keyboard avoidance
}
```

This is especially annoying when a Sheet view has a textfield and the keyboard pushes the parent content up.

1. Need to add the ignore code to the parent view
2. Add a GeometryReader, even if you're not using it
3. If you are using a NavigationStack the GeometryReader needs to be inside the NavigationStack

[SwiftUI iOS14 - Disable keyboard avoidance](https://stackoverflow.com/a/77029231)
