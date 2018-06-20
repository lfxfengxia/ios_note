### 终端命令除了cd都不能使用的情况下的处理

1. 在终端输入 (首先cd到home目录下)
    
        export PATH=/usr/bin:/usr/sbin:/bin:/sbin:/usr/X11R6/bin
2. 此时可以正常使用vi命令
        
        vi.zshrc 
3. 打开zshrc 添加路径

        export PATH="/usr/local/bin:/usr/local/sbin:~/bin:$PATH"
4. 重启zshrc
        
        source .zshrc

        如果vim .zs 使用tab不会自动补齐，反而报如下错误错：
        _arguments:451: _vim_files: function definition file not found
        _arguments:451: _vim_files: function definition file not found
        _arguments:451: _vim_files: function definition file not found
        解决办法
        rm ~/.zcompdump*

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

###  autojump的安装

* 安装命令

		brew install autojump
		vi .zshrc
		编辑 添加 plugins=(autojump) 保存退出，重启终端即可
