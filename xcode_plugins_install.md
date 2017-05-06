### Xcode-Plugins Install

1. 打开终端：执行安装Alcatraz的命令  `注意` ： 安装Alcatraz过程中Xcode必须是完全关闭的状态

		$ curl -fsSL https://raw.githubusercontent.com/supermarin/Alcatraz/deploy/Scripts/install.sh | sh
		
2. 安装成功后，添加UUIDS 
			
		Xcode 6.4 的是这个
		7FDF5C7A-131F-4ABB-9EDC-8C5F8F0B8A90
		Xcode 6.0.1的是这个
		C4A681B0-4A26-480E-93EC-1218098B9AA0

        //查看Xcode UUID的方法：
        1. 在终端执行 defaults read /Applications/Xcode.app/Contents/Info DVTPlugInCompatibilityUUID
        2. 在/Applications目录中找到Xcode.app，右键”显示包内容”，进入Contents文件夹，双击Info.plist打开，找到DVTPlugInCompatibilityUUID，拷贝后面的字符串
		
		$ cd Library/Application\ Support/Developer/Shared/Xcode/Plug-ins/
		$ open .
		Alcatraz.xcplugin 右键显示包内容->content->info.plist 添加DVTPlugInCompatibilityUUIDs的items为xcode6的UUID
		
3. 打开Xcode，在跳出的提示窗口中选择load Bundle，不能选择Skip Bundle
4. 打开Package Manager，搜索想要的插件，安装之后重启xcode即可使用已安装的插件功能
5. 常用的Xcode 插件

	- VVDocumenter 注释
	- KSImageNamed [UIImage imageNamed:];图片名称的显示
	- SCXcodeSwitchExpander 自动实现switch cases
