- title : Bringing unidirectional architecture to XAML platforms
- description : Introduction to Gjallarhorn for Unidirectional XAML
- author : Reed Copsey, Jr.
- theme : night
- transition : default

***

## Goodbye MVVM!

<br />
<br />

### Bringing unidirectional architecture to XAML platforms

<br />
<br />
Reed Copsey, Jr. - [@ReedCopsey](https://www.twitter.com/ReedCopsey)

***

### UI in .NET

First, there was WinForms

    private void btnLogin_Click( object sender, EventArgs e ) 
        { 
        Login(); 
        } 

    private void Login() 
        { 

        if ( tbPassword.Text.Length > 0 && tbUsername.Text.Length > 0 ) 
            { 
            if ( UserAuthenticated( tbUsername.Text, tbPassword.Text ) ) 
                { 
                Authenticated = true; 
                this.Close(); // close this form - do not exit the application 
            //...

Via https://code.msdn.microsoft.com/windowsdesktop/CCS-LABS-C-Creating-a-9ba05613

---

### WinForms

 <img src="images/scream.jpg" style="background: transparent; border-style: none;"  width=700 />

---

### WinForms

- Making sense of WinForms
     - MVC
     - MVP
     - MVSpaghetti

---

### WPF

    private void Button_Click_1(object sender, RoutedEventArgs e) 
        { 
            Button b = (Button) sender; 
            tb.Text += b.Content.ToString(); 
        } 
 
    private void Off_Click_1(object sender, RoutedEventArgs e) 
        { 
            //to close the app 
            Application.Current.Shutdown(); 
        }

Via https://code.msdn.microsoft.com/windowsdesktop/Simple-Calculator-d1d8cf4c

---

### WPF

- Added XAML - A declarative mechanism to design the UI
- Initially logic handled very much like WinForms

---

### WPF with MVVM

- Leverage Data Binding capabilities in XAML
- Separate Model from UI
    - Separate "ViewModel" layer for application logic
    - Goal of "View" being near 100% declarative, via XAML
- Decoupled Model from VM, and VM from View
- View can change with _no code changes at all_

---

### WPF with MVVM

- Recommended by most experts
- New samples almost exclusively use pattern
- Xamarin Forms brought XAML and MVVM to mobile
- Life is good!

---

### WPF with MVVM

- Life is good...? Not quite!

*** 

### MVVM is fundamentally flawed

- Everything is based on mutation.  
  - View Model layer is always going to be mutable
  - Edits work via (two way) data binding that set properties

---

### MVVM is fundamentally flawed

- Large amount of boilerplate 
  - Each view usually means a complete class written as VM
  - INotifyPropertyChanged, INotifyCollectionChanged, and INotifyDataErrorInfo introduce extra plumbing
  - Difficult to reason about, especially when ViewModels need to "communicate"

---

### MVVM is fundamentally flawed

- Plumbing requirements for collections become onerous quickly
  - Often leads to violations such as making Model mutable with INPC
  - Makes application more complicated, more difficult to reason about, etc

***

### Breaking out of the bubble

- Look to other front end trends
  - React
  - Flux
  - Redux
  - Elm

---

### Breaking out of the bubble
- What's common here?
  - Unidirectional flow of data
  - Virtual DOM
  - "View as data"

***

### What would it take for the best of both worlds?

- Keep declarative XAML / Decoupled view layer
- Reactive system
- Low plumbing overhead
- Allow immutable data model

--- 

### Step one: 

- Keep declarative XAML / Decoupled view layer
  - Easy: Just keep using XAML

--- 

### Step two: 
  
- Reactive system
  - Take inspiration from Rx/IObservable
  - Do we need more?

---

### Step three:  

- Low plumbing overhead
  - Good library/framework to wire it all up

--- 

### Step four: 

- Allow immutable data model
  - Rethinking binding
  - What is a property, really?


***

### Gjallarhorn's Approach

#### XAML for Views - Unchanged from MVVM

    <TextBlock Grid.Row="0" Grid.Column="0" Margin="5">Current:</TextBlock>
    <TextBox Text="{Binding Current, Mode=OneWay}" IsReadOnly="True" 
             Grid.Row="0" Grid.Column="1" Margin="5" />

    <Button  Command="{Binding Increment}" Margin="5" Grid.Row="1" 
             Grid.Column="0">Increment</Button>
    <Button  Command="{Binding Decrement}" Margin="5" Grid.Row="1" 
             Grid.Column="1">Decrement</Button>


---

### Gjallarhorn's Approach

#### Model

    // Model is a simple integer for counter
    type Model = { Value : int }

---

### Gjallarhorn's Approach

#### Message and Update

    // We define a union type for each possible message
    type Msg = 
        | Increment 
        | Decrement

    // Create a function that updates the model given a message
    let update msg model =
        match msg with
        | Increment -> { Value = min 10 (model.Value + 1) }
        | Decrement -> { Value = max 0 (model.Value - 1) }

---

### Gjallarhorn's Approach

#### (Optional) ViewModel

    // Our "ViewModel" 
    type ViewModel = 
        {
            Current : int 
            Increment : VmCmd<Msg>
            Decrement : VmCmd<Msg>
        }        
    let d = { Current = 5 ; Increment = Vm.cmd Increment; Decrement = Vm.cmd Decrement }

--- 

### Gjallarhorn's Approach

#### View Binding

    let bindToSource =                   
        Component.fromBindings [
            <@ d.Current    @> |> Bind.oneWay (fun m -> m.Value)
            <@ d.Increment  @> |> Bind.cmdIf (fun m -> m.Value < 10)
            <@ d.Decrement  @> |> Bind.cmdIf (fun m -> m.Value > 0)
        ]         
    
---

### Demo

#### Demo of Gjallarhorn

 <img src="images/logo.png"/>

*** 

### TakeAways

* There are other options than MVVM, with huge advantages
* Learn all the FP you can!
* Simple modular design

*** 

### Thank you!

- <a href="https://github.com/ReedCopsey/Gjallarhorn.Bindable"><img src="images/logo.png" width="100" style="vertical-align: middle;"/> https://github.com/ReedCopsey/Gjallarhorn.Bindable </a>

- <a href="https://twitter.com/ReedCopsey"><img src="images/ReedCopseyJr.jpeg" width="100" style="vertical-align: middle;"/> @ReedCopsey </a>

- <a href="http://fsharp.org"><img src="images/fsharp256.png" width="100" style="vertical-align: middle;"/> http://fsharp.org</a>  
