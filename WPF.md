# Windows Presentation Foundation

# Creating the project

* In Visual Studio we have to select the `WPF Application` template for creating a WPF Project

# Structure of project

1. `App.xaml`: This file contains data related to the application such as the startup file, resources, etc. It is not a window displayed to the UI.
2. `App.xaml.cs`: The code-behind file for the App.xaml file
3. `MainWindow.xaml`: The main UI displayed to the user. It is of type `Window`
4. `MainWindow.xaml.cs`: The code-behind for the main window, it also implements the Window

* For each `.xaml` file we have a corresponding `.cs` file as well. In the c# file we can see that the class that we are implementing is a `partial` class & it is also used in the xaml file. This indicates that both the xaml & cs file are working together to implement a class

# XAML

* Extensible Application Markup Language.
* XAML is very similar to XML format in the sense that it has elements which have opening & closing tags.
* If we dont want to nest tags inside of an element, we can skip the closing tag by closing the tag in the opening tag itself by ending it with a `/>` instead of `>`
```xml
<Page>
    <Grid>
        <TextButton Text="Sample Text" />
    </Grid>
</Page>
```

* We can either set the values in the opening tag itself, or we can do it inside of the tag.
```xml
<Label Content="Some Content">
    <Label.Margin>10</Label.Margin>
</Label>
```

## Grid

* Allows us to arrange elements in the form of rows & columns
```xml
<Grid>
    <Grid.ColumnDefinitions>
        <!-- Setting the width as a constant value -->
        <ColumnDefinition Width="100" />

        <!-- Takes up only the necessary width -->
        <ColumnDefinition Width="Auto" />

        <!-- Takes up the entire width possible -->
        <ColumnDefinition Width="*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="2*"/>
        <RowDefinition Height="*"/>
        <RowDefinition Height="3*"/>
    </Grid.RowDefinitions>
</Grid>
```

## StackPanel

* Stacks the elements one after the other.
* If we do not use this, then every element will be stacked on top of each other & will also take up the whole height & width

## TextBox

* Used for taking user input

## Xaml to C# linking

* The elements that we define in the xaml file are also accessible in the code behind. This is because they are defined in the `obj\Debug\MainWindow.g.i.cs`

* Inside that file we see that the `MainWindow` class is also partially defined in that, in that class are our elements declared


## Arranging Elements inside of the grid

* For arranging elements, we first need to define how many rows & columns will be in the grid using the `RowDefinitions` & `ColumnDefinitions` properties
* The following grid has 3 columns & 3 rows. All the 3 columns are of same width, while the 3 row are of different size each. Instead of dividing the grid in 3 parts, it will instead be divided into 6 parts, from which the first two parts will be given to first row, second will have 1 part & the last one will consist of 3 parts
```xml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="*" />
        <ColumnDefinition Width="*" />
        <ColumnDefinition Width="*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="2*"/>
        <RowDefinition Height="*"/>
        <RowDefinition Height="3*"/>
    </Grid.RowDefinitions>
</Grid>
```

* After defining out grid, we can put elements inside of the cells formed
```xml
<Grid>
    <Grid.ColumnDefinitions>
        ...
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        ...
    </Grid.RowDefinitions>

    <!-- Will be displayed in 1st column & 2nd row -->
    <Label Grid.Column="1" Grid.Row="2" Content="Some Content">
</Grid>
```

# Styling

## Static Resources

* Just like in html we can have some styling defined using css which will be applied, we can do similar in xaml as well.

* We have to define properties inside of the `<Window.Resources>` tag

```xml
<Window.Resources>
    <!-- The Key is the name of the stying resource -->
    <SolidColorBrush x:Key="buttonColor" Color="Red">
</Window.Resources>
```

* For using this resource, we can use `{StaticResource StyleResourceName}`

```xml
<Button 
    Background={StaticResource buttonColor}
/>
```

## Application-Wide Styling 

* Static resources are defined & used inside of the same file & cannot be reused in other files.

* To define resources that are accessible in all the files, we have to define them in the `App.xaml` file, inside of the `<Application.Resources>` tag 

## Implicit Styles

* We can apply style to a specific type of element in xaml, for example to all the buttons, or lables, etc.
* It has the following syntax

