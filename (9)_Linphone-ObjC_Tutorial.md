Linphone-ObjC Tutorial
======================


設定 Xcode Project
-----------------

目前 Linphone 的 Library 是使用 32-bit，因此必須將專案指定在 32-bit

在 xcodeproj 的 **Build Settings** -> **Architectures** -> **Architectures** 設定為 `$(ARCHS_STANDARD_32_BIT)`


使用 CocoaPods 安裝
------------------

XAspect 為必要的套件

	pod 'XAspect', :path => '/Users/Xaree/Development/iOS/Libraries/XAspect/'
	pod 'Linphone-ObjC/TBTS-Sip-Phone-iOS', :path => '../../Libraries/'
	
完成之後，開啟 .xcworkspace


加入 Aspect 及相關檔案，並設定相關參數
---------------------------------