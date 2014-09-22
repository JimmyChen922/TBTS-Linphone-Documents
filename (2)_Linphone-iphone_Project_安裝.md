Linphone-iphone Project 安裝與介紹
================================

本文件共包括以下幾個部分

* [安裝 Linphone-iphone](#Installation)
	* [下載最新版本 linphone-iphone 專案](#Download)
	* [準備 Build 的環境](#EssentialLibraries)
	* [Build linphone SDK](#Build_Linphone)
* [更新 Linphone-iphone](#Update_Linphone_Project)


<a name="Installation">Installation</a>
------------


### <a name="Download">下載最新版本 linphone-iphone 專案</a>


1. 在已經下載的 linphone-iphone 資料夾下，使用 Git 做版本控制，需要有 Git

		$ git --version

1. 使用 Git 下載最新的 **Linphone-iphone** Project

		$ git clone git://git.linphone.org/linphone-iphone.git --recursive



### <a name="EssentialLibraries">準備 Build 的環境</a>


說明請見 linphone-iphone 的 **README**。安裝之前，你應該在 Mac 電腦上要安裝完成以下項目：

* **Xcode**：包括 Command Line Tools，以及 xcodebuild 的 license。
* **MacPort**：包括相關的 libraries。
* **JAVA** 和 **JDK**


以下則是列出自己安裝步驟：

1. Xcdoe: 需要先安裝好 Xcode，並同意其 license

		$ sudo xcodebuild -license

2. 安裝 MacPort，及所有需要用到的 libraries

	* 用 `$ echo $PATH` 確認 `/opt/local/bin` 為第一個指定路徑。例如：
	
			/opt/local/bin:/opt/local/sbin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin

	* 然後安裝需要的 libraries

			$ sudo port install coreutils automake autoconf libtool intltool wget pkgconfig cmake gmake yasm nasm grep doxygen ImageMagick optipng antlr3

	* 安裝 [**gas-preprosessor.pl**](http://github.com/yuvi/gas-preprocessor/) (2013 七月以後的版本)，並複製到 `/opt/local/bin` 底下：

			$ wget --no-check-certificate https://raw.github.com/yuvi/gas-preprocessor/master/gas-preprocessor.pl
			$ sudo mv gas-preprocessor.pl /opt/local/bin/.
			$ sudo chmod +x /opt/local/bin/gas-preprocessor.pl
			
	* 連結 macport 的 *libtoolize* 到 *glibtoolize*

			$ sudo ln -s /opt/local/bin/glibtoolize /opt/local/bin/libtoolize
	
	* 連結 host's strings 到 simulator SDK

			$ sudo ln -s  /usr/bin/strings /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/usr/bin/strings


3. 需要安裝 JAVA 和 JDK



### <a name="Build_Linphone">Build Linphone SDK</a>

1. 若使用 GPL license，則進行以下指令：

		# 若之前已經有 build 過的話，需要先 clean。
		$ sudo make make veryclean 
		$ cd submodules/build
		$ make all


完成 Build 後，Linphone 的使用
----------------------------

### Linphone SDK

安裝完成後，在 linphone-iphone 目錄底下會多出一個 `liblinphone-sdk` 資料夾，裡面包括各個平台的 .a 檔，及一個合併的 `apple-darwin` 資料夾。通常都是直接使用 `apple-darwin` 加到專案即可。

### Linphone-iPhone Project 的執行

在 `linphone-iphone` 資料夾底下，有一個 `linphone.xcodeproj` 打開後理論上可以直接編譯執行。這是一個含有 GUI 的 sample code，並可以在 App Store 上找到下載。



<a name="Update_Linphone_Project">Linphone-iphone 版本更新</a>
-----------------------

1. 使用 Git 下載指定版本，或最新版本

		$ git pull

2. 重新編譯 `.a` library (記得要先 clean)

		# 若之前已經有 build 過的話，需要先 clean。
		$ sudo make make veryclean 
		$ cd submodules/build
		$ make all