```xml
<Style TargetType="ElementType">
    <Setter Property="PropertyName" Value="PropertyValue" />
    <Setter Property="PropertyName" Value="PropertyValue" />
    ...
</Style>
```


```xml
<Application.Resources>
    <Style TargetType="Button">
        <Setter Property="Foreground" Value="Black">
    </Style>
</Application.Resources>
```

## Explicit Styles

* We can define named styles which can be applied to elements

```xml
<Application.Resources>
    <!-- The Key is the name of the stying resource -->
    <SolidColorBrush x:Key="buttonColor" Color="Red">

    <Style TargetType="Button" x:Key="NumberButtonStyle">
        <Setter Property="Foreground" Value="Black">
        <Setter Property="Margin" Value="5">
    </Style>

    <!-- Derives properties from `NumberButtonStyle` using BasedOn Property -->
    <Style TargetType="Button" x:Key="NonNumberButtonStyle" BasedOn="{StaticResource NumberButtonStyle}">
        <!-- Overwriting few properties -->
        <Setter Property="Foreground" Value="Green">
    </Style>
</Application.Resources>
...

<Button Style="{StaticResource NumberButtonStyle}"/>

```

# Window

## Creating new windows

* We can create multiple windows in our WPF application.
* This can be done by `right-clicking our solution -> Add -> Window`. 

## Displaying the new window

### Window.Show

* After creating this window, we can popup our new window from the main window. For that we first need to create a instance of the newly window, then we can use the `Show` method which will open our new window.

```c#
public partial class MainWindow : Window
{
    ...
    private void NewTaskOpenButton_Click(object sender, RoutedEventArgs e)
    {
        NewTaskWindow newTaskWindow = new NewTaskWindow();
        newTaskWindow.Show();
    }
}
```
### Window.ShowDialog

* While using the `Show` method, the new window will show up as another window & the main window is interactable at the same time.

* If we want to disable our main window while the new window is open, we can instead use the `ShowDialog` method

* This method doesn't return until the new window is closed

```c#
public partial class MainWindow : Window
{
    ...
    private void NewTaskOpenButton_Click(object sender, RoutedEventArgs e)
    {
        NewTaskWindow newTaskWindow = new NewTaskWindow();
        newTaskWindow.ShowDialog();
    }
}
```

### Window Position

* By default the new window opening can open anywhere decided by our OS, but what if we want to display it on top of our main window.

* For doing that we first have to set the owner of the new window to be our main window, & then from there we can set the new window's relative positioning

```c#
public EditTaskWindow()
{
    InitializeComponent();

    // Setting the owner
    Owner = Application.Current.MainWindow;

    // Setting the position of the new window to be at the center of main window
    WindowStartupLocation = WindowStartupLocation.CenterOwner;
}

```

### Window.Close

* For closing the window, we can use the `Close` method from either another window or the same window itself.

# Adding Database Support to our application

* For adding database support, we are going to install the package `sqlite-net-pcl` from Nuget package manager.

## Creating tables

* For creating tables, we need to create models the same as we create in ASP .NET Applications

* We can to create a class file inside a folder named Models like the following.

```c#
using SQLite;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace TasksApp.Models
{
    internal class Task
    {
        [PrimaryKey]
        [AutoIncrement]
        public int Id { get; set; }
        public string Name { get; set; }
        public string Description { get; set; }
        public string Importance { get; set; }
    }
}
```

* Because we are using a DB, we need to have a unique identifier (primary key) for each record, so we must have an `Id` propety in our class

## Establishing connection to DB

* We can establish connection to database using `var conn = new SQLiteConnection("AbsolutePathToDatabaseFileOrConnectionString")`.

## CRUD Operations

### Create

* For inserting to database, we can use the `Insert` method & pass in the data to be inserted. The sqlite engine will automatically determine which table to insert this data to

```c#
private void InsertToDb()
{
    ...
    // Establish connection to the database
    using (SQLiteConnection sqlConn = new SQLiteConnection(Constants.DatabaseName))
    {
        // Create the MyTask table if it doesn't exist
        sqlConn.CreateTable<MyTask>();

        // Insert the new task into the table
        sqlConn.Insert(task);
    }
}
```

### Read

