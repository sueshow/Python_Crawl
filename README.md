# Python_Crawl

## 環境
* Python 3.6.6
* 套件
  * pip 10.0.0：為Python內建的套件管理
  * jupyter notebook 4.4.0
  * requests：對網路發動請求的套件，可實作對網頁做get、post等HTTP協定的行為
  * beautifulsoup4：借助網頁的結構特性來解析網頁的工具，只需要簡單的幾條指令就可以提取HTML標籤裡的元素
<br>


## 範例
* PTT資訊版
  * 網址：https://www.ptt.cc/bbs/MobileComm/index.html
  * 範例一：文章標題
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
<br>


## 參考資料
* [Python爬蟲小人生](https://ithelp.ithome.com.tw/articles/10202121)
