# Crawl

## 環境及套件版本
  * Python 3.6.6
  * pip 10.0.0：為Python內建的套件管理
  * jupyter notebook 4.4.0
<br>


## 爬蟲
* 說明：可「自動」瀏覽全球資訊網的網路機器人，許多的搜尋入口網站 (如 Google)，都會透過網路爬蟲收集網路上的各種資訊，進一步分析後成為使用者搜尋的資料，許多開發者也會自行開發不同的爬蟲程式，進行大數據收集與分析
* 類型
  * 靜態爬蟲：網站完成一個請求 (request) 與回應 (response) 後，用戶端即不再與伺服器有任何的交流，所有的互動都只與瀏覽器的網頁互動，資訊不會傳遞到後端伺服器
  * 動態爬蟲：網站會依照使用者的行為不斷的與伺服器進行交流，必須要知道網站需要什麼「資訊」，提供正確的資訊，才能取得所需要的資料
* 使用爬蟲的禮儀
  * 不造成網站伺服器的負擔：每次爬取資料時，設定適當的等待延遲
  * 確認網站是否有提供 API：節省讀取與分析網站 HTML 的時間
  * 注意 robots.txt：規範一個網站允許什麼樣的 User-Agent 訪問，也會規範 Crawl-delay 訪問間隔時間，如果 Crawl-delay 設定 1，表示這個網站期望每次訪問的時間間隔一秒鐘
* 反爬蟲機制
  <table border="1" width="30%">
    <tr>
        <th width="2%"> 機制	</a>
        <th width="10%"> 說明 </a>
        <th width="16%"> 作法 </a>
        <th width="2%"> 破解難度 </a>
    </tr>
    <tr>
        <td> 判斷瀏覽器 headers 資訊 </td>
        <td> 利用 headers 判斷來源是否合法，headers 通常會由瀏覽器自動產生，直接透過程式所發出的請求預設沒有 headers </td>
        <td> 只要能透過爬蟲程式，送出模擬瀏覽器的 headers 資訊，就能進行破解 </td>
        <td> 低 </td>
    </tr>  
    <tr>
        <td> 清空 window.navigator </td>
        <td> 有些反爬蟲的網頁，會檢測瀏覽器的 window.navigator 是否包含 webdriver 屬性，在正常使用瀏覽器的情況下，webdriver 屬性是 undefined，一旦使用了 selenium 函式庫，這個屬性就被初始化為 true </td>
        <td> 程式使用 selenium webdriver 的 execute_cdp_cmd 的方法，將 webdriver 設定為 undefined (詳如 selenium 說明) </td>
        <td>  </td>
    </tr> 
    <tr>
        <td> 使用動態頁面 </td>
        <td> 將網頁內容全部由動態產生，大幅增加爬蟲處理網頁結構的複雜度 </td>
        <td> 只要確認動態頁面的架構，就能進行破解，如果打開的網頁是動態頁面，「檢視網頁原始碼」時看到的結構往往會很簡單，通常都只會是一些簡單的 HTML、CSS 和壓縮過的 js 文件 </td>
        <td> 中低 </td>
    </tr>  
    <tr>
        <td> 加入使用者行為判斷 </td>
        <td> 在網頁的某些元素，加入使用者行為的判斷，例如滑鼠移動順序、滑鼠是否接觸...等，增加爬蟲處理的難度 </td>
        <td> 確認頁面加入的使用者行為，就能模擬並進行破解，<br>
             如：有些網頁會在按鈕加上「滑鼠碰觸」的保護，如果不是真的用滑鼠碰觸，只是用程式撰寫「點擊」指令，就會被當作爬蟲而被阻擋，模故擬出先碰觸元素，再進行點擊的動作，藉此突破這個反爬蟲的機制 (詳如模擬點擊說明) <br>
             如：有些網頁也會判斷使用者刷新網頁的時間 (通常使用者不會在極短的時間內連續刷新)，這時也可以使用 time 函式庫的 sleep 方法讓網頁有所等待，避開這個檢查機制 </td>
        <td> 中 </td>
    </tr> 
    <tr>
        <td> 提交使用者授權 </td>
        <td> 在使用者登入時，會將使用者的授權 (token) 加入瀏覽器的 Cookie 當中，藉由判斷 Cookie 確認使用者是否合法 </td>
        <td> 只要知道 request 與 response 的機制後，取得 Cookie 內的 token 就能破解 </td>
        <td> 中 </td>
    </tr> 
    <tr>
        <td> 破解驗證碼 </td>
        <td> 相當常見的驗證機制，可相當程度的防堵惡意的干擾與攻擊，對於非人類操作與大量頻繁操作都有不錯的防範機制 (如防堵高鐵搶票、演唱會搶票...等) </td>
        <td> 必須搭配一些 AI 來處理圖形、數字、文字的識別，通常只要能識別驗證碼就能破解，要破解一般驗證碼，需要先將網頁上的驗證碼圖片下載，再將圖片提交到 2Captcha 服務來幫我們進行辨識 </td>
        <td> 高 </td>
    </tr>
    <tr>
        <td> 破解代理伺服器與第三方 IP 封鎖 </td>
        <td> 針對惡意攻擊的 IP 進行封鎖 </td>
        <td> 通常必須更換 IP 或更換代理伺服器才能破解，許多網站上也有提供免費的 Proxy IP，以 Free Proxy List 網站為例，就能取得許多免費的 Proxy IP </td>
        <td> 高 </td>
    </tr>
  </table>