```c#
private void ReadTasksDb()
{
    using (SQLiteConnection sqlConn = new SQLiteConnection(Constants.DatabaseName))
    {
        // Create the MyTask table if it doesn't exist
        // Done just as a measure to prevent the exception
        sqlConn.CreateTable<MyTask>();

        // Prepare the sql SELECT query
        var tasksQuery = sqlConn.Table<MyTask>();

        // Return all the data from the MyTask Table
        var tasks = tasksQuery.ToList();
    }
}
```

# ListView

* Used for displaying a list of data from the code behind

* In the xaml we need to first create the `<ListView>` tag & then from the code behind we can add elements to this listview using the foll code

```c#
// More prefarable, this allows us to bind data
myListView.ItemsSource = myTaskObjList

// Or
// Data binding doesnt work when using this
myListView.Items.Add(new ListViewItem(){
    Content = myTaskObj
});
```

* When assigning the task object, it will not display the data but will call the `ToString` method on the object. We can override this behaviour by overriding the ToString method of the class by navigating to the class & writing the ToString method

```c#
using SQLite;

namespace TasksApp.Models
{
    internal class MyTask
    {
        [PrimaryKey]
        [AutoIncrement]
        public int Id { get; set; }
        public string? Name { get; set; }
        public string? Description { get; set; }
        public string? Importance { get; set; }

        public override string ToString()
        {
            return $"[{Importance}] {Name}: {Description}";
        }
    }
}
```

# Data binding

* Data binding is a concept by which we can bind data present in either backend or frontend to its corresponding other side. Whenever data is changed in one of the places, it will be reflected on other side automatically.

* General syntax of binding is

```xml
    <Label Content="{Binding SomeSourceFromBackend}" />
```

## Binding to a source

1. Path
2. Mode
  1. OneWay - from source to receiver (Backend -> UI) (displaying data from backend to the UI)
  2. TwoWay - from source to receiver & back (Backend <-> UI) (like a user entering text in a textbox)
  3. OneWayToSource - from receiver to source (UI -> backend)
  4. OneTime

## Binding data from a variable

* For binding variables, first we need to create a class variable which we will modify it from code-behind, and a property which will get & set the class variable.

```c#
private string _someProperty;
public string SomeProperty {
    get
    {
        return _someProperty;
    }
    set
    {
        _someProperty = value;
    }
}
```

* For the data-binding to work correctly it should follow the following rules:
  1. The property should be `public`.
  2. The class containing the property should inherit from `INotifyPropertyChanged` class.
  3. If not, the property should be a dependency property.

* If the property is not a dependency property, we need to implement the `NotifyPropertyChanged` method.

```c#
    public event PropertyChangedEventHandler? PropertyChanged;

    public void NotifyPropertyChanged(string propName)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propName));
    }
```

* Finally whenever we modify/set the value of the property, we need to call this method.

```c#
using System.ComponentModel;
using System.Windows;

namespace WPF_BackgroundWorkers
{
    /// <summary>
    /// Interaction logic for MainWindow.xaml
    /// </summary>
    public partial class MainWindow : Window, INotifyPropertyChanged
    {
        public event PropertyChangedEventHandler? PropertyChanged;

        public void NotifyPropertyChanged(string propName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propName));
        }

        private string _someProperty = "";
        public string SomeProperty
        {
            get
            {
                return _someProperty;
            }
            set
            {
                _someProperty = value;

                NotifyPropertyChanged(nameof(SomeProperty));

                // Or this
                // NotifyPropertyChanged("SomeProperty");
            }
        }
    }
}
```

* And in the front-end

```xml
    <Label Content={Binding SomeProperty} />
```

* Now whenever there is a change in the `SomeProperty`, it will be automatically & instantly propagated in the front-end UI.


## Binding data in a ListView

* We can bind data into a list view which will dynamically create element for us with the data provided

