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

	UIImagePickerController *imagePicker = [[UIImagePickerController alloc] init];
	imagePicker.editing = YES;
	imagePicker.allowsEditing = YES;
	imagePicker.delegate = self;
	imagePicker.sourceType = UIImagePickerControllerSourceTypeCamera;
	[self presentViewController:imagePicker animated:YES completion:nil];
	if ([UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypeCamera]) {
	            imagePicker.sourceType = UIImagePickerControllerSourceTypeCamera;
	            [self presentViewController:imagePicker animated:YES completion:nil];
	}
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

### 5. NSThread

	performselector 直接在当前线程中进行调用目标selector
	detachNewThreadSelector 在一个新的线程中调用目标selector 用户指定目标线程
	performSelectorInBackground 在一个新的线程中调用目标selector 自动指定线程
	performSelectorOnMainThread 在主线程中调用目标的selector 如所有界面相关的操作必须在main thread中完成（uikit, appkit相关)
	
### 6. Button占有其父试图的点击事件，即扩大Button的点击区域
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
	
#### 	用法，用LargeButton创建按钮对象，添加target即可实现在点击按钮对象父试图的一定范围内可以响应按钮对象的selector方法。