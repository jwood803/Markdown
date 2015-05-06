While we certainly have been busy getting our [hands dirty](http://www.wintellect.com/devcenter/tag/xamarin) with Xamarin I've been doing some of my own. However, I've been messing around with using Xamarin with F#.

I mentioned that F# can be used very well for [enterprise applications](http://www.wintellect.com/devcenter/jwood/using-f-for-enterprise-applications). Here's an in depth look at F# with Xamarin for mobile applications.

But why F# with Xamarin, you ask? Well, it just turns out that I love working with both of these technologies and I got lucky that the awesome folks at Xamarin and [Dave Thomas](http://7sharpnine.com/) - who does most of the great work with F# for Xamarin -  decided to take on F# as a [first class language](http://developer.xamarin.com/guides/cross-platform/fsharp/).

Currently in Xamarin (at the time of this writing - version 5.9) there aren't any actual Xamarin Forms or PCL templates. Though, it is rumored there will be more templates in 6.0.

![Available F# Projects](https://dl.dropboxusercontent.com/s/mxdnb6gfnxbhbs0/FSharp-Solution.jpg?dl=0)

#### Using Xamarin Forms
Larry O'Brien already has a fantastic [tutorial](http://www.knowing.net/index.php/2014/08/27/xamarin-forms-programming-in-f/) on how to use Xamarin Forms in an F# iOS or Android project. Though, I did notice that I didn't have to bring in the `FSharp.Core Mono delay signed` NuGet package as F# is already included with Xamarin Studio. I just needed to bring in `Xamarin.Forms` from NuGet and add the following references from the Mono library as the post also describes:

 - System.ObjectModel.dll
 - System.Runtime.dll

After you have all that, you're ready to roll with Xamarin Forms in F#. 

##### Sample Xamarin Forms Application with F#
To help understand a bit more of the F# process I ported the Phoneword application that we work with quite a bit in [Xamarin University](https://xamarin.com/university) to F# with this. The results are the same if it was a C# project.

#### Adding an F# PCL