```xml
<ListView>
    <ListView.ItemTemplate>
        <DataTemplate>
            <StackPanel>
                <!-- `Name`, `Description` & `Importance` is the fields of out model -->
                <TextBlock Text="{Binding Name}"/>
                <TextBlock Text="{Binding Description}"/>
                <TextBlock Text="{Binding Importance}"/>
            </StackPanel>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

* Notice here we are not specifying from which table it should show the data, it is because we can just bind whatever object we want, & if it contains the properties binded, it will display it.

* From the code behind we can fetch the records & directly assign them to our listview's `ItemSource` now

```c#
public void ShowTasksFromDatabase()
{
    List<MyTask> tasks = new();

    using (SQLiteConnection conn = new(Constants.DatabasePath))
    {
        // Create table if it doesnt exist
        conn.CreateTable<MyTask>();

        // Fetch the tasks from DB
        tasks = conn.Table<MyTask>().ToList();
    }

    // Assign the tasks to the listview
    TasksList.ItemsSource = tasks;
}
```

* We can apply searching & sorting to these elements by using a textbox & implementing its `TextChanged` event handler & whenever the user enters some data, we can filter out the data of the listview from code behind by either saving the prefetched records from the DB, storing it in a variable & then applying filtering, or we can fetch the records again from the DB with the filtering applied on the fetching query itself

## Binding data in a GridView

* Similar to ListView, we can bind the data to a gridview

```xml
<ListView x:Name="ListView1">
    <ListView.View>
        <GridView>
            <GridViewColumn
                Header="Header text 1"
                DisplayMemberBinding={Binding Property1} />
            <!--
                StringFormat specifies how the data will displayed,
                we can even have format strings there like "Data is: {0}"
                Here P2 means display the data as percentage with 2 digits after decimal
            -->
            <GridViewColumn
                Header="Header text 2"
                DisplayMemberBinding={Binding Path=Property2, StringFormat=P2} />
            ...
        </GridView>
    </ListView.View>
</ListView>
```

```c#
public static void SomeFunc
{
    // SomeDataSource must have the properties `Property1` & `Property2`
    ListView1.ItemsSource = SomeDataSource;
}
```

# Custom User Control

* User controls allows us to create an element or a group of elements once & then we can reuse that same block of markup in many different places, just like we create functions in our code.

## Creating user control
* It is a good practice to store controls in a folder named as `Controls`.

* For creating a control, `Right Click -> Add -> User Control`.

* Now on the created control page, we can add our own markup

```xml
<UserControl x:Class="TasksApp.Controls.TaskControl"
        ...>
    <Grid>
        <StackPanel>
            <TextBlock Foreground="DodgerBlue" FontSize="25" Text="{Binding Property1}"/>
            <TextBlock Foreground="CornflowerBlue" Text="{Binding Property2}"/>
            <TextBlock Foreground="DarkRed" Text="{Binding Property3}"/>
        </StackPanel>
    </Grid>
</UserControl>
```

## Using the control
* For using this control, we need to first import its namespace in the MainWindow file by adding `xmlns:uc="clr-namespace:TasksApp.Controls"` in the `Window` tag. The `xmlns:uc` means that we are importing files in the `uc` namespace, so for accessing its items, we need to import it using uc

* Finally we can use it as follows

```xml
    <!--
        uc:TaskControl means that we are using the `TaskControl` control
        NameOfDependencyPropertyOfUserControl is the dependency property created in the TaskControl control.
    -->
    <uc:TaskControl NameOfDependencyPropertyOfUserControl={Binding}/>
```

* The above line will be dynamically replaced with the user control's xaml markup.

* We have to create a dependency property inside of the control when we want to use data binding.

### Data binding

* For binding data in user controls, we need to create a dependency property in the code behind of our user control.
* We can easily do this in visual studio by using the shortcut `propdp` & pressing tab, which will auto-generate the property for us.

```c#
public MyTask Task
{
    get { return (MyTask)GetValue(TaskProperty); }
    set { SetValue(TaskProperty, value); }
}

// Using a DependencyProperty as the backing store for Task.  This enables animation, styling, binding, etc...
public static readonly DependencyProperty TaskProperty =
    DependencyProperty.Register(
        // The string is the name of the dependency property which is same as the property name
        "Task",
        // Type of the property is the datatype of the property defined above
        typeof(MyTask),
        // Owner type is the name (type) on which this property is from
        typeof(TaskControl),
        // PropertyMetadata has first parameter as default which will be used if not provided
        // it also takes an optional callback function as param which is triggered when the data is binded
        new PropertyMetadata(new MyTask(), CallbackFunc));
        // The binding will be done automatically, we dont need to add code for displaying it

