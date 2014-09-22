Linphone-iphone Development Notes
==========================

本文件為教學如何使用 Linphone Project 的 C API，提供 Linphone Project 開發者一個指引的文件。

官方文件可參考這邊連結 –– [Linphone C API Documentation](http://www.linphone.org/docs/liblinphone/modules.html)。



Basic –– Linphone Core
----------------------

### Basic Introduction

`LinphoneCore` 是 Linphone library 的核心物件，它提供了 high-level C API，並負責與 low-level library 進行管理和調用 (用到的 low-level 套件可參見[這裡](Linphone-iphone_Project_安裝與介紹.md#Introduction_of_sublibraries))。

`LinphoneCore` 在程式中應該要為 **Singleton**。並在初始化之後，以一個 runloop/timer 持續呼叫 `linphone_core_iterate()`，來讓 `LinphoneCore` 可以持續處理接收到的事件。

在使用 Linphone 上，分為三個部分：

1. 設定 linphone core configuration
2. 設定 callback 與處理 (例如：來電處理)
3. 主動要求 linphone 進行特定任務 (例如：撥打電話)

其中，configuration 和 callback 設定大部分的工作，是在 Linphone 初始化時進行。

### Lifecycle

* App did finish launching:
	* `startLibLinphone`: 啟動所有 linphone 會用到的 libraries 
	* `createLinphoneCore`: 初始化 linphone core，並開始 iterate
	* `finishCoreConfiguration`: 
	* (`setupNetworkReachabilityCallback`)



### Configuration and Initialization

`linphone_core_new()`、`linphone_core_new_with_config()` 是創造 `LinphoneCore` 方式，兩者的都是給予一個 configuration 的 source，以及一個 callback 的 vtable。


#### Configuration

Linphone Project 是用 `LpConfig` 這個物件來存放 `LinphoneCore` 參數，包括了能夠把參數資料從硬碟中進行存取。在初始化 `LpConfig` 時，可以指定兩個 file_path：

* `config_filename`: 用來儲存 `LpConfig` 參數的指定路徑，要確保該路徑可以進行讀寫。若該檔案不存在，Linphone 會自動在此路徑建立一個新的 file。若不指定路徑，則 runtime 的執行參數是不會寫入硬碟中。
* `factory_config_filename`: 若有指定此路徑，則在 `LpConfig` 讀取 `config_filename` 的參數進行初始化之後，會再用 `factory_config_filename` 裡的參數來覆蓋。換而言之，每次 `LpConfig` 初始化時，都會被此 factory 中的參數給覆蓋。

若是需要只有在第一次時初始化，則可先檢查 `config_filename` 是否存在，再決定是否要指定 `factory_config_filename`。

相關參數在檔案的設定，可以參見 [Linphone Core Configuration File (連結的 Documentatoin 分類下)](http://www.linphone.org/technical-corner/linphone.html)。

#### Creation

參見 `-[LinphoneManager createLinphoneCore]`


### Start Linphone

`-[LinphoneManager startLibLinphone]`



#### Initialization


Linphone core state change:

> `LinphoneGlobalStateStartup` -> `LinphoneConfiguringStateSkipped` -> `LinphoneGlobalStateOn`

你可以使用檔案來設定 configuration

1. `-[LinphoneManager finishCoreConfiguration]`


### Reset LinphoneCore


Linphone 在使用 `linphone_core_set_provisioning_uri()` 更改 provisioning uri 之後，會重新設定 LinphoneCore：

	-[LinphoneCore resetLinphoneCore]


會更改 provisioning uri 主要在下面幾個地方：

* 創建新帳號: 會觸發 `-[WizardViewController onCreateAccountClick:]` 並使用 `wizard_linphone_create.rc`。
* 使用 Linphone.org 的帳號：會觸發 `-[WizardViewController onConnectAccountClick:]` 並使用 `wizard_linphone_existing.rc`。
* 使用其他的 SIP 帳號：會觸發 `-[WizardViewController onExternalAccountClick:]` 並使用 `wizard_external_sip.rc`。
* 使用 Remote provisioning：會觸發 `-[WizardViewController onRemoteProvisioningClick:]`

#### Provisioning URI

[Wikipedia](http://en.wikipedia.org/wiki/Provisioning) 有對 Provisioning 的基本解釋。



### Codecs


**待補充**



其他功能說明
==========

Registration
------------

1. `-[WizardViewController onRegisterClick:]`
2. 會在 callback 中處理 (使用 *XMLRPC*)


Sign In
-------

1. `-[WizardViewController onSignInClick:]`
2. `-[WizardViewController addProxyConfig:password:domain:]`

?
1. `-[FirstLoginViewController onLoginClick:]`


Make a SIP Phone Call (Call Out)
--------------------------------

### Linphone Project 解析

1. 在 Linphone Project 中，當按下播出按鈕時 (該按鈕為 `UICallButton` 類別)，會觸發 `-[UICallButton touchUp:]` 並進行以下事項：

	* 檢查 address 是否有輸入：若沒有輸入，則尋找上次撥打記錄，並填入 TextField 中（使用者此時必須再按一次撥打才能播出）；若已經有輸入，嘗試從通訊錄中找到該帳號對應的「顯示名稱」
	
	* 真正播打電話的程式碼是 `-[LinphoneManager call:displayName:transfer:]`


2. 在 `-[LinphoneManager call:displayName:transfer:]` 進行撥打功能：

	* 先檢查網路是否可以使用。並檢查是否在 GSM 情況下進行電話通訊中。以確定是否可以合法的進行 SIP phone call。否則取消播出並跳出提醒說明原因。

	* 取得 `LinphoneProxyConfig` 和 `LinphoneCallParams` 物件並進行相關設定：

			LinphoneProxyConfig* proxyCfg;	
			//get default proxy
			linphone_core_get_default_proxy(theLinphoneCore,&proxyCfg);
			LinphoneCallParams* lcallParams = linphone_core_create_default_call_parameters(theLinphoneCore);

	* 可由 `linphone_core_get_call_logs()` 取得撥打記錄。

4. `-[LinphoneManager onCall:StateChanged:withMessage:]` 處理進入撥打狀態的 callback，並發出 Notification (`kLinphoneCallUpdate`)。

5. 在 `InCallViewController` 底下會 subscribe `kLinphoneCallUpdate`，並於 `-[InCallViewController callUpdate:]` 處理。



### Accept Call

1. `-[LinphoneManager acceptCall:]`

### 進入 Video


1. 當 video call 建立時，LinphoneCore 會呼叫 `LinphoneCoreCallStateChangedCb` 的 callback，且 call state 會為 `LinphoneCallStreamsRunning`。

	* 在此時檢查 LinphoneCall 的 params 中的 video 是否 enable。若是，則表示進入視訊（也可順便檢查 codec） 

			// 檢查 video 是否建立
			linphone_call_params_video_enabled(linphone_call_get_current_params(call))
			// 檢查 codec 是否存在
			linphone_call_params_get_used_video_codec(linphone_call_get_current_params(call));

2. 建立之後，必須要先設定 video window

		linphone_core_set_native_video_window_id(lc, videoView);
		linphone_core_set_native_preview_window_id(lc, videoPreview);
		

3. 可以確認 video view 和 video preview 是否開啟

		linphone_core_video_preview_enabled(lc);
		linphone_core_self_view_enabled(lc);
		
4. `linphone_call_params_low_bandwidth_enabled()` 檢查低頻寬時，不要使用 video



### Call In

1. `-[LinphoneManager acceptCall:]`


Video
-----

### 寄出邀請

1. `-[UIVideoButton onOn]`

### 接受邀請

1. `[InCallViewController displayAskToEnableVideoCall:]`
1. `-[LinphoneManager acceptCall:]`

### 開始視訊

1. `-[InCallViewController callUpdate:state:animated:]` 的 `case LinphoneCallStreamsRunning`
2. `-[InCallViewController enableVideoDisplay:]`

### 關掉視訊

1. `-[UIVideoButton onOff]`

### 更換視訊使用鏡頭

1. `-[UICamSwitch touchUp:]`




* video size
* available codec