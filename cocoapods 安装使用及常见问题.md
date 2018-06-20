### cocoapods 安装 ([安装报错问题](http://www.cnblogs.com/hankkk/p/5703050.html))

1. 查看 sources
	
		$ gem sources
		或
		$ gem sources -l

2. 修改 sources 为 taobao
	
		$ gem sources --remove https://rubygems.org/
		//$ gem sources -a https://ruby.taobao.org/   //淘宝镜像已经失效
        $ gem sources -a https://gems.ruby-china.org/
	
3. 安装

		sudo gem install cocoapods
        1.安装报错（hostname "upyun.gems.ruby-china.org" does not match the server certificate） 更新gem
        gem update --system
        2.更新gem报错（You don't have write permissions for the /Library/Ruby/Gems/2.0.0 directory.）安装最新ruby 通过安装rvm 安装最新ruby
            a. ruby -v //查看当前ruby version
            b. curl -L get.rvm.io | bash -s stable  //安装rvm
            c. rvm list known //查看可按装的版本 找到最新的ruby 版本号（如2.4.0）
            d. rvm install 2.4.0 //安装ruby
            e. ruby -v //安装成功 查看ruby version ruby 2.4.0p0
        3.sudo gem install cocoapods //再次安装cocoapods
        4.pod setup
        
4. 使用

		1. 进入项目路径下，
		    $ pod init   //创建Podfile文件
		2. 编辑Podfile
			$ vi Podfile 
			eg : 
				platform :ios, '7.0'
				pod "AFNetworking", "~> 2.0"
		3. 保存退出
			$ :wq
		4. 安装引入的第三方库
			$ pod install
		5. 在以后使用时，只需要更新
			$ pod update
		6. 当你拿到别人的使用cocoapods管理的项目时，首先炫耀做的就是更新，否则会报找不到各种第三方库的错误

	<mark>注意<mark>

 		pod install 对Podfile文件中存在的且已安装的库不会做更新处理
 		pod update  对Podfile文件中存在的库，不管是否已安装，如果检测到改库已安装且有符合改库在Podfile文件中版本限制的最新库，则对该库做更新处理；如果检测到改库为安装，则进行安装。	

### pod 常见问题

1. pod init 报错处理

	```
   可能pod版本更新或是一些依赖文件更新之后 导致使用pod init 或者 pod update 以及pod的一些相关命令都会报如下错误
   RuntimeError - [Xcodeproj] Unknown object version.
            
   解决办法：
  	$ gem install cocoapods --pre //更新cocoapods
	```
	```
	其他命令
  	$ gem update --system //更新gem
	$ gem install rubygems-update //安装rubygems
 	$ update_rubygems //更新rubygems
 	
	```
	
2. Xcode升级导致新版本Xcode创建项目 pod install 失败

	```
	报错信息：
		Unable to add a source with url `https://github.com/CocoaPods/Specs.git` named `master-1`.
	解决办法：
		找到repos文件夹下的master 手动删除master 并重新下载即可
		1. 打开终端
		2. $ cd
		3. $ cd .cocoapods/repos
		4. $ open . //打开当前目录 手动删除master文件夹
		5. $ git clone https://git.coding.net/CocoaPods/Specs.git ~/.cocoapods/repos/master 

		一般操作到这就可以了，如果还是还是报错的话，可以进行如下操作：
		1. pod repo add master https://github.com/CocoaPods/Specs.git
		2. $ pod setup
	```
