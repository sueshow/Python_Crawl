# Python_Crawl

## 環境
* Python 3.6.6
* 套件
  * pip 10.0.0：為Python內建的套件管理
  * jupyter notebook 4.4.0
  * requests：對網路發動請求的套件，可實作對網頁做 get、post 等 HTTP 協定的行為
  * beautifulsoup4：借助網頁的結構特性來解析網頁的工具，只需要簡單的幾條指令就可以提取HTML標籤裡的元素
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
* 套件
  * Selenium
    * 介紹：提供簡單的 API(Application Programming Interface) 應用程式介面
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
    * 先開啟瀏覽器開發者模式(F12)，並點選至network(網路)，觀察點選「已滿18歲」後，會送給伺服器之封包內容
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
* [網路問題判斷](http://wiki.kmu.edu.tw/index.php/%E7%B6%B2%E8%B7%AF%E5%95%8F%E9%A1%8C%E5%88%A4%E6%96%B7)
