# LibLinphone C API 簡介

[Linphone C API](http://fossies.org/dox/linphone-3.7.0/modules.html)

以下介紹 LibLinphone 的主要物件


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


LinphoneProxyConfig 
-------------------
1. User registration is controled by LinphoneProxyConfig settings.
2. 基本使用流程
	* 使用 linphone_proxy_config_new() 來創造一個 LinphoneProxyConfig 物件
	* 設定 Proxy Config
	* 使用 linphone_core_add_proxy_config() 來加到 linphone core 內
	* 建議使用 linphone_core_set_default_proxy() 設定 linphone core 的 default proxy；如此一來，當使用 linphone_proxy_config_enable_register() 時，會自動再下次 linphone_core_iterate() 時，進行 SIP register。
1. registration 需要先完成 authentication 才能成功。因此 linphone_core_add_auth_info() 必須在 linphone_core_add_proxy_config() 之前。


LinphoneAddress
---------------
Object that represents a SIP address.



LinphoneAuthInfo
----------------
1. 包含了 username、userid、passwd、ha1、realm、domain。
	* 絕大多數情況下，username 和 passwd 就足夠；某些情況之下，Proxy 會使用到 userid。
2. 建立好 LinphoneAuthInfo 之後，必須要加到 LinphoneCore 才會起作用 (linphone_core_add_auth_info())。



LinphoneCall
------------
1. 代表了 incoming 以及 outgoing 的 call。
	* outgoing 可以用 `linphone_core_invite()` 或是 `linphone_core_invite_address()` 來創造。
	* incoming 則是用 vtable 中的 `LinphoneCoreCallStateChangedCb` 的 callback 來 取得 call 物件。


### Outgoing Call

在 invite 之後，call state 的轉變

```
// 成功播出之後：
LinphoneCallOutgoingInit -> LinphoneCallOutgoingProgress -> LinphoneCallOutgoingRinging (等待下一步)

// 結束通話 (無論是等待接通前、或是通話中：無論是對方掛電話，或是自己掛斷電話都是一樣的狀態轉變):
LinphoneCallEnd -> LinphoneCallReleased

// 接通電話
LinphoneCallConnected -> LinphoneCallStreamsRunning ==> 持續獲得 LinphoneCallStats 更新 (不是 CallState) ==結束電話時==> LinphoneCallLogUpdatedNotification (Call log 更新)

// 例外情況：撥打不存在的帳號:
LinphoneCallOutgoingInit -> LinphoneCallOutgoingProgress -> LinphoneCallError -> LinphoneCallReleased (沒有 Call End)

// 例外情況：撥打途中網路斷線
收到 LinphoneRegistrationStateChangedNotification (LinphoneRegistrationNone)


```


LinphoneContent
---------------

LinphoneDictionary
---------------