<br>


## 工具介紹
* 搭配使用工具
  * Webdriver 
    * 不同瀏覽器會有不同的 driver，如[Chrome](https://chromedriver.chromium.org/downloads)、[Edge](https://developer.microsoft.com/zh-tw/microsoft-edge/tools/webdriver/)、[Firefox]()、[Safari](https://developer.apple.com/documentation/webkit/testing_with_webdriver_in_safari)，需下載目前瀏覽器版本的 Webdriver
    * Chromedriver：其他瀏覽器類似
      ```
      # 載入需要的套件
      from selenium import webdriver
    
      # 開啟瀏覽器視窗(Chrome)
      # 方法一：執行前需開啟chromedriver.exe且與執行檔在同一個工作目錄
      driver = webdriver.Chrome()
      # 方法二：或是直接指定exe檔案路徑
      driver = webdriver.Chrome('桌面\chromedriver')
    
      # get()：輸入網址，即可前往特定網頁
      driver.get('http://www.google.com') # 更改網址以前往不同網頁
      # close()：可關閉目前網頁視窗
      driver.close() # 關閉瀏覽器視窗
      ```
  * 額外工具
    * [SelectorGadget](https://chrome.google.com/webstore/detail/selectorgadget/mhjhnkcfbdhnjickkkdbjoemdmbfginb?hl=zh-TW)
    * [Quick Javascript Switcher](https://chrome.google.com/webstore/detail/quick-javascript-switcher/geddoclleiomckbhadiaipdggiiccfje)
    * [XPath Helper](https://chrome.google.com/webstore/detail/xpath-helper/hgimnogjllphhhkhlmebbmlgjoejdpjl?hl=zh-TW)
  * 網頁檢視器：網頁中看得到的內容一定抓得下來
    * Windows：請按 f12、ctrl+shift+i
    * macOS：請按 option+command+c
* 套件
  * Selenium
    * 指令：
      ```
      from selenium import webdriver
      # 請求頭信息偽裝為瀏覽器，可以更好地請求數據信息
      user_agent = "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.0.3 Safari/605.1.15"
      opt = webdriver.ChromeOptions()
      # 加入 headers 資訊
      opt.add_argument('--user-agent=%s' % user_agent)
      driver = webdriver.Chrome('./chromedriver', options=opt)
      # 清空 window.navigator
      driver.execute_cdp_cmd("Page.addScriptToEvaluateOnNewDocument", {
          "source": """
            Object.defineProperty(navigator, 'webdriver', {
              get: () => undefined
            })
          """
      })
      driver.get('要爬的網址')
      ```
    * 介紹：提供簡單的 API(Application Programming Interface) 應用程式介面
    * 使用規則
      * 兩種函數找出 WebElement
        * find_element：抓取符合條件的第一個項目，可搭配 By 方法
        * find_elements：抓取所有符合條件的項目，並回傳成 list
      * 八種方法
        * ID = id
        * CLASS_NAME = class_name
          * 透過標籤的 class 屬性搜尋
          * 字串內有空格的話要改為「.」
        * NAME = name
        * LINK_TEXT = link_text
          * 透過連結標籤的文字搜尋
        * PARTIAL_LINK_TEXT = partial_link_text
        * TAG_NAME = tag_name
          * 使用 TAG 選擇器特別要注意的是，HTML 標籤時常重複，因此 TAG 選擇器通常適用於抓取大框架的網頁元素再做細部搜尋
        * XPATH = xpath
          * 用於在 XML 文檔中定位節點的語言(想像網頁是圖書館，而各個網頁元素是書籍，XPath 就有點類似於目錄編號，可以直接查找)
          * 原因：當想要查找的元素沒有合適的 id 或 name 屬性時，可以使用 XPath 以絕對路徑(不建議使用)定位元素，也可以利用已知特殊 id 或 name 屬性的元素的相對路徑定位
          * XPath 包含來自 HTML 所有元素的位置(絕對路徑)，因此，僅對網頁進行一點點調整就可能導致失敗(超級容易失敗，要不斷維護)
          * 步驟
            * 打開網頁檢視器，找到目標網頁元素的內容
            * 在內容上點擊右鍵 → Copy→Copy (full) XPath，獲取絕對或相對 XPath
            * 將複製下來的 XPath 貼上
          * 範例：
        * CSS_SELECTOR = css_selector
          * 重要
            * 「/」改寫成「>」
            * 「//」改寫成「 (空格)」
            * 用 id 查找「#」
            * 用 classname 查找「.」
          * 參考網頁：[CSS Selectors](https://www.w3schools.com/cssref/css_selectors.asp)
      * 取得標籤的資訊
        * 取得標籤的內部文字
          ```
          element = driver.find_element(搜尋欄位, '搜尋條件')
          element.text
          ```
        * 取得標籤的某個屬性
          ```
          element = driver.find_element(搜尋欄位, '搜尋條件')
          element.get_attribute('屬性名稱')
          ```
        * 模擬使用者點擊標籤
          ```
          element = driver.find_element(搜尋欄位, '搜尋條件')
          element.click()
          ```
  * Requests
    * 指令：
      ```
      import requests
      url = '要爬的網址'
      res = requests.get(url)
      # 返回 200：請求成功
      
      # 請求頭信息偽裝為瀏覽器，可以更好地請求數據信息
      headers = {'user-agent': 'Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36'}
      res = requests.get(url, headers=headers)
      res.encoding = 'utf8'
      print(res.text)
      ```
    * 介紹：對網路發動請求的套件，可實作對網頁做 get、post 等 HTTP 協定的行為，請求網站獲取網頁數據
    * 類型
      <table border="1" width="20%">
        <tr>
          <th width="2%"> HTTP 方法	</a>
          <th width="3%"> requests 方法 </a>
          <th width="10%"> 說明 </a>
          <th width="5%"> 備註 </a>
        </tr>
        <tr>
          <td> GET </td>
          <td> requests.get(url) </td>
          <td> 向指定資源提交請求，可額外設定 params 參數字典 </td>
          <td> 提交的參數會放在標頭中傳送 (公開) </td>
        </tr>
        <tr>
          <td> POST </td>
          <td> requests.post(url) </td>
          <td> 向指定資源提交請求，可額外設定 data 參數字典 </td>
          <td> 提交的參數會放在內容中傳送 (隱密) </td>
        </tr>
        <tr>
          <td> PUT </td>
          <td> requests.put(url) </td>
          <td> 向指定資源提供最新內容，可額外設定 data 參數字典 </td>
          <td>  </td>
        </tr>
        <tr>
          <td> DELETE </td>
          <td> requests.delete(url) </td>
          <td> 請求刪除指定的資源 </td>
          <td>  </td>
        </tr>
        <tr>
          <td> HEAD </td>
          <td> requests.head(url) </td>
          <td> 請求提供資源的回應標頭 (不含內容) </td>
          <td>  </td>
        </tr>
        <tr>
          <td> OPTIONS </td>
          <td> requests.options(url) </td>
          <td> 請求伺服器提供資源可用的功能選項 </td>
          <td>  </td>
        </tr>
      </table>
    * Response 物件的屬性與方法
      * 說明：當伺服器收到 requests HTTP 方法所發出的請求後，會傳回一個 Response 物件，物件裡包含伺服器回應的訊息資訊，可以透過下列的屬性與方法，查詢相關內容 (bytes 表示資料以 bytes 表示，str 以字串表示，dict 以字典表示)
        <table border="1" width="20%">
          <tr>
            <th width="3%"> Response 物件	</a>
            <th width="7%"> 說明 </a>
            <th width="3%"> Response 物件	</a>
            <th width="7%"> 說明 </a>
          </tr>
          <tr>
            <td> url </td>
            <td> 資源的 URL 位址 </td>
            <td> content </td>
            <td> 回應訊息的內容 (bytes) </td>
          </tr>
          <tr>
            <td> text </td>
            <td> 回應訊息的內容字串 (str) </td>
            <td> raw </td>
            <td> 原始回應訊息串流 (bytes) </td>
          </tr>
          <tr>
            <td> status_code </td>
            <td> 回應的狀態 (int) </td>
            <td> encoding </td>
            <td> 回應訊息的編碼 </td>
          </tr>
          <tr>
            <td> headers </td>
            <td> 回應訊息的標頭 (dict) </td>
            <td> cookies </td>
            <td> 回應訊息的 cookies (dict) </td>
          </tr>
          <tr>
            <td> history </td>
            <td> 請求歷史 (list) </td>
            <td> json() </td>
            <td> 將回應訊息進行 JSON 解碼後回傳 (dict) </td>
          </tr>
          <tr>
            <td> rasise_for_status() </td>
            <td> 檢查是否有例外發生，如果有就拋出例外 </td>
            <td>  </td>
            <td>  </td>
          </tr>
        </table>
  * BeautifulSoup
    * 指令：
      ```
      soup = BeautifulSoup(res.text, 'html.parser')
      infos = soup.select('路徑')  #路徑提取方式：在固定位置右鍵->copy->copy selector
      ```
    * 介紹：借助網頁的結構特性來解析網頁的工具，可透過 Requests 提取 HTML 標籤裡的元素，對網頁進行解析得到結構化的數據
  * Lxml
    * 指令：
      ```
      From lxml import etree
      Html = etree.HTML(text)
      infos = Html.xpath('路徑')  #路徑提取方式：在固定位置右鍵->copy->copy xpath
      ```
    * 介紹：Lxml 為 XML 解析庫，可修正 HTML 代碼，形成結構化的 HTML 結構
<br>


## 重要
* 重要查詢
  * 查詢設定：conda config --show
  * 查詢 proxy
    ```
    import urllib
    urllib.request.getproxies()
    ```
* 重要說明
  * headers：用來讓瀏覽器向伺服器表明自己的身分，有些 HTTP 伺服器僅允許來自普通瀏覽器的請求，而不接受來自程式腳本的請求
    * 經常用於「偽裝」User-Agent
    * Mozilla Firefox 的 header 的值為 "Mozilla/5.0 (X11; U; Linux i686) Gecko/20071127 Firefox/2.0.0.11"
    * urllib 的 header 的值為 "Python-urllib/2.6"
    * 執行方式：Mozilla、AppleWebKit、Chrome，解析網頁：用 text/html 的方式，以 UTF-8 的格式去做解析
      ```
      headers = {
         'content-type': 'text/html; charset=UTF-8',
         'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36'
      }
      ```
  * https://stevenjhu.com/2022/02/13/python%E7%88%AC%E8%9F%B2web-crawler-%E6%93%8D%E4%BD%9C-cookie%E3%80%81request-headers/
  * https://www.learncodewithmike.com/2020/06/how-to-scrape-different-pages-using-python-scraper.html
<br>


## 範例
* requests：
  * 範例一 ：【PTT 資訊版】爬取文章標題
    * 網址：https://www.ptt.cc/bbs/MobileComm/index.html
    * import Python套件
      ```
      import requests   
      from bs4 import BeautifulSoup 
      ```
    * 將網頁 Get 下來
      ```
      r = requests.get('https://www.ptt.cc/bbs/MobileComm/index.html') #將此頁面的HTML GET下來
      print(r.text) #印出HTML
      ```
    * 將抓下來的資料用 Beautifulsoup4 轉為 HTML 的 parser，想選取的網頁裡的文章標題，故 soup.select 中放的是 div.title a
      ```
      soup = BeautifulSoup(r.text, 'html.parser') #將網頁資料以html.parser
      sel = soup.select('div.title a') #取HTML標中的 <div class="title"></div> 中的<a>標籤存入sel
      ```
    * 最後寫一個迴圈將爬下來的文章標題印出來
      ```
      for s in sel:
        print(s['href'], s.text) 
      ```
  * 範例二：【PTT Joke版】爬取多頁資料
    * 觀察下一頁按鈕的HTML標籤內容，發現上一頁按鈕的 a 標籤中有上一頁的網址為「/bbs/joke/index8140.html」
      ```
      r = requests.get('https://www.ptt.cc/bbs/joke/index.html')
      soup = BeautifulSoup(r.text, 'html.parser')
      u = soup.select('div.btn-group.btn-group-paging a')#上一頁按鈕的a標籤
      url = 'https://www.ptt.cc'+ u[1]['href'] #組合出上一頁的網址
      ```
    * 藉由迴圈來重複GET網頁
      ```
      url = 'https://www.ptt.cc/bbs/joke/index.html'
      for i in range(3): #往上爬3頁
        r = requests.get(url)
        soup = BeautifulSoup(r.text, 'html.parser')
        sel = soup.select('div.title a') #標題
        u = soup.select('div.btn-group.btn-group-paging a') #a標籤
        print ('本頁的URL為'+url)
        url = 'https://www.ptt.cc' + u[1]['href'] #上一頁的網址

      for s in sel: #印出網址跟標題
        print(s['href'], s.text)
      ```
  * 範例三：【PTT 八卦版】遇到按鈕
    * 進入網站後會看到讓使用者點選「是否已滿18歲」按鈕
    * 說明：
      * Cookie：網站存放在瀏覽器的一小段內容
      * 與伺服器互動：連線時，Cookie 放在 Request Headers 中送出
      * 先開啟瀏覽器開發者模式(F12)，並點選至network(網路)，觀察點選「已滿18歲」後，會送給伺服器之封包內容，意即透過「檢視原始碼」→「Application」→「Cookies」觀察重要的 Cookie
    * 詳如：爬蟲_簡易說明
* selenium
  * 範例：模擬點擊
    ```
    from selenium import webdriver
  
    driver = webdriver.Chrome()
    driver.get('http://www.google.com') # 更改網址以前往不同網頁
    ```
    * 情境一：搜尋輸入
      ```
      # 定位搜尋框
      element = driver.find_element(by=By.CLASS_NAME, value='gLFyf.gsfi')

      # 傳入字串
      element.send_keys('Selenium Python')
  
      # 點擊搜尋按鈕
      button = driver.find_element(by=By.CLASS_NAME, value='gNO89b')
        # 點擊搜尋按鈕
      button.click()
  
        # 反爬蟲：點擊搜尋按鈕
      from selenium.webdriver.common.action_chains import ActionChains
      actions = ActionChains(element)
      actions.move_to_element(button).click(button)   # 滑鼠先移到 button 上，然後再點擊 button
      actions.perform()
      ```
    * 情境二：刪除輸入
      ```
      # 定位搜尋框
      element = driver.find_element(by=By.CLASS_NAME, value='gLFyf.gsfi')

      # 傳入字串
      element.send_keys('Selenium Python')
  
      # 刪除原本已輸入的文字
      element.clear()
      ```
    * 瀏覽網頁的瀏覽紀錄
      ```
      # 前往下一項
      driver.forward()
      # 前往上一項
      driver.back()
      ```
<br>


## 查詢
* 網路狀況
  * Windows
    * 檢視 IP Address
      * 指令：ipconfig
      * 結果：如果 IP Address 顯示為 0.0.0.0 或 169.x.x.x 則無法上網
    * 測試網站是否活著
      * 指令：ping 目的地網站名稱或IP Address
      * 結果：如果出現 Request timed out. 就表示該網站和我們電腦間的網路連線無法建立
    * 追蹤網路路徑
      * 指令：tracert 目的地網站名稱或IP Address
    * 測試網頁服務(HTTP)
      * 指令：
        * telnet 目的地網站名稱或IP Address 80
        * telnet 目的地網站名稱或IP Address 443
  * Linux
    * 測試網站是否活著
      * 指令：ping 目的地網站名稱或IP Address
      * 停止輸出：ctrl+c
    * 追蹤網路路徑
      * 指令：traceroute 目的地網站名稱或IP Address
* [HTTP 狀態代碼](https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Status)
  <table border="1" width="15%">
          <tr>
            <th width="2%"> 狀態代碼	</a>
            <th width="3%"> 說明 </a>
            <th width="2%"> 狀態代碼	</a>
            <th width="3%"> 說明 </a>
            <th width="2%"> 狀態代碼	</a>
            <th width="3%"> 說明 </a>
          </tr>
          <tr>
            <td> 200 </td>
            <td> 網頁正常 </td>
            <td> 301 </td>
            <td> 網頁搬家，重新導向到新的網址 </td>
            <td> 400 </td>
            <td> 錯誤的要求 </td>
          </tr>
          <tr>
            <td> 401 </td>
            <td> 未授權，需要憑證 </td>
            <td> 403 </td>
            <td>	沒有權限 </td>
            <td> 404 </td>
            <td> 找不到網頁 </td>
          </tr>
          <tr>
            <td> 500 </td>
            <td> 伺服器錯誤 </td>
            <td> 503 </td>
            <td> 伺服器暫時無法處理請求 (附載過大) </td>
            <td> 504 </td>
            <td> 伺服器沒有回應 </td>
          </tr>
  </table>
* 
<br>


## 參考資料
* [Python爬蟲小人生](https://ithelp.ithome.com.tw/articles/10202121)
* [如何使用Webdriver、send_keys](https://medium.com/marketingdatascience/selenium%E6%95%99%E5%AD%B8-%E4%B8%80-%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8webdriver-send-keys-988816ce9bed)
* [Selenium教學：如何使用find_element(s)取得任何網頁上能看到的內容](https://medium.com/marketingdatascience/%E5%8B%95%E6%85%8B%E7%B6%B2%E9%A0%81%E7%88%AC%E8%9F%B2%E7%AC%AC%E4%BA%8C%E9%81%93%E9%8E%96-selenium%E6%95%99%E5%AD%B8-%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8find-element-s-%E5%8F%96%E5%BE%97%E7%B6%B2%E9%A0%81%E5%85%83%E7%B4%A0-%E9%99%84python-%E7%A8%8B%E5%BC%8F%E7%A2%BC-b66920fc8cab)
* [網路問題判斷](http://wiki.kmu.edu.tw/index.php/%E7%B6%B2%E8%B7%AF%E5%95%8F%E9%A1%8C%E5%88%A4%E6%96%B7)
* [Python爬虫基础库和实践](https://blog.csdn.net/weixin_39618456/article/details/112153519)
* [用來開啟 URLs 的可擴充函式庫](https://docs.python.org/zh-tw/3/library/urllib.request.html)
* [Python 網路爬蟲教學](https://steam.oxxostudio.tw/category/python/spider/spider.html)
