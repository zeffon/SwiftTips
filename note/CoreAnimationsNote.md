

#Core Animation

---

###Ssection 2 autolayout

In iOS 8 and newer, the preferred approach now is to add constraints by setting their active property or calling `NSLayoutConstraint.activateConstraints(_:)` with an array to activate many constraints at once.

---
###Ssection 3 layer animation

--
####Chapter 8 getting started with layer animations

--

- **Views vs. Layers**

| Views      |     Layers |  
| :--------: | :--------:| 
| Complex view hierarchy layouts, Auto Layout, etc.    |   Simpler hierarchy, faster to resolve layout, faster to draw. | 
|User interactions.|No responder chain overhead.| 
|Often have custom logic or custom drawing code that executes on the main thread on the CPU.|No custom logic by default. Often cached and drawn directly on the GPU.|
|Very flexible, powerful, lots of classes to subclass.|Not as flexible, fewer classes to subclass.|

**Animations vs. real content**

When you animate a text field, you’re not actually seeing the field itself animated; instead, you’re seeing a cached version of it known as the presentation layer. The presentation layer is removed from the screen once the animation completes and the original layer shows itself again.

---
####Chapter 9: Animation Keys and Delegates

--

1. **change the layer** (Key-value coding compliance)

	```
	let layer = anim.valueForKey("layer") as? CALayer
	anim.setValue(nil, forKey: "layer")
	
	let pulse = CABasicAnimation(keyPath: "transform.scale") pulse.fromValue = 1.25
	pulse.toValue = 1.0
	pulse.duration = 0.25
	
	layer?.addAnimation(pulse, forKey: nil)
	```

2. **name CAAnimation**

	```
	flyRight.setValue("form", forKey: "zephoony”)//name caanimation
	flyRight.setValue(heading.layer, forKey: "layer”)//name calayer
	```

3. **get CALayer’s CAAnimation key name**
	```
	info.layer.animationKeys()
	```

4. **remove CAAnmation**
	Call `removeAllAnimations()` on the layer to stop all running animations or `removeAnimationForKey(_:)` to remove just one.

5. **delegate**

	```
	flyRight.delegate = self
	override func animationDidStart(anim: CAAnimation, finished flag: Bool) {}
	override func animationDidStop(anim: CAAnimation, finished flag: Bool) {
	
		if let name = anim.valueForKey("name") as? String { //This returns an optional value
		if name == "form" {
		//form field found
		  }
		}
	}
	```

---
####Chapter 10 Groups and Advanced Timing
1. **CAAnimationGroup**

	```
	let groupAnimation = CAAnimationGroup() groupAnimation.beginTime = CACurrentMediaTime() + 0.5 groupAnimation.duration = 0.5
	groupAnimation.fillMode = kCAFillModeBackwards
	
	    
	    let scaleDown = CABasicAnimation(keyPath:"transform.scale")
	    scaleDown.fromValue = 3.5
	    scaleDown.toValue = 1.0
	    
	    let rotation = CABasicAnimation(keyPath:"transform.rotation")
	    rotation.fromValue = CGFloat(M_PI_4)
	    rotation.toValue = 0.0
	    
	    let fade = CABasicAnimation(keyPath: "opacity")
	    fade.fromValue = 0.0
	    fade.toValue = 1.0
	    
	    groupAnimation.animations = [scaleDown, rotation, fade]
	    loginButton.layer.add(groupAnimation, forKey: nil)
	```

2. **Animation easing**

 **CAMediaTimingFunction**
    - **kCAMediaTimingFunctionLinear**
    - **kCAMediaTimingFunctionEaseIn**
    - **kCAMediaTimingFunctionEaseOut**
    - **kCAMediaTimingFunctionEaseInEaseOut**

	```
	groupAnimation.timingFunction = CAMediaTimingFunction( name: kCAMediaTimingFunctionEaseIn)
	//CAMediaTimingFunction(controlPoints:(float)c1x :(float)c1y :(float)c2x :(float)c2y)
	```

3. **More timing options**

- **Repeating animations**

	```
	flyLeft.repeatCount = 4
	flyLeft.autoreverses = true
	```
- **Changing the animation speed**

	```
	flyLeft.speed = 2.0
	```

