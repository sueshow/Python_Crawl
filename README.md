# Crawl

## 環境及套件版本
  * Python 3.6.6
  * pip 10.0.0：為Python內建的套件管理
  * jupyter notebook 4.4.0
<br>


## 重要介紹
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
  * 打開網頁檢視器，網頁看得到的內容一定抓得下來
    * Windows：請按 f12、ctrl+shift+i
    * macOS：請按 option+command+c
* 套件
  * Selenium
    * 指令：
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
        * CSS_SELECTOR = css_selector
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
      res = requests.get(url)
      > 返回 200：請求成功
      > 返回 404、400：請求失敗
      
      res = requests.get(url, headers=headers)
      # 請求頭信息偽裝為瀏覽器，可以更好地請求數據信息
      
      res.text
      ```
    * 介紹：對網路發動請求的套件，可實作對網頁做 get、post 等 HTTP 協定的行為，請求網站獲取網頁數據
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
* PTT資訊版
  * 範例一：文章標題
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
  * 範例二：爬多頁資料
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
  * 範例三：遇到按鈕
    * 進入網站後會看到讓使用者點選「是否已滿18歲」按鈕
    * 說明：
      * Cookie：網站存放在瀏覽器的一小段內容
      * 與伺服器互動：連線時，Cookie 放在 Request Headers 中送出
      * 先開啟瀏覽器開發者模式(F12)，並點選至network(網路)，觀察點選「已滿18歲」後，會送給伺服器之封包內容，意即透過「檢視原始碼」→「Application」→「Cookies」觀察重要的 Cookie
    * 詳如：爬蟲_簡易說明
* 範例：模擬點擊
  ```
  from selenium import webdriver
  
  driver = webdriver.Chrome()
  driver.get('http://www.google.com') # 更改網址以前往不同網頁
  ```
  ```
  # 情境一：搜尋輸入
  # 定位搜尋框
  element = driver.find_element(by=By.CLASS_NAME, value='gLFyf.gsfi')

  # 傳入字串
  element.send_keys('Selenium Python')
  
  # 點擊搜尋按鈕
  button = driver.find_element(by=By.CLASS_NAME, value='gNO89b')
  button.click()
  ```
  ```
  # 情境二：刪除輸入
  # 定位搜尋框
  element = driver.find_element(by=By.CLASS_NAME, value='gLFyf.gsfi')

  # 傳入字串
  element.send_keys('Selenium Python')
  
  # 刪除原本已輸入的文字
  element.clear()
  ```
  ```
  # 瀏覽網頁的瀏覽紀錄
  # 前往下一項
  driver.forward()
  # 前往上一項
  driver.back()
  ```
<br>


## 查詢
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
<br>


## 參考資料
* [Python爬蟲小人生](https://ithelp.ithome.com.tw/articles/10202121)
* [如何使用Webdriver、send_keys](https://medium.com/marketingdatascience/selenium%E6%95%99%E5%AD%B8-%E4%B8%80-%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8webdriver-send-keys-988816ce9bed)
* [Selenium教學：如何使用find_element(s)取得任何網頁上能看到的內容](https://medium.com/marketingdatascience/%E5%8B%95%E6%85%8B%E7%B6%B2%E9%A0%81%E7%88%AC%E8%9F%B2%E7%AC%AC%E4%BA%8C%E9%81%93%E9%8E%96-selenium%E6%95%99%E5%AD%B8-%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8find-element-s-%E5%8F%96%E5%BE%97%E7%B6%B2%E9%A0%81%E5%85%83%E7%B4%A0-%E9%99%84python-%E7%A8%8B%E5%BC%8F%E7%A2%BC-b66920fc8cab)
* [網路問題判斷](http://wiki.kmu.edu.tw/index.php/%E7%B6%B2%E8%B7%AF%E5%95%8F%E9%A1%8C%E5%88%A4%E6%96%B7)
* [Python爬虫基础库和实践](https://blog.csdn.net/weixin_39618456/article/details/112153519)
* [用來開啟 URLs 的可擴充函式庫](https://docs.python.org/zh-tw/3/library/urllib.request.html)
