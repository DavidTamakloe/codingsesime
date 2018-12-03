---
title: And all that code was useless [Today In Code]
date: '2018-12-03'
---

![fabian-grohs-437907-unsplash.jpg](nathan-dumlao-492751-unsplash.jpg)

### And all that code was useless

Today I worked on a whole bunch of stuff, I was crazy productive. I wrote things down on the todo list. I crossed them out with vim. Self high-fives all over the place. I felt pretty damn good about myself and my day. And all that code was useless.

I’ve been working on a React Native app for the past month or so. Incredibly low throughput if we’re being completely honest. But today I did a lot. I finally run the app on android, (everything I did up until now was on iOS).
It was horrible.
All the UI goodness that worked on iOS was just all over the place on the Nexus emulator.
I worked on all the little differences. I sorted them out one by one, until the android app looked just as good as the iOS version. And all that code was useless.

### Why all that code was useless

The app has to interface with a third party SDK from a company we’re collaborating with. They only have an android version for now - hence the necessity for me to run on android.  
After I’d had my very productive day I settled down to read their SDK documentation to plan out the course of action for integration. And that’s when it began to dawn on me.
I argued with myself. I googled endlessly. But in the dying light of the 5:45pm sun I had to accept the cold hard facts.
The SDK just isn’t built in such a way that a React Native app will integrate easily with it. I mean I could be stubborn and pour hours of hacky coding into it, but who has the energy y’know.

I’m still going to write out the work I did though. Because it’s necessary that I remember what my time was spent doing, so it doesn’t feel like I’m in the red queen’s race.. Jay-Z taught me that.

### Things that are different on React Native for Android vs iOS.

#### Button didn't work on android (They fixed it)

**Issue:** There’s a bug in React Native version 0.57.3 that makes it such that the Button component doesn’t work on android. It works well on iOS though.  
**What I did:** I ended up having to nuke my node_modules folder and reinstall an earlier version of React Native. I found that on a [github issue thread](https://github.com/facebook/react-native/issues/21754). The thread got a bit heated; people criticising react-native as a whole. Contributors had to lock it and everything. Open Source is hard chale.

#### Fonts

**Issue**. Fonts work differently on android and iOS. Well obviously.  
So on iOS once you put the fonts where they need to be, if you specify the fontFamily, the OS can determine the fontWeight from the name.  
So say a font is called Montserrat. I have different .ttf files for different font weights. (i.e. Montserrat-Bold.ttf, Montserrat-Regular.ttf etc.)  
On iOS if I set the fontFamily to Montserrat and I set the fontWeight to ‘bold’,

```
{
    fontFamily: 'Montserrat',
    fontWeight: 'bold'
}
```

the OS knows to pick Montserrat-Bold.ttf.  
On android however you have to set the exact _filename_ of the font you want to use. So using Montserrat with `fontWeight: 'bold'` does not work. In fact even using `fontFamily: Montserrat` won’t work! because the exact _filename_ is _Montserrat-Regular.ttf_.  
**What I did:** The good news though, If you use the exact filename as fontFamily, iOS picks it too. So I ended up having to use that approach in all files to get the fonts to work on both platforms.
(by that I mean if I wanted bold I would set say `fontFamily: 'Montserrat-Bold'`)

#### Emulator vs Simulator

**Issue:**. I realised from another [github thread](https://github.com/facebook/react-native/issues/10404#issuecomment-267303151) (in the react-native community you get more answers from github issue threads than from stackoverflow.. weird), that the android virtual device is an _emulator_, while the iOS virtual device is a _simulator_.
Seems like a non-consequential difference doesn’t it. Well there’s an important distinction: since the emulator is _emulating_ a real world device, it doesn’t know what "localhost" is. In your app development if you’re testing against a local api this can be a real head-scratcher.  
**What I did:** I ended up having to check the current ip address of my computer and use that instead of localhost.

There were a number of other problems with things like KeyboardAvoidingView, for which I ended up having to use conditional code to set the behaviour prop and things like that that I’m not gonna write out here.

### In conclusion

Sometimes all your code is useless. :)
