# AC3.2-AnimationBasics
---

### Animations the Old Way

Previously, like laying out views in general, basic movement animations were done using primarily `frame`s. *(LINK TO ANIMATABLE PROPERTIES)*. The code is somewhat straightforward to read, but difficult to extend and create complex animations.

#### Animating a View Left-to-Right: `UIView.animate`

```swift
  // This code moves a view's origin.x by 300pt
  let newFrame = view.frame.offsetBy(dx: 300.0, dy: 0.0)
  UIView.animate(withDuration: 1.0) { 
    view.frame = newFrame
  }
```

`UIView.animate(withDuration:)` was used as a simple way to execute view animations inside of its closure. iOS would look at the starting state of the view, along with the changes specified in the animation block and then automatically generate all of the "tweening" (in-between states) needed for the animation to occur smoothly. The class functions on `UIView` are very convenient and allow for some decent customization for basic animations to a view's animatable properties. Along with the duration of the animation it is also possible to adjust delays, animation curves, and other options. It is still possible (and pretty common) to do these sorts of animations with `UIView`'s class functions, but in iOS10 Apple has taken things one step further with `UIViewPropertyAnimator` *(LINK TO DOCUMENTATION)*

> "A UIViewPropertyAnimator object lets you animate changes to views and dynamically modify your animations before they finish. With a property animator, you can run your animations from start to finish normally or you can turn them into interactive animations and control the timing yourself." 

---

### A Simple Example of `UIViewPropertyAnimator`

`UIViewPropertyAnimator` works similar to its `animate(withDuration:)`: you put in the changes to animate within a block, you adjust durations, delay and (animation) curves, and have optional completion handlers.

#### Animating a View Left-to-Right: `UIViewPropertyAnimator`

```swift

  let newFrame = view.frame.offsetBy(dx: 300.0, dy: 0.0)
  UIViewPropertyAnimator(duration: 1.0, curve: .linear) { 
      view.frame = newFrame
  }.startAnimation()
```

Not too much different right? Before we go onto doing some of the more interesting things `UIViewPropertyAnimator` can do, let's first talk about animation curves, and explain what that `.linear` means. 

#### Animation Curves: [`UIViewAnimationCurve`](https://developer.apple.com/reference/uikit/uiviewanimationcurve)

 - `.easeInOut` - An ease-in ease-out curve causes the animation to begin slowly, accelerate through the middle of its duration, and then slow again before completing.
 - `.easeIn` - An ease-in curve causes the animation to begin slowly, and then speed up as it progresses.
 - `.easeOut` - An ease-out curve causes the animation to begin quickly, and then slow down as it completes.
 - `.linear` - A linear animation curve causes an animation to occur evenly over its duration.
 
Just writing them down isn't really going to help us visualize the actual movement. Let's code a view for each of these curves. 

> Class Coding: I will demo creating a single view and animating it. Follow along and complete 3 additional views, each with a differing animation curve. Oh, and did I mention? We're using Snapkit to save us some time 💪. Let's also control when the animations start by adding in a button to signal it's start.

```swift
  // instatiation
  internal lazy var darkBlueView: UIView = {
    let view: UIView = UIView()
    view.backgroundColor = UIColor(colorLiteralRed: 51.0/255.0, green: 77.0/255.0, blue: 92.0/255.0, alpha: 1.0)
    return view
  }()
  
  // constraints
  darkBlueView.snp.makeConstraints { (view) in
    view.leading.equalToSuperview().offset(20.0)
    view.top.equalToSuperview().offset(20.0)
    view.size.equalTo(CGSize(width: 100.0, height: 100.0))
  }
  
  // example function
  internal func animateDarkBlueViewWithSnapkit() {
    
    // we use remakeConstraints for.. well, remaking a view's constraints in Snapkit
    self.darkBlueView.snp.remakeConstraints({ (view) in
      view.trailing.equalToSuperview().inset(20.0)
      view.top.equalToSuperview().offset(20.0)
      view.size.equalTo(CGSize(width: 100.0, height: 100.0))
    })
    
    // this one will be .linear, add 3 more views for .easeInOut, .easeIn, .easeOut
    let propertyAnimation = UIViewPropertyAnimator(duration: 1.0, curve: .linear) {
        self.view.layoutIfNeeded()
    }
    
    propertyAnimation.startAnimation()
    
  }
```

> Why is it necessary to use `self.view.layoutIfNeeded()` inside of the animation block, instead of just remaking the constraints? Adjusting the constraints is just part of the process; `layoutIfNeeded()` signals to the autolayout engine that it must immediately re-evaluate a view's constraints. Calling for that signal inside of an animation block results in getting an animation out of the redrawing/arranging that needs to happen.

---


