## audio focus

(audio focus)[https://source.android.com/docs/automotive/audio/audio-focus]


以下是對原文的中文翻譯，並逐段進行解釋：

---

### 音頻焦點

**翻譯：**  
在啟動邏輯音頻流之前，應用程式使用與該邏輯流相同的音頻屬性請求音頻焦點。應用程式必須尊重音頻焦點的丟失，以在汽車使用場景中按預期執行。

**解釋：**  
這段介紹了音頻焦點（Audio Focus）的概念，這是 Android 音頻系統中用於管理多個應用程式音頻播放的核心機制。在汽車環境中，應用程式在播放音頻前需要請求音頻焦點，並使用與音頻流相關聯的 AudioAttributes。應用程式需要響應音頻焦點丟失事件（例如暫停播放），以確保在汽車場景中音頻行為符合預期。這對於避免音頻衝突和確保駕駛安全至關重要。

---

**原文：**  
While sending a focus request is recommended, it isn't enforced by the system. Therefore, consider focus as a means to indirectly control and avoid conflict during playback instead of as a primary audio control mechanism. The vehicle shouldn't depend on the focus system for operation of the audio subsystem.

**翻譯：**  
雖然建議發送音頻焦點請求，但系統並不強制執行。因此，應將音頻焦點視為間接控制和避免播放衝突的手段，而不是主要的音頻控制機制。車輛不應依賴焦點系統來運作音頻子系統。

**解釋：**  
這段強調音頻焦點是一種建議性的管理工具，而非強制性要求。系統鼓勵應用程式請求音頻焦點以協調音頻播放，但車輛的音頻系統不應完全依賴焦點系統來管理所有音頻。例如，安全相關的音效（如警告音）可能繞過焦點系統直接播放，以確保其可靠性。這表明音頻焦點在汽車環境中是輔助性的，車輛製造商需要其他機制來保證關鍵音頻的播放。

---

### 焦點交互

**原文：**  
To support AAOS, audio focus requests are handled based on predefined interactions between the request's CarAudioContext and that of current focus holders. There are three types of interactions:  
- Exclusive  
- Reject  
- Concurrent  

**翻譯：**  
為了支援 AAOS，音頻焦點請求根據請求的 CarAudioContext 與當前焦點持有者之間的預定義交互進行處理。交互類型有三種：  
- 獨占  
- 拒絕  
- 並行  

**解釋：**  
這段介紹了 AAOS 中音頻焦點的交互模式，分為三種類型：獨占（Exclusive）、拒絕（Reject）和並行（Concurrent）。這些交互模式由 CarAudioContext（音頻上下文）決定，該上下文定義了音頻的類型（如音樂、導航、通話）。不同的交互模式決定了當多個應用程式請求音頻焦點時，系統如何分配焦點並管理音頻播放。

---

### 獨占交互

**原文：**  
This is the interaction model most commonly used with Android.  
In exclusive interactions, only one app is allowed to hold focus at a time. Therefore, an incoming focus request is granted focus while the existing focus holder loses focus. Since both apps play media, only one app is allowed to hold focus. As a result, the newly started app's focus request is returned with AUDIOFOCUS_REQUEST_GRANTED while the current playing music app receives a focus change event with a loss status that corresponds to the type of request that was made.

**翻譯：**  
這是 Android 最常用的交互模型。  
在獨占交互中，同一時間僅允許一個應用程式持有焦點。因此，新的焦點請求會被授予焦點，而現有的焦點持有者將失去焦點。由於兩個應用程式都在播放媒體，只有一個應用程式可以持有焦點。因此，新啟動的應用程式的焦點請求返回 AUDIOFOCUS_REQUEST_GRANTED，而當前播放音樂的應用程式會收到一個帶有丟失狀態的焦點變更事件，該狀態對應於請求的類型。

**解釋：**  
獨占交互是 Android 音頻焦點的標準模式，適用於大多數場景。在這種模式下，當一個新應用程式請求音頻焦點時，系統會授予其焦點，同時通知當前焦點持有者（例如正在播放音樂的應用程式）失去焦點。這確保只有一個應用程式的音頻能夠主導播放，避免聲音重疊。例如，如果導航應用程式請求焦點，音樂應用程式將被暫停。

---

### 拒絕交互

**原文：**  
With reject interactions, the incoming request is always rejected. For example, when attempting to play music while a call is in progress. In this case, if the Dialer holds audio focus for a call and a second app requests focus to play music, the music app receives AUDIOFOCUS_REQUEST_FAILED in response to the request. Since the focus request is rejected, no focus loss is dispatched to the current focus holder.

**翻譯：**  
在拒絕交互中，新的焦點請求總是被拒絕。例如，當電話通話進行時嘗試播放音樂。如果撥號應用程式持有通話的音頻焦點，而第二個應用程式請求播放音樂的焦點，音樂應用程式將收到 AUDIOFOCUS_REQUEST_FAILED 的響應。由於焦點請求被拒絕，當前焦點持有者不會收到焦點丟失通知。

**解釋：**  
拒絕交互適用於高優先級音頻場景，例如電話通話。當一個高優先級應用程式（如撥號器）持有焦點時，其他應用程式的焦點請求會被直接拒絕，且現有焦點持有者不會受到影響。這確保關鍵音頻（如通話）不被中斷。例如，當用戶在通話中，音樂應用程式無法獲得焦點，從而避免干擾通話。

---

### 並行交互

**原文：**  
Unique to AAOS are concurrent interactions. This gives apps that request audio focus in the car the ability to hold focus concurrently with other apps. For a concurrent interaction to take place, the following conditions must be met. The:  
- Incoming focus request must ask for AudioManager.AUDIOFOCUS_GAIN_TRANSIENT_MAY_DUCK  
- Current focus holder doesn't setPauseWhenDucked(true)  
- Current focus holder opts not to receive duck events  
If these criteria are met, then the focus request returns with AUDIOFOCUS_REQUEST_GRANTED while the current focus holder has no change in focus. However, if the current focus holder opts to receive duck events or to pause when ducked, the current focus holder loses focus, as occurs with an exclusive interaction.

**翻譯：**  
AAOS 獨有的並行交互。這種模式允許在車內請求音頻焦點的應用程式與其他應用程式同時持有焦點。要實現並行交互，必須滿足以下條件：  
- 新的焦點請求必須請求 AudioManager.AUDIOFOCUS_GAIN_TRANSIENT_MAY_DUCK  
- 當前焦點持有者未設置 setPauseWhenDucked(true)  
- 當前焦點持有者選擇不接收降低音量（duck）事件  
如果滿足這些條件，焦點請求將返回 AUDIOFOCUS_REQUEST_GRANTED，而當前焦點持有者的焦點狀態不變。然而，如果當前焦點持有者選擇接收降低音量事件或在降低音量時暫停，則當前焦點持有者將失去焦點，如同獨占交互一樣。

**解釋：**  
並行交互是 AAOS 的獨特功能，允許多個應用程式同時播放音頻，這在汽車環境中特別有用（例如導航語音和音樂同時播放）。要實現並行，請求必須是臨時的（AUDIOFOCUS_GAIN_TRANSIENT_MAY_DUCK），且當前焦點持有者不能要求在音量降低時暫停或接收降低音量事件。如果條件滿足，新應用程式獲得焦點，而現有焦點持有者繼續播放（可能音量降低）。如果現有應用程式要求暫停或接收降低音量事件，則會像獨占模式一樣失去焦點。

---

### 處理並行流

**原文：**  
While the concurrent interaction has numerous uses, be careful at the mixing and ducking at the hardware level across output devices. We strongly recommend that instances of CarAudioContext that are allowed to play concurrently should be routed to different output devices.  
By having separate output devices for concurrent streams, this enables the HAL to duck one of the streams before mixing them, or to route the physical streams to different speakers in the vehicle. If the logical streams are mixed within Android, gains are unaltered and delivered as part of the same physical stream.  
For example, when navigation and media are delivered simultaneously, the gain for the media stream could temporarily be reduced (or, ducked) so that navigation instructions can be heard more clearly. Alternatively, the navigation stream could be routed to the driver side speakers while media continues to play throughout the rest of the cabin.

**翻譯：**  
雖然並行交互有許多用途，但在硬體層面的混音和音量降低（ducking）時需要小心。我們強烈建議允許並行播放的 CarAudioContext 實例應路由到不同的輸出設備。  
通過為並行流設置獨立的輸出設備，HAL 可以在混音前降低某個流的音量，或將物理流路由到車輛中的不同揚聲器。如果邏輯流在 Android 內部混音，增益不會改變，並作為同一物理流的一部分傳送。  
例如，當導航和媒體同時傳送時，媒體流的增益可能會暫時降低（即降低音量），以便更清楚地聽到導航指令。或者，導航流可以路由到駕駛員側的揚聲器，而媒體繼續在車艙其餘部分播放。

**解釋：**  
這段討論了並行交互在硬體層面的實現細節。為了有效管理並行音頻流，建議將不同類型的音頻（例如導航和媒體）路由到不同的輸出設備（如不同的揚聲器）。這允許硬體抽象層（HAL）在混音前調整音量（例如降低媒體音量以突出導航語音），或將音頻流分發到車內不同位置的揚聲器（例如導航語音僅在駕駛員側播放）。如果所有音頻在 Android 內部混音，則無法單獨調整音量，可能導致音頻體驗不佳。

---

### 交互矩陣

**原文：**  
This table shows the interaction matrix as defined by CarAudioService. Each row represents the current focus holder's CarAudioContext and each column represents that of the incoming request.  
For example, when a music media app holds focus as a navigation app requests focus, the matrix indicates that the two interactions can play concurrently, assuming the other criteria for concurrent interactions are fulfilled.  
Because of the concurrent interactions, it's possible to have more than one focus holder. In this case, an incoming focus request is compared with each of the current focus holders before determining what interaction to apply. In this case, the most conservative interaction wins. Reject, then exclusive, and finally concurrent.

**翻譯：**  
此表格展示了由 CarAudioService 定義的交互矩陣。每行表示當前焦點持有者的 CarAudioContext，每列表示新進請求的 CarAudioContext。  
例如，當音樂媒體應用程式持有焦點，而導航應用程式請求焦點時，矩陣表明這兩個交互可以並行播放，假設滿足並行交互的其他條件。  
由於並行交互的存在，可能有多個焦點持有者。在這種情況下，新進的焦點請求會與每個當前焦點持有者進行比較，以確定應應用哪種交互。在這種情況下，最保守的交互優先：拒絕、獨占、並行。

**解釋：**  
這段介紹了音頻焦點交互矩陣，這是一個由 CarAudioService 定義的表格，用於決定不同音頻上下文（CarAudioContext）之間的焦點交互行為。例如，音樂和導航可能被配置為並行播放。當有多個焦點持有者時，系統會比較新請求與每個現有焦點持有者的交互，並選擇最保守的策略（優先順序為：拒絕 > 獨占 > 並行）。這確保高優先級音頻（如通話）不會被低優先級音頻干擾。

---

### 通話期間的導航

**原文：**  
In Android 11, a new user setting was introduced to allow users to alter the interaction behavior between navigation and phone calls. When set, android.car.KEY_AUDIO_FOCUS_NAVIGATION_REJECTED_DURING_CALL changes the interaction between incoming NAVIGATION focus requests and current CALL focus holders from concurrent to rejects. If a user prefers that navigation instructions not interrupt a call, they can enable the setting. This is persisted for the user, and can be set dynamically so that subsequent focus requests respect the new setting.

**翻譯：**  
在 Android 11 中，引入了一個新用戶設置，允許用戶更改導航與電話通話之間的交互行為。當啟用 android.car.KEY_AUDIO_FOCUS_NAVIGATION_REJECTED_DURING_CALL 時，新的導航焦點請求與當前通話焦點持有者之間的交互從並行變為拒絕。如果用戶希望導航指令不打斷通話，可以啟用此設置。此設置會為用戶保存，並可動態設置，以使後續焦點請求遵循新設置。

**解釋：**  
這段描述了 Android 11 引入的一個新功能，允許用戶自定義導航和通話之間的音頻焦點交互。默認情況下，導航和通話可能是並行播放（導航語音與通話同時進行），但用戶可以通過設置 android.car.KEY_AUDIO_FOCUS_NAVIGATION_REJECTED_DURING_CALL 選擇拒絕導航焦點請求，從而確保通話不被打斷。這提供了更高的用戶控制權，適應不同駕駛者的偏好。

---

### 可延遲音頻焦點

**原文：**  
In Android 11, AAOS added support for requesting delayable audio focus. This allows non-transient focus requests to be delayed when their interaction with current focus holders would normally result in them being rejected. Once a change in focus results in a state where the delayed request can gain focus, the request is granted.

**翻譯：**  
在 Android 11 中，AAOS 增加了對可延遲音頻焦點請求的支援。當非臨時焦點請求因與當前焦點持有者的交互而被拒絕時，這些請求可以被延遲。一旦焦點狀態改變使得延遲的請求可以獲得焦點，該請求將被授予。

**解釋：**  
可延遲音頻焦點是 Android 11 的一項新功能，適用於非臨時（長期）音頻焦點請求。如果請求因高優先級音頻（如通話）而被拒絕，系統會將其延遲，而不是立即拒絕。一旦高優先級音頻結束，延遲的請求可以獲得焦點。這適用於需要長期播放的音頻（如音樂），確保其在適當時機獲得播放機會。

---

### 可延遲音頻焦點請求的規則

**原文：**  
- Non-transient requests only. A delayed request can only be made for non-transient sources in order to avoid having a transient sound play long after it's relevant.  
- Only one request can be delayed at a time. If a delayable request is made while there is already a delayed request, the original delayed request receives a AUDIOFOCUS_LOSS change event and the new request receives a synchronous response of AUDIOFOCUS_REQUEST_DELAYED.  
- Delayable requests must have OnAudioFocusChangeListener. After a request is delayed, the listener is used to notify the requester when the request is eventually granted (AUDIOFOCUS_GAIN), or if it's rejected later (AUDIOFOCUS_LOSS).

**翻譯：**  
- 僅限非臨時請求。可延遲請求僅適用於非臨時來源，以避免臨時聲音在失去相關性後播放。  
- 一次只能延遲一個請求。如果在已有延遲請求時提出新的可延遲請求，原延遲請求將收到 AUDIOFOCUS_LOSS 變更事件，而新請求將收到 AUDIOFOCUS_REQUEST_DELAYED 的同步響應。  
- 可延遲請求必須具有 OnAudioFocusChangeListener。請求延遲後，該監聽器用於通知請求者在最終獲得焦點（AUDIOFOCUS_GAIN）或稍後被拒絕（AUDIOFOCUS_LOSS）時的情況。

**解釋：**  
這段列出了可延遲音頻焦點的具體規則：  
1. **僅限非臨時請求**：臨時音頻（如導航提示音）不適合延遲，因為它們通常與特定事件相關，延遲可能導致無效播放。  
2. **一次僅一個延遲請求**：系統限制同時只能有一個延遲請求，以避免管理複雜性。如果有新請求，舊請求會被取消（收到 AUDIOFOCUS_LOSS）。  
3. **必須有監聽器**：應用程式需要設置 OnAudioFocusChangeListener 來接收延遲請求的狀態更新（例如最終獲得焦點或被拒絕）。這些規則確保可延遲焦點的有序管理。

---

### 請求可延遲焦點

**原文：**  
To build a request that can be delayed:  
Use AudioFocusRequest.Builder#setAcceptsDelayedFocusGain.  
```java
mMediaWithDelayedFocusListener = new MediaWithDelayedFocusListener();

mDelayedFocusRequest = new AudioFocusRequest
     .Builder(AudioManager.AUDIOFOCUS_GAIN)
     .setAudioAttributes(mMusicAudioAttrib)
     .setOnAudioFocusChangeListener(mMediaWithDelayedFocusListener)
     .setForceDucking(false)
     .setWillPauseWhenDucked(false)
     .setAcceptsDelayedFocusGain(true)
     .build();
```  
When making the request, handle the AUDIOFOCUS_REQUEST_DELAYED response:  
```java
int delayedFocusRequestResults = mAudioManager.requestAudioFocus(mDelayedFocusRequest);
if (delayedFocusRequestResults == AudioManager.AUDIOFOCUS_REQUEST_GRANTED) {
    // start audio playback
    return;
}
if (delayedFocusRequestResults == AudioManager.AUDIOFOCUS_REQUEST_DELAYED) {
     // audio playback delayed to audio focus listener
     return;
}
```  
When the request is delayed, the focus listener handles changes in focus:  
```java
private final class MediaWithDelayedFocusListener implements OnAudioFocusChangeListener {
       @Override
       public void onAudioFocusChange(int focusChange) {
           synchronized (mLock) {
               switch (focusChange) {
                   case AudioManager.AUDIOFOCUS_GAIN:
                       … // Start focus playback
                   case AudioManager.AUDIOFOCUS_LOSS_TRANSIENT:
                       … // Pause media transiently
                   case AudioManager.AUDIOFOCUS_LOSS:
                       … // Stop media
```

**翻譯：**  
要構建一個可延遲的請求：  
使用 AudioFocusRequest.Builder#setAcceptsDelayedFocusGain。  
```java
mMediaWithDelayedFocusListener = new MediaWithDelayedFocusListener();

mDelayedFocusRequest = new AudioFocusRequest
     .Builder(AudioManager.AUDIOFOCUS_GAIN)
     .setAudioAttributes(mMusicAudioAttrib)
     .setOnAudioFocusChangeListener(mMediaWithDelayedFocusListener)
     .setForceDucking(false)
     .setWillPauseWhenDucked(false)
     .setAcceptsDelayedFocusGain(true)
     .build();
```  
在提出請求時，處理 AUDIOFOCUS_REQUEST_DELAYED 響應：  
```java
int delayedFocusRequestResults = mAudioManager.requestAudioFocus(mDelayedFocusRequest);
if (delayedFocusRequestResults == AudioManager.AUDIOFOCUS_REQUEST_GRANTED) {
    // 開始音頻播放
    return;
}
if (delayedFocusRequestResults == AudioManager.AUDIOFOCUS_REQUEST_DELAYED) {
     // 音頻播放延遲到音頻焦點監聽器
     return;
}
```  
當請求被延遲時，焦點監聽器處理焦點變化：  
```java
private final class MediaWithDelayedFocusListener implements OnAudioFocusChangeListener {
       @Override
       public void onAudioFocusChange(int focusChange) {
           synchronized (mLock) {
               switch (focusChange) {
                   case AudioManager.AUDIOFOCUS_GAIN:
                       … // 開始焦點播放
                   case AudioManager.AUDIOFOCUS_LOSS_TRANSIENT:
                       … // 臨時暫停媒體
                   case AudioManager.AUDIOFOCUS_LOSS:
                       … // 停止媒體
```

**解釋：**  
這段提供了如何在程式碼中實現可延遲音頻焦點請求的具體示例。應用程式通過 AudioFocusRequest.Builder 構建請求，設置 setAcceptsDelayedFocusGain(true) 表示接受延遲焦點。請求時，系統可能返回 AUDIOFOCUS_REQUEST_GRANTED（立即獲得焦點）或 AUDIOFOCUS_REQUEST_DELAYED（延遲）。焦點監聽器（OnAudioFocusChangeListener）負責處理焦點狀態變化，例如開始播放（AUDIOFOCUS_GAIN）、臨時暫停（AUDIOFOCUS_LOSS_TRANSIENT）或停止播放（AUDIOFOCUS_LOSS）。這段代碼展示了如何讓應用程式適應延遲焦點的場景。

---

### 系統強制淡化

**原文：**  
Android 15 introduces system-enforced audio fade in AAOS. In Android, the audio focus isn't enforced by the system. So, while app developers are encouraged to comply with the audio focus guidelines, if an app continues to play loudly even after losing audio focus, the system can't prevent it.  
In safety-critical automotive environments, adherence to audio focus is vital for minimizing driver distraction. With this feature, the audio framework now automatically fades apps that lose audio focus, for a more controlled and predictable audio experience.  
This enhancement helps ensure that apps adhere to audio focus loss decision as defined by the interaction matrix, preventing audio playback conflicts.

**翻譯：**  
Android 15 在 AAOS 中引入了系統強制音頻淡化。在 Android 中，音頻焦點不由系統強制執行。因此，雖然鼓勵應用程式開發者遵守音頻焦點指南，但如果應用程式在失去音頻焦點後仍大聲播放，系統無法阻止。  
在安全關鍵的汽車環境中，遵守音頻焦點對於減少駕駛員分心至關重要。有了這項功能，音頻框架現在會自動淡化失去音頻焦點的應用程式，以提供更可控和可預測的音頻體驗。  
此增強功能有助於確保應用程式遵守交互矩陣定義的音頻焦點丟失決策，防止音頻播放衝突。

**解釋：**  
Android 15 引入了系統強制音頻淡化功能，解決了應用程式在失去焦點後仍繼續播放的問題。在汽車環境中，這種行為可能導致駕駛員分心，因此系統會自動降低失去焦點的應用程式的音量（淡化），確保高優先級音頻（如導航或通話）清晰可聞。這增強了音頻焦點交互矩陣的執行效果，提供更安全和一致的音頻體驗。

---

### 高階設計

**原文：**  
The following figure shows the high-level design and support for focus loss feature in the cars:  
*High-level design for system-enforced fade feature*  
*Figure 2. High-level design for system-enforced fade feature.*  
- **Targeted fading**: System enforcement of fading in Android 15 is specifically designed for situations where an app loses audio focus but continues to play audio.  
- **Fade-out mechanism**: When an app loses audio focus to a new requesting app:  
  - The audio framework automatically fades out the losing app audio.  
  - Following the fade-out, the audio stream is silenced by the system.  
  - The app then receives an audio focus loss notification.  
  - Misbehaving apps are silenced until they regain the audio focus.  
- The default logic is to fade in the apps that are faded out after 2 seconds. However, OEMs can configure this to any timeout value.  
- The audio framework uses the OEM configs for both fade-out and fade-in operations.  
- **OEM configuration file**: Android 15 includes a new configuration file, car_audio_fade_configuration.xml:  
  - This file allows OEMs to define criteria for when the system's audio focus enforcement is applied to a losing app.  
  - The audio framework enforces fade-out and silencing only if the losing app matches the OEM-defined rules in this XML file.  
  - This provides a mechanism for OEMs to customize the feature's behavior based on app characteristics or audio usage types.  
- **Feature control with RRO**: A new runtime resource overlay (RRO) feature flag, audioUseFadeManagerConfiguration, has been introduced to enable or disable this feature:  
  - The feature is disabled by default.  
  - To activate system-enforced audio focus loss, OEMs must set this flag to true.  
  - Although car audio framework expects valid fade config definitions when the flag is enabled, the absence of such definitions does not automatically result in a fatal exception.  
  - All apps of fade configs must have matching fade definitions. It's a fatal error to call out a fade config (by its name) as part of car audio configuration without providing a valid definition.  
  - When the flag is disabled, all fade config definitions and any config references are ignored.

**翻譯：**  
以下圖表展示了汽車中焦點丟失功能的高階設計和支援：  
*系統強制淡化功能的高階設計*  
*圖 2. 系統強制淡化功能的高階設計。*  
- **目標淡化**：Android 15 的系統強制淡化專為應用程式失去音頻焦點但繼續播放音頻的情況設計。  
- **淡出機制**：當應用程式因新請求應用程式而失去音頻焦點時：  
  - 音頻框架自動淡出失去焦點的應用程式音頻。  
  - 淡出後，系統將音頻流靜音。  
  - 應用程式隨後收到音頻焦點丟失通知。  
  - 行為異常的應用程式將被靜音，直到重新獲得音頻焦點。  
- 默認邏輯是在 2 秒後淡入被淡出的應用程式。然而，OEM 可以將此配置為任意超時值。  
- 音頻框架使用 OEM 配置進行淡出和淡入操作。  
- **OEM 配置文件**：Android 15 引入了新的配置文件 car_audio_fade_configuration.xml：  
  - 此文件允許 OEM 定義系統音頻焦點強制執行的標準，適用於失去焦點的應用程式。  
  - 僅當失去焦點的應用程式符合此 XML 文件中 OEM 定義的規則時，音頻框架才會執行淡出和靜音。  
  - 這為 OEM 提供了一個根據應用程式特性或音頻使用類型自定義功能的機制。  
- **使用 RRO 控制功能**：引入了新的運行時資源覆蓋（RRO）功能標誌 audioUseFadeManagerConfiguration，用於啟用或禁用此功能：  
  - 該功能默認禁用。  
  - 要啟用系統強制音頻焦點丟失，OEM 必須將此標誌設置為 true。  
  - 雖然車輛音頻框架在啟用標誌時期望有效的淡化配置定義，但缺少此類定義不會自動導致致命異常。  
  - 所有淡化配置的應用程式必須具有匹配的淡化定義。在車輛音頻配置中調用淡化配置（按名稱）而未提供有效定義是致命錯誤。  
  - 當標誌禁用時，所有淡化配置定義和任何配置引用都將被忽略。

**解釋：**  
這段詳細介紹了 Android 15 中系統強制淡化功能的高階設計。該功能針對失去焦點但繼續播放的應用程式，通過自動淡出和靜音來確保音頻優先級。淡出後，系統會在 2 秒後（或 OEM 自定義的時間）恢復音量。OEM 可以通過 car_audio_fade_configuration.xml 文件自定義淡化行為，指定哪些應用程式或音頻類型需要淡化。RRO 標誌 audioUseFadeManagerConfiguration 控制該功能的啟用與否，默認禁用以避免不必要的系統干預。配置文件和標誌為 OEM 提供了靈活性，確保功能適應不同車輛和市場需求。

---

### 淡化管理器配置

**原文：**  
The Android 15 audio framework introduces a unified FadeManagerConfiguration to provide OEMs with granular control over audio fading behavior. This framework is illustrated in Figure 3:  
*Fade manager configuration*  
*Figure 3. Fade manager configuration.*  
This configuration includes:  
- **Fade transition properties**: Settings for both fade-out and fade-in.  
  - Can be defined with specific audio usages or attributes.  
  - Allows for custom duration settings.  
  - These settings are used to construct VolumeShaper.Configuration.  
- **Fading policies**: Rules governing when fading occurs.  
  - A global toggle to enable or disable fading.  
  - A configurable list of fadeable audio usages (eligible for fade-out upon losing focus).  
  - Exclusion lists (unfadeable) prevent critical or designated audio sources from being faded. These lists can be based on:  
    - Content types  
    - Audio attributes  
    - App UIDs (can be set during runtime only)  

**翻譯：**  
Android 15 音頻框架引入了統一的 FadeManagerConfiguration，為 OEM 提供對音頻淡化行為的精細控制。此框架如圖 3 所示：  
*淡化管理器配置*  
*圖 3. 淡化管理器配置。*  
此配置包括：  
- **淡化過渡屬性**：淡出和淡入的設置。  
  - 可針對特定音頻用途或屬性定義。  
  - 允許自定義持續時間設置。  
  - 這些設置用於構建 VolumeShaper.Configuration。  
- **淡化策略**：控制何時進行淡化的規則。  
  - 全局開關啟用或禁用淡化。  
  - 可配置的可淡化音頻用途列表（在失去焦點時可進行淡出）。  
  - 排除列表（不可淡化）防止關鍵或指定音頻來源被淡化。這些列表可以基於：  
    - 內容類型  
    - 音頻屬性  
    - 應用程式 UID（僅在運行時設置）

**解釋：**  
這段描述了 Android 15 引入的 FadeManagerConfiguration，該配置為 OEM 提供了對音頻淡化行為的精細控制。淡化過渡屬性允許設置淡出和淡入的具體參數（如持續時間），並與 VolumeShaper.Configuration 結合使用。淡化策略則定義了哪些音頻可以淡化（例如媒體音頻），以及哪些關鍵音頻（如安全警告）不能淡化。排除列表可根據內容類型、音頻屬性或應用程式 UID 動態設置，確保靈活性和安全性。

---

### OEM 配置

**原文：**  
**Car audio fade configuration XML file**  
Android 15 introduces a new configuration file, car_audio_fade_configuration.xml, enabling extensive OEM customization of audio fade-out behavior during focus loss.  
- This XML file allows for the definition of multiple distinct fade configurations, each requiring a unique name for cross-referencing within car_audio_configuration.xml.  
- These configurations can be flexibly applied across different audio zones and zone configs.  
- Notably, each fade configuration solely accepts duration values in milliseconds, which the system then uses to internally generate the corresponding VolumeShaper.Configuration.  
For practical implementation guidance, consult the example configurations provided for the emulator located at device/generic/car/emulator/audio/car_audio_fade_configuration.xml.  

**翻譯：**  
**車輛音頻淡化配置文件**  
Android 15 引入了新的配置文件 car_audio_fade_configuration.xml，使 OEM 能夠廣泛自定義焦點丟失時的音頻淡出行為。  
- 此 XML 文件允許定義多個不同的淡化配置，每個配置需要一個唯一的名稱，以便在 car_audio_configuration.xml 中進行交叉引用。  
- 這些配置可以靈活應用於不同的音頻區域和區域配置。  
- 值得注意的是，每個淡化配置僅接受以毫秒為單位的持續時間值，系統隨後使用這些值內部生成對應的 VolumeShaper.Configuration。  
有關實際實現指導，請參閱模擬器提供的示例配置，位於 device/generic/car/emulator/audio/car_audio_fade_configuration.xml。

**解釋：**  
這段介紹了 car_audio_fade_configuration.xml 文件，該文件允許 OEM 自定義音頻淡出行為。每個淡化配置需要唯一的名稱，並可在不同音頻區域應用。配置僅接受毫秒為單位的持續時間，系統根據這些值生成 VolumeShaper.Configuration 用於淡化控制。模擬器中的示例文件為 OEM 提供了實現參考，幫助他們根據車輛需求配置淡化行為。

---

**原文：**  
**Car audio configuration XML file**  
Android 15 introduces an updated car_audio_configuration.xml file, now at version 4, which incorporates new applyFadeConfigs and fadeConfig tags. The applyFadeConfigs tag can contain multiple fadeConfig definitions, allowing for flexible fade configuration. Each definition:  
- Must include one default fadeConfig designated with isDefault = true.  
- Can include several transient fadeConfig definitions. These transient configurations are applied specifically during audio focus loss interactions, and only when the audio focus gaining app matches the criteria defined within the transient config.  
For practical implementation guidance, consult the example configurations provided for the emulator located at device/generic/car/emulator/audio/car_audio_configuration.xml.

**翻譯：**  
**車輛音頻配置文件**  
Android 15 引入了更新的 car_audio_configuration.xml 文件，現為版本 4，新增了 applyFadeConfigs 和 fadeConfig 標籤。applyFadeConfigs 標籤可包含多個 fadeConfig 定義，允許靈活的淡化配置。每個定義：  
- 必須包含一個默認的 fadeConfig，指定為 isDefault = true。  
- 可包含多個臨時 fadeConfig 定義。這些臨時配置專門應用於音頻焦點丟失交互，且僅在獲得音頻焦點的應用程式符合臨時配置中定義的標準時應用。  
有關實際實現指導，請參閱模擬器提供的示例配置，位於 device/generic/car/emulator/audio/car_audio_configuration.xml。

**解釋：**  
這段描述了更新的 car_audio_configuration.xml 文件（版本 4），新增了 applyFadeConfigs 和 fadeConfig 標籤以支援淡化配置。文件必須包含一個默認淡化配置（isDefault = true），並可包含多個臨時配置，這些臨時配置僅在特定焦點交互時應用。模擬器中的示例文件為 OEM 提供了配置參考，幫助他們實現靈活的淡化策略。

---

**原文：**  
**OEM audio focus service extension**  
OEMs who implement a custom car audio focus service have the flexibility to configure audio fade settings by including them within OemCarAudioFocusResult. This can be achieved using the setAudioAttributesToCarAudioFadeConfigurationMap() builder method:  
```java
/** @see OemCarAudioFocusResult#getAudioAttributesToCarAudioFadeConfigurationMap() **/
@NonNull
public Builder setAudioAttributesToCarAudioFadeConfigurationMap(@NonNull
        Map<AudioAttributes, CarAudioFadeConfiguration> attrsToCarAudioFadeConfig) {
}
```  
Notably, OEMs can choose to use either preconfigured boot-time fade settings or dynamically apply configurations through their custom audio focus service, offering adaptable control.

**翻譯：**  
**OEM 音頻焦點服務擴展**  
實現自定義車輛音頻焦點服務的 OEM 可以通過在 OemCarAudioFocusResult 中包含音頻淡化設置來靈活配置。可以使用 setAudioAttributesToCarAudioFadeConfigurationMap() 構建器方法實現：  
```java
/** @see OemCarAudioFocusResult#getAudioAttributesToCarAudioFadeConfigurationMap() **/
@NonNull
public Builder setAudioAttributesToCarAudioFadeConfigurationMap(@NonNull
        Map<AudioAttributes, CarAudioFadeConfiguration> attrsToCarAudioFadeConfig) {
}
```  
值得注意的是，OEM 可以選擇使用預配置的開機時淡化設置，或通過其自定義音頻焦點服務動態應用配置，提供適應性控制。

**解釋：**  
這段描述了 OEM 如何通過自定義音頻焦點服務擴展淡化功能。使用 setAudioAttributesToCarAudioFadeConfigurationMap() 方法，OEM 可以將特定的音頻屬性映射到淡化配置，從而在運行時動態控制淡化行為。OEM 可以選擇靜態配置（開機時設置）或動態配置，提供了高度靈活性以滿足不同車輛和市場需求。

---

### 序列圖

**原文：**  
This sequence diagram illustrates the behavior following audio focus grant to App2 and the subsequent loss of audio focus by App1:  
- Upon the car audio service dispatching audio focus loss to App1, the playback from App1 player undergoes a fade-out as defined by the active FadeManagerConfigurations.  
- Once the fade-out operation is complete, App1 receives the standard audio focus loss callback.  
- Optionally, the audio for App1 can be faded back in after a configurable duration. OEMs have the flexibility to set this duration through Builder#setFadeInDurationForUsage(int, long) according to their specific product requirements.  
*Sequence diagram for car audio fade feature*  
*Figure 4. Sequence diagram for car audio fade feature.*

**翻譯：**  
此序列圖展示了向 App2 授予音頻焦點以及 App1 隨後失去音頻焦點的行為：  
- 當車輛音頻服務向 App1 分派音頻焦點丟失時，App1 播放器的播放會根據活動的 FadeManagerConfigurations 進行淡出。  
- 淡出操作完成後，App1 收到標準的音頻焦點丟失回調。  
- 可選地，App1 的音頻可以在可配置的持續時間後淡入。OEM 可以通過 Builder#setFadeInDurationForUsage(int, long) 根據其特定產品要求設置此持續時間。  
*車輛音頻淡化功能的序列圖*  
*圖 4. 車輛音頻淡化功能的序列圖。*

**解釋：**  
這段通過序列圖說明了音頻焦點轉移的過程。當 App2 獲得焦點時，App1 的音頻會根據 FadeManagerConfigurations 淡出，隨後收到焦點丟失通知。淡入時間可由 OEM 通過 Builder#setFadeInDurationForUsage 配置，允許根據產品需求自定義淡入行為。序列圖（圖 4）提供了視覺化的流程展示，幫助理解淡化功能的執行邏輯。

---

### 多區域焦點管理

**原文：**  
For vehicles with multiple audio zones, audio focus is managed independently for each zone. As such, a request to one zone doesn't take into account what holds focus in other zones, nor does it cause focus holders in other zones to lose focus. With this, the main cabin's focus can be managed separately from a rear seat entertainment system, thereby not interrupting the audio playback in one zone by changes made in focus to another.

**翻譯：**  
對於具有多個音頻區域的車輛，每個區域的音頻焦點獨立管理。因此，對一個區域的焦點請求不會考慮其他區域的焦點持有者，也不會導致其他區域的焦點持有者失去焦點。通過這種方式，主艙的焦點可以與後座娛樂系統的焦點分開管理，從而不會因一個區域的焦點變化而中斷另一區域的音頻播放。

**解釋：**  
這段描述了多區域音頻焦點管理，適用於具有多個音頻區域的車輛（例如主艙和後座娛樂系統）。每個區域的焦點獨立處理，確保一個區域的音頻焦點請求不會影響其他區域。這允許主艙播放導航語音，而後座乘客繼續觀看電影或聽音樂，互不干擾，提高了車內音頻體驗的靈活性。

---

**原文：**  
**Request audio from multiple zones concurrently**  
If an app wants to play audio in multiple zones concurrently, it must request focus for each zone by including AUDIOFOCUS_EXTRA_REQUEST_ZONE_ID in the bundle:  
```java
//Create attribute with bundle and AUDIOFOCUS_EXTRA_REQUEST_ZONE_ID
Bundle bundle = new Bundle();
bundle.putInt(CarAudioManager.AUDIOFOCUS_EXTRA_REQUEST_ZONE_ID,
               zoneId);

AudioAttributes attributesWithZone = new AudioAttributes.Builder()
     .setUsage(AudioAttributes.USAGE_MEDIA)
     .addBundle(bundle)
     .build();

//Create focus request using built attributesWithZone
```  
This bundle parameter allows the requestor to override the automatic audio zone mappings to instead use the specified zone ID. Therefore, an app could issue separate requests for different audio zones.

**翻譯：**  
**同時從多個區域請求音頻**  
如果應用程式希望在多個區域同時播放音頻，必須為每個區域請求焦點，方法是在 bundle 中包含 AUDIOFOCUS_EXTRA_REQUEST_ZONE_ID：  
```java
//創建帶有 bundle 和 AUDIOFOCUS_EXTRA_REQUEST_ZONE_ID 的屬性
Bundle bundle = new Bundle();
bundle.putInt(CarAudioManager.AUDIOFOCUS_EXTRA_REQUEST_ZONE_ID,
               zoneId);

AudioAttributes attributesWithZone = new AudioAttributes.Builder()
     .setUsage(AudioAttributes.USAGE_MEDIA)
     .addBundle(bundle)
     .build();

//使用構建的 attributesWithZone 創建焦點請求
```  
此 bundle 參數允許請求者覆蓋自動音頻區域映射，改用指定的區域 ID。因此，應用程式可以為不同的音頻區域發出單獨的請求。

**解釋：**  
這段提供了在多區域車輛中同時請求音頻焦點的程式碼示例。應用程式通過在 AudioAttributes 中添加 AUDIOFOCUS_EXTRA_REQUEST_ZONE_ID 指定目標區域 ID，從而為每個音頻區域單獨請求焦點。這允許應用程式靈活控制音頻在不同區域的播放，例如在主艙和後座播放不同的內容。

---

### HAL 音頻焦點

**原文：**  
Starting in Android 11, the HAL is enabled to request focus on behalf of external streams. While optional, use of these APIs is highly encouraged to enable external sounds to be optimal participants in the Android ecosystem and to provide a seamless user experience.  
The HAL makes the final determination around which sounds should get priority. To this extent, emergency and safety critical sounds should be played regardless of whether or not the HAL is granted audio focus and should continue to be played as appropriate even if the HAL loses audio focus. The same is true for any sounds required by government regulations.  
The HAL should proactively mute Android streams as appropriate when playing emergency or safety-critical sounds to ensure they are heard clearly.

**翻譯：**  
從 Android 11 開始，HAL 可以代表外部流請求焦點。雖然這是可選的，但強烈鼓勵使用這些 API，以使外部聲音成為 Android 生態系統的最佳參與者，並提供無縫的用戶體驗。  
HAL 對哪些聲音應優先播放做出最終決定。因此，緊急和安全關鍵聲音應無論是否授予 HAL 音頻焦點都播放，並在 HAL 失去音頻焦點時繼續適當播放。政府法規要求的任何聲音也是如此。  
HAL 應在播放緊急或安全關鍵聲音時適當主動靜音 Android 流，以確保這些聲音被清楚聽到。

**解釋：**  
這段描述了 HAL（硬體抽象層）在 Android 11 中新增的功能，允許其代表外部音頻流（如安全警告音）請求音頻焦點。雖然這是可選的，但使用這些 API 有助於外部聲音與 Android 系統更好整合。HAL 負責決定音頻優先級，確保緊急和安全相關聲音（如警報）始終播放，即使未獲得焦點。HAL 還應在播放這些聲音時主動靜音 Android 音頻流，以確保關鍵聲音的清晰度。

---

### AudioControl@2.0

**原文：**  
Version 2.0 of AudioControl HAL introduces these new APIs:  
- **IAudioControl#registerFocusListener**: Registers an instance of IFocusListener with the AudioControl HAL. This listener enables the HAL to request and abandon audio focus. The HAL provides an ICloseHandle instance to be used by Android to unregister the listener.  
- **IAudioControl#onAudioFocusChange**: Notifies the HAL of changes in status to focus requests made by the HAL through the IFocusListener, including responses to initial focus requests.  
- **IFocusListener#requestAudioFocus**: Requests focus on behalf of the HAL for a specified usage, zone Id, and focus gain type.  
- **IFocusListener#abandonAudioFocus**: Abandons existing HAL focus requests for the specified usage and zone Id.  
The HAL can have multiple focus requests at the same time, but is limited to one request per usage and zone Id pairing. Android assumes the HAL immediately starts playing sounds for a usage once a request has been made and continues to do so until it abandons focus.  
Other than registerFocusListener, these requests are oneway to ensure that Android doesn't delay the HAL while a focus request is processed. The HAL should not wait to gain focus before playing safety-critical sounds. It's optional for the HAL to listen for and respond to changes in audio focus through IAudioControl#onAudioFocusChange.

**翻譯：**  
AudioControl HAL 版本 2.0 引入了以下新 API：  
- **IAudioControl#registerFocusListener**：向 AudioControl HAL 註冊一個 IFocusListener 實例。此監聽器使 HAL 能夠請求和放棄音頻焦點。HAL 提供一個 ICloseHandle 實例，供 Android 用於取消註冊監聽器。  
- **IAudioControl#onAudioFocusChange**：通知 HAL 通過 IFocusListener 進行的焦點請求狀態變化，包括對初始焦點請求的響應。  
- **IFocusListener#requestAudioFocus**：代表 HAL 為指定的用途、區域 ID 和焦點增益類型請求焦點。  
- **IFocusListener#abandonAudioFocus**：放棄 HAL 對指定用途和區域 ID 的現有焦點請求。  
HAL 可以同時有多個焦點請求，但每個用途和區域 ID 組合限制為一個請求。Android 假設 HAL 在提出請求後立即開始播放該用途的聲音，並持續播放直到放棄焦點。  
除了 registerFocusListener 外，這些請求是單向的，以確保 Android 在處理焦點請求時不會延遲 HAL。HAL 不應在播放安全關鍵聲音前等待獲得焦點。HAL 通過 IAudioControl#onAudioFocusChange 監聽和響應音頻焦點變化是可選的。

**解釋：**  
這段介紹了 AudioControl HAL 2.0 版本的新 API，這些 API 增強了 HAL 與音頻焦點的交互能力。registerFocusListener 允許 HAL 註冊焦點監聽器，requestAudioFocus 和 abandonAudioFocus 用於請求和放棄焦點，onAudioFocusChange 則通知焦點狀態變化。這些 API 設計為單向（除了註冊），確保不延遲 HAL 的操作。HAL 可以同時為不同用途和區域請求焦點，但每個用途和區域組合僅限一個請求。對於安全關鍵聲音，HAL 不需要等待焦點即可播放，確保其優先級。

---

### OEM 車輛音頻焦點服務

**原文：**  
In Android 14, AAOS introduced the car OEM plugin services to enable configurability for some car components. For Car Audio Plugin Service, the plugin service allows for OEMs to manage focus requests intercepted by the car audio service. This gives OEMs more flexibility in terms of managing focus as required by rules and regulations. As such, audio focus interaction may differ between manufacturers, and from region to region. The basic premise for audio focus still holds, that apps should still request focus for better management audio to enhance user experience. In general, certain rules still apply for audio focus request by apps:  
- Without any standing, high priority audio focus (including a phone call, emergency alert, or safety notification) apps should be able to gain audio focus either transiently or permanently.  
- While a media focus is active:  
  - Apps requesting call usage focus should be able to receive the call either concurrently or exclusively.  
  - Apps requesting navigation usage focus should be able to receive navigation focus either concurrently or exclusively.  
  - Apps requesting assistant usage focus should be able to receive usage focus either concurrently or exclusively.  
- While standing high priority audio focus (including a phone call, emergency alert, or safety notification) apps are active, any incoming delayed audio focus request should be granted or delayed as needed.  
While these suggestions are not exhaustive, they can help apps requesting focus to obtain focus if no active high priority sounds exist. Even while high priority sounds are active, delayed focus requests should still be respected and should be able to gain focus when the high priority sound stops.

**翻譯：**  
在 Android 14 中，AAOS 引入了車輛 OEM 插件服務，以實現某些車輛組件的可配置性。對於車輛音頻插件服務，該插件服務允許 OEM 管理由車輛音頻服務攔截的焦點請求。這為 OEM 提供了更大的靈活性，以根據規則和法規管理焦點。因此，音頻焦點交互可能因製造商和地區而異。音頻焦點的基本前提仍然成立，即應用程式應請求焦點以更好地管理音頻，提升用戶體驗。一般來說，應用程式的音頻焦點請求適用以下規則：  
- 在沒有高優先級音頻焦點（包括電話通話、緊急警報或安全通知）的情況下，應用程式應能夠臨時或永久獲得音頻焦點。  
- 當媒體焦點處於活動狀態時：  
  - 請求通話用途焦點的應用程式應能夠並行或獨占接收通話。  
  - 請求導航用途焦點的應用程式應能夠並行或獨占接收導航焦點。  
  - 請求助理用途焦點的應用程式應能夠並行或獨占接收用途焦點。  
- 當高優先級音頻焦點（包括電話通話、緊急警報或安全通知）的應用程式處於活動狀態時，任何新進的可延遲音頻焦點請求應根據需要授予或延遲。  
雖然這些建議並非詳盡無遺，但它們有助於應用程式在無高優先級聲音活動時獲得焦點。即使在高優先級聲音活動時，可延遲焦點請求也應受到尊重，並在高優先級聲音停止時能夠獲得焦點。

**解釋：**  
這段描述了 Android 14 引入的車輛 OEM 插件服務，允許 OEM 自定義音頻焦點管理。OEM 可以通過插件服務攔截和處理焦點請求，根據法規或市場需求調整交互行為。儘管如此，應用程式仍應遵循音頻焦點的基本原則，請求焦點以優化音頻管理。規則確保在無高優先級音頻（如通話或警報）時，應用程式可以獲得焦點；在高優先級音頻存在時，可延遲請求會被適當處理。這為 OEM 和應用程式開發者提供了靈活性和一致性。

---

### 總結
這篇文章詳細介紹了 Android Automotive OS (AAOS) 中的音頻焦點管理機制，包括獨占、拒絕和並行三種交互模式，並通過交互矩陣定義不同音頻上下文的行為。Android 11 引入了可延遲焦點和外部流焦點請求功能，Android 15 則新增了系統強制淡化功能，確保失去焦點的應用程式自動淡出，減少駕駛員分心。OEM 可以通過 car_audio_fade_configuration.xml 和 car_audio_configuration.xml 文件自定義淡化行為，並利用插件服務動態管理焦點。多區域焦點管理和 HAL API 進一步增強了系統靈活性，確保安全關鍵聲音的優先級和無縫用戶體驗。
