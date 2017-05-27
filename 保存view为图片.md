

#### 根据给定view 获取图片

```
- (UIImage *)crateImageWithView:(UIView *)view {
    
    //开始绘制并确定绘制内容区域
    UIGraphicsBeginImageContextWithOptions(view.bounds.size, NO, [UIScreen mainScreen].scale);
    //用view.layer渲染绘制的内容
    [view.layer renderInContext:UIGraphicsGetCurrentContext()];
    //从绘制的内容获得image
    UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
    //结束绘制
    UIGraphicsEndImageContext();
    return image;
}
```

####  UIImage categrory 单例获取图片

```
@interface UIImage (BolderImage)

+ (instancetype)getRedBolderImage;

@end

@implementation UIImage (BolderImage)

+ (instancetype)getRedBolderImage {

    static UIImage *img = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        
        UIView *view = [[UIView alloc]initWithFrame:CGRectMake(0, 0, 60, 30)];
        view.backgroundColor = [UIColor whiteColor];
        view.clipsToBounds = YES;
        view.layer.cornerRadius = 2.0;
        view.layer.borderColor = [UIColor redColor].CGColor;
        view.layer.borderWidth = 1.0;
        
        UIGraphicsBeginImageContextWithOptions(view.bounds.size, NO, [UIScreen mainScreen].scale);
        [view.layer renderInContext:UIGraphicsGetCurrentContext()];
        UIImage *currentImg = UIGraphicsGetImageFromCurrentImageContext();
        UIGraphicsEndImageContext();
        
        img = [currentImg resizableImageWithCapInsets:UIEdgeInsetsMake(currentImg.size.height * 0.49, currentImg.size.width * 0.49, currentImg.size.height * 0.49, currentImg.size.width * 0.49) resizingMode:UIImageResizingModeStretch];
    });
    return img;
}

@end
```

#### 调用

```
UIImageView *imgV1 = [[UIImageView alloc]initWithImage:[UIImage getRedBolderImage]];
imgV1.frame = CGRectMake(20, 100, 200, 60);
[self.view addSubview:imgV1];
```
