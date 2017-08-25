## ios_note

### 1. String转AttributedString 部分字符变化

##### 设置一段文字的标题为粗体及段间距

	- (NSMutableAttributedString *)getAttributedStringFromSting:(NSString *)string {
	
	    NSMutableAttributedString *attributedStr = [[NSMutableAttributedString alloc]initWithString:string];
	
	    for (int i = 0; i < 10; i ++) {
	        NSRange range1 = [string rangeOfString:[NSString stringWithFormat:@"%d、",i + 1]];
	        NSRange range2 = [self rangeOfString:@"？\n" inString:string atOccurrence:i];
	        
	        [attributedStr addAttribute:NSFontAttributeName value:[UIFont boldSystemFontOfSize:16.0] range:NSMakeRange(range1.location, range2.location - range1.location)];
	    }
	    //设置段间距
	    NSMutableParagraphStyle *paragraphStyle = [[NSMutableParagraphStyle alloc]init];
	    [paragraphStyle setLineSpacing:10];
	    [attributedStr addAttribute:NSParagraphStyleAttributeName value:paragraphStyle range:NSMakeRange(0, attributedStr.length)];
	    
	    return attributedStr;
	}


##### 获取String指定某个subString的range

	-(NSRange)rangeOfString:(NSString*)subString inString:(NSString*)string atOccurrence:(int)occurrence{
	
	    int currentOccurrence = 0;
	    NSRange searchRange = NSMakeRange(0, [string length]);
	    
	    while (YES){
	    
	        NSRange searchResult = [string rangeOfString:subString options:NSCaseInsensitiveSearch range:searchRange];
	        
	        if (currentOccurrence == occurrence) {
	            return searchResult;
	        }
	        
	        if (currentOccurrence < occurrence)
	        {
	            currentOccurrence++;
	            CGFloat newStartLocation = searchResult.location + searchResult.length;
	            searchRange = NSMakeRange(newStartLocation, string.length - newStartLocation);
	        }
	    }
	}

### 对Label设置不同字体不同颜色

	NSMutableAttributedString *str = [[NSMutableAttributedString alloc] initWithString:contentString];
	//设置字体和字号

    [str addAttribute:NSFontAttributeName value:[UIFont fontWithName:@"HelveticaNeue-Bold" size:15.0] range:NSMakeRange(0, 5)];

    //设置文字颜色

    [str addAttribute:NSForegroundColorAttributeName value:[UIColor whiteColor] range:NSMakeRange(0,5)];

    //设置文字背景颜色

    [str addAttribute:NSBackgroundColorAttributeName value:RGBACOLOR(43, 96, 192, 1) range:NSMakeRange(0,5)];
    
    label.attributedText = str;


