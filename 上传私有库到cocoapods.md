# 上传私有库到cocoapods

### 登录github创建私有<mark>repo<mark>
--

1. eg：<mark>PodDemo<mark>![create repo](http://wx1.sinaimg.cn/mw690/bd64595dgy1ffyrgfxtpij20l00f1q5q.jpg)

2. 终端clone repo

	```
	$ git clone https://github.com/github_userName/PodDemo.git
	```
3. 添加自己的adk 到clone之后的文件夹 **PodDemo** 并提交到github
	
	```
	$ git add *
	$ git commit -a -m "提交PodDemo sdk"
	```
	
### 创建<mark>podspec<mark>
--

#####  创建podspec文件的另种方法

- 使用vi 编辑器直接编辑
	
	```
	$ vi PodDemo.podspec
	
	```
	复制下面内容最相应修改
	
	```
	Pod::Spec.new do |s|
   s.name         = "PodDemo"
   s.version      = "0.0.1"
   s.summary      = "a test for pod"
   s.description  = <<-DESC
                      a test about how to upload sdk to   cocoapods 
                   DESC
   s.homepage     = "https://github.com/github_userName/PodDemo"
   s.license      = "MIT"
   s.license      = { :type => "MIT"， :file => "LICENSE" }
   s.author             = { "username" => "github_emil_address" }
   s.platform     = :ios
   s.source       = { :git => "https://github.com/github_userName/PodDemo.git"， :tag => "0.0.1" }
   s.source_files  = "PodDemo"， "PodDemo/**/*.{h，m}"
   # s.exclude_files = "Classes/Exclude"
   # s.public_header_files = "PodDemo/Classes/Foundation/Foundation_Category.h"，"PodDemo/Classes/**/*.h"
   s.requires_arc = true
 end

	```
	
	
	
- 使用命令直接创建，并做对应修改
	
	```
	$ pod spec create PodDemo
	$ vi PodDemo.podspec //打开podspec 文件选择性修改（修改你需要的内容，因为里面默认有很多）
	```
	
### 	验证<mark>podspec<mark>

	$ pod lib lint
	
- 可能报错1

  	```
	[!] The validator for Swift projects uses Swift 3.0 by default, if you are using a different version of swift you can use a `.swift-version` file to set the version for your Pod. For example to use Swift 2.3, run:
    `echo "2.3" > .swift-version`:
    
    ```
    - 解决方法
    
   ```
   	$ echo "3.1" > .swift-version
   ```
- 其他错误

	```
	查看具体错误信息，对应修改
	$ Pod lib lint --verbose
	```
	
- 成功信息

	```
	PodDemo passed validation.
	```
	
### 提交tag到 <mark>github<mark>
	
```
$ git tag -m"first commit PodDemo.podspec" "0.0.1"
$ git push --tags
```
### 注册cocoapods <已经注册过的可以跳过该步骤>

$ pod trunk register `emil_address` '`user_name`' --description='`macOS`'

- emil_address github 邮箱地址
- macOS 当前注册的设备

命令执行后会返回如下

```
[!] Please verify the session by clicking the link in the verification email that has been sent to (emial—_address)
```
 打开对应邮箱 打开链接完成认证既可
 
 
### 提交PodDemo 到cocoapods
 
 ```
 pod trunk push PodDemo.podspec
 ```
 提交成功返回如下
 
 ```
 Updating spec repo `master`
Validating podspec
 -> FXADScrollView (1.0.0)

Updating spec repo `master`

--------------------------------------------------------------------------------
 🎉  Congrats

 🚀  PodDemo (0.0.1) successfully published
 📅  May 25th, 19:29
 🌎  https://cocoapods.org/pods/PodDemo
 👍  Tell your friends!
--------------------------------------------------------------------------------
 ```

### 搜索上传的sdk

```
pod search PodDemo
```

若返回如下

```
[!] Unable to find a pod with name, author, summary, or description matching `PodDemo`
```
执行下列命令 删除pod的本地索引

```
rm ~/Library/Caches/CocoaPods/search_index.json
```
然后在搜索，出发pod重新拉取所以文件

```
pod search PodDemo
```
搜索成功返回数据包含pod方法

```
pod 'PodDemo'，'~>0.0.1'
```

[pod library 更简单参考] (http://www.jianshu.com/p/2f0458c79a3e)

	