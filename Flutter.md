# Flutter

* Flutter apps have a so-called **Widget tree** structure, which starts with Material App (not necessarily), then we can add components under this root


# Setup

* We need to install the Flutter SDK from its official website, after that we need to extract the zip file, no installation needed, & finally add that into our path

* We also need to install `git` for some reason

* Next install Android studio. After installing, go to the plugins manager & install the flutter plugin. If we see a **New Flutter Project** this means that the installation was successful

* Running `flutter doctor` tells us if we are ready for application development or some requisites are missing, we can then fix them


# Creating a project

* In android studio, after installing the plugin, we will see a **New Flutter Project** button. click on that

* In the opened window, on the left side, select flutter. Provide the path for flutter SDK, on the next window give a name to the application & in the **Project type** select **Application** & finally click on create

# Starting to write code

* If we create a new project, we will by default have some sample code, which creates a button clicker app, remove everything from this **main.dart** file

* Firstly we will need to import **material** package, which is used for adding the material design which is just a design style (just like bootstrap XXX), which was developed by Google [material](http://material.io)

```dart
import 'package:flutter/material.dart';
```

* Now we need to create a **main()** method which will create the UI & run out application

```dart
void main() {
  runApp(MyApp());
}
```

* The **runApp** function just runs our application

* To write text to the application, we will use the **Text** widget

```dart
void main() {
  runApp(
    MaterialApp(home: Text("Hello World"))
  );
}
```

* The above code will write the string "Hello World" onto the screen on the top-left corner, because of its default behavior.

* To center this text, we will wrap it under another widget known as **Center**, this will center the child of it vertically & horizontally.

```dart
MaterialApp(home: Center(child: Text("Hello World")))
```

* We can also use `const` before certain widgets just to improve performance a little bit

## Scaffold Widget

* The scaffold widget allows us to have a systematic layout of the app.
* Scaffolds can have properties like appBar (the bar on top), body, buttons, etc.

```dart
MaterialApp(
  home: Scaffold(
    backgroundColor: Colors.blueGrey.shade500,
    appBar: AppBar(
      foregroundColor: Colors.black, // Color of the text on app bar
      title: Text("My Application"),
    ),
    body: Image(
      image: NetworkImage('url_of_image_here'),
    ),
  )
)
```

# Adding assets

* For adding assets into our application, firstly we would need to copy the assets into our application project.

* Then we need to make an entry into the **pubspec.yaml** file like so

```yaml
...
flutter:
  ...
  assets:
    # Including a specific file
    - images/image.jpeg
    # Including the whole images directory / at the end is important
    - images/
    ...
```

* Then a message will show up in Android Studio, in that click get packages

* Finally, we can directly use these assets in our application

```dart
Image(image: AssetImage("images/image1.png"));
```


# Changing icon of our application

* By default our application will have the flutter icon, if we want to change it, we have to change the icon from android as well as ios folder in our project
* Firstly, we need to convert our image into a icon, we can use [this website](https://appicon.co) for that
* After downloading the converted files, we get a directory structure one for each platform
* Now we can move these files according to the platform. Android icons in the android/src/main/res/* & for iOS the directory will be ios/Runner/* & delete the previously present folders in those directories

* In newer versions of android, the app icons are icon images surrounded by a white circular background, we can keep the app icon like that, or we can change our app icon to be circular

* For that, right click on project -> Flutter -> Open Android Module, in the opened window, expand the app folder & right click on app-> New -> Image Asset, a new window will popup, here we can specify the path of our icon & resize our icon, finally we can click on next & then finish.


# Deploying app to physical device

* Enable **Developer Options** on the android phone.
* In that, turn on **USB Debugging**.
* Connect the phone to the Computer using a USB cable.
* In the mobile phone, there will be a prompt to allow **USB debugging**, click yes
* Finally in android studio, we can select physical device from the drop-down & run the app on it. Physical device also supports Hot reloading & restarting

# Hot reload

* Flutter apps support hot reload, whenever we modify and save the source files, android studio will hot reload the app.
* But for using this feature, we need to use **Stateless Widgets**, which can be done by creating a class which inherits the **StatelessWidget** class & inside of that the build method needs to be overridden

```dart
void main() {
  runApp(MyApp())
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      ...
    );
  }
}
```

* Whenever we hot reload our application, it will run the build method again, so the changes will be propagated instantaneously. It will only modify the changes made in the source & keep everything as it is

# Flutter Layout widgets

* By default, whatever widgets we create, are created on the top-left side of the application, colliding with the notification bar, to keep our content within the app's area, i.e not touch the notch or bottom navigation buttons, we need to wrap them around a **SafeArea** widget
* **Container** widget is similar to a **\<div\>** tag in HTML
* If this container has no child element, it will take the whole possible space.
* We can define the width & height properties of the Container as well
* Similar to CSS, we can set margin & padding for our container, to make have some space around the element. The parameter to it is a EdgeInsets
* Margin is used for moving the container around, Padding is used for moving the content inside of the container around

```dart
Scaffold(
  child: Container(
    width: 100.0,
    height: 200.0,
    margin: EdgeInsets.LTRB(left: 10.0, right: ...),
    padding: EdgeInsets.only(top: 20.0),
    child: Text("Some Element text"),
  )
)
```

## Layout multiple child widgets

* By default, Container's child can be only one widget, but we can have multiple children by using Column or Row widgets

```dart
SafeArea(
  child: Column(
    children: <Widget>[
      Container(...),
      Container(...),
      ...
    ],
  )
)
```

* The Column or the Row will take up the entire height or width, respectively, if we want to only allow it to take up the height/width of all the children combined, we can use the **mainAxisSize** & set its value to be **MainAxisSize.min**

* We can also change the direction of how the elements are stacked, i.e., from top to bottom (default) or bottom to top by using the property **verticalDirection: VerticalDirection.up**

* To justify children around the container, we can use the **mainAxisAlignment** property, which is kinda similar to **flex** property of CSS & we can specify properties like **MainAxisAlignment.spaceEvenly**

* The children inside of this Column will be from left hand side of the screen, if we want them to start from right hand side, we will first define an invisible Container & set its height to be infinity, that way the Column widget will take up the whole width of the screen, then we can use the **CrossAxisAlignment** property to **CrossAxisAlignment.end**

```dart
Column:
  // Align the contents from the right
  CrossAxisAlignment: CrossAxisAlignment.end,
  // Align the contents from the bottom
  MainAxisAlignment: MainAxisAlignment.end,
  children: Widget>[
  Container(...)
  Container(...)
  ...
  Container(
    // Will take up the whole width of the screen
    width: double.infinity;
    height: 0.0
  )
]
```

* To make our containers take up the whole width or height of the screen, we can specify their width/height to be `double.infinity`, or we can just set the **CrossAxisAlignment** property to have the value **CrossAxisAlignment.stretched**

# Drawing icons

* We can use the **Icon** widget, which takes a **Icons** value, to draw an icon on the application, like **Icons.phone**, **Icons.add**, etc.

```dart
Icon(Icons.add, color: Colors.Red, ...)
```

# Adding space between elements

* For adding space between elements, on options is that we can insert a **Container** widget between two widget & then define the width/height to have some space between them, but we can also use a **SizedBox** widget, which basically is used for creating space between widgets


# Card & Padding Widget

* The **Card** widget can be used to create beautiful card widgets, which have rounded borders, shadow underneath them, etc, **Card** is basically a **Container** widget with the addition of some styling

* The one downfall of **Card** widget is that they doesn't have a padding property, to specify one anyway, we can use the **Padding** widget

```dart
Card(
  child: Padding(
    padding: EdgeInsets.all(15.0),
    children: [
      ...
    ]
  )
)
```

# ListTile

* When we want to create a info box, which has an icon on the left/right of a text box & in the middle some text, or anything, we can draw this using **Container**, **Row**, **Icon** & **Text**, or we can use the **ListTile** widget to get the same effect

```dart
Card(
  child: ListTile(
    leading: Icon(
      ...
    ),
    title: Text(
      ...
    ),
    ...
  )
)
```

# Divider widget

* As in HTML, we have the \<hr /\> tag, flutter has a **Divider** widget, which draws a horizontal line on the screen.

# Random Points

* After installing dart plugin, we also get the dartfmt plugin installed in our android studio, which helps us reformat code better, but to use it effectively, we need to add commas (,) after every widget/parenthesis, so that it can easily & correctly format the code

* [Material Palette](https://materialpalette.com) can be used for finding theme colors for our application, similar to what we used while deciding colors for games.