####  label中显示不同颜色的字以及不同字体，字体高亮，DIY label
##### 设置颜色属性和字体属性 (首先继承一个label，要想在一个label中实现各种不同颜色的字，就是重绘。)

	- (NSAttributedString *)illuminatedString:(NSString *)text font:(UIFont *)AtFont{
	
	    int len = [text length];
	    //创建一个可变的属性字符串
	    NSMutableAttributedString *mutaString = [[[NSMutableAttributedString alloc] initWithString:text] autorelease];
	    //改变字符串 从1位 长度为1 这一段的前景色，即字的颜色。
	/*    [mutaString addAttribute:(NSString *)(kCTForegroundColorAttributeName) 
	                       value:(id)[UIColor darkGrayColor].CGColor 
	                       range:NSMakeRange(1, 1)]; */
	    [mutaString addAttribute:(NSString *)(kCTForegroundColorAttributeName)
	                       value:(id)self.stringColor.CGColor
	                       range:NSMakeRange(0, len)];
	
	
	
	    if (self.keywordColor != nil)
	    {
	        for (NSValue *value in list) 
	        {
	         //   NSValue *value = [list objectAtIndex:i];
	            NSRange keyRange = [value rangeValue];
	            [mutaString addAttribute:(NSString *)(kCTForegroundColorAttributeName)
	                                                    value:(id)self.keywordColor.CGColor
	                                                    range:keyRange];
	        }
	    }
	
	
	
	    //设置部分字段的字体大小与其他的不同
	/*    CTFontRef ctFont = CTFontCreateWithName((CFStringRef)AtFont.fontName, 
	                                            AtFont.pointSize, 
	                                            NULL);
	    [mutaString addAttribute:(NSString *)(kCTFontAttributeName) 
	                       value:(id)ctFont 
	                       range:NSMakeRange(0, 1)];*/
	
	    //设置是否使用连字属性，这里设置为0，表示不使用连字属性。标准的英文连字有FI,FL.默认值为1，既是使用标准连字。也就是当搜索到f时候，会把fl当成一个文字。
	    int nNumType = 0;
	//    float fNum = 3.0;
	    CFNumberRef cfNum = CFNumberCreate(NULL, kCFNumberIntType, &nNumType);
	//    CFNumberRef cfNum2 = CFNumberCreate(NULL, kCFNumberFloatType, &fNum);
	    [mutaString addAttribute:(NSString *)kCTLigatureAttributeName
	                       value:(id)cfNum
	                       range:NSMakeRange(0, len)];
	    //空心字
	//    [mutaString addAttribute:(NSString *)kCTStrokeWidthAttributeName value:(id)cfNum2 range:NSMakeRange(0, len)];
	
	
	    CTFontRef ctFont2 = CTFontCreateWithName((CFStringRef)AtFont.fontName, 
	                                             AtFont.pointSize,
	                                             NULL);
	    [mutaString addAttribute:(NSString *)(kCTFontAttributeName) 
	                       value:(id)ctFont2 
	                       range:NSMakeRange(0, len)];
	 //   CFRelease(ctFont);
	    CFRelease(ctFont2);
	    return [[mutaString copy] autorelease];
	}

####重绘Text

	- (void)drawRect:(CGRect)rect 
	{
	    //获取当前label的上下文以便于之后的绘画，这个是一个离屏。
		CGContextRef context = UIGraphicsGetCurrentContext();
	    //压栈，压入图形状态栈中.每个图形上下文维护一个图形状态栈，并不是所有的当前绘画环境的图形状态的元素都被保存。图形状态中不考虑当前路径，所以不保存
	    //保存现在得上下文图形状态。不管后续对context上绘制什么都不会影响真正得屏幕。
		CGContextSaveGState(context);
	    //x，y轴方向移动
		CGContextTranslateCTM(context, 0.0, 0.0);/*self.bounds.size.height*/
		
	    //缩放x，y轴方向缩放，－1.0为反向1.0倍,坐标系转换,沿x轴翻转180度
		// CGContextScaleCTM(context, 1, 100); 
	
		NSArray *fontArray = [UIFont familyNames];
		NSString *fontName;
		if ([fontArray count]) {
			fontName = [fontArray objectAtIndex:0];
		}
	    //创建一个文本行对象，此对象包含一个字符
		CTLineRef line = CTLineCreateWithAttributedString((CFAttributedStringRef) 
		                                                      [self illuminatedString:self.text font:self.font]); //[UIFont fontWithName:fontName size:60]
	    //设置文字绘画的起点坐标。
		CGContextSetTextPosition(context, 0.0, 0.0); /*ceill(self.bounds.size.height/4)*/
	    //在离屏上绘制line
		CTLineDraw(line, context);
	    //将离屏上得内容覆盖到屏幕。此处得做法很像windows绘制中的双缓冲。
		CGContextRestoreGState(context); 
		CFRelease(line);
		
		//CGContextRef myContext = UIGraphicsGetCurrentContext();
		//CGContextSaveGState(myContext);
		//[self MyColoredPatternPainting:myContext rect:self.bounds];
		//CGContextRestoreGState(myContext);
	}
	
### 2. 相机拍照

#### 调出相机

	UIImagePickerController *imagePicker = [[UIImagePickerController alloc] init];
	imagePicker.editing = YES;
	imagePicker.allowsEditing = YES; //设置未可编辑状态
	imagePicker.delegate = self;
	imagePicker.sourceType = UIImagePickerControllerSourceTypeCamera;
	[self presentViewController:imagePicker animated:YES completion:nil];
	if ([UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypeCamera]) {
	            imagePicker.sourceType = UIImagePickerControllerSourceTypeCamera;
	            [self presentViewController:imagePicker animated:YES completion:nil];
	}