// For creating the CallbackFunc, we can press Ctrl + . on CallbackFunc & click generate method
private static void CallbackFunc(DependencyObject d, DependencyPropertyChangedEventArgs e)
{
    // Converting the dependency object to its control type which gives us access to its elements
    TaskControl control = d as TaskControl;

    // Holds whatever it is binded to
    var updatedTask = e.NewValue as MyTask;

    // Using its properties
    control.nameTextBox = updatedTask.Name;
}
```

# MVVM

* **Model View ViewModel** pattern is common in WPF applications

* We can write the code logic once & use it in another applications which use C# such as Xamarin

* The main motive of using this pattern is to separate out the UI part from the backend part, which allows the UI/UX designer & the backend developer to work separately.

* Structure of MVVM pattern

```rs
            +------------+
            |    View    |
            +------------+
                 ^  |
    Send         |  |Data
    Notifications|  |Binding
                 |  |
                 |  V
            +------------+   Updates    +------------+
            |   View     |------------->|    Model   |
            |   Model    |<-------------|            |
            +------------+    Sends     +------------+
                           Notification
```

* **View** is the GUI visible to the user. There is no business logic. This receives notifications from the **View Model** whenever there is a change.

* **ViewModel** is binded to the **Model** which will update the model whenever there is a change in the data. It works as an intermediate between the View & the Model. It holds View's logic.

* Model is the data in the form of classes & properties. This can only have business & validation logic for the data. **Model** will also send notifications to **ViewModel** about the changes, which will be propagated to **View** as well

* Because the ViewModel holds the logic, it can even be a separate project all together, which can then be reused in other solutions as well

## Folder structure

* All the models should be kept in the **Model** folder. Models are just the structure of data which we have.
* 

## Creating a ViewModel

* Create a class in the `ViewModel` folder, named as `SomethingVM.cs`
* This class should implement the `INotifyPropertyChanged` interface.
* Declare a property which will be modified in the ViewModel & displayed in the View
* Notify whenever there are changes in the property

```c#
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ApiFetcher_WPF.ViewModel
{
    class BookVM : INotifyPropertyChanged
    {
        private string bookName = "";

        public string BookName
        {
            get { return bookName; }
            set
            {
                bookName = value;

                // Notifying that there is a change in the value, update the UI
                NotifyPropertyChanged(nameof(BookName));
            }
        }

        public event PropertyChangedEventHandler? PropertyChanged;

        public void NotifyPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

* For using this property in our View, first we will need to import our ViewModel name space in the `Window` tag.

```xml
<Window x:Class="ApiFetcher_WPF.View.Books"
        ...
        xlmns:vm="clr-namespace:ApiFetcher_WPF.ViewModel" />
```

* Then we need to create an instance of this ViewModel.

```xml
<Window ...>
    <Window.Resources>
        <!-- We can use resources of BookVM under the `someNameSpaceName` namespace -->
        <vm:BookVM x:Key="someNameSpaceName" />
    </Window.Resources>
    ...
</Window>
```

* Now we can set the `DataContext` of the whole window, or any tag to be this resource we just created.

```xml
<Window ...>
    <Window.Resources>
        <!-- We can use resources of BookVM under the `someNameSpaceName` namespace -->
        <vm:BookVM x:Key="someNameSpaceName" />
    </Window.Resources>

    <Grid DataContext="{StaticResource someNameSpaceName}">
        ...
    </Grid>
</Window>
```

## Intellisense not working in xaml designer

* When setting the `DataContext` using `StaticResource`, in Visual Studio 2022, the intellisense doesnt work, also when pressing *F12* on the binded data, it doesnt resolve to the correct variable.

* To fix these issues, there are 2 methods:
  1. **Setting the DataContext from code-behind**:
    * From the code behind of our UI window, which we can easily get to by pressing *F7* on the xaml file, we can set its DataContext property in the constructor, which fixes this issue.
    ```c#
    using System.Windows;

    namespace Fetcher
    {
        /// <summary>
        /// Interaction logic for MainWindow.xaml
        /// </summary>
        public partial class MainWindow : Window
        {
            public MainWindow()
            {
                // Setting the data context
                DataContext = new MainViewModel();
                InitializeComponent();
            }
        }
    }
    ```

  2. **Setting the DataContext without using StaticResource**:
    * Another way is to just set the `DataContext` from the xaml using the property.
    * First we need to import our ViewModel in the `Window` tag in the xaml file.
    * Then we can manually set the data context, or using the Property tab
    ```xml
    <Window x:Class="ApiFetcher_WPF.View.Books"
            ...
            xlmns:vm="clr-namespace:ApiFetcher_WPF.ViewModel" />
        <Grid>
            <Grid.DataContext>
                <vm:BookVM/>
            </Grid.DataContext>
        </Grid>
    </Window>
    ```

## Binding Data

* After we have set the DataContext the properties inside our ViewModel is accessible here, so we can use `Binding` for binding the View & the ViewModel.

* If the property we are binding to is a complex object containing other class/struct members in it, we can also use it by again setting the `DataContext` of a sub child.

```xml
<Window ...>
    <Window.Resources>
        <!-- We can use resources of BookVM under the `someNameSpaceName` namespace -->
        <vm:BookVM x:Key="someNameSpaceName" />
    </Window.Resources>

    <Grid DataContext="{StaticResource someNameSpaceName}">
        ...
        <Grid DataContext="{Binding MyProperty}">
            <TextBlock Text="{Binding SomeMemberOfMyProperty}">
        </Grid>
    </Grid>
</Window>
```

* If in case the parent has a different DataContext, but we want its child to access a property from other place, then we can individually set the DataContext of that specific element.

```xml
<Window ...>
    <Grid DataContext="{StaticResource someNameSpaceName}">
        ...
        <Grid DataContext="{Binding MyProperty}">
            <TextBlock
                DataContext="{Binding AnotherProperty}"
                Text="{Binding SomeMemberOfAnotherProperty}">
        </Grid>
    </Grid>
</Window>
```


* This binding can be a bi-directional by setting the `Mode` of Binding to be `TwoWay`.

```xml
    <TextBox
        Text="{Binding SomeProperty, Mode=TwoWay}" />
```

* Now if we make changes in the textbox & de-select the textbox, its setter method will be triggered which indicates a two way communication.

* We need to deselect because the update default is set to be `LostFocus`, but we can change it to work whenever we enter any text, i.e. there is a change in the textbox, by setting its `UpdateSourceTrigger` to be `PropertyChanged`.

```xml
    <TextBox
        Text="{Binding SomeProperty, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}" />
```

*  In addition to that we also need to modify the `CanExecuteChanged` event handler in the Command.

```c#
class SearchCommand : ICommand
{
    public event EventHandler? CanExecuteChanged
    {
        add    { CommandManager.RequerySuggested += value; }
        remove { CommandManager.RequerySuggested -= value; }
    }
}
```

* What we are doing is whenever we add an event handler, we are adding/removing whatever event handler is being set/removed from wherever.

* `CommandManager.RequerySuggested` will run the event handler which it is being set to. `value` holds the event handler itself

* We can also bind to data using the `Source` property of Binding

```xml
<Grid>
    <Grid.DataContext>
        <vm: DataContext1 />
    </Grid.DataContext>
    <TextBox Text={Binding Source={Binding DataContext2}, Path=SomeProperty}>
</Grid>
```


### Using dummy values in designer tab

* When using bindings, we will not be able to see any data when the property it is binded to doesnt have a data, but for testing purposes, we might need to see data in the UI in the designer tab.

* One solution might be to hardcode these values in the backend, but when deploying this application one might forget to remove these dummy values which is incorrect.

* The proper solution to it is to use the `GetIsInDesignerMode` which tells if the application is in designer mode

```c#
public SomeMethod()
{
    if (DesignerProperties.GetIsInDesignerMode(new System.Window.DependencyObject()))
    {
        // We are inside designer mode
        MyProperty = "Some Dummy Value";
    }
}
```

# ICommand

* This is used to move the logic from code-behind to the ViewModel, essentially allowing us to have reusable code.

* The commands should be placed in the **ViewModel/Commands** folder

* Inside that folder create a class file, that class should implement the `ICommand` interface. We need to implement the `CanExecute` & `Execute` methods. If we are using Visual Studio, its quick action (Ctrl + .) will help us generate that code.

```c#
public class MyClass: ICommand
{
    public event EventHandler? CanExecuteChanged;

    public bool CanExecute(object? parameter)
    {
        throw new NotImplementedException();
    }

    public void Execute(object? parameter)
    {
        throw new NotImplementedException();
    }
}
```

* Then we need to create an instance of this in our ViewModel.

```c#
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ApiFetcher_WPF.ViewModel
{
    class BookVM : INotifyPropertyChanged
    {
        public MyClass MyClass { get; set; }

        public BookVM()
        {
            MyClass = new MyClass();
        }
    }
}
```

* Now we can bind this to a button by setting its `Command` property.

```xml
<Button
    Command="{Binding MyClass}" />
```

# ObservableCollection

* It is a list awarable of changes. It updates whenever we insert or delete.

* We are using this instead of a `List` because whenever we add an element to an `ObservableCollection`, it triggers the `set` event of it, but the `List`'s set event will only be triggered when we assign a new value to the list variable itself such as

```c#
// ObservableCollection
ObservableCollection<int> myList = new();

// Triggers the set event
myList.Add(1);

// Generic List
List<int> myList2 = new();

// Does not trigger the set event
myList2.Add(1);

// Triggers the set event
myList2 = new List<int>();
```

* The Observable collection has to be initialized only once & updated whenever there are changes. Do note that the collection should be a property & not a variable, else the binding will fail.

```c#
using ApiFetcher_WPF.Model;
using ApiFetcher_WPF.ViewModel.Commands;
using System.Collections.ObjectModel;
using System.ComponentModel;

namespace ApiFetcher_WPF.ViewModel
{
    class BookVM : INotifyPropertyChanged
    {
        public ObservableCollection<Book> Books { get; set; }

        public BookVM()
        {
            Books = new ObservableCollection<Book>();
        }


        public async void FetchBooks()
        {
            var fetchedBooks = await Helpers.FetchBooks();

            Books.Clear();
            foreach (var book in fetchedBooks)
            {
                Books.Add(book);
            }
        }
    }
}

```

* We can bind this into a *ListView* as such

```xml
<ListView ItemsSource={Binding MyObservableCollection}>
    <ListView.ItemTemplate>
        <DataTemplate>
            <StackPanel>
                <Label Content={Binding Property1}>
                <Label Content={Binding Property2}>
                ...
            </StackPanel>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

## Change UI data from separate thread.

* Sometimes we have to modify variables which are displayed in the UI from another thread instead of the main UI thread.

* But doing so as it is, will throw an exception `System.NotSupportedException: 'This type of CollectionView does not support changes to its SourceCollection from a thread different from the Dispatcher thread.'`.

* This means that we are not allowed to modify data on UI from another thread, possibly due to concurrency issues.

* To fix this, we can `delegate` the call, meaning we can put the operations to perform in the main UI thread's queue. by using the following syntax

```c#
private async void FetchBooksWorker_DoWork(object? sender, DoWorkEventArgs e)
{
    var fetchedBooks = await Helpers.FetchBooks();
    foreach (var book in fetchedBooks)
    {
        // Put the operations inside the Invoke to the main UI's queue
        App.Current.Dispatcher.Invoke((Action)delegate
        {
            Books?.Add(book);
        });
    }
}
```

* When using Dispatcher in a loop, we have to be aware that the queue has a limited size.
> The invoke should be outside the foreach. If you are adding hundreds of items, you will saturate the dispatcher queue and introduce UI delays for the user.


# IValueConverter

* Allows us to modify the data format from the model into a form that is needed by the View.

* It also allows the conversion of value from the View into what the Model needs.

## Implemeting IValueConverter

* For implementing, we need to have a class which implements the `IValueConverter` interface.

* It is a good practice to keep the convertes inside `ViewModel\Convertors` folder.

* For this interface, we need to implement the `Convert` (from Model to View) & `ConvertBack` (from View back to Model) methods.

```c#
internal class BookAvailabilityConvertor : IValueConverter
{
    // Converts data from Model to View
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        bool isBookAvailable = (bool)value;

        if (isBookAvailable)
        {
            return "Available";
        }
        else
        {
            return "Sold out";
        }
    }

    // Converts data back from View to Model
    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        string bookStatus = (string)value;
        if (bookStatus == "Available")
        {
            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## Using the Convertor

* For using the converter we first need to import the convertors namespace.

* Finally in binding we need to pass this convertor in the `Converter` attribute

```xml
<Window
    ...
    xmlns:convertors="clr-namespace:ApiFetcher_WPF.ViewModel.ValueConverters">
    ...

    <TextBlock Text={Binding SomeProperty, Converter={Binding BookAvailabilityConvertor}}>
</Window>
```

# Menus

* For creating menus we can use the `Menu` tag.
* For adding items onto our menu, we need to use the `MenuItem` tag.

* We can nest the **MenuItem**s, which will display it as a sub menu of the current menu item

```xml
<Menu>
    <MenuItem Header="File">
        <MenuItem Header="New File" />
        <MenuItem Header="Open File" />

        <!-- Displays a horizontal line between the menu items -->
        <Separator />

        <MenuItem Header="Exit" />
    </MenuItem>
</Menu>
```

* In all the applications, the menu is at the top of the window. To stick it to the top, we can use the `DockPanel` & then set the menu's `DockPanel.Dock` property to be `top`

```xml
<DockPanel>
    <!-- Sticked at the top -->
    <Menu DockPanelHeader="File">
        <MenuItem Header="New File" />
        <MenuItem Header="Open File" />

        <!-- Displays a horizontal line between the menu items -->
        <Separator />

        <MenuItem Header="Exit" />
    </Menu>

    <!-- All other items after the Menu -->
    ...
<DockPanel>
```

# ContextMenu

* `ContextMenu` is a menu which pops up when we right-click on an element

* We can define a context menu in numerous items

```xml
<TextBlock>
    <TextBlock.ContextMenu>
        <ContextMenu>
            <MenuItem Header="Copy" />
            <MenuItem Header="Paste" />
        </ContextMenu>
    </TextBlock.ContextMenu>
</TextBlock>
```


# Binding Event Handlers

* Most of the elements have event handlers, which we cannot bind to when using the MVVM pattern.

* For binding, first we need a package `Microsoft.Xaml.Behaviors.Wpf`.

* After installing, we need to include it in our xaml file

```xml
<Window
    ...
    xmlns:i="http://schemas.microsoft.com/xaml/behavious"
>
```

* Now we can use this package

```xml
<TextBox ...>
    <i:Interaction.Triggers>
        <i:EventTrigger EventName="LostFocus">
            <i:InvokeCommandAction Command={Binding MyCommand} />
        </i:EventTrigger>
    </i:Interaction.Triggers>
</TextBox>
```

# Accessing ViewModel in Code-behind

* We can access our ViewModel & its properties from the code-behind.
* Because we have already set the data context, we can assign a name to it & access it from the code-behind

```xml
<DockPanel>
    <DockPanel.DataContext>
        <vm:EditorVM x:Name="EditorVM" />
    </DockPanel.DataContext>
</DockPanel>
```

```c#
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
        var contents = EditorVM.EditorContents;
    }
}
```

* If we are using the data context loaded as a resource in xaml file, we can still access it in code-behind by assigning a key to it & then accessing it using `Resource["ViewModelKey"]`

```xml
<Window ...>
    <Window.Resources>
        <ResourceDictionary>
            <vm:MyViewModel x:Key="vm">
        </ResourceDictionary>
    </Window.Resources>
</Window>
```

```c#
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
        var vm = Resources["vm"] as MyViewModelType;

        var contents = vm.EditorContents;
    }
}
```



# Random Points

* If the binding fails in the UI even though you pressing *F12* takes us to the correct data, then make sure that the thing that we are binding to is actually a property & not a variable. It doesnt have to be a full property.

* [This](https://jsonutils.com) website allows us to convert our json response into c# classes, which we can then directly deserialize into.

## SQLite Attributes

* SQLite has attributes which can be applied to the class properties. Some properties are
  1. `Table("TableName")` - Applied to class to override name of the table
  2. `Column("ColumnName")` - Applied to property to override name of the column
  3. `PrimaryKey` - Sets the property to be primary key of the table
  4. `AutoIncrement` - Automatically increment the value of the column
  5. `Indexed` - Sets the column to be foreign key
  6. `MaxLength(IntegerLength)` - Applied to string columns. Specifies the maximum length value which can be stored
  7. `Unique` - Applies constraint that all the values in this column must be unique
  8. `Ignore` - Applied to properties whose columns we do not want to create in the table
