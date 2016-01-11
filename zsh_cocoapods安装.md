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

### cocoapods 安装

1. 查看 sources
	
		$ gem sources
		或
		$ gem sources -l

2. 修改 sources 为 taobao
	
		$ gem sources --remove https://rubygems.org/
		$ gem sources --a http://ruby.taobao.org/
	
3. 安装

		sudo gem install cocoapods
		
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

###  autojump的安装

* 安装命令

		brew install autojump
		vi .zshrc
		编辑 添加 plugins=(autojump) 保存退出，重启终端即可