With the latest news of the release of [Visual Studio 2015]() some folks may not have realized but included in it is the newest release of F# 4.0. The .NET Blog has also [officially announced the RTM](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-the-rtm-of-visual-f-4-0.aspx). The most exciting thing about this release of F# is that, since F# has been open source for quite some time now, most of the fixes and updates were made by the F# community.

What exactly is different in the new version of F# is very nicely highlighted in the below video by [Lincoln Atkinson](http://latkin.org/blog/).

[![F# 4.0 Quick Picks](http://www.wintellect.com/devcenter/wp-content/uploads/2015/07/fsharp40Picks.jpg)](https://channel9.msdn.com/Events/Visual-Studio/Visual-Studio-2015-Final-Release-Event/Six-Quick-Picks-from-Visual-F-40)

However, there are quite a [few other new features](https://www.visualstudio.com/en-us/news/vs2015-vs#fsharp). Let's take a quick look at a few of the ones I'm excited about.

#### Script Debugging

I'm sure I'm like most of the folks who mess with F# that they do a lot of their work with F# scripts and the [F# Interactive](http://www.wintellect.com/devcenter/jwood/using-the-f-interactive), however before F# 4.0 there was no way to debug your scripts. Now we get the best of continuing to use scripts and being able to debug them!

![F# Debugging](http://www.wintellect.com/devcenter/wp-content/uploads/2015/07/fSharpDebug.gif)

#### Normalized Modules

As Lincoln mentioned in the above video, all functions are now normalized across `List`, `Seq`, and `Array` collections. This means that you can access the same functions among each of these collections where as in F# 3.1 that wasn't always the case.

#### Slicing Syntax in Lists

In F# 4.0 we can now use the syntax similar to the below for slicing in Lists instead of converting to an `Array` to slice then convert back to a `List`.

```fsharp
let l = [1..5] // val l : int list = [1; 2; 3; 4; 5]

l.[0..] // val it : int list = [2; 3; 4; 5]
l.[..4] // val it : int list = [1; 2; 3; 4; 5]
```

Those are just some of the more interesting changes made to the language. The folks over at the .NET Blog also [made a nice summary of all the big changes](http://blogs.msdn.com/b/dotnet/archive/2015/04/29/rounding-out-visual-f-4-0-in-vs-2015-rc.aspx).

---
Wonder what F# and Wintellect can do for you or just have more questions about F# in general? Feel free to [reach out to us]()!