---
####Chapter 11 layer spring
**the oscillating system**

 - **damping**
 - **mass**
 - **stiffness**
 - **initial velocity**

1. **UIKit vs. Core Animation springs**

	**CASpringAnimation** creates the spring animations for UIKit behind the scenes
	- **damping** – the damping applied to the system
	- **mass**– the mass of the weight in the system
	- **stiffness** – the stiffness of the spring attached to the weight 
	- **initialVelocity** – the initial push applied to the weight.

	```
	let pulse = CASpringAnimation(keyPath: "transform.scale") 
	pulse.duration = pulse.settlingDuration
	//settlingDuration estimates the time required for the system to settle
	```

	| Property      |     Default Value |  
	| :-------- | --------:| 
	| damping    |   10.0 |  
	| mass    |   1.0 | 
	| stiffness    |   100.0 | 
	| initialVelocity    |   0.0 | 

---
####Chapter 12 Keyframe Animations and Struct Properties

**CAKeyframeAnimation**

```swift
    let wobble = CAKeyframeAnimation(keyPath: "transform.rotation")
    wobble.duration = 0.25
    wobble.repeatCount = 4
//    wobble.autoreverses = true
    wobble.values = [0.0, -M_PI_4/4, 0.0, M_PI_4/4,0.0]
    wobble.keyTimes = [0.0, 0.25, 0.5, 0.75, 1.0]
    heading.layer.add(wobble, forKey: nil)
```

 **Animating struct values**
 
```swift
flight.values = [
	CGPoint(x: -50.0, y: 0.0),
	CGPoint(x: view.frame.width + 50.0, y: 160.0), 
	CGPoint(x: -50.0, y: loginButton.center.y)
].map { NSValue(CGPoint: $0) } 
flight.keyTimes = [0.0, 0.5, 1.0]
```

---
####Chapter 13 Shapes and Masks
**CABasicAnimation**
**CAShapeLayer** -- a CALayer subclass 

- `path` – morph the layer’s shape into a different shape.(`UIBezierPath`)
- `fillColor` – change the fill tint of shape to a different color.
- `lineDashPhase` – create a marquee or “marching ants” effect around your shape. 
- `lineWidth` – grow or shrink the size of the stroke line of your shape.

```swift

let circleLayer = CAShapeLayer()
let maskLayer = CAShapeLayer()
let photoLayer = CALayer()
  
//Size the avatar image to fit
photoLayer.frame = CGRect(
x: (bounds.size.width - image.size.width + lineWidth)/2,
y: (bounds.size.height - image.size.height - lineWidth)/2,
width: image.size.width,
height: image.size.height)

//Draw the circle
circleLayer.path = UIBezierPath(ovalIn: bounds).cgPath
circleLayer.strokeColor = UIColor.white.cgColor
circleLayer.lineWidth = lineWidth
circleLayer.fillColor = UIColor.clear.cgColor
//Size the layer
maskLayer.path = circleLayer.path
maskLayer.position = CGPoint(x: 0.0, y: 10.0)


photoLayer.mask = maskLayer
```

**Morphing shapes**


---
####Chapter 14 Gradient Animations

**CAGradientLayer**

- `colors`: animate the gradient’s colors to give it a tint.
- `locations`: animate the color milestone locations to make the colors move around inside the gradient.
- `startPoint` and `endPoint`: animate the extents of the layout of the gradient.

**Animating gradients**
**CABasicAnimation**
**Creating a text mask**

```
let textAttributes: [String: AnyObject] = { 
	let style = NSMutableParagraphStyle() 
	style.alignment = .Center
	return [
	NSFontAttributeName:UIFont(name: "HelveticaNeue-Thin", size: 28.0)!, 
	NSParagraphStyleAttributeName: style
	] 
}()

view.setNeedsDisplay()
        UIGraphicsBeginImageContextWithOptions(frame.size, false, 0.0)
        text.draw(in: bounds, withAttributes: textAttributes)
        let image = UIGraphicsGetImageFromCurrentImageContext()
        UIGraphicsEndImageContext()
        let maskLayer = CALayer()
        maskLayer.backgroundColor = UIColor.clear.cgColor
        maskLayer.frame = bounds.offsetBy(dx: bounds.size.width, dy: 0.0)
        maskLayer.contents = image
        gradientLayer.mask = maskLayer
```


