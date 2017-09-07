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


### Model - View - Update

#### "Elm - Architecture"

 <img src="images/Elm.png" style="background: white;" width=700 />


 <small>http://danielbachler.de/2016/02/11/berlinjs-talk-about-elm.html</small>


--- 

### Model - View - Update

    // MODEL

    type Model = int

    type Msg =
    | Increment
    | Decrement

    let init() : Model = 0

---

### Model - View - Update

    // VIEW

    let view model dispatch =
        div []
            [ button [ OnClick (fun _ -> dispatch Decrement) ] [ str "-" ]
              div [] [ str (model.ToString()) ]
              button [ OnClick (fun _ -> dispatch Increment) ] [ str "+" ] ]

---

### Model - View - Update

    // UPDATE

    let update (msg:Msg) (model:Model) =
        match msg with
        | Increment -> model + 1
        | Decrement -> model - 1

---

### Model - View - Update

    // wiring things up

    Program.mkSimple init update view
    |> Program.withConsoleTrace
    |> Program.withReact "elmish-app"
    |> Program.run

---

### Model - View - Update

# Demo

***

### Sub-Components

    // MODEL

    type Model = {
        Counters : Counter.Model list
    }

    type Msg = 
    | Insert
    | Remove
    | Modify of int * Counter.Msg

    let init() : Model =
        { Counters = [] }

---

### Sub-Components

    // VIEW

    let view model dispatch =
        let counterDispatch i msg = dispatch (Modify (i, msg))

        let counters =
            model.Counters
            |> List.mapi (fun i c -> Counter.view c (counterDispatch i)) 
        
        div [] [ 
            yield button [ OnClick (fun _ -> dispatch Remove) ] [  str "Remove" ]
            yield button [ OnClick (fun _ -> dispatch Insert) ] [ str "Add" ] 
            yield! counters ]

---

### Sub-Components

    // UPDATE

    let update (msg:Msg) (model:Model) =
        match msg with
        | Insert ->
            { Counters = Counter.init() :: model.Counters }
        | Remove ->
            { Counters = 
                match model.Counters with
                | [] -> []
                | x :: rest -> rest }
        | Modify (id, counterMsg) ->
            { Counters =
                model.Counters
                |> List.mapi (fun i counterModel -> 
                    if i = id then
                        Counter.update counterMsg counterModel
                    else
                        counterModel) }

---

### Sub-Components

# Demo

***

### React

* Facebook library for UI 
* <code>state => view</code>
* Virtual DOM

---

### Virtual DOM - Initial

<br />
<br />


 <img src="images/onchange_vdom_initial.svg" style="background: white;" />

<br />
<br />

 <small>http://teropa.info/blog/2015/03/02/change-and-its-detection-in-javascript-frameworks.html</small>

---

### Virtual DOM - Change

<br />
<br />


 <img src="images/onchange_vdom_change.svg" style="background: white;" />

<br />
<br />

 <small>http://teropa.info/blog/2015/03/02/change-and-its-detection-in-javascript-frameworks.html</small>

---

### Virtual DOM - Reuse

<br />
<br />


 <img src="images/onchange_immutable.svg" style="background: white;" />

<br />
<br />

 <small>http://teropa.info/blog/2015/03/02/change-and-its-detection-in-javascript-frameworks.html</small>


*** 

### ReactNative

 <img src="images/ReactNative.png" style="background: white;" />


 <small>http://timbuckley.github.io/react-native-presentation</small>

***

### Show me the code

*** 

### TakeAways

* Learn all the FP you can!
* Simple modular design

*** 

### Thank you!

* https://github.com/fable-compiler/fable-elmish
* https://ionide.io
* https://facebook.github.io/react-native/
