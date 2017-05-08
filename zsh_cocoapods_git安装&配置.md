
### zsh安装
1. 安装brew命令

		ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
2. 安装wget命令 
	
		brew install wget
3. 自动安装
	
		wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
		
4. 设置别名

	vi .zshrc 打开zshrc配置文件
	
	插入如下内容： 复制插入即可
		
		alias cls='clear'
		alias ll='ls -l'
		alias la='ls -a'
		alias vi='vim'
		alias javac="javac -J-Dfile.encoding=utf8"
		alias grep="grep --color=auto"
		alias -s html=mate   # 在命令行直接输入后缀为 html 的文件名，会在 TextMate 中打开
		alias -s rb=mate     # 在命令行直接输入 ruby 文件，会在 TextMate 中打开
		alias -s py=vi       # 在命令行直接输入 python 文件，会用 vim 中打开，以下类似
		alias -s js=vi
		alias -s c=vi
		alias -s java=vi
		alias -s txt=vi
		alias -s gz='tar -xzvf'
		alias -s tgz='tar -xzvf'
		alias -s zip='unzip'
		alias -s bz2='tar -xjvf'
	
	
3. 关闭iterm 重启即可
4. 创建.vimrc vim配置文件
	
		git clone https://github.com/lfxfengxia/ios_note.git
		下载ios_note文件夹下的.vimrc文件
		cd ios_note
		cp .vimrc ~/.vimrc

### cocoapods 安装 (安装报错问题详见http://www.cnblogs.com/hankkk/p/5703050.html)

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
		
### git 配置

* 配置个人信息

		git config --global user.name "liufengxia"
		git config --global user.email "18749620739@163.com" //gitHub账号邮箱
		git config --global vim.editor zsh //配置编辑器为zsh 默认未emacs
		
* ~/.gitconfig 配置文件编辑

		方法一： 
			git clone https://github.com/lfxfengxia/ios_note.git 
			下载ios_note文件夹下的.gitconfig文件
			cd ios_note
			cp .gitconfig ~/.gitconfig
		方法二：
			cd ~
			vi .gitconfig
			复制一下内容到.gitconfig文件
			----------------------------------------------------------
			[merge]
			    summary = true
			    tool = vimdiff
			[diff]
			    renames = copy
			[color]
			    diff = auto
			    status = true
			    branch = auto
			    interactive = auto
			    ui = auto
			    log = true
			[status]
			    submodulesummary = -1
			[mergetool "vimdiff"]
			    cmd = "vim --noplugin \"$PWD/$MERGED\" \
			          +\":split $PWD/$REMOTE\" +\":set buftype=nowrite\" \
			          +\":vertical diffsplit $PWD/$LOCAL\" +\":set buftype=nowrite\" \
			          +\":vertical diffsplit $PWD/$BASE\" +\":set buftype=nowrite\" \
			          +\":wincmd l\""
			[format]
			    numbered = auto
			[alias]
			    co = checkout
			    ci = commit
			    st = status
			    pl = pull
			    ps = push
			    dt = difftool
			    l = log --stat
			    cp = cherry-pick
			    ca = commit -a
			    b = branch
			----------------------------------------------------------
			
		
* git 易忘记的命令


		git reset HEAD filname 取消放入暂存的文件
		
		git checkout -- filename 取消对文件的修改
		进而使用git reset HEAD filename 取消对文件的暂存

* git error 的处理

	fatal: multiple stage entries for merged file 'Assets/Prefabs/Resources'

		You can remove Git index file from your project. In the root of your project, run the following command:
		$ rm .git/index
		Then I had to add and commit my local changes again
		$ git add -A
		$ git commit -m "..."
		
###  git合作开发
1. 创建裸仓库 
	- 创建裸仓库文件夹，一般以.git作为后缀
	
			$ mkdir respo.git （respo.git 为要创建的裸仓库的名字）
	- 初始化裸仓库
	
			$ cd respo.git
			$ git init --bare
2. developer A 完成必要的操作
	- 克隆裸仓库
	
			$ git clone <裸仓库的URL或是本地的地址，URL如：https://github.com/lfxfengxia/respo.git，本地地址格式：ssh:device_name@device_ip/bare_respositore_absolute_filepath（如：ssh:emoneybag@192.168.1.134/Users/emoneybag/Desktop/respo.git）>
			此时所处分支为master
	- 添加.gitignore，忽略不必要的提示，如以~和.开头的文件的改变
		
			编辑.gitignore,添加如下命令
			*.DS_Store
			*~
			profile
			保存退出
	- 完成第一次提交并推送
	
			$ git add .gitignore
			$ git ci -m "commit .gitignore"
			$ git push
	- 创建跟踪分支dev，作为推送到裸仓库的一层间接，并推送到裸仓库
	
			$ git co -b dev
			此时所处分支为dev
			$ git push
	- 创建自己的开发分支 dev-A，不需要推送到裸仓库
	
			$ git co -b dev-A
			此时所处分支为dev-A，developerA只在此分支进行开发
3. developer B 必须完成的操作
	- 克隆裸仓库<同developer A克隆>
	- 创建自己的开发分支 dev-B, 并推送到裸仓库
	
			$ git co -b dev-B
			此时所处分支为dev-B，developerB只在此分支进行开发
4. 首次开发的更新操作(以developerA首次开发过程为例)
	- 修改好项目后，在dev-A分支上进行的操作：
			
			$ git st (查看状态，可以看到文件的变化)
			$ git add *
			$ git ci -m "提交内容的描述信息"
			$ git co dev (切换到dev分支)
	- 切换到dev分支上进行的操作：
			
			$ git meger dev-A (dev 与dev-A合并)
			$ git push origin dev （将所做的修改提交到裸仓库）
			$ git co dev-A （切换到自己的开发分枝）
	- 最后来到自己的开发分支继续进行开发。
5. 非首次开发中的更新操作(以developerB开发过程为例)
	- 修改好项目后，在dev-B分支上进行的操作：
			
			$ git st (查看状态，可以看到文件的变化)
			$ git add *
			$ git ci -m "提交内容的描述信息"
			$ git co dev (切换到dev分支)
	- 切换到dev分支上进行的操作：
	
			$ git fetch (查看裸仓库是否有变化，有变化从1步骤开始完成以下操作，没有变化直接从2步骤完成以下操作)
			1.$ git merge origin/dev
			2.$ git meger dev-B (dev 与dev-B合并，有冲突的话解决冲突并进行步骤3提交，没有冲突的话跳过步骤3）
			3.$ git ci -a -m "解决冲突的提交" （跟踪并提交）
			$ git push origin dev （将所做的修改提交到裸仓库）
			$ git co dev-B （切换到自己的开发分枝）
	- 最后来到自己的开发分支继续进行开发。

###  autojump的安装

* 安装命令

		brew install autojump
		vi .zshrc
		编辑 添加 plugins=(autojump) 保存退出，重启终端即可
