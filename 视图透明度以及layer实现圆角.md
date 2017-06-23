## 设置背景父试图透明度不影响子视图透明度的方法
1. 用一张半透明的图片做背景。一般采用工程中尽量少加入资源，能不用图片的尽量不用图片
2. 使用colorWithWhite：alpha：方法
    
    ```
    view.backgroundColor = [UIColor colorWithWhite:0.f alpha:0.5];
    white后面的参数表示灰度，从0-1之间表示从黑到白的变化，alpha就是你想调整的透明度。这个方法用于非黑即白的半透明背景基色，不能设置其他颜色（彩色）的半透明
    ```
3. 使用colorWithRed:green:blue:alpha:方法
4. 使用colorWithAlphaComponent:方法
   UIColor *color = [UIColor blackColor];
   view.backgroundColor = [color colorWithAlphaComponent:0.5];
 
 
## layer设置圆角对性能的影响

1. layer.mask (`影响性能最高`)
	
	```
	CAShapeLayer *layer = [CAShapeLayer layer]; 
	UIBezierPath *aPath = [UIBezierPath bezierPathWithOvalInRect:view.bounds]; 
	 layer.path = aPath.CGPath; 
	view.layer.mask = layer;
		
	```
2. layer.cornerRadius (`影响性能次之`)

	```
	view.layer.cornerRadius = view.frame.size.width/2.0; 
	view.layer.masksToBounds = YES;
	
	```
3. 设置shouldRasterize (`影响性能再次之`)

	```
	self.layer.shouldRasterize = YES;
	//当shouldRasterize设成YES时，layer被渲染成一个bitmap，并缓存起来，等下次使用时不会再重新去渲染了。实现圆角本身就是在做颜色混合（blending），如果每次页面出来时都blending，消耗太大，设置shouldRasterize = yes，下次就只是简单的从渲染引擎的cache里读取那张bitmap，节约系统资源。
	注意：仅仅设置为yes 会导致label的text textField的text placeHolder 以及left/rightView  或是button的title image 模糊，要想不模糊 需要设置下面的属性
	self.layer.rasterizationScale = [UIScreen mainScreen].scale;
 
	```
4. 设置好的圆角图片 保存为图片(`推荐使用`) [如何保存](https://github.com/lfxfengxia/ios_note/blob/master/%E4%BF%9D%E5%AD%98view%E4%B8%BA%E5%9B%BE%E7%89%87.md)
