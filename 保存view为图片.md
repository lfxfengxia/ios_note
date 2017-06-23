

#### 根据给定view 获取图片

```
+ (UIImage *)crateImageWithView:(UIView *)view {
    
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

#####  1. UIImage categrory 单例获取图片

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
        
        UIImage * currentImg = [UIImage createImageFromView:view];
     
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

##### 保存图片到本地

```
//.h文件
typedef enum : NSUInteger {
    kGrayImage,
    kRedImage
} ImageStyle;

----------------------------------------------------
//.m文件
+ (UIImage *)getImageFromLocalWithImageStyle:(ImageStyle)style {
    
    NSString *fileName;
    switch (style) {
        case kGrayImage:
            fileName = @"GrayImage.png";
            break;
        case kRedImage:
            fileName = @"RedImage.png";
            break;
    }
    
    
    NSString *doucumentPath = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject;
    NSString *absoulatePath = [doucumentPath stringByAppendingString:fileName];
    if ([[NSFileManager defaultManager] fileExistsAtPath:absoulatePath]) {
        
        UIImage *currentImg = [UIImage imageWithContentsOfFile:absoulatePath];
        UIImage *img = [currentImg resizableImageWithCapInsets:UIEdgeInsetsMake(currentImg.size.height * 0.49, currentImg.size.width * 0.49, currentImg.size.height * 0.49, currentImg.size.width * 0.49) resizingMode:UIImageResizingModeStretch];
        return img;
    }else {
    
        UIView *view = [[UIView alloc]initWithFrame:CGRectMake(0, 0, 60, 30)];
        switch (style) {
            case kGrayImage:{
                
                view.clipsToBounds = YES;
                view.layer.cornerRadius = 7.0;
                view.backgroundColor = [UIColor lightGrayColor];
            }
                break;
            case kRedImage:{
                
                view.clipsToBounds = YES;
                view.layer.cornerRadius = 3.0;
                view.backgroundColor = [UIColor redColor];
            }
                break;
        }
        
        UIImage *getImage = [UIImage createImageFromView:view];
        
        NSData *imgData = UIImagePNGRepresentation(getImage);
        [imgData writeToFile:absoulatePath atomically:YES];
        
        UIImage *img = [getImage resizableImageWithCapInsets:UIEdgeInsetsMake(getImage.size.height * 0.49, getImage.size.width * 0.49, getImage.size.height * 0.49, getImage.size.width * 0.49) resizingMode:UIImageResizingModeStretch];

        return img;
    }
}

```
