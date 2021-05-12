本章节主要从视图、网络、设计模式几个方面考察开发者的开发水准，这是任何一个合格的 iOS 开发者都应该具备的基本素养。
![](https://upload-images.jianshu.io/upload_images/22877992-62362c3afb69b44f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
iOS 开发中最重要的 API 就是 UIKit。它是苹果官方提供的管理界面和交互的最基本的 API。UIKit 被用在所有的 iPhone 和 iPad 开发中，它涵盖的内容包括触摸和交互处理、视图布局、图形绘制中。可以说 UIKit 相关知识点的考察是所有面试中最基本、最必不可少、最重要的一环。

本节将从用户界面聊起，回答开发中常见的布局和交互问题；之后将重点集中在动画渲染上，最后的问答题将集中在 iPad 的多屏开发上。对于 iOS 11 中最新的 drag and drop 和安全区域亦有涉及。

## UI 控件和基本布局

### 1.要在 UIView 上定义一个 Label有 哪几种方式？

**关键词：#storyboard #xib #Frame #Auto Layout**

这道题本身问法十分模糊。定义一个 Label，指的是创建一个，还是说给它做相应的布局，亦或是设置它的属性值？这都是要和面试官进行进一步沟通确定的。

假如我们要从零创建一个 label，配置它在页面上的布局，并设置属性值，有以下几种方式。

*   用 storyboard 或 xib 完成。直接在库面板中拖拽一个 label 完成创建，然后设置相应的 constraint 进行布局，最后在属性检查器面板对相应属性进行设置。这是苹果推荐的做法。

*   用纯代码的方式来做。在 ViewController 中新建一个 label，然后用 frame 或是 auto layout（可以用 anchor 或 NSLayoutConstraint ）来布局，最后再一个个属性进行手动设置。

### 2.storyboard/xib，和纯代码构建 UI 相比，有什么优缺点？

**关键词：#可视化 #多人协作 #性能**

storyboard/xib 的开发方式优点和缺点都十分明显。优点是：

*   **简单直接。**直接拖拽和点选即可配置 UI，界面所见即所得。

*   **跳转关系清楚。**Storyboards 中可以清楚的区分 View Controller 界面之间的跳转关系。而且在代码中，通过实现 prepare(for segue: UIStoryboardSegue, sender: Any?)，我们可以统一管理界面跳转和数据管理。

缺点是：

*   **协作冲突。**多人编辑时很容易产生冲突，且冲突很难解决。因为自带 Xcode 和系统的版本号，协作时 storyboard/xib 会在相同位置做同样修改，这样代码冲突几乎是不可避免的。解决方法是细分 storyboard 以及对应工程师的职责，但是这样同样带来了维护成本。

*   **很难做到界面继承和重用。**代码中实现要容易和明确得多，然而 storyboard/xib 却很难做到。

*   **不便于进行模块化管理。**storyboard/xib 中搜索起来很不方便，且统一修改多个 UI 控件的属性值不可能，必须一个一个改。在代码中一个工厂模式就可以搞定。

*   **性能影响。**storyboard/xib 在界面渲染上有时会成为性能杀手。例如首页 UI 构造时，代码书写和优化就会比 storyboard 多图层的渲染要好很多。

### 3.Auto Layout 和 Frame 在 UI 布局和渲染上有什么区别？

**关键词： #性能**

*   **Auto Layout 是针对多尺寸屏幕的设计。**其本质是通过线性不等式对 UI 控件的相对位置进行设定，从而适配多种 iPhone/iPad 屏幕的尺寸。

*   **Frame 是基于 xy 坐标轴系统的布局机制。**它从数学上限定了 UI 控件的具体位置，是 iOS 开发中最底层、最基本的界面布局机制。

*   **Auto Layout 的性能比 Frame 差很多。**Auto Layout 的布局过程首先求解线性不等式，然后再转化为 Frame 去进行布局。其中求解的计算量非常大，通常 Auto Layout 的性能损耗是 Frame 布局的 10 倍左右。

**加分回答：**

解决方法是尽量压缩视图层级减少计算量；同时 Layout 的计算也可以通过后台线程来处理，这样就可以不阻塞主线程操作。计算结果亦可以缓存起来，加速之后界面布局渲染。成熟的解决方案有 Facebook 的 ComponentKit，Pinterest 的 Texture（前身是 ASDK ），以及 LinkdedIn 的 LayoutKit。

### 4.UIView 和 CALayer 有什么区别？

**关键词： #性能 #交互**

*   **UIView 和 CALayer 都是 UI 操作的对象。**两者都是 NSObject 的子类，发生在 UIView 上的操作本质上也发生在对应的 CALayer 上。

*   **UIView 是 CALayer 用于交互的抽象。**UIView 是 UIResponder 的子类（ UIResponder 是 NSObject 的子类），提供了很多 CALayer 所没有的交互上的接口，主要负责处理用户触发的种种操作。

*   **CALayer 在图像和动画渲染上性能更好。**这是因为 UIView 有冗余的交互接口，而且相比 CALayer 还有层级之分。CALayer 在无需处理交互时进行渲染可以节省大量时间。

### 5.请说明并比较以下关键词：Frame, Bounds, Center

**关键词： #坐标 #父视图**

*   Frame 是指当前视图（View）相对于父视图的平面坐标系统中的位置和大小。

*   Bounds 是指当前视图相对于自己的平面坐标系统中的位置和大小。

*   Center 是一个 CGPoint，指当前视图在父视图的平面坐标系统中最中间位置点 。

**加分回答：**

介绍完上述概念后，下面用画图的方式来讲解这三个词的区别。如下图：

![image](https://upload-images.jianshu.io/upload_images/22877992-fd7efc8696625243.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中 View B 左上角的点的frame 值是(200, 100)，bounds 值是(0, 0)，center 所对应点的值是
（275, 200）。

### 6.请说明并比较以下方法：layoutIfNeeded, layoutSubviews, setNeedsLayout

**关键词： #布局 #周期**

*   layoutIfNeeded 方法一旦调用，主线程会立即强制重新布局，它从当前视图开始，一直到完成所有子视图的布局。

*   layoutSubviews 是用来自定义视图尺寸调整的。它是系统自动调用的，开发者不能手动调用。我们能做的就是重写该方法，让系统在尺寸调整时能按照希望的效果去进行布局。这个方法主要在屏幕旋转、滑动或触摸界面、子视图修改时被触发。

*   setNeedsLayout 与 layoutIfNeeded 相似，唯一不同的就是它不会立刻强制视图重新布局，而是在下一个布局周期才会触发更新。它主要用在多个 view 布局先后更新的场景下。例如我们要在两个布局不停变化的点之间连一条线，这个线的布局就可以调用 setNeedsLayout 方法。

### 7.请说明并比较以下关键词：Safe Area, SafeAreaLayoutGuide, SafeAreaInsets

**关键词： #安全区域**

由于 iPhone X 全新的刘海设计，iOS 11 中引入了安全区域（Safe Area）这套概念。如下图：

![image](https://upload-images.jianshu.io/upload_images/22877992-3ad7ff38b7efd9ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   **Safe Area 是指应用合理显示内容的区域。**它不包括 status bar, navigation bar, tab bar , tool bar 等。iPhone X 中一般是指扣除了顶部的 status bar（高度为20）、navigation bar（高度为44）和底部的 home indicator 区域（高度为34），这样应用的内容不会被刘海挡住或是影响底部手势操作。

*   **SafeAreaLayoutGuide 是指 SafeArea 的区域范围和限制 。**在布局设置中，我们可以分别取得它的上下左右 4 个边界的位置进行相应布局处理。

*   **SafeAreaInsets 限定了 SafeArea 区域与整个屏幕之间的布局关系。**一般我们用上下左右 4 个值来获取 SafeArea 与屏幕边缘之间的距离。

## 动画

### 8.iOS 中实现动画的方式有几种？

**关键词： #UIViewPropertyAnimator #UIView Animation #CALayer Animation**

最主要的实现动画方式有 3 种，UIView Animation、CALayer Animation、UIViewPropertyAnimator。

*   **UIView Animation 可以实现基于 UIView 的简单动画。**它是 CALayer Animation 的封装，主要可以实现移动、旋转、缩放、变色等基本操作。其基本函数为`+ animateWithDuration:animations:`，其中持续时间（duration）为基本参数，block 中对 UIView 属性的调整就是动画结束后的最终效果。除此之外他还有关键帧动画和两个 view 转化等接口。它实现的动画无法回撤、暂停、与手势交互。

*   **CALayer Animation 是更在底层 CALayer 上的动画接口。**除了 UIView Animation 可以实现的效果。它可以修改更多的属性以实现各种复杂的动画效果。其实现的动画可以回撤、暂停、与手势交互。

*   **UIViewPropertyAnimator 是 iOS 10 引进的处理交互式动画的接口。**它也是基于 UIView 实现，可以实现所有的 UIView Animation 效果。它最大的优点在于 timing function 以及与手势配合的交互式动画设置相比 CALayer Animation 十分简便，可以说是为交互而生的动画接口。

### 9.代码实现：控制屏幕上的圆形小球，使其水平向右滑动 200 个 point。

**关键词： #UIViewPropertyAnimator #交互式动画**

这道题很明显是要求实现动画。然而，题目中对于动画的各种参数（持续时间，延时，速度控制等）都没有要求。我们在做这道题目的时候一定要就相关细节向面试官询问清楚，切忌上来就写——实际开发中最怕用户需求都不明白就写代码，最终也只会是南辕北辙。

假设圆形小球已经在屏幕上，面试官没有参数要求，只是要实现水平移动的效果。那么实现代码如下：

```
// UIViewPropertyAnimator实现
let animator = UIViewPropertyAnimator(duration: 2, curve: .linear) {
  circle.frame = circle.frame.offsetBy(dx: 200, dy: 0)
}
animator.startAnimation()

// UIView Animation实现
UIView.animate(withDuration: 2) {
  circle.frame = circle.frame.offsetBy(dx: 200, dy: 0)
}

// CALayer实现
let animation = CABasicAnimation.init(keyPath: "position.x")
animation.fromValue = circle.center.x
animation.toValue = circle.center.x + 200
animation.duration = 2
self.circle.layer.add(animation, forKey: nil)

```

**追问：假如需要根据手势来控制小球的水平移动，该怎么操作？**

这次考察的是交互式动画，那么交互式动画用 UIViewPropertyAnimator 来做最为方便。关于手势具体如何控制球的移动，请向面试官询问。我们假设面试官给出如下要求：

*   一开始小球静止，除非用户触摸屏幕，否则小球不动
*   按住屏幕并左右滑动，此时小球随手势线性左右滑动
*   松开手，小球从当前位置滑动到水平初始距离向右 200 points 处，整个移动过程是先快后慢的效果
*   当再次触摸屏幕时，如果小球未滑动到终点，则小球将暂停滑动，再次随手势线性滑动
*   当到达终点后，无论用户如何触摸屏幕，小球在终点静止不动

从上述要求中我们知道：timing function 是 ease out，开始时暂停动画。随着手势的移动，我们记录动画的完成度 fractionComplete。当手势释放时，我们继续动画，让其自动完成。注意手势操控动画进行交互的时候，Animator 会自动将 timing function 从 ease out 转为 linear。代码如下：

```
var progress: CGFloat = 0
var animator: UIViewPropertyAnimator!

override func viewDidLoad() {
  let gesture = UIPanGestureRecognizer(target: self, action: Selector.handlePan)
  view.addGestureRecognizer(gesture)

  animator = UIViewPropertyAnimator.init(duration: 2, curve: .easeOut, animations: {
    self.circle.frame = self.circle.frame.offsetBy(dx: 200, dy: 0)
  })
}

func handlePan(recognizer:UIPanGestureRecognizer) {
  switch recognizer.state {
  case .began:
    animator.pauseAnimation()
    progress = animator.fractionComplete
  case .changed:
    let translation = recognizer.translation(in: self.circle)
    animator.fractionComplete = translation.x / 200 + progress
  case .ended:
    animator.continueAnimation(withTimingParameters: nil, durationFactor: 0)
  default:
    break
  }

```

## 多任务开发

### 10.iOS 开发中，如何保证应用的 UI 在 iPhone、iPad 以及 iPad 分屏情况下依然适用？

**关键词：#Adaptive UI #Size Class #Auto Layout**

为了针对各种机型，苹果在 iOS 8 中引入了 Adaptive UI 的概念。所以要保证应用的 UI 在各种情况下依然良好，主要注意以下几个点：

*   **采用 Auto Layout。**与 frame 设置绝对位置不同，所有的 UI 控件将保持相对位置。例如将 label 设置成对应屏幕 center X, center Y，此时无论是 iPhone 还是 iPad，此 label 都将相对于屏幕居中。

*   **采用 Size Class。**很多时候 UI 控件可能在 iPhone 上大小刚好，但在 iPad 上可能偏小，位置也有可能有偏移。此时用 Size Class，可以分别在不同的机型上进行安装/卸载对应的 constraint，并且可以方便的进行预览。苹果将自家设备按照横纵两个尺寸进行区别，不同的情况对应的 Regular 和Compact 组合。比如竖屏的 iPhone 宽度是 Compact，高度是 Regular。具体分类请看下图：

![image](https://upload-images.jianshu.io/upload_images/22877992-b738667fdea3506f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   **关注多屏情况。**iPad 上引进的多屏情况主要分三种：Slide Over，Split View，Picture in Picture。苹果明确指出应用应该支持 Slide Over 和 Split View。这时候作为工程师，应该多多与设计师交流针对这两种情况的 UI 设计，并配合 Size Class 进行分类适配。下图详尽说明了 iPad 上多任务的尺寸分类：

![image](https://upload-images.jianshu.io/upload_images/22877992-6816b11c3ecf4fac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 11.代码实现：将 UIImageView 上的图片直接拖拽到另一个 UIImageView 上。

**关键词：#Drag and Drop**

这道题考察的是 iOS 11 最新引入的 Drag and Drop 功能。跟很多面试题一样，它没有说明起始和终止的 UIImageView 是否在一个应用之内。如果在同一个应用之内，那么无论是 iPhone 还是 iPad 都能实现这样的功能；如果是把图片从一个应用拖拽到另一个应用之上，那么只能是 iPad 实现。

我们假设面试官考察的是在同一个应用中，将一张图片从一个 UIImageView 中拖拽到另一个 UIImageView 。

Drag and Drop 一般实现起来分3步：

**1\. 对相应的 UIImageView 分别添加 drag 和 drop delegate**

```
dragImageView.addInteraction(UIDragInteraction(delegate: self))
dropImageView.addInteraction(UIDropInteraction(delegate: self))

```

注意，dragImageView 和 dropImageView 的 userInteractionEnabled 必须是 true。

**2.实现 drag delegate 规定的方法**

```
extension ViewController: UIDragInteractionDelegate {
  func dragInteraction(_ interaction: UIDragInteraction, itemsForBeginning session: UIDragSession) -> [UIDragItem] {
    if interaction.view == dragImageView {
      let dragImage = dragImageView.image
      let itemProvider = NSItemProvider(object: dragImage!)
      let dragItem = UIDragItem(itemProvider: itemProvider)
      return [dragItem]
    } else {
      return []
    }
  }
}

```

这个方法的功能就是告诉系统，我们要拖动的对象。

方法里面的 NSItemProvider 简单来说就是用来在 Drag and Drop，或者 Extension app 和 Host app 之间传输数据的类。

UIDragItem 则是像对 NSItemProvider 的进一步封装，除了包含传输数据外，还可以自定义一些数据。

实现完该方法后，图片就可以从 dragImageView 里拖动出来了。

**3.实现 drop delegate 对应的方法**

一般来讲，需要实现 3 个方法：

```
// 询问是否可以处理 drag 的数据，默认是 true，所以并不一定要实现
func dropInteraction(_ interaction: UIDropInteraction, canHandle session: UIDropSession) -> Bool

// 询问系统当 drop 之时，以何种方式处理 drag 的数据
// UIDropProposal对应的操作是 cancel, forbidden, copy, move
func dropInteraction(_ interaction: UIDropInteraction, sessionDidUpdate session: UIDropSession) -> UIDropProposal

// drop 已经发生，取出 drag 中的数据并进行处理
func dropInteraction(_ interaction: UIDropInteraction, performDrop session: UIDropSession)

```

具体的代码如下：

```
func dropInteraction(_ interaction: UIDropInteraction, sessionDidUpdate session: UIDropSession) -> UIDropProposal {
  let dropLocation = session.location(in: view)
  let operation: UIDropOperation

  if dropImageView.frame.contains(dropLocation) {
    operation = .copy
  } else {
    operation = .cancel
  }
  return UIDropProposal(operation: operation)
}

func dropInteraction(_ interaction: UIDropInteraction, performDrop session: UIDropSession) {
  session.loadObjects(ofClass: UIImage.self) { [weak self] (imageItems) in
  self?.dragImageView.image = nil
  self?.dropImageView.image = imageItems.first as? UIImage
}

```

以上是最简单直接的实现方法。Drag and Drop 中还有很多可以定制的方法和属性，例如支持多点触摸的 preview 方法。工作中，你可能只需要实现 drag 功能，也可能只需要支持 drop 功能。

# 推荐👇：

如果你正在跳槽或者正准备跳槽不妨动动小手，添加一下咱们的交流群[**931 542 608**](https://jq.qq.com/?_wv=1027&k=cfVxrMAH)来获取一份详细的大厂面试资料为你的跳槽多添一份保障。

![](https://upload-images.jianshu.io/upload_images/22877992-0bfc037cc50cae7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
