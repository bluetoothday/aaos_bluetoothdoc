

## Overview

(overview)[https://source.android.com/docs/automotive/audio]


以下是對原文的中文翻譯，並逐段進行解釋：

---

### 概覽

**翻譯：**  
Android Automotive OS (AAOS) 基於核心 Android 音頻堆棧，支援作為車輛資訊娛樂系統的使用場景。AAOS 負責處理資訊娛樂音效（包括媒體、導航和通訊），但不直接負責具有嚴格可用性和時序要求的提示音和警告音。

**解釋：**  
這段介紹了 Android Automotive OS（AAOS）的主要功能，強調它利用 Android 的音頻框架來支援車輛的資訊娛樂系統。AAOS 負責管理與娛樂相關的音頻（如音樂、導航語音、通話），但對於需要高可靠性和精確時序的音效（例如安全帶警告音），則由其他系統或硬體負責。這表明 AAOS 的設計將娛樂音頻與安全相關音頻分開，以確保安全音效的優先級。

---

**原文：**  
While AAOS provides signals and mechanisms to help the vehicle manage audio, in the end it is up to the vehicle to make the call as to what sounds should be played for the driver and passengers, ensuring safety critical sounds and regulatory sounds are properly heard without interruption.

**翻譯：**  
雖然 AAOS 提供了信號和機制來幫助車輛管理音頻，但最終由車輛決定應為駕駛員和乘客播放哪些聲音，確保關鍵安全音效和法規要求的音效能夠不受干擾地被聽到。

**解釋：**  
這段強調了車輛系統在音頻管理中的最終決策權。AAOS 提供工具和信號來協助音頻管理，但車輛製造商需要確保關鍵音效（例如安全警告音或符合法規的音效）優先播放且不被中斷。這反映了 AAOS 的靈活性，允許車輛製造商根據需求定制音頻策略。

---

**原文：**  
Since AAOS leverages the Android audio stack, third party applications playing audio do not need to do anything different then they would in phones. The application's audio routing is automatically managed by AAOS as described in Audio policy configuration.

**翻譯：**  
由於 AAOS 利用了 Android 音頻堆棧，第三方應用程式播放音頻時無需與手機上的操作有所不同。應用程式的音頻路由由 AAOS 根據音頻策略配置自動管理。

**解釋：**  
這段說明 AAOS 的音頻系統與標準 Android 平台高度兼容。第三方應用程式（例如音樂或導航應用）在 AAOS 上運行時，無需特別調整其音頻處理方式，因為 AAOS 會自動根據預設的音頻策略（詳見「音頻策略配置」）管理音頻的路由。這降低了開發者的適配成本。

---

**原文：**  
As Android manages the vehicle’s media experience, external media sources such as the radio tuner should be represented by apps, which can handle audio focus and media key events for the source.

**翻譯：**  
由於 Android 管理車輛的媒體體驗，外部媒體來源（例如收音機調諧器）應由應用程式表示，這些應用程式可以處理音頻焦點和該來源的媒體按鍵事件。

**解釋：**  
這段指出，為了讓外部媒體來源（如收音機）與 Android 的音頻系統整合，這些來源需要通過 Android 應用程式來表示。這些應用程式負責管理音頻焦點（即決定哪個音頻源優先播放）和處理媒體按鍵事件（例如播放/暫停）。這確保外部來源能夠與 Android 的音頻生態系統無縫協作。

---

### Android 音頻和流

**原文：**  
Automotive audio systems handle the following sounds and streams:  
*image*  
Figure 1. Stream-centric architecture diagram.

**翻譯：**  
汽車音頻系統處理以下音頻和流：  
*圖片*  
圖 1. 以流為中心的架構圖。

**解釋：**  
這段介紹了汽車音頻系統處理的音頻類型，並提到一個圖表（圖 1）來說明以流為中心的音頻架構。雖然原文未提供圖表內容，但可以推測這是一個展示音頻流如何在系統中處理和路由的示意圖。這種架構強調音頻流的分類和管理方式。

---

**原文：**  
Android manages the sounds coming from Android apps, controlling those apps and routing their sounds to output devices at the HAL based on the type of sound:  
- Logical streams, known as sources in core audio nomenclature, are tagged with Audio attributes.  
- Physical streams, known as devices in core audio nomenclature, have no context information after mixing.  
For reliability, external sounds (coming from independent sources, such as seatbelt warning chimes) are managed outside Android, below the HAL or even in separate hardware. System implementers must provide a mixer that accepts one or more streams of sound input from Android and then combines those streams in a suitable way with the external sound sources required by the vehicle. The Android Control HAL provides a different mechanism for sounds generated outside Android to communicate back to Android:  
- Audio focus request  
- Gain or volume limitations  
- Gain and volume changes  
The audio HAL implementation and external mixer are responsible for ensuring the safety-critical external sounds are heard and for mixing in the Android-provided streams and routing them to suitable speakers.

**翻譯：**  
Android 管理來自 Android 應用程式的聲音，根據聲音類型控制這些應用程式並將其聲音路由到硬體抽象層（HAL）的輸出設備：  
- 邏輯流，在核心音頻術語中稱為來源，帶有音頻屬性標籤。  
- 物理流，在核心音頻術語中稱為設備，在混音後不包含上下文資訊。  
為了可靠性，外部聲音（來自獨立來源，例如安全帶警告提示音）在 Android 之外管理，位於 HAL 之下或甚至在獨立硬體中。系統實現者必須提供一個混音器，接受來自 Android 的一個或多個音頻流輸入，然後以適當方式將這些流與車輛所需的外部聲音來源結合。Android 控制 HAL 為外部生成的聲音提供了一種與 Android 通信的機制：  
- 音頻焦點請求  
- 增益或音量限制  
- 增益和音量變化  
音頻 HAL 實現和外部混音器負責確保關鍵安全的外部聲音被聽到，並將 Android 提供的流混音並路由到適當的揚聲器。

**解釋：**  
這段詳細描述了 Android 如何處理音頻流，並將其分為邏輯流（帶有上下文的音頻來源，例如音樂或導航語音）和物理流（混音後的輸出，無上下文資訊）。對於安全關鍵的外部聲音（例如安全帶警告音），這些聲音繞過 Android 的音頻系統，直接在硬體層或獨立硬體中處理。系統實現者需要提供一個混音器，將 Android 的音頻流與外部聲音結合。Android 控制 HAL 提供了一個接口，讓外部聲音可以與 Android 系統交互，例如請求音頻焦點或調整音量。這確保了安全音效的優先級，同時允許 Android 管理娛樂音頻。

---

**原文：**  
**Android sounds**  
Apps may have one or more players that interact through the standard Android APIs (for example, AudioManager for focus control or MediaPlayer for streaming) to emit one or more logical streams of audio data. This data could be single channel mono or 7.1 surround, but is routed and treated as a single source. The app stream is associated with AudioAttributes that give the system hints about how the audio should be expressed.  
The logical streams are sent through AudioService and routed to one (and only one) of the available physical output streams, each of which is the output of a mixer within AudioFlinger. After the audio attributes have been mixed down to a physical stream, they are no longer available.  
Each physical stream is then delivered to the Audio HAL for rendering on the hardware. In automotive apps, rendering hardware can be local codecs (similar to mobile devices) or a remote processor across the vehicle's physical network. Either way, it's the job of the Audio HAL implementation to deliver the actual sample data and cause it to become audible.

**翻譯：**  
**Android 音頻**  
應用程式可能擁有一個或多個播放器，通過標準 Android API（例如，用於焦點控制的 AudioManager 或用於串流的 MediaPlayer）發出一個或多個邏輯音頻數據流。這些數據可以是單聲道或 7.1 環繞聲，但被路由並視為單一來源。應用程式流與 AudioAttributes 相關聯，這些屬性為系統提供如何表達音頻的提示。  
邏輯流通過 AudioService 發送，並路由到可用物理輸出流中的一個（且僅一個），每個物理流都是 AudioFlinger 內混音器的輸出。在音頻屬性被混音為物理流後，它們不再可用。  
每個物理流隨後被傳送到音頻 HAL，以便在硬體上渲染。在汽車應用中，渲染硬體可以是本地編解碼器（類似於行動設備）或車輛物理網絡中的遠程處理器。無論哪種方式，音頻 HAL 實現的任務是傳送實際的樣本數據並使其可聽。

**解釋：**  
這段描述了 Android 應用程式如何通過標準 API（如 AudioManager 或 MediaPlayer）生成音頻流。這些流（邏輯流）可以是單聲道或多聲道（如 7.1 環繞聲），但在系統中被視為單一來源，並帶有 AudioAttributes 來指示音頻的類型或用途。這些邏輯流通過 AudioService 處理，進入 AudioFlinger 混音器，最終成為物理流，然後由音頻 HAL 傳送到硬體進行播放。硬體可以是本地設備或車輛網絡中的遠程處理器。這展示了 Android 音頻系統的模組化設計，允許靈活的音頻路由。

---

**原文：**  
**External streams**  
Sound streams that shouldn't be routed through Android (for certification or timing reasons) may be sent directly to the external mixer. As of Android 11, the HAL is now able to request focus for these external sounds to inform Android such that it can take appropriate actions such as pausing media or preventing others from gaining focus.  
If external streams are media sources that should interact with the sound environment Android is generating (for example, stop MP3 playback when an external tuner is turned on), those external streams should be represented by an Android app. Such an app would request Audio focus on behalf of the media source instead of the HAL, and would respond to focus notifications by starting and stopping the external source as necessary to fit into the Android focus policy.  
The app is also responsible for handling media key events such as play and pause. One suggested mechanism to control such external devices is HwAudioSource. To learn more, see Connect an input device in AAOS.

**翻譯：**  
**外部流**  
出於認證或時序原因，不應通過 Android 路由的音頻流可以直接發送到外部混音器。從 Android 11 開始，HAL 現在可以為這些外部聲音請求焦點，以通知 Android 採取適當行動，例如暫停媒體或防止其他應用獲得焦點。  
如果外部流是應與 Android 生成的音頻環境交互的媒體來源（例如，當外部調諧器開啟時停止 MP3 播放），這些外部流應由 Android 應用程式表示。此類應用程式將代表媒體來源請求音頻焦點，而不是由 HAL 請求，並根據焦點通知啟動或停止外部來源，以適應 Android 的焦點策略。  
應用程式還負責處理媒體按鍵事件，例如播放和暫停。控制此類外部設備的一個建議機制是 HwAudioSource。要了解更多資訊，請參閱 AAOS 中的「連接輸入設備」。

**解釋：**  
這段討論了不適合通過 Android 音頻系統路由的外部音頻流（例如出時序或認證需求）。這些流直接發送到外部混音器，但從 Android 11 開始，HAL 可以為這些外部聲音請求音頻焦點，讓 Android 系統暫停其他音頻或限制焦點分配。對於需要與 Android 音頻環境交互的外部媒體來源（如收音機），應通過 Android 應用程式來管理，這些應用程式負責請求焦點和處理按鍵事件（如播放/暫停）。HwAudioSource 是一個建議的控制機制，用於管理外部設備的音頻交互。

---

**原文：**  
**Output devices**  
At the Audio HAL level, the device type AUDIO_DEVICE_OUT_BUS provides a generic output device for use in vehicle audio systems. The bus device supports addressable ports (where each port is the end point for a physical stream) and is expected to be the only supported output device type in a vehicle.  
A system implementation can use one bus port for all Android sounds, in which case Android mixes everything together and delivers it as one stream. Alternatively, the HAL can provide one bus port for each CarAudioContext to allow concurrent delivery of any sound type. This makes it possible for the HAL implementation to mix and duck the different sounds as desired.  
The assignment of audio contexts to output devices is done through the car_audio_configuration.xml file. To learn more, see Audio policy configuration.

**翻譯：**  
**輸出設備**  
在音頻 HAL 層面，設備類型 AUDIO_DEVICE_OUT_BUS 提供了一個通用的輸出設備，用於車輛音頻系統。總線設備支援可尋址的端口（每個端口是物理流的終端），預計是車輛中唯一支援的輸出設備類型。  
系統實現可以使用一個總線端口來處理所有 Android 聲音，在這種情況下，Android 將所有聲音混合在一起並作為一個流傳送。或者，HAL 可以為每個 CarAudioContext 提供一個總線端口，以允許任何聲音類型的並行傳送。這使得 HAL 實現可以根據需要混合和降低不同聲音的音量。  
音頻上下文到輸出設備的分配通過 car_audio_configuration.xml 文件完成。要了解更多資訊，請參閱音頻策略配置。

**解釋：**  
這段描述了車輛音頻系統的輸出設備，特別是 AUDIO_DEVICE_OUT_BUS，這是一個通用的輸出設備類型，支援多個可尋址端口，每個端口對應一個物理音頻流。系統實現者可以選擇將所有 Android 音頻混合為單一輸出流，或為不同音頻上下文（CarAudioContext）分配獨立的端口，以實現更精細的音頻管理。音頻上下文與輸出設備的映射通過 car_audio_configuration.xml 文件配置，這為車輛製造商提供了靈活性來定義音頻路由和優先級。

---

### 總結
這篇文章詳細介紹了 Android Automotive OS (AAOS) 的音頻系統架構，強調其基於 Android 音頻堆棧的設計，並與車輛硬體和外部音頻來源的整合。AAOS 負責管理娛樂音頻（如媒體、導航、通訊），而安全相關音效則由外部系統處理。音頻流分為邏輯流和物理流，通過 AudioService 和 AudioFlinger 處理，並最終由音頻 HAL 傳送到硬體。外部音頻來源可以通過應用程式或 HAL 與 Android 交互，確保音頻焦點和媒體按鍵的正確管理。輸出設備使用 AUDIO_DEVICE_OUT_BUS，支援靈活的音頻路由配置。
