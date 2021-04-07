## layoutSubviews

> You should not call this method directly. If you want to force a layout update, call the **setNeedsLayout**method instead to do so prior to the next drawing update. If you want to update the layout of your views immediately, call the **layoutIfNeeded**method.
>
> **setNeedsLayout**
>
> > Invalidates the current layout of the receiver and triggers a layout update during the next update cycle.
>
> **layoutIfNeeded**
>
> > Lays out the subviews immediately, if layout updates are pending.

### layoutSubviews 的调用时机

上述有提到不应该直接调用方法，那什么时候调用呢？

#### 1. addSubView 会触发

#### 2. 修改 view 的 Frame 会触发 layoutSubviews

#### 3. UIScrollView 滚动时会触发

#### 4. 旋转 screen 会触发



### drawRect

> The default implementation of this method does nothing. Subclasses that use technologies such as Core Graphics and UIKit to draw their view’s content should override this method and implement their drawing code there. You do not need to override this method if your view sets its content in other ways. For example, you do not need to override this method if your view just displays a background color or if your view sets its content directly using the underlying layer object.

1. `-drawRect:(CGRect)rect`方法：重写此方法，执行重绘任务
2.  `-setNeedsDisplay`方法：标记为需要重绘，异步掉用`drawRect`
3.  `-setNeedsDisplayInRect:(CGRect)rect`方法：标记为需要局部重绘

> This method is called when a view is first displayed or when an event occurs that invalidates a visible part of the view. You should never call this method directly yourself.
>
>  To invalidate part of your view, and thus cause that portion to be redrawn, call the **setNeedsDisplay** or **setNeedsDisplayInRect:**method instead.



### Size To Fit

#### sizeToFit

> Resizes and moves the receiver view so it just encloses its subviews.
>
> Call this method when you want to resize the current view so that it uses the most appropriate amount of space.

会计算出最优的 size 而且会改变自己的size



#### sizeThatFits

> Asks the view to calculate and return the size that best fits the specified size.
>
> The default implementation of this method returns the existing size of the view. 

会计算出最优的 size 但是不会改变 自己的 size