# Linphone 開發環境設定 (Using CocoaPods)

本文件是教學如何在 Xcode 建立 Linphone 開發環境，並包括如何加入自己的開發套件。


## 使用 CocoaPods 管理 Linphone Project

CocoaPods 是一套方便管理第三套件的程式


> 關於使用 CocoaPods 來開發 In-House Library 的詳細資料，請參見 [Developing Private In-House Libraries with CocoaPods](Developing_Private_In-House_Libraries_with_CocoaPods.md)


### Podspec

記得要加上 HEADER_SEARCH_PATHS 的設定

	s.xcconfig = { 
		"HEADER_SEARCH_PATHS" => "/Users/Xaree/Development/iOS/Libraries/linphone-TBTS/Libraries/liblinphone-sdk/apple-darwin/include"
	}


### Static Library Dependency

請參見 [Linphone-iphone Project 安裝與介紹](Linphone-iphone_Project_安裝與介紹.md) 內的說明。非相依性的的套件可以選擇是否要加入。


## Using CocoaPods

使用 [CocoaPods](http://cocoapods.org) 來管理第三方套件是較好的解決方案，尤其當有多個案子需要仰賴同一套 library 時。

在開發初期，其實可以先用 Local Pods 來開發和管理本機端的套件。等到開發完成之後，再轉移到 Public/Private 的 Pod Repository 上，供其他開發者使用。


### Local Pods

Local Pods 能夠快速開發本機端的套件，將程式碼模組化，並在本機端多個專案共享同一個 library。將來要將 Pods 發佈到 server 上讓其他使用者使用時（無論是公開或是私有），只需要做一些修改即可發佈。

CocoaPods 能讓開發者發佈 local 的 pod。只要在 Podfile 內，直接指定特定 pod 的路徑即可：

	pod 'linphone-ObjC', :path => '../../Libraries/'


* [Local CocoaPods](http://www.cocoanetics.com/2013/05/8130/)
* [Developing private static library for iOS with CocoaPods](http://blog.sigmapoint.pl/developing-static-library-for-ios-with-cocoapods/)

### Private Pods 

當開發的套件需要成為 Private Pods 讓多人

關於 Private Pods，可以參考

1. [官方文件](http://guides.cocoapods.org/making/private-cocoapods.html)
2. [CocoaPods: Working With Internal Pods Without Hassle](http://albertodebortoli.github.io/blog/2014/03/11/cocoapods-working-with-internal-pods/)
3. [Cocoanetics: Private Pods](http://www.cocoanetics.com/2013/04/private-pods/)
4. [Stackoverflow 的討論-1](http://stackoverflow.com/questions/22298850/creating-private-cocoapods-repo-outside-github)、[Stackoverflow 的討論-2](http://stackoverflow.com/questions/25052755/how-to-add-a-private-spec-repo-for-using-private-pods)
5. [Developing Private In-House Libraries with CocoaPods](http://www.objectpartners.com/2014/06/25/developing-private-in-house-libraries-with-cocoapods/)


### Podspecs

* [theonlylars: Creating a Pod Spec](http://www.theonlylars.com/blog/2013/01/20/cocoapods-creating-a-pod-spec/)