**CGRectInset**--CGRect CGRectOffset(CGRect rect, CGFloat dx, CGFloat dy)是以rect为中心，根据dx和dy来实现缩小。
```
CGRect CGRectInset ( 
	CGRect rect, 
	CGFloat dx, 
	CGFloat dy 
); 
```
**CGRectOffset**--CGRect CGRectOffset(CGRect rect, CGFloat dx, CGFloat dy)
它是以rect左上角为基点，向X轴和Y轴偏移dx和dy像素。
```
CGRect CGRectOffset( 
	CGRect rect, 
	CGFloat dx, 
	CGFloat dy 
); 
```


**layout**

- **layoutSubviews**
- **layoutIfNeeded**
- **setNeedsLayout**
- **setNeedsDisplay**
- **drawRect**
- **sizeThatFits**
- **sizeToFit**

>1. `layoutSubviews`
这个方法，默认没有做任何事情，需要子类进行重写 。 系统在很多时候会去调用这个方法：
 1. 初始化不会触发layoutSubviews，但是如果设置了不为CGRectZero的frame的时候就会触发。
 2. addSubview会触发layoutSubviews
 3. 设置view的Frame会触发layoutSubviews，当然前提是frame的值设置前后发生了变化
 4. 滚动一个UIScrollView会触发layoutSubviews
 5. 旋转Screen会触发父UIView上的layoutSubviews事件
 6. 改变一个UIView大小的时候也会触发父UIView上的layoutSubviews事件
>>在苹果的官方文档中强调: You should override this method only if the autoresizing behaviors of the subviews do not offer the behavior you want.layoutSubviews, 当我们在某个类的内部调整子视图位置时，需要调用。反过来的意思就是说：如果你想要在外部设置subviews的位置，就不要重写。


>2. `setNeedsLayout` 标记为需要重新布局，不立即刷新，但layoutSubviews一定会被调用。配合layoutIfNeeded立即更新
>3. `layoutIfNeeded`如果有需要刷新的标记，立即调用layoutSubviews进行布局
>4. `drawRect` 这个方法是用来重绘的。
  * drawRect在以下情况下会被调用：
     1. 如果在UIView初始化时没有设置rect大小，将直接导致drawRect不被自动调用。drawRect调用是在Controller->loadView, Controller->viewDidLoad 两方法之后掉用的.所以不用担心在控制器中,这些View的drawRect就开始画了.这样可以在控制器中设置一些值给View(如果这些View draw的时候需要用到某些变量值).
     2. 该方法在调用sizeToFit后被调用，所以可以先调用sizeToFit计算出size。然后系统自动调用drawRect:方法。
     3. 通过设置contentMode属性值为UIViewContentModeRedraw。那么将在每次设置或更改frame的时候自动调用drawRect:。
     4. 直接调用setNeedsDisplay，或者setNeedsDisplayInRect:触发drawRect:，但是有个前提条件是rect不能为0。以上1,2推荐；而3,4不提倡
  * drawRect方法使用注意点：
     1. 若使用UIView绘图，只能在drawRect：方法中获取相应的contextRef并绘图。如果在其他方法中获取将获取到一个invalidate的ref并且不能用于画图。drawRect：方法不能手动显示调用，必须通过调用setNeedsDisplay 或者 setNeedsDisplayInRect，让系统自动调该方法。
     2. 若使用calayer绘图，只能在drawInContext: 中（类似于drawRect）绘制，或者在delegate中的相应方法绘制。同样也是调用setNeedDisplay等间接调用以上方法3、若要实时画图，不能使用gestureRecognizer，只能使用touchbegan等方法来掉用setNeedsDisplay实时刷新屏幕
5. `sizeToFit` and `sizeThatFits `
 * sizeToFit会自动调用sizeThatFits方法；
 * sizeToFit不应该在子类中被重写，应该重写sizeThatFits
 * sizeThatFits传入的参数是receiver当前的size，返回一个适合的size
 * sizeToFit可以被手动直接调用sizeToFit和sizeThatFits方法都没有递归，对subviews也不负责，只负责自己

