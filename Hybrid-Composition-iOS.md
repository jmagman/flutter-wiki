# Hybrid Composition for iOS

## How are UIViews embedded?
To support embedded native views, a new leaf node type **EmbeddedView** has been added. 

EmbeddedViews are not painted by Flutter but rather contain a view id to a UIView that will be composited into the Flutter layer tree.

## Composition
Flow (the Flutter Compositor) and Quartz (the iOS Compositor) work together to composite EmbeddedViews.

General strategy: Traverse the Flow layer tree until we encounter an EmbeddedView node. 

The parents on the EmbeddedView into one Quartz node with the UIView as a child node.

### Flow Layer Tree / Quartz Layer Tree Examples

### Example 1: No EmbeddedViews
When no EmbeddedViews are included, all of the Flow nodes can be painted on one canvas and result in a single Quartz node.

The left is the Flow layer tree, the right is the Quartz layer tree.

![Flow Layer Tree](https://user-images.githubusercontent.com/25163644/94746860-a88a5280-034b-11eb-8d50-26a600ba76dd.png)
*Flow Layer Tree*

![Quartz Layer Tree](https://user-images.githubusercontent.com/25163644/94746837-9d372700-034b-11eb-8abe-52de2217cce8.png)
*Quartz Layer Tree*

### Example 2: EmbeddedViews
Similar to the previous example, however an EmbeddedView is painted after all the pure Flutter layers.
In this case, the pure Flutter nodes are still combined into a single Quartz node and the UIView is overlayed on-top of the single node.

![Flow Layer Tree](https://user-images.githubusercontent.com/25163644/94747151-3b2af180-034c-11eb-9a8f-fe09c1eb0903.png)
*Flow Layer Tree*

![Quartz Layer Tree](https://user-images.githubusercontent.com/25163644/94747026-04ed7200-034c-11eb-8860-f8757bd6dcc1.png)
*Quartz Layer Tree*

### Example 3: Practical Example
For this example we will cover the layer trees for the following app.

![image](https://user-images.githubusercontent.com/25163644/94748265-e6d54100-034e-11eb-8eb8-db0109976b3e.png)

In this case, the pure Flutter button layer is drawn after the EmbeddedView (Webview) and cannot be combined into a single Quartz node with the rest of the pure Flutter layers, button is drawn after the Webview on it's own CALayer.

![Embedded Views Flow WebView](https://user-images.githubusercontent.com/25163644/94747304-804f2380-034c-11eb-888e-8d39d1f0c53a.png)
*Flow Layer Tree*

![Embedded Views Quartz Webview](https://user-images.githubusercontent.com/25163644/94747302-804f2380-034c-11eb-98ee-eaa36c5dea1e.png)
*Quartz Layer Tree*

## Compositing Unobstructed Platform Views

### Example 1
The following image illustrates an example where the EmbeddedView is overlayed above a single background canvas.

![image](https://user-images.githubusercontent.com/25163644/94747651-72e66900-034d-11eb-8175-71247346fbe3.png)

### Example 2
There are two platform views, the algorithm successfully determines that it only needs to allocate an additional UIView for the upper left corner of the FAB.

![image](https://user-images.githubusercontent.com/25163644/94747735-acb76f80-034d-11eb-985a-0c4755b80bef.png)


## Applying mutations
For non platform view widgets, mutations are saved to the Skia canvas.
Since UIViews are not painted by Skia unlike pure Flutter widgets, the mutations applied to the Skia canvas will not directly apply to the UIView.
Mutations for EmbeddedViews must be recorded and applied separately. To do this, we introduce the mutator stack which tracks the mutations specifically for EmbeddedViews and ensures iOS applies the mutations when the UIView is drawn.

The following example illustrates the mutator stack during the layer tree traversal. 
When leaving any node that pushed a mutation onto the stack, the mutation is popped off the stack.

![Mutator Stack](https://user-images.githubusercontent.com/25163644/94747464-f5baf400-034c-11eb-9ef5-3d82d69ed477.png)


## Handling Touch
How do EmbeddedViews handle touch events?

Problem: Impossible to synchronously determine whether Flutter or Native iOS should handle the touch event - may need to see multiple events before decision.

Solution: Custom UIGestureRecognizer for views. Prevent other gesture recognizers from recognizing event until UIGestureRecognizer fails.  Then Flutter can make async decision whether to use the UIGestureRecognizer.
