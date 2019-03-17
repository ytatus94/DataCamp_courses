6. Importing Data in Python (Part 2)

- 從 web 上抓檔到 local 後讀入：
from urllib.request import urlretrieve 要從 web 上抓檔案要用這個
- url='檔案的URL'
urlretrieve(url, 'filename.csv') url 已經包含檔名了，第二個參數是抓下來後要叫什麼檔名，抓下來後可以用 pd.read_csv() 讀取
- url retrieve() 實際上是做一個 GET request

- 想直接用 Pandas 從 web 上抓：df = pd.read_csv(url, sep=';')
- df.ix[:, 0:1] 用 ix[列 index, 欄 index] 來存取，得到的是 DataFrame
- ix[] 是 loc[] 和 iloc[] 的混合體，可用 label和 index 來索引，但是盡量別用 ix[]
- xl = pd.read_excel(url, sheetname=None) 是用來從 web 上讀入 excel 檔案
- 用 sheetname=None 才能讀全部的 worksheets，讀進來後 xl 的型態是 dict，sheet name 是 key 而 DataFrame 是 value，所以用 xl.keys() 顯示所有的 worksheets，用 xl['sheet name'] 存取 value

- 用 request 的方法：
from urllib.request import urlopen, Request
url = "檔案的URL"
- request = Request(url) 設定 request，request 的型態是 http.client.HTTPResponse
- response = urlopen(request) 跟 url 的檔案連起來
- html = response.read() 可以讀檔，但是顯示的是沒排版過，很難讀的
- print(html) 然後印出來
- response.close() 記得關掉

- 用 request 的另一個方法：
import requests
url = "檔案的URL"
- r = requests.get(url) 跟 url 的檔案連起來，用這方法的話不用自己關掉
- text = r.text 讀檔，顯示的是排版過，比較好讀的
- print(text)
- from bs4 import BeautifulSoup 用來讓排版更好看，更容易取得 html 內的資訊，BeautifulSoup 仍然要靠 requests 來從網站上取得資料
- BeautifulSoup 的應用：
soup = BeautifulSoup(r.text) 把從 url 讀下來的餵給 BeautifulSoup
- pretty_soup = soup.prettify() 用 BeautifulSoup 讓 html 排版變漂亮
- guido_title = soup.title 取得 <title> 標籤的內容，包含標籤，如果要去掉標籤就用 guido_title.string
- guido_text = soup.get_text() 全部的 text
- a_tags = soup.find_all('a') 找所有 <a> 標籤
- for link in a_tags:
    print(link.get('href')) 所有 href 指定的連結
- API 用 json 的格式來傳遞資訊，json 實際上是一個字典

- 讀 json：
- import json
with open("a_movie.json") as json_file:
    json_data = json.load(json_file)
也可以用 json_data = r.json() r 是上面提到的用 requests 連結的東西

- 使用 tweepy 來抓 tweeter 上的資料：
# Import package
import tweepy
# Store OAuth authentication credentials in relevant variables
access_token = "..."
access_token_secret = "..."
- consumer_key = "..."
- consumer_secret = "..."
- # Pass OAuth details to tweepy's OAuth handler
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
- sns.set(color_codes=True) 設定 seaborn 風格
- ax = sns.barplot([x labels], [每個 label 對應的值])
ax.set(ylabel="y title")