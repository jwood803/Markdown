One of the best and easiest ways to use F# in your projects is to use it for [unit testing](http://fsharpforfunandprofit.com/posts/low-risk-ways-to-use-fsharp-at-work-3/) and the same can be said for your mobile applications with Xamarin.

While C# has [FluentAssertions](http://www.fluentassertions.com/) as well as [Should](https://github.com/erichexter/Should) (I prefer the syntax of FluentAssertions between the two) F# has [FsUnit](http://fsprojects.github.io/FsUnit/) to give more readable test conditions. I would say even more readable than the C# equivalent.

With this post I'll have a small demo Xamarin project using [Xamarin Forms]() to build the UI. This will consist of a simple navigation and a small class to do some logic. We will use F# for our test project to test both the logic and use [Xamarin UI Test]() to make sure our navigation and screens appear as we intend.

Let's quickly compare the two and see how they look. 