#### 上传图片	
	eg:
	
    NSString *URLStr = [NSString stringWithFormat:@"%@%@",Baseurl,@"/app/uploadUserPhoto"];
    
    NSMutableDictionary *parameters = [NSMutableDictionary dictionary];
    [parameters setObject:AppDelegateInstance.userInfo.userId forKey:@"userId"];
    [parameters setObject:@"1" forKey:@"type"];
    
    AFHTTPRequestOperationManager *manager = [AFHTTPRequestOperationManager manager];
    manager.responseSerializer.acceptableContentTypes = [NSSet setWithObject:@"text/html"];
    
    [manager POST:URLStr parameters:parameters constructingBodyWithBlock:^(id<AFMultipartFormData> formData) {
    
        [SVProgressHUD showWithStatus:@"正在上传..."];
         NSData *imageData = UIImageJPEGRepresentation(_hearImg, 0.5);
        //上传时使用当前的系统事件作为文件名
        NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
        formatter.dateFormat = @"yyyyMMddHHmmss";
        NSString *str = [formatter stringFromDate:[NSDate date]];
        NSString *fileName = [NSString stringWithFormat:@"%@.jpg", str];
        
        [formData appendPartWithFileData:imageData
                                    name:@"imgFile"
                                fileName:fileName
                                mimeType:@"image/jpeg"];
    } success:^(AFHTTPRequestOperation *operation, id responseObject) {
        
        NSDictionary *dic = (NSDictionary *)responseObject;
        if([[dic objectForKey:@"error"] integerValue] == -1){
            [SVProgressHUD showSuccessWithStatus:[dic objectForKey:@"msg"]];
            if ([[dic objectForKey:@"filename"] hasPrefix:@"http"]) {
                
                AppDelegateInstance.userInfo.headImg =[NSString stringWithFormat:@"%@",[dic objectForKey:@"filename"]] ;
                [[AppDefaultUtil sharedInstance] setDefaultHeaderImageUrl:[NSString stringWithFormat:@"%@", [dic objectForKey:@"filename"]]];
            }else{
                
                AppDelegateInstance.userInfo.headImg =[NSString stringWithFormat:@"%@%@", Baseurl, [dic objectForKey:@"filename"]] ;
                [[AppDefaultUtil sharedInstance] setDefaultHeaderImageUrl:[NSString stringWithFormat:@"%@%@", Baseurl, [dic objectForKey:@"filename"]]];
            }
            dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1 * 600000000ull)), dispatch_get_main_queue(), ^{
                [self dismissViewControllerAnimated:YES completion:^(){}];
            });
        }else{
            [SVProgressHUD showErrorWithStatus:[dic objectForKey:@"msg"]];
        }
        
    } failure:^(AFHTTPRequestOperation *operation, NSError *error) {
        
         NSLog(@"Failure >>>>>>>>>>>>>>>>>%@", error.description);
        [SVProgressHUD showErrorWithStatus:[NSString stringWithFormat:@"%@",error]];
    }];
	
### 3. 获取指定区域指定时间

#### 获取当地推迟半小时后的时间

	    NSDate *date = [NSDate dateWithTimeIntervalSinceNow:30*60];
	//    NSDate *delyDate = [NSDate dateWithTimeInterval:30*60 sinceDate:date];
	    NSDateFormatter *formater = [[NSDateFormatter alloc]init];
	    NSString *formaterStr = @"hh:mm";
	    formater.dateFormat = formaterStr;
	    NSTimeZone *timeZone = [NSTimeZone timeZoneWithName:@"GMT+8"];
	    [formater setTimeZone:timeZone];
	    NSString *dateStr = [formater stringFromDate:date];
	    
### 4. Swift Autolayout

