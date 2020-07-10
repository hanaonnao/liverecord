# 寫在開頭
  * 此Forked或許會較與原版有所改動，並且不會及時更新bug，如有需求請各位移步原作者[lovezzzxxx/liverecord](https://github.com/lovezzzxxx/liverecord)

# 功能介紹
  * record.sh為自動錄播腳本。支援youtube頻道、twitcast頻道、twitch頻道、openrec頻道、niconico生放送、niconico社區、niconico頻道（支援登錄niconico帳號進行錄製）、mirrativ頻道、reality頻道、17live頻道、chaturbate頻道、bilibili頻道、streamlink支援的直播網址、ffmpeg支援的m3u8位址。bilibili錄製支援在上述頻道有直播時不進行錄製，從而簡單的排除轉播的錄製；支援使用代理錄製bilibili直播。支持定時分段。支持rclone上傳、onedrive上傳(含世紀互聯版)、百度雲上傳；支持可指定次數的上傳出錯重試；支援根據上傳結果選擇是否保留本地檔。
  
  * install.sh為一鍵安裝腳本。目前僅在ubuntu18.04與19.10系統測試過，理論上較新的linux系統應該都可以使用(centos系統應該把apt替換為yum就行了)。

  * record_twitcast.py為可選，是一個可以錄製websocket的精簡腳本。因為twitcast分別提供了基於h5與websocket的流，但部分直播的最高清晰度僅能通過websocket獲取，而ffmpeg並不能支援websocket，所以提供一個可以錄製websocket的腳本。也可單獨使用，方法為`python3 record_twitcast.py "ws或wss網址" "輸出檔目錄"`。  

  * download.sh與錄製功能無關，是一個完全獨立的小腳本。本質是輪詢檢測youtube頻道的直播和視頻頁第一頁，產生一個youtube視頻清單，並對清單中視頻的錄影、封面圖、標題與簡介進行備份。支援嘗試在下播後立即下載直播錄影，因為直播結束後到刪檔前仍有一段時間可以下載完整的錄影，而且一旦開始下載則下載過程不受刪檔影響。支持設置觸發下播後下載的最長直播時長，因為大於兩個小時的直播需要等待壓制下載的錄影可能不全。支援在下載到未壓制完成的錄影的情況下等待壓制完成後建立新的下載，確保下載到壓制完成的未刪檔視頻。具體用法可直接不加參數運行`./download.sh`查看。（另外由於腳本的工作狀態完全取決於視頻清單的內容，所以直接指定或修改視頻清單大概會有奇怪的作用）

感謝[live-stream-recorder](https://github.com/printempw/live-stream-recorder)、[GiGaFotress/Vtuber-recorder](https://github.com/GiGaFotress/Vtuber-recorder)  

# 安裝方法
### 一鍵安裝
`curl https://raw.githubusercontent.com/hanaonnao/liverecord/master/install.sh | bash`  
  * 一鍵腳本將會自動安裝下列所有環境依賴， __同時會覆蓋安裝go環境並添加一些環境變數__ ，如果有需要可以注釋掉相應的命令或者手動安裝環境依賴。其中record.sh和record_twitcast.py會保存于運行時命令列所在目錄的record資料夾下，livedl會保存于運行時命令列所在目錄的livedl資料夾下  
  * __一鍵腳本安裝後腳本調用方式應為`record/record.sh`而非下文示例中的`./record.sh`__  
  * 一鍵腳本運行結束後會提示仍需要手動進行的操作，如更新環境變數和登錄網盤帳號  

### 環境依賴
此處列舉自動錄播腳本運行所需的所有程式，如果一鍵腳本安裝失敗或希望手動安裝環境可以參考  
  * 自動錄播腳本，安裝方法為`mkdir record ; wget -O "record/record.sh" "https://github.com/hanaonnao/liverecord/raw/master/record.sh" ; chmod +x record/record.sh`
  * [ffmpeg](https://github.com/FFmpeg/FFmpeg)，安裝方法為`sudo apt install ffmpeg`。否則無法使用除了youtube、twitcast、twitcastpy、nicolv、nicoco、nicoch、bilibili、bilibiliproxy以外的參數。
  * [streamlink](https://github.com/streamlink/streamlink)(基於python3)，安裝方法為`pip3 install streamlink`。否則無法使用youtube、youtubeffmpeg、twitch、streamlink、17live參數。
  * [livedl](https://github.com/hanaonnao/livedl)(基於go，原項目[himananiito/livedl](https://github.com/himananiito/livedl)已失效)，具體編譯安裝方法可以參考作者的說明， __請將編譯完成的livedl檔放置于運行時命令列所在目錄的livedl/資料夾內__ 。否則無法使用twitcast、nicolv、nicoco、nicoch參數。
  * [record_twitcast.py文件](https://github.com/lovezzzxxx/liverecord/blob/master/record_twitcast.py)(基於python3 websocket庫)，安裝方法為`mkdir record ; wget -O "record/record_twitcast.py" "https://github.com/lovezzzxxx/liverecord/raw/master/record_twitcast.py" ; chmod +x "record/record_twitcast.py"`， __如果手動安裝請將record_twitcast.py檔放置于運行時命令列所在目錄的record/資料夾內並給予可執行許可權即可__ 。否則無法使用twitcastpy參數。
  * [you-get](https://github.com/soimort/you-get)(基於python3)，安裝方法為`pip3 install you-get`。否則無法使用bilibili、bilibiliproxy參數。
 安裝方法為`pip3 install you-get`
  * [rclone](https://github.com/rclone/rclone)(支援onedrive、dropbox、googledrive等多種網盤，需登錄後使用)，安裝方法為`curl https://rclone.org/install.sh | sudo bash`，配置方法為`rclone config`後根據說明進行。否則無法使用rclone參數上傳。
  * [OneDriveUploader](https://github.com/MoeClub/OneList/tree/master/OneDriveUploader)(支持包括世紀互聯版在內的各種onedrive網盤，需登錄後使用)，安裝和登錄方法可以參考[Rat's Blog](https://www.moerats.com/archives/1006)。否則無法使用onedrive參數上傳。
  * [BaiduPCS-Go](https://github.com/felixonmars/BaiduPCS-Go)(給予go，支援百度雲網盤，需登錄後使用，原專案[iikira/BaiduPCS-Go](https://github.com/iikira/BaiduPCS-Go)已失效)，安裝和登錄方法可以參考作者的說明。否則無法使用baidupan參數上傳。

# 使用方法
### 方法
`./record.sh youtube|youtubeffmpeg|twitcast|twitcastffmpeg|twitcastpy|twitch|openrec|nicolv[:用戶名,密碼]|nicoco[:用戶名,密碼]|nicoch[:用戶名,密碼]|mirrativ|reality|17live|chaturbate|bilibili|bilibiliproxy[,代理ip:代理埠]|streamlink|m3u8 頻道號碼 [best|其他清晰度] [loop|once|視頻分段時間] [10,10,1|迴圈檢測間隔,最短錄製間隔,錄製開始所需連續檢測開播次數] [record_video/other|其他本地目錄] [nobackup|rclone:網盤名稱:|onedrive|baidupan[重試次數][keep|del]] [noexcept|排除轉播的youtube頻道號碼] [noexcept|排除轉播的twitcast頻道號碼] [noexcept|排除轉播的twitch頻道號碼] [noexcept|排除轉播的openrec頻道號碼] [noexcept|排除轉播的nicolv頻道號碼] [noexcept|排除轉播的nicoco頻道號碼] [noexcept|排除轉播的nicoch頻道號碼] [noexcept|排除轉播的mirrativ頻道號碼] [noexcept|排除轉播的reality頻道號碼] [noexcept|排除轉播的17live頻道號碼]  [noexcept|排除轉播的chaturbate頻道號碼] [noexcept|排除轉播的streamlink支援的頻道網址]`

### 示例
  * 使用默認參數錄製https://www.youtube.com/channel/UCWCc8tO-uUl_7SJXIKJACMw   
`./record.sh youtube "UCWCc8tO-uUl_7SJXIKJACMw"`  

  * 使用ffmpeg錄製https://www.youtube.com/channel/UCWCc8tO-uUl_7SJXIKJACMw ，依次獲取1080p 720p 480p 360p worst中第一個可用的清晰度，在檢測到直播並進行一次錄製後終止，間隔30秒檢測，錄影保存於record_video/mea資料夾中並在錄製完成後自動上傳到rclone中名稱為vps的網盤和百度雲網盤的相同路徑 如果出錯則重試最多三次 上傳結束後根據上傳情況刪除本地錄影，如果上傳失敗則會保留本地錄影  
`./record.sh youtubeffmpeg "UCWCc8tO-uUl_7SJXIKJACMw" 1080p,720p,480p,360p,worst once 30 "record_video/mea" rclone:vps:baidupan3`  

  * 使用ffmpeg錄製https://ch.nicovideo.jp/kurorin2525 ，直接獲取最佳的清晰度，重複檢測到直播並進行錄製，間隔30秒檢測，每次錄製從開始到結束最短間隔10秒，連續檢測開播次數為5才開始錄製，錄影保存於record_video/kurorin_nico資料夾中並在錄製完成後自動上傳到onedrive的相同路徑 如果出錯則重試最多三次 上傳結束後根據上傳情況刪除本地錄影，如果上傳失敗則會保留本地錄影  
`./record.sh nicoch:nico登入賬號,nico登入密碼 kurorin2525 best loop 30,10,5 record_video/kurorin_nico onedrive3`  

  * 後臺運行，使用代理伺服器127.0.0.1:1080錄製https://live.bilibili.com/12235923 ，最高清晰度，迴圈檢測並在錄製進行7200秒時分段，間隔30秒檢測 每次錄製從開始到結束最短間隔5秒，錄影保存於record_video/mea資料夾中並在錄製完成後自動上傳到rclone中名稱為vps的網盤和onedrive和百度雲網盤的相同路徑 如果出錯則重試最多三次 上傳完成後無論成功與否都保留本地錄影，在https://www.youtube.com/channel/UCWCc8tO-uUl_7SJXIKJACMw https://twitcasting.tv/kaguramea_vov 有直播時不進行錄製，log記錄保存於mea_bilibili.log文件  
`nohup ./record.sh bilibiliproxy,127.0.0.1:1080 "12235923" best 7200 30,5 "record_video/mea_bilibili" rclone:vps:onedrivebaidupan3keep "UCWCc8tO-uUl_7SJXIKJACMw" "kaguramea_vov" > mea_bilibili.log &`  


### 參數說明

  * 必選參數，選擇錄製方式與相應頻道號碼  

網站|第一個參數|第二個參數|說明|注意事項
:---|:---|:---|:---|:---
youtube|`youtube`、`youtubeffmpeg`|`個人主頁網址中的ID部分`(如UCWCc8tO-uUl_7SJXIKJACMw)|youtubeffmpeg為使用ffmpeg進行錄製|請不要將第三個清晰度參數指定為best或1080p60及以上的解析度
twitcast|`twitcast`、`twitcastffmpeg`、`twitcastpy`|`個人主頁網址中的ID部分`(如kaguramea_vov)|twitcastffmpeg為使用ffmpeg進行錄製，twitcastpy為使用record_twitcast.py進行錄製|如果未安裝相應依賴，則僅能使用twitcast參數，無法錄製twitcast最高清晰度。 __請不要對同一場直播進行多個錄製，會導致檔命名問題__
niconico|`nicolv`、`nicoco`、`nicoch`|分別為`niconico生放送號碼`(如lv320447549)，`niconico社區號碼`(如co41030)，`niconico頻道號碼`(如macoto2525)|可以在後方添加`:用戶名,密碼`來登錄nico帳號進行錄製(如nicolv:user@mail.com,password)|如果未安裝相應依賴，則無法錄製niconico。 __請不要對同一場直播使用同一帳號進行多個錄製，會產生websocket連結衝突導致錄影卡頓或反復斷連__
bilibili|`bilibili`、`bilibiliproxy`|`直播間網址中的ID部分`(如12235923)|bilibiliproxy為通過代理進行錄製，可以直接在後方添加`,代理ip:代理埠`指定代理伺服器(如bilibiliproxy,127.0.0.1:1080)，也可以在腳本內相應部分添加代理獲取方法
其他網站| `twitch`、`openrec`、`mirrativ`、`reality`、`17live`、`chaturbate`|`個人主頁網址中的ID部分`，其中reality為頻道名稱(如果為部分名字則匹配含有這些文字的其中一個頻道)或vlive_id(獲取方法可於腳本內查找)|其中twitch使用streamlink檢測直播狀態，系統佔用較高||
其他|`streamlink`、`m3u8`|`streamlink支持的個人主頁網址或直播網址`、`直播媒體流的m3u8網址`||

  * 可選參數， __需要補全中間的參數才能指定後續的參數__

參數|功能|預設值|其他可選值|說明
:---|:---|:---|:---|:---
第三個參數|清晰度|`best`|`清晰度1,清晰度2`，可以用,分隔來指定多個清晰度|僅支援streamlink含有的清晰度，將會依次獲取嘗試直到獲取第一個可用的清晰度
第四個參數|是否迴圈和錄製分段時間|`loop`|`once`或`分段秒數`|如果指定為once則會在檢測到直播並進行一次錄製後終止，如果指定為數位則會以loop模式進行錄製並在在錄製進行相應秒數時分段。 __注意分段時可能會有十秒左右的視頻缺失__
第五個參數|迴圈檢測間隔和最短錄製間隔和錄製開始所需連續檢測開播次數|`10,10,1,20`|`迴圈檢測間隔秒數,最短錄製間隔秒數,錄製開始所需連續檢測開播次數,每次檢測隨機增加時間的範圍`，如果不以,分隔則最短錄製間隔也為此值而錄製開始所需連續檢測開播次數為1|迴圈檢測間隔是指如果未檢測到直播，則等待相應時間進行下一次檢測；最短錄製間隔是指如果一次錄製結束後，如果距離錄製開始小於最短錄製間隔，則等待到最短錄製間隔進行下一次檢測；每次檢測隨機增加時間的範圍則是在檢測間隔的基礎上每次隨機增加幾秒時間，如果不指定則默認範圍是增加0-19秒。最短錄製間隔以及每次檢測隨機增加時間的範圍主要是為了防止檢測到直播但錄製出錯的情況，此時一次錄製結束如果立即進行下一次檢測可能會因為檢測過於頻繁導致被封禁IP或者導致高系統佔用，這種情況可能出現在網站改版等特殊時期，需要注意的是如果一次直播時間過短或者頻繁斷流也能觸發等待；錄製開始所需連續檢測開播次數是指需要連續檢測到相應次數的開播才會開始錄製，可以用於預防一些檢測到直播狀態實際卻並沒有直播的情況。
第六個參數|本地錄影存放目錄|`record_video/other`|`本地目錄`||
第七個參數|是否自動備份|`nobackup`|`rclone:網盤名稱:` + `onedrive` + `baidupan` + `重試次數` + `無/keep/del`，不需要空格直接連接在一起即可(如rclone1del或rclone:vps:onedrivebaidupan3keep)|其中前三項的rclone、onedrive、baidupan分別指上傳rclone相應名稱的網盤、OneDriveUploader登錄的onedrive網盤、BaiduPCS-Go登錄的百度雲網盤。第四項為重試次數，如果不指定則默認為嘗試一次。第五項為上傳完成後是否保留本地檔，如果不指定則上傳成功將刪除本地檔，上傳失敗將保留本地檔，keep參數為不論結果始終保留本地檔，del參數為不論結果始終刪除本地檔。如果因為偶發的檢測異常導致沒有直播時開始錄製，進而產生沒有相應錄影檔的log檔，腳本將會自動刪除這個沒有對應錄影檔的log檔
第八至十四個參數|bilibili的錄製需要排除的轉播|`noexcept`|`相應頻道號碼`，具體同第二個參數，順序分別為youtube、twitcast、twitch、openrec、nicolv、nicoco、nicoch、mirrativ、reality、17live、chaturbate、streamlink|僅bilibili錄製有效，檢測到相應頻道正在直播時不進行bilibili的錄製

# 可選擇設置

## Youtube可選功能 - Cookies.txt檔案
由於Youtube對於機器檢測的不確定性，在使用Youtube錄製時有一定的幾率會發生429 Too Many Requests的錯誤。  
通常發生這種錯誤是因為彈出了reCaptcha的驗證系統，__目前沒有官方描述檔稱__ 登入后就可以 __降低reCaptcha驗證系統出現的可能__，但是在出現了429錯誤后Youtube-DL只需要傳入登入Youtube后的Cookies.txt檔案即可正常進行下載Youtube的內容。
 
如果您出現了429 Too Many Requests的錯誤，您可以嘗試此方法。   
如果您有需求錄製成員限定的直播，您也可以嘗試此方法。  

### __如何生成Cookies.txt__ 

__準備__  
如果您使用Chrome抑或是使用基於Chromium內核的瀏覽器,您可以[點擊此處](https://chrome.google.com/webstore/detail/cookiestxt/njabckikapfpffapmjgojcnbfjonfjfg)安裝cookies.txt擴充功能。  
如果您使用FireFox瀏覽器,您可以[點擊此處](https://addons.mozilla.org/ja/firefox/addon/cookies-txt/)安裝cookies.txt擴充功能。  

__獲取__  
在安裝好擴充功能后，您需要打開[Youtube賬戶](https://www.youtube.com/account),然後使用擴充功能進行獲取Cookies.txt的操作。  
如果您使用的是Chrome抑或是使用基於Chromium內核的瀏覽器的cookies.txt擴充功能，請選擇 `download cookies for this tab`后的`click here`。  
如果您使用的是FireFox的cookies.txt擴充功能，請選擇 `Current Site`。  

__處理（可跳過)__  
您下載好后的的Cookies應當諸如以下的格式,並且將會有超過5個以上的Cookies的類型。  
```
.youtube.com	TRUE	/	TRUE	0000000000	VISITOR_INFO1_LIVE	xxx 
.youtube.com	TRUE	/	FALSE	0000000000	_ga	xxx 
.youtube.com	TRUE	/	FALSE	0000000000	PREF	xxx 
.youtube.com	TRUE	/	FALSE	0000000000	_gcl_au	xxx 
.youtube.com	TRUE	/	FALSE	0000000000	SID	xxx 
.youtube.com	TRUE	/	TRUE	0000000000	__Secure-3PSID	xxx
.youtube.com	TRUE	/	FALSE	0000000000	HSID	xxx
.youtube.com	TRUE	/	TRUE	0000000000	SSID	xxx
.youtube.com	TRUE	/	FALSE	0000000000	APISID	xxx
.youtube.com	TRUE	/	TRUE	0000000000	__Secure-HSID	xxx
.youtube.com	TRUE	/	TRUE	0000000000	__Secure-SSID	xxx
.youtube.com	TRUE	/	TRUE	0000000000	__Secure-APISID	xxx
.youtube.com	TRUE	/	TRUE	0000000000	__Secure-3PAPISID	xxx
.youtube.com	TRUE	/	TRUE	0000000000	LOGIN_INFO	xxx
.youtube.com	TRUE	/	TRUE	0	YSC	xxx
.youtube.com	TRUE	/	FALSE	0000000000	SIDCC	xxx
```
在這其中，[驗證Youtube登入](https://policies.google.com/technologies/types?hl=ja)只需要`SID`、`SSID`、`HSID`這三個Cookies類型。您可以只留下這三個類型的行數。  
請注意，如果您使用的是FireFox的cookies.txt擴充功能，您應當刪除`#HttpOnly_.`這部分內容。  

最終您的cookies.txt檔案的內容應該與下方類似。
```
.youtube.com	TRUE	/	FALSE	0000000000	SID	xxx 
.youtube.com	TRUE	/	FALSE	0000000000	HSID	xxx
.youtube.com	TRUE	/	TRUE	0000000000	SSID	xxx
```

### __如何使用__

在準備好cookies.txt檔案后,請您將Cookies.txt放在與存放record.sh相同的資料夾內。  
直接運行record.sh可以檢查是否生效。  
如果已經生效：
```
[root@hanaon record]# ls
record.sh  cookies.txt  livedl
[root@hanaon record]# ./record.sh 
...
當前YouTube已登入（授權尚未過期),預計過期時間為2022-07-08,請注意及時更新Cookies。
```
如果未生效:
```
[root@hanaon record]# ls
record.sh  livedl
[root@hanaon record]# ./record.sh 
...
當前YouTube未登入（授權已過期)。
```

__請注意__  
在您變更Google賬戶密碼、在獲取cookies的瀏覽器上退出賬戶等賬戶安全相關敏感操作后可能會導致cookies直接失效。
