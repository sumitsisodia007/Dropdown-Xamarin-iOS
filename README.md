![DropDown](Screenshots/logo.png)

[![Twitter: @banhmixaolan](http://img.shields.io/badge/contact-banhmixaolan-70a1fb.svg?style=flat)](https://twitter.com/banhmixaolan)
[![License: MIT](http://img.shields.io/badge/license-MIT-70a1fb.svg?style=flat)](https://github.com/AssistoLab/DropDown/blob/master/README.md)
[![Version](http://img.shields.io/badge/version-1-green.svg?style=flat)](https://github.com/AssistoLab/DropDown)
[![Nuget](http://img.shields.io/badge/Nuget-inProgress-red.svg?style=flat)](http://cocoadocs.org/docsets/DropDown/)


A Material Design drop down for iOS written in C#.
***

[![](Screenshots/1.png)](Screenshots/1.png)
[![](Screenshots/2.png)](Screenshots/2.png)
[![](Screenshots/3.png)](Screenshots/3.png)

## Demo

Do `pod try DropDown` in your console and run the project to try a demo.
To install [CocoaPods](http://www.cocoapods.org), run `sudo gem install cocoapods` in your console.

## Installation 📱

We support Xamarin.iOS. DLLs can be found in https://github.com/FPTInformationSystem/Dropdown-Xamarin-iOS/tree/master/FPT.Framework.iOS.UI.Dropdown/Lib/DLLs

### Nugets & Xamarin Components

Sorry, I am currently very busy. I will post it to Nuget and Xamarin Components as soon as possible

1. Add `pod 'DropDown'` to your *Podfile*.
2. Install the pod(s) by running `pod install`.
3. Add `import DropDown` in the .swift files where you want to use it

### Source files

A regular way to use DropDown in your project would be using Reference Assembly. There are two approaches, using source code and adding submodule.

Add source code:

1. Download the [latest code version](https://github.com/FPTInformationSystem/Dropdown-Xamarin-iOS/archive/master.zip).
2. Unzip the download file, add `FPT.Framework.iOS.UI.DropDown` project to your project folder

Add DLLs

1. Add the assembly `FPT.Framework.iOS.UI.Dropdown.DLL`

## Basic usage ✨

Currently, We don't support UIBarButtonItem yet!!!

```C#
var dropDown = new DropDown();

// The view to which the drop down will appear on
dropDown.AnchorView = new WeakReference<UIView>(view); // UIView or UIBarButtonItem

// The list of items to display. Can be changed dynamically
dropDown.DataSource = new string[] { "Car", "Motorcycle", "Truck" };
```

Optional properties:

```C#
// Action triggered on selection
dropDown.SelectionAction = (nint index, string item) =>
{
	Console.WriteLine("Select item: {0} at index: {1}", item, index);
};

// Will set a custom width instead of the anchor view width
dropDownLeft.Width = 200f;
```

Display actions:

```C#
dropDown.Show();
dropDown.Hide();
```

## Important ⚠️

Don't forget to put:

```C#
DropDown.StartListeningToKeyboard();
```

in your `AppDelegate`'s `DidFinishLaunching` method so that the drop down will handle its display with the keyboard displayed even the first time a drop down is showed.

## Advanced usage 🛠

### Direction

The drop down can be shown below or above the anchor view with:
```swift
dropDown.Direction = Direction.Any;
```

With `Direction.Any` the drop down will try to displa itself below the anchor view when possible, otherwise above if there is more place than below.
You can restrict the possible directions by using `Direction.Top` or `Direction.Bottom`.

### Offset

We currently have issue with AnchorView because C# does not allow Extension class + Implement interface for built-in UIKit's class.
So we use Swift version here instead. We are trying to improve this soon.

By default, the drop down will be shown onto to anchor view. It will hide it.
If you need the drop down to be below your anchor view when the direction of the drop down is `Direction.Bottom`, you can precise an offset like this:

```swift
// Top of drop down will be below the anchorView
dropDown.BottomOffset = new CGPoint(x: 0, y:(dropDown.anchorView?.plainView.bounds.height)!)
```

If you set the drop down direction to `Direction.Any` or `Direction.Top` you can also precise the offset when the drop down will shown above like this:

```swift
// When drop down is displayed with `Direction.top`, it will be above the anchorView
dropDown.topOffset = CGPoint(x: 0, y:-(dropDown.anchorView?.plainView.bounds.height)!)
```
*Note the minus sign here that is use to offset to the top.*

### Cell configuration

#### Formatted text

By default, the cells in the drop down have the `dataSource` values as text.
If you want a custom formatted text for the cells, you can set `cellConfiguration` like this:

```C#
dropDown.CustomCellConfiguration = (nint index, string item, DropDownCell cell) =>
{
  
}
```

#### Custom cell

You can also create your own custom cell, from your .xib file. To have something like this for example:
<br/>[![](Screenshots/3.png)](Screenshots/3.png)

For this you have to:

- Create a [`DropDownCell`](DropDown/src/DropDownCell.swift) subclass (e.g. *MyCell.swift*)
```swift
class MyCell: DropDownCell {
   @IBOutlet weak var suffixLabel: UILabel!
}
```
- Create your custom xib (e.g. *MyCell.xib*) and design your cell view in it
<br/>![](https://s3.postimg.org/8k3s2ya0z/custom_1.png)
- Link the cell in your xib to your custom class
<br/>![](https://s3.postimg.org/wcd3ehc1v/custom_2.png)
- At least have a label in your xib to link to the [`optionLabel`](DropDown/src/DropDownCell.swift#L14) `IBOutlet` in code (`optionLabel` is a property of `DropDownCell`)
<br/>![](https://s3.postimg.org/3o05b99vn/custom_3.png)
- Then, you simply need to do this:
```swift
let dropDown = DropDown()

// The view to which the drop down will appear on
dropDown.anchorView = view // UIView or UIBarButtonItem

// The list of items to display. Can be changed dynamically
dropDown.dataSource = ["Car", "Motorcycle", "Truck"]

/*** IMPORTANT PART FOR CUSTOM CELLS ***/
dropDown.cellNib = UINib(nibName: "MyCell", bundle: nil)

dropDown.customCellConfiguration = { (index: Index, item: String, cell: DropDownCell) -> Void in
   guard let cell = cell as? MyCell else { return }

   // Setup your custom UI components
   cell.suffixLabel.text = "Suffix \(index)"
}
/*** END - IMPORTANT PART FOR CUSTOM CELLS ***/
```
- And you're good to go! 🙆

For a complete example, don't hesitate to check the demo app and code.

### Events

```swift
dropDown.cancelAction = { [unowned self] in
  println("Drop down dismissed")
}

dropDown.willShowAction = { [unowned self] in
  println("Drop down will show")
}
```

### Dismiss modes

```swift
dropDown.dismissMode = .onTap
```

You have 3 dismiss mode with the `DismissMode` enum:

- `onTap`: A tap oustide the drop down is needed to dismiss it (Default)
- `automatic`: No tap is needed to dismiss the drop down. As soon as the user interact with anything else than the drop down, the drop down is dismissed
- `manual`: The drop down can only be dismissed manually (in code)

### Others

You can manually (pre)select a row with:

```swift
dropDown.selectRow(at: 3)
```

The data source is reloaded automatically when changing the `dataSource` property.
If needed, you can reload the data source manually by doing:

```swift
dropDown.reloadAllComponents()
```

You can get info about the selected item at any time with this:

```swift
dropDown.selectedItem // String?
dropDown.indexForSelectedRow // Int?
```

## Customize UI 🖌

You can customize these properties of the drop down:

- `textFont`: the font of the text for each cells of the drop down.
- `textColor`: the color of the text for each cells of the drop down.
- `backgroundColor`: the background color of the drop down.
- `selectionBackgroundColor`: the background color of the selected cell in the drop down.
- `cellHeight`: the height of the drop down cells.

You can change them through each instance of `DropDown` or via `UIAppearance` like this for example:

```swift
DropDown.appearance().textColor = UIColor.black
DropDown.appearance().textFont = UIFont.systemFont(ofSize: 15)
DropDown.appearance().backgroundColor = UIColor.white
DropDown.appearance().selectionBackgroundColor = UIColor.lightGray
DropDown.appearance().cellHeight = 60
```

## Expert mode 🤓

when calling the `show` method, it returns a tuple like this:

```swift
(canBeDisplayed: Bool, offscreenHeight: CGFloat?)
```

- `canBeDisplayed`: Tells if there is enough height to display the drop down. If its value is `false`, the drop down is not showed.
- `offscreenHeight`: If the drop down was not able to show all cells from the data source at once, `offscreenHeight` will contain the height needed to display all cells at once (without having to scroll through them). This can be used in a scroll view or table view to scroll enough before showing the drop down.

## Requirements

* Xcode 8+
* Swift 3.0
* iOS 8+
* ARC

## License

This project is under MIT license. For more information, see `LICENSE` file.

## Credits

DropDown was inspired by the Material Design version of the [Simple Menu](http://www.google.com/design/spec/components/menus.html#menus-simple-menus).

DropDown was done to integrate in a project I work on:<br/>
[![Assisto](https://assis.to/images/logouser_dark.png)](https://assis.to)

It will be updated when necessary and fixes will be done as soon as discovered to keep it up to date.

I work at<br/>
[![Pinch](http://pinch.eu/img/pinch-logo.png)](http://pinch.eu)

You can find me on Twitter [@kevinh6113](https://twitter.com/kevinh6113).

Enjoy!