[Autolayout 教程 一 ](http://www.cocoachina.com/swift/20141013/9893.html)

[Autolayout 教程 二 ](http://www.cocoachina.com/ios/20141014/9908.html)

[手写代码约束 教程 一 ](http://blog.csdn.net/yangtiang/article/details/46483999)

[手写代码约束 教程 二 ](http://blog.csdn.net/yangtiang/article/details/46492083)

[手写代码约束 教程 三 ](http://blog.csdn.net/yangtiang/article/details/46795231)

### 5. NSThread

	performselector 直接在当前线程中进行调用目标selector
	detachNewThreadSelector 在一个新的线程中调用目标selector 用户指定目标线程
	performSelectorInBackground 在一个新的线程中调用目标selector 自动指定线程
	performSelectorOnMainThread 在主线程中调用目标的selector 如所有界面相关的操作必须在main thread中完成（uikit, appkit相关)
	
### 6. Button占有其父视图的点击事件，即扩大Button的点击区域
#### oc版本

	@interface LargeButton : UIButton

	@end
	
	@implementation LargeButton
	
	- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {
	
	    return self;
	}
	
	@end
	
#### swift版本

	class LargeButton: UIButton {
	    override func hitTest(point: CGPoint, withEvent event: UIEvent?) -> UIView? {
	        return self
	    }
	}
	
#### 	用法，用LargeButton创建按钮对象，添加target即可实现在点击按钮对象父视图的一定范围内可以响应按钮对象的selector方法。

### Xcode 7 UIStackView

[UIStackView 教程 ](http://www.cocoachina.com/ios/20150820/13118.html)

### Info.plist 常见字段的作用
	
	1. Localiztion native development region --- CFBundleDevelopmentRegion 本地化相关,如果⽤户所在地没有相应的语言资源,则用这个key的value来作为默认
	2. Bundle display name --- CFBundleDisplayName 设置程序安装后显示的名称。应⽤程序名称限制在10-12个字符,如果超出,将被显示缩写名称。
	3. Executaule dile -- CFBundleExecutable 程序安装包的名称
	4. Bundle identidier --- CFBundleIdentidier 该束的唯一标识字符串,该字符串的格式类似 com.yourcompany.yourapp,如果使⽤用模拟器跑你的应用,这个字段没有用处,如果你需要把你的应⽤部署到设备上,你必须⽣成一个证书,⽽而在⽣生成证书的时候,在apple的⽹网站上需要增加相应的app IDs.这⾥有一个字段Bundle identidier,如果这个Bundle identidier是一个完整字符串,那么文件中的这个字段必须和后者完全相同,如果app IDs中的字段含有通配符*,那么文件中的字符串必须符合后者的描述。
	5. InfoDictionary version --- CFBundleInfoDictionaryVersion Info.plist 格式的版本信息
	6. Bundle name --- CFBundleName 产品名称
	7. Bundle OS Type code -- CFBundlePackageType ⽤来标识束类型的四个字母长的代码
	8. Bundle versions string, short --- CFBundleShortVersionString ⾯向用户市场的束的版本字符串
	9. Bundle creator OS Type code --- CFBundleSignature 用来标识创建者的四个字母长的代码
	10. Bundle version --- CFBundleVersion 应⽤程序版本号,每次部署应用程序的一个新版本时, 将会增加这个编号,在app store上用的。
	11. Application require iPhone environment -- LSRequiresIPhoneOS 用于指示程序包是否只能运行在iPhone OS 系统上。Xcode自动加入这个键,并将它的值设置为true。您不应该改变这个键的值。
	12. Main storybard dile base name -- UIMainStoryboardFile 这是一个字符串,指定应用程序主nib文件的名称。
	13. supported interface orientations -- UISupportedInterfaceOrientations 程序默认支持的设备方向。
	14. Icon file ---- 应用程序图片名称，例：icon.png icon@2x.png
	15. Application uses Wi-Fi --- 如果应用程序需要wifi才能工作，应该将此属性设置为true。这么做会提示用户，如果没有打开wifi的话，打开wifi。为了节省电力，iphone会在30分钟后自动关闭app中的任何wifi。设置这一个属性可以防止这种情况的发生，并且保持连接处于活动状态。
	
#### 	version 与 bulid 的区别

	1，Version是显示对外的版本号，（itunesconect和Appstore用户可以看到），对应O-C中获取version的值：
	[[[NSBundle mainBundle]infoDictionary]valueForKey:@"CFBundleShortVersionString"]；
	该版本的版本号是三个分隔的整数组成的字符串。第一个整数代表重大修改的版本，如实现新的功能或重大变化的修订。第二个整数表示的修订，实现较突出的特点。第三个整数代表维护版本
	例如：1.0.12或者  1.2.3等等
	
	2，build别人看不到，只有开发者自己才能看到，相当于内部版本号。【更新版本的时候，也要高于之前的build号】 对应获取方式：
	[[[NSBundle mainBundle]infoDictionary]valueForKey:@"CFBundleVersion"]；
	标示（发布或者未发布）的内部版本号。这是一个单调增加的字符串，包括一个或者多个分割的整数。
	
### 	动画

    CALayer *groupLayer = [[CALayer alloc] init];
    groupLayer.frame = CGRectMake(60, 100, 50, 50);
    groupLayer.cornerRadius = 10;
    groupLayer.backgroundColor = [[UIColor purpleColor] CGColor];
    [self.view.layer addSublayer:groupLayer];

    CABasicAnimation *scaleAnimation = [CABasicAnimation animationWithKeyPath:@"transform.scale"];
    scaleAnimation.fromValue = [NSNumber numberWithFloat:1.0];
    scaleAnimation.toValue = [NSNumber numberWithFloat:1.5];
    scaleAnimation.autoreverses = YES;
    scaleAnimation.repeatCount = MAXFLOAT;
    scaleAnimation.duration = 0.8;
    
    CABasicAnimation *moveAnimationY = [CABasicAnimation animationWithKeyPath:@"position"];
    moveAnimationY.fromValue = [NSValue valueWithCGPoint:groupLayer.position];
    moveAnimationY.toValue = [NSValue valueWithCGPoint:CGPointMake(320 - 80,
                                                                  groupLayer.position.y)];
    moveAnimationY.autoreverses = YES;
    moveAnimationY.repeatCount = MAXFLOAT;
    moveAnimationY.duration = 8;
    
    CABasicAnimation *moveAnimation1 = [CABasicAnimation animationWithKeyPath:@"position"];
    moveAnimation1.fromValue = [NSValue valueWithCGPoint:groupLayer.position];
    moveAnimation1.toValue = [NSValue valueWithCGPoint:CGPointMake(kWidth,
                                                                  groupLayer.position.y + 100)];
    moveAnimation1.autoreverses = NO;
    moveAnimation1.repeatCount = 1;
    moveAnimation1.duration = 8;
    
    CABasicAnimation *moveAnimation2 = [CABasicAnimation animationWithKeyPath:@"position"];
    moveAnimation2.fromValue = [NSValue valueWithCGPoint:CGPointMake(kWidth,
                                                                     groupLayer.position.y + 100)];
    moveAnimation2.toValue = [NSValue valueWithCGPoint:CGPointMake(0,
                                                                   groupLayer.position.y + 200)];
    moveAnimation2.autoreverses = NO;
    moveAnimation2.repeatCount = 1;
    moveAnimation2.duration = 8;
    
    CABasicAnimation *rotateAnimation = [CABasicAnimation animationWithKeyPath:@"transform.rotation.x"];
    rotateAnimation.fromValue = [NSNumber numberWithFloat:0.0];
    rotateAnimation.toValue = [NSNumber numberWithFloat:6.0 * M_PI];
    rotateAnimation.autoreverses = YES;
    rotateAnimation.repeatCount = MAXFLOAT;
    rotateAnimation.duration = 2;
    
    CAAnimationGroup *groupAnnimation = [CAAnimationGroup animation];
    groupAnnimation.duration = 2;
    groupAnnimation.autoreverses = YES;
    groupAnnimation.animations = @[moveAnimation1,moveAnimation2];
    groupAnnimation.repeatCount = MAXFLOAT;
    //开演
    [groupLayer addAnimation:groupAnnimation forKey:@"groupAnnimation"];
    
    
    -------------------
    UIImageView *balloon = [[UIImageView alloc]initWithImage:[UIImage imageNamed:@"2balloon"]];
    balloon.frame = CGRectMake(60, 100, kWidth * 0.18, kWidth * 0.18 + 10);
    [self.view addSubview:balloon];
    
    CAKeyframeAnimation *keyframePath = [CAKeyframeAnimation animationWithKeyPath:@"position"];
    //贝塞尔曲线
    //1.指定贝塞尔曲线的半径
    CGFloat  radius = [UIScreen mainScreen].bounds.size.height / 2.0;
    //01:圆心
    //02:半径
    //03:开始的角度
    //04:结束的角度
    //05:旋转方向 (YES表示顺时针 NO表示逆时针)
    UIBezierPath *path = [UIBezierPath bezierPathWithArcCenter:CGPointMake(0, radius) radius:radius startAngle:-M_PI_2 endAngle:M_PI_2 clockwise:YES];
    //将贝塞尔曲线作为运动轨迹
    keyframePath.path = path.CGPath;
    //2.创建第二组关键帧动画,让热气球在运动的时候  由小--->大--->小   ;
    CAKeyframeAnimation *keyFrameScale = [CAKeyframeAnimation animationWithKeyPath:@"transform.scale"];
    //通过一组数据修改热气球的大小
    keyFrameScale.values = @[@1.0,@1.2,@1.4,@1.6,@1.8,@1.6,@1.4,@1.2,@1.0];
    //创建动画分组对象
    CAAnimationGroup *group = [CAAnimationGroup animation];
    //将两个动画效果添加到分组动画中
    group.animations = @[keyframePath,keyFrameScale];
    group.duration = 12;
    group.repeatCount = 1000;
    [balloon.layer addAnimation:group forKey:nil];
    
###     UIWebView 保存网络图片


	- (void)viewDidLoad
	{
		self.webView.delegate = self;
		[self.webView  loadRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:@"http://image.baidu.com/wisebrowse/index?tag1=%E7%BE%8E%E5%A5%B3&tag2=%E5%85%A8%E9%83%A8&tag3=&pn=0&rn=10&from=index&fmpage=index&pos=magic#/home"]]];
		UILongPressGestureRecognizer * longPressed = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(longPressed:)];
		longPressed.delegate = self;
		[self.webView addGestureRecognizer:longPressed];
	}
	- (void)longPressed:(UITapGestureRecognizer*)recognizer{
		//只在长按手势开始的时候才去获取图片的url
		if (recognizer.state != UIGestureRecognizerStateBegan) {
			return;
		}
		CGPoint touchPoint = [recognizer locationInView:self.webView];
		NSString *js = [NSString stringWithFormat:@"document.elementFromPoint(%f, %f).src", touchPoint.x, touchPoint.y];
		NSString *urlToSave = [self.webView stringByEvaluatingJavaScriptFromString:js];
		if (urlToSave.length == 0) {
			return;
		}
		NSLog(@"获取到图片地址：%@",urlToSave);
		NSData *data = [NSData dataWithContentsOfURL:urlToSave];
		UIImage *image = [UIImage imageWithData:data];
		[self saveImageToPhotos:image];
	}
	
	- (void)saveImageToPhotos:(UIImage*)image {
    
	    UIImageWriteToSavedPhotosAlbum(image, self, @selector(image:didFinishSavingWithError:contextInfo:), NULL);
	}

	// 指定回调方法
	- (void)image: (UIImage *) image didFinishSavingWithError: (NSError *) error contextInfo: (void *) contextInfo  {
	
	    if(error != NULL){
	        NSLog(@"保存失败");
	    }else{
	
	        NSLog(@"保存成功");
	    }
	}


### dispatch_group 多线程实现网络请求

        dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{

            dispatch_group_t serviceGroup = dispatch_group_create();

            //请求1
            dispatch_group_enter(serviceGroup);
            [NetworkRequest requestWithPath:@"index.do" withMethod:@"POST"  needSetHead:YES params:nil success:^(id responseObject) {

                dispatch_group_leave(serviceGroup);
            } failure:^(id error) {

            }];


            //请求1
            dispatch_group_enter(serviceGroup);
            [NetworkRequest requestWithPath:@"index.do" withMethod:@"POST"  needSetHead:YES params:nil success:^(id responseObject) {

                dispatch_group_leave(serviceGroup);
            } failure:^(id error) {

            }];

            dispatch_group_notify(serviceGroup, dispatch_get_main_queue(), ^{
                // 更新界面
            });
        });

        ‘注释’： 该操作实现了 对请求1 请求2 再serviceGroup异步处理，当serviceGroup的队列里的操作全部执行完毕，dispatch_group_notify使用通知进行ui更新
	
## WebView清除缓存的方法

```
//==============方法一
if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 9) {
        
        NSArray * types = @[WKWebsiteDataTypeMemoryCache,WKWebsiteDataTypeDiskCache]; // 9.0之后才有的
        NSSet *websiteDataTypes = [NSSet setWithArray:types];
        NSDate *dateFrom = [NSDate dateWithTimeIntervalSince1970:0];
        [[WKWebsiteDataStore defaultDataStore] removeDataOfTypes:websiteDataTypes modifiedSince:dateFrom completionHandler:^{
            
            dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(0.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
                
                NSLog(@"清除完成");
            });
        }];
        
    }else{
        
        NSString *libraryPath = [NSSearchPathForDirectoriesInDomains(NSLibraryDirectory,NSUserDomainMask, YES) objectAtIndex:0];
        NSString *cookiesFolderPath = [libraryPath stringByAppendingString:@"/Cookies"];
        NSError *errors;
        [[NSFileManager defaultManager] removeItemAtPath:cookiesFolderPath error:&errors];
    }
    
//==============方法二	
- (void)clearCache {
    
    /* 取得Library文件夹的位置*/
    NSString *libraryDir = NSSearchPathForDirectoriesInDomains(NSLibraryDirectory, NSUserDomainMask, YES)[0];
    /* 取得bundle id，用作文件拼接用*/
    NSString *bundleId  =  [[[NSBundle mainBundle] infoDictionary] objectForKey:@"CFBundleIdentifier"];
    /*
     * 拼接缓存地址，具体目录为App/Library/Caches/你的APPBundleID/fsCachedData
     */
    NSString *webKitFolderInCachesfs = [NSString stringWithFormat:@"%@/Caches/%@/fsCachedData",libraryDir,bundleId];
    
    NSError *error;
    /* 取得目录下所有的文件，取得文件数组*/
    NSFileManager *fileManager = [NSFileManager defaultManager];
    NSArray *fileList = [[NSArray alloc] init];
    //fileList便是包含有该文件夹下所有文件的文件名及文件夹名的数组
    fileList = [fileManager contentsOfDirectoryAtPath:webKitFolderInCachesfs error:&error];
    
    NSLog(@"路径==%@,fileList%@",webKitFolderInCachesfs,fileList);
    /* 遍历文件组成的数组*/
    for(NSString * fileName in fileList){
        /* 定位每个文件的位置*/
        NSString * path = [[NSBundle bundleWithPath:webKitFolderInCachesfs] pathForResource:fileName ofType:@""];
        /* 将文件转换为NSData类型的数据*/
        NSData * fileData = [NSData dataWithContentsOfFile:path];
        /* 如果FileData的长度大于2，说明FileData不为空*/
        if(fileData.length > 2){
            /* 创建两个用于显示文件类型的变量*/
            int char1 = 0;
            int char2 = 0;
            
            [fileData getBytes:&char1 range:NSMakeRange(0, 1)];
            [fileData getBytes:&char2 range:NSMakeRange(1, 1)];
            /* 拼接两个变量*/
            NSString *numStr = [NSString stringWithFormat:@"%i%i",char1,char2];
            /* 如果该文件前四个字符是6033，说明是Html文件，删除掉本地的缓存*/
            if([numStr isEqualToString:@"6033"]){
                [[NSFileManager defaultManager] removeItemAtPath:[NSString stringWithFormat:@"%@/%@",webKitFolderInCachesfs,fileName] error:&error];
                continue;
            }
            
        }
    }
}

```