---
####Chapter 15 Stroke and Path Animations


**CAShapeLayer**


	```
	    ovalShapeLayer.strokeColor = UIColor.white.cgColor
	    ovalShapeLayer.fillColor = UIColor.clear.cgColor
	    ovalShapeLayer.lineWidth = 4.0
	    ovalShapeLayer.lineDashPattern = [2.0, 3.0]
	    
	    let refreshRadius = frame.size.height / 2 * 0.8
	    ovalShapeLayer.path = UIBezierPath(ovalIn: CGRect(
	        x: frame.size.width / 2 - refreshRadius,
	        y: frame.size.height / 2 - refreshRadius,
	        width: 2 * refreshRadius,
	        height: 2 * refreshRadius
	    )).cgPath
	    layer.addSublayer(ovalShapeLayer)
	    ovalShapeLayer.strokeEnd = progress
	    
	    let strokeStartAnim = CABasicAnimation(keyPath: "strokeStart")
	    strokeStartAnim.fromValue = -0.5
	    strokeStartAnim.toValue = 1.0
	    
	    let strokeEndAnim = CABasicAnimation(keyPath: "strokeEnd")
	    strokeEndAnim.fromValue = 0.0
	    strokeEndAnim.toValue = 1.0
	    
	    let strokeAnimGroup = CAAnimationGroup()
	    strokeAnimGroup.duration = 1.5
	    strokeAnimGroup.repeatCount = 5.0
	    strokeAnimGroup.animations = [strokeStartAnim, strokeEndAnim]
	    ovalShapeLayer.add(strokeAnimGroup, forKey: nil)
	    
	    let flightAnimation = CAKeyframeAnimation(keyPath: "position") 
	    flightAnimation.path = ovalShapeLayer.path 
	    flightAnimation.calculationMode = kCAAnimationPaced
	    
	    let airplaneOrientationAnimation = CABasicAnimation( keyPath: "transform.rotation")	airplaneOrientationAnimation.fromValue = 0 airplaneOrientationAnimation.toValue = 2 * M_PI
	    
	```

- `lineDashPattern` This property lets you set a dash pattern for the shape stroke; you simply you provide an array with the length of the dash and the length of the gap in pixels.
- `calculationMode` is yet another way you can control the timing of your animation. When you set that property to kCAAnimationPaced Core Animation animates your layer with a constant pace ignoring any key times you’ve set. This is very useful for producing smooth animations across an arbitrary path.
- M_PI = 180度

---
####Chapter 16 Replicating Animations

**CAReplicatorLayer**

- `instanceDelay` – animate the amount of delay between instances- `instanceTransform` – change the transform between replications on the fly- `instanceColor` – change the blend color used for all instances- `instanceRedOffset`, instanceGreenOffset, instanceBlueOffset – apply a delta to apply to each instance color component
- `instanceRedOffset`, instanceGreenOffset, instanceBlueOffset – apply a delta to apply to each instance color component- `instanceAlphaOffset` – change the opacity delta applied to each instance

```
replicator.instanceCount = Int(view.frame.size.width / dotOffset)
replicator.instanceTransform = CATransform3DMakeTranslation( -dotOffset, 0.0, 0.0)
	
	
let scale = CABasicAnimation(keyPath:"transform")
scale.fromValue = NSValue(caTransform3D:CATransform3DIdentity)
scale.toValue = NSValue(caTransform3D:CATransform3DMakeScale(1.4, 15, 1.0))
scale.duration = 0.33
scale.repeatCount = Float(CGFloat.infinity)
scale.autoreverses = true
scale.timingFunction = CAMediaTimingFunction(name:kCAMediaTimingFunctionEaseOut)
dot.add(scale, forKey: "dotScale")
```  
	
-----
####CALayer
>`CALayer`属于`QuartzCore`框架

CALayer

 1. **CAShapeLayer**
 2. **CAGradientLayer**
 3. **CAReplicatorLayer**
 
--

-  Examples of animations you can perform on layers
 - **move**
 - **scale**
 - **rotate**
 - **transparency**
 - **corner radius**
 - **background color**

