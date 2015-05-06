While we certainly have been busy getting our [hands dirty](http://www.wintellect.com/devcenter/tag/xamarin) with Xamarin I've been doing some of my own. However, I've been messing around with using Xamarin with F#.

I mentioned that F# can be used very well for [enterprise applications](http://www.wintellect.com/devcenter/jwood/using-f-for-enterprise-applications). Here's an in depth look at F# with Xamarin for mobile applications.

But why F# with Xamarin, you ask? Well, it just turns out that I love working with both of these technologies and I got lucky that the awesome folks at Xamarin and [Dave Thomas](http://7sharpnine.com/) - who does the great work with F# for Xamarin -  decided to take on F# as a [first class language](http://developer.xamarin.com/guides/cross-platform/fsharp/).

Currently in Xamarin (at the time of this writing - version 5.9) there aren't any actual Xamarin Forms or PCL templates. Though, it is rumored there will be more templates in 6.0.

![Available F# Projects](https://dl.dropboxusercontent.com/s/mxdnb6gfnxbhbs0/FSharp-Solution.jpg?dl=0)

#### Using Xamarin Forms
Larry O'Brien already has a fantastic [tutorial](http://www.knowing.net/index.php/2014/08/27/xamarin-forms-programming-in-f/) on how to use Xamarin Forms in an F# iOS or Android project. Though, I did notice that I didn't have to bring in the `FSharp.Core Mono delay signed` NuGet package as F# is already included with Xamarin Studio. I just needed to bring in `Xamarin.Forms` from NuGet and add the following references from the Mono library as the post also describes:

 - System.ObjectModel.dll
 - System.Runtime.dll

After you have all that, you're ready to roll with Xamarin Forms in F#. 

To help understand a bit more of the F# process I ported the Phoneword application that we work with quite a bit in [Xamarin University](https://xamarin.com/university) to F# with this. The results are the same if it was a C# project. This fairly simple app takes in a phone number and, if the number includes any letters, it translates the letters into the appropriate numbers and allows you to call that number.

For the most part I followed Larry O'Brien's example from above and included all of the Xamarin Forms code inside of the iOS project. However, I did separate out the Xamarin Forms stuff from the `AppDelegate` file.

Here is the full Xamarin Forms code in F#.
```fsharp
open Xamarin.Forms
open UIKit
open Foundation
open PhoneWordFSharp.Core

type App() =
    static member GetMainPage =
       let contentPage = new ContentPage()
       let panel = new StackLayout()
       let phoneNumberText = new Entry()
       let translateButton = new Button()
       let callButton = new Button()
       let transator = PhoneTranslator()

       contentPage.Padding <- new Thickness(20., Device.OnPlatform(40., 20., 20.), 20., 20.)

       panel.VerticalOptions <- LayoutOptions.FillAndExpand
       panel.HorizontalOptions <- LayoutOptions.FillAndExpand
       panel.Orientation <- StackOrientation.Vertical
       panel.Spacing <- 15.

       phoneNumberText.Text <- "1-855-XAMARIN"
       translateButton.Text <- "Translate"
       callButton.Text <- "Call"
       callButton.IsEnabled <- false

       translateButton.Clicked.Add(fun i -> 
            callButton.Text <- transator.toNumber phoneNumberText.Text
            callButton.IsEnabled <- true
        )

       callButton.Clicked.Add(fun i ->
           let isCalling = contentPage.DisplayAlert("Dial a number", "Would you like to call " + phoneNumberText.Text, "Yes", "No")

           match isCalling.Result with
            | true -> UIApplication.SharedApplication.OpenUrl(new NSUrl("tel:" + phoneNumberText.Text)) |> ignore
            | false -> ()
       )

       panel.Children.Add(new Label(Text = "Enter a Phoneword:"))
       panel.Children.Add(phoneNumberText)
       panel.Children.Add(translateButton)
       panel.Children.Add(callButton)

       contentPage.Content <- panel

       contentPage
```

As you can see, we can use the Xamarin Forms APIs just like we would use them in C#.

And in our `AppDelegate` we can do the same as if we were in a C# project.

```fsharp
[<Register("AppDelegate")>]
type AppDelegate() = 
    inherit UIApplicationDelegate()

    member val Window = null with get, set

    // This method is invoked when the application is ready to run.
    override this.FinishedLaunching(app, options) = 
        this.Window <- new UIWindow(UIScreen.MainScreen.Bounds)

        Xamarin.Forms.Forms.Init()

        this.Window.RootViewController <- App.GetMainPage.CreateViewController()
        this.Window.MakeKeyAndVisible()
        true
```

All that we added here is to initialize Xamarin Forms and set the `Window.RootViewController` to the `App.GetMainPage.CreateViewController()`.

As you can tell this is only for iOS, but the same can be done for an Android project.

#### Adding an F# PCL
Unfortunately, one of the things Xamarin Studio isn't able to do yet (though it's definitely being worked on and will be out fairly soon) is the ability to add an F# Portable Class Library to the project. To do this you'll have to open the solution up in Visual Studio and add it through there.

![F# PCL in Visual Studio](https://dl.dropboxusercontent.com/s/91wpconrqy7oh83/FSharpPCL.jpg?dl=0)

---
This gives a brief introduction to using F# with Xamarin to make mobile applications. 