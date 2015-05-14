While we certainly have been busy getting our [hands dirty](http://www.wintellect.com/devcenter/tag/xamarin) with Xamarin I've been doing some of my own. However, I've been messing around with using Xamarin with F#. I mentioned in a previous post that F# can be used very well for [enterprise applications](http://www.wintellect.com/devcenter/jwood/using-f-for-enterprise-applications). Here's an in depth look at F# with Xamarin for mobile applications.

But why F# with Xamarin, you ask? Well, it just turns out that I love working with both of these technologies and I got lucky that the awesome folks at Xamarin and [Dave Thomas](http://7sharpnine.com/) - who does most of the great work with F# for Xamarin -  decided to take on F# as a [first class language](http://developer.xamarin.com/guides/cross-platform/fsharp/).

> Before we get started, it's helpful to remember that, in F#, if you reference any other part of your code, whether it's in the same file or not, the part you are referencing needs to be defined *before* you reference it. More info can be found at the wonderful [F# for Fun and Profit](http://fsharpforfunandprofit.com/posts/cyclic-dependencies/) site.

Currently in Xamarin (at the time of this writing - version 5.9) there aren't any actual Xamarin Forms or PCL templates. Though, it is rumored there will be more templates in 6.0.

![Available F# Projects](https://dl.dropboxusercontent.com/s/mxdnb6gfnxbhbs0/FSharp-Solution.jpg?dl=0)

### Using Xamarin Forms
Larry O'Brien already has a fantastic [tutorial](http://www.knowing.net/index.php/2014/08/27/xamarin-forms-programming-in-f/) on how to use Xamarin Forms in an F# iOS or Android project. Though, I did notice that I didn't have to bring in the `FSharp.Core Mono delay signed` NuGet package as F# is already included with Xamarin Studio. I just needed to bring in `Xamarin.Forms` from NuGet and add the following references from the Mono library as the post also describes:

 - System.ObjectModel.dll
 - System.Runtime.dll

After you have all that, you're ready to roll with Xamarin Forms in F#. 

To help understand a bit more of combining F# with Xamarin I ported the Phoneword application that we work with quite a bit in [Xamarin University](https://xamarin.com/university) to F#. The results are the same if it was a C# project. This fairly simple app takes in a phone number and, if the number includes any letters, it translates the letters into the appropriate numbers and allows you to call that number.

### Adding an F# PCL
Unfortunately, one of the things Xamarin Studio isn't able to do yet (again, it's definitely being worked on and will be out fairly soon) is the ability to add an F# Portable Class Library to the project. To do this you'll have to open the solution up in Visual Studio and add it through there.

![F# PCL in Visual Studio](https://dl.dropboxusercontent.com/s/91wpconrqy7oh83/FSharpPCL.jpg?dl=0)

>At this time, there seems to be an issue of opening this in Xamarin Studio (currently already being fixed), so I'll continue using Visual Studio throughout the rest of this post.

For the most part I followed Larry O'Brien's example from above. However, I did separate out the Xamarin Forms stuff from the `AppDelegate` file into the PCL project. With the very generous help from Dave Thomas, we were able to get a running sample all in F# and Xamarin Forms.

Here is the full Xamarin Forms code in F#.
```fsharp
[<Extension>]
type IListExtensions () =
    [<Extension>]
    static member inline AddRange(xs:'a IList, range) = range |> Seq.iter xs.Add

type IOpenUrlService =
    abstract member OpenUrl: string -> bool 

type MainPage() =
    static member GetMainPage() =
       let contentPage = ContentPage(Padding = Thickness(20., Device.OnPlatform(40., 20., 20.), 20., 20.))
       let panel = StackLayout(VerticalOptions = LayoutOptions.FillAndExpand, 
                               HorizontalOptions = LayoutOptions.FillAndExpand, 
                               Orientation = StackOrientation.Vertical, 
                               Spacing = 15.)
       let phoneNumberText = Entry(Text = "1-855-XAMARIN")
       let translateButton = Button(Text = "Translate")
       let callButton = Button(Text = "Call", IsEnabled = false)

       translateButton.Clicked.Add(fun _ -> callButton.Text <- PhoneTranslator.toNumber phoneNumberText.Text
                                            callButton.IsEnabled <- true)

       callButton.Clicked.Add(fun _ ->
           let isCalling = contentPage.DisplayAlert("Dial a number", "Would you like to call " + phoneNumberText.Text, "Yes", "No").Result

           if isCalling then
             let dialer = DependencyService.Get<IOpenUrlService>()
             dialer.OpenUrl phoneNumberText.Text |> ignore)

       panel.Children.AddRange([Label(Text = "Enter a Phoneword:")
                                phoneNumberText
                                translateButton
                                callButton])

       contentPage.Content <- panel
       contentPage

type App() =
    inherit Application(MainPage = MainPage.GetMainPage())
```

The `type IOpenUrlService` there is to create our interface so we can use shared code in our Xamarin Forms page and implement it separately in each platform specific project. You may notice we call this lower down and use Xamarin Forms dependency service locator.

Also the `string -> bool` statement there simply states that the `OpenUrl` method takes in a `string` parameter and the return type is `bool`. Very similar to C#'s `Func` type where the last type in the signature represents the return type.

```fsharp
DependencyService.Get<IOpenUrlService>()
dialer.OpenUrl phoneNumberText.Text |> ignore
```

> Note that we pipe into the built-in F# function `ignore` since we don't need to do anything with the return type of `dialer.OpenUrl` at this point.

We also have to actually add our `App` that our `MainPage` will be set to. Surprisingly, this is very simple and concise. So much so that, you may have noticed, that it was just added to the end of our file that contains the `MainPage`.

```fsharp
type App() =
    inherit Application(MainPage = MainPage.GetMainPage)
```

As you can see, we can use Xamarin Forms just like we would use them in C#.

#### iOS
And in our `AppDelegate` for iOS we can do the same as if we were in a C# project.

```fsharp
[<assembly: Dependency(typeof<OpenUrlService>)>] do ()

[<Register("AppDelegate")>]
type AppDelegate() = 
    inherit FormsApplicationDelegate()

    override val Window = null with get, set

    // This method is invoked when the application is ready to run.
    override this.FinishedLaunching(app, options) = 
        this.Window <- new UIWindow(UIScreen.MainScreen.Bounds)
        Xamarin.Forms.Forms.Init()
        this.LoadApplication(App())
        base.FinishedLaunching(app, options)
```

With our iOS implementation of `OpenUrlService`.

```fsharp
type OpenUrlService() =
    interface IOpenUrlService with
        member this.OpenUrl url = UIApplication.SharedApplication.OpenUrl(new NSUrl("tel:" + url))
```

All we needed to include here is to set our dependency to let Xamarin Forms know where the platform specific code is, initialize Xamarin Forms, and tell it what application to load.

#### Android
Similar to iOS, we just have our `MainActivity` like we would in C# and we basically add the same from iOS.

```fsharp
[<assembly: Dependency(typeof<OpenUrlService>)>] do ()

[<Activity (Label = "PhoneWordFSharp.Droid", MainLauncher = true)>]
type MainActivity () =
    inherit FormsApplicationActivity ()

    override this.OnCreate (bundle) =
        base.OnCreate (bundle)

        Forms.Init(this, bundle)

        this.LoadApplication(App())
```

With our Android implementation of `OpenUrlService`.

```fsharp
type OpenUrlService() =
    let isIntentAvailable (context:Context) (intent:Intent) = 
        let packageManager = context.PackageManager
        let list = packageManager.QueryIntentServices(intent, PM.PackageInfoFlags.Services).Union(packageManager.QueryIntentActivities(intent, PM.PackageInfoFlags.Activities))

        if list.Any() then true
        else false

    interface IOpenUrlService with
        member this.OpenUrl url = 
            let context = Forms.Context
            let intent = new Intent(Intent.ActionCall)
            
            match context = null with
            | true -> ()
            | false -> intent.SetData(Uri.Parse("tel:" + url)) |> ignore

            isIntentAvailable context intent
```

---
This gives a brief introduction to using F# with Xamarin to make full mobile applications and without the need for using any C#. This will only get easier in the future so look out for updates to Xamarin Studio for all the good stuff.

The full code can be found on [GitHub](https://github.com/Wintellect/XamarinSamples/tree/master/PhoneWordFSharp)

---
[![Xamarin Partner][2]][1]

  [1]: http://www.wintellect.com/certified-xamarin-mobile-consultants
  [2]: http://www.wintellect.com/devcenter/wp-content/uploads/2015/05/xamarin-partner_thumb1.png (Xamarin Partner)