--
1. **position and size**

 - **bounds**: modify this to animate the bounding frame of the layer. - **position**: modify this to animate the position of the layer within its parent layer. You can animate position.x or position.y separately if you want to control movement on only one axis. - **transform**: modify this to move, scale, and rotate the layer. You can even animate layers in 3D space, which you can’t do with views alone. You’ll learn about 3D layer transforms in Section IV, “3D Animations”.

2. **Border**

 - **borderColor**: modify this to change the border tint. - **borderWidth**: modify this to grow or shrink the width of the border. - **cornerRadius**: modify this to change the radius of the layer’s rounded corners. 
 
3. **Shadow**
 - **shadowOffset**: modify this to make the shadow appear closer to or further away from the layer. - **shadowOpacity**: modify this to make the shadow fade in or out. - **shadowPath**: modify this to change the shape of the layer’s shadow. You can make different 3D effects to make your layer look like it’s floating with different shadow shapes and positions. - **shadowRadius**: modify this to control the blur of the shadow; this is especially useful when simulating movement of the view towards or away from the surface where the shadow is cast.
 
4.**Contents**

  - **contents**: modify this to assign raw TIFF or PNG data as the layer contents.  - **mask**: modify this to establish the shape or image you’ll use to mask the visible contents of the layer; you’ll use this property to create some very cool effects in Chapter 13, “Shapes and masks”.  - **opacity** – animate the transparency of the layer contents.
 
--
>
- 所有的非`Root Layer`在设置`Animatable Properties`的时候都存在着隐式动画,默认的`duration`是**0.25**秒。对于每一个`UIView` 都有一个 `layer`,把这个 `layer` 且称作`RootLayer`,而不是 `View` 的根`Layer`的叫做 非 `RootLayer`。
- `CALayer`的`position`类似`UIView`的`center`
- `presentation layer`: The presentation layer is removed from the screen once the animation completes and the original layer shows itself again.You can’t tap the animated field, enter any text or engage any other specific text field functionality, because it’s not the real text field, just a “phantom” visible representation.To solve this conundrum, you’ll need to use another `CABasicAnimation` property: isRemovedOnCompletion.
>
	```
	flyRight.isRemovedOnCompletion = false
	flyRight.fillMode = kCAFillModeBoth//
	```

---

####CABasicAnimation
>1. A `CABasicAnimation` object is just a data **model**, which is not bound to any particular layer.
>2. `layer.add(_: forKey:)` makes a **copy** of the animation object

--
**CAAnimation types:**

1. **CABasicAnimation**
2. **CASpringAnimation**
3. **CAKeyFrameAnimation**
4. **CAAnimationGroup**

--
**properties:**

1. **fillMode**（需要将`isRemovedOnCompletion`设置为`false`）
>The fillMode property lets you control the behavior of your animation at the beginning and end of its sequence.

- `kCAFillModeRemoved`：默认，`CAAnimation`和`CALayer`互相不影响
- `kCAFillModeForwards`：当`CAAnimation`结束后，`CALayer`保留`CAAnimation`最后的状态
- `kCAFillModeBackwards`：当`CAAnimation`结束后，`CALayer`保留`CAAnimation`初始的状态
- `kCAFillModeBoth`：`CAAnimation`开始前，`CALayer`保留`CAAnimation`的初始状态，`CAAnimation`结束后，`CALayer`保留`CAAnimation`的最后状态。

2. **repeatCount**

```
flyLeft.repeatCount = 2.5
flyLeft.autoreverses = true
```

3. **speed**

```
flyLeft.speed = 2.0
info.layer.speed = 2.0
```



--
**delegate**

```
func animationDidStart(anim: CAAnimation)func animationDidStop(anim: CAAnimation, finished flag: Bool)
```

--
**keyPath types**

- `backgroundColor`
- `cornerRadius`
- `opacity`
- `position.x`
- `position.y`
- `transform.scale`
- `transform.rotation` `//rotate.fromValue = CGFloat(M_PI_4)`



---
参考链接

1. [谈谈iOS动画](http://geeklu.com/2012/09/animation-in-ios/)
2. [Key-Value Coding Extensions](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CoreAnimation_guide/Key-ValueCodingExtensions/Key-ValueCodingExtensions.html)