# -
爬取实时的微博热搜并形成html文件
# -*-coding = utf-8 -*-
# @Time: 2020/8/4 16:30
# @Auther:MIGHTYJOI
# @File: test.py
# @Software:PyCharm
import bs4
import requests
# 代理
def getdata(url):
    head ={"User-Agent":"Mozilla/5.0(Windows NT 10.0; Win64; x64)AppleWebKit/537.36(KHTML,like Gecko)Chrome/84.0.4147.105 Safari/537.36"}
    data = requests.get(url, headers=head)
# 找出热搜
    soup = bs4.BeautifulSoup(data.text, "html.parser")
    resou = []
    targets = soup.find_all("td", class_="td-02")
    for each in targets:
        resou.append(each.a.text)
    return resou

def geturl(url):
    head = {
        "User-Agent": "Mozilla/5.0(Windows NT 10.0; Win64; x64)AppleWebKit/537.36(KHTML,like Gecko)Chrome/84.0.4147.105 Safari/537.36"}
    data = requests.get(url, headers=head)
# 找到链接
    url = bs4.BeautifulSoup(data.text, "html.parser")
    urls = []
    targets = url.find_all("td", class_="td-02")
    for each in targets:
        urls.append("https://s.weibo.com" + each.a.get("href"))
    return urls
# 求总共的热搜数量
'''def depth_(url):
    depths = []
    head = {
        "User-Agent": "Mozilla/5.0(Windows NT 10.0; Win64; x64)AppleWebKit/537.36(KHTML,like Gecko)Chrome/84.0.4147.105 Safari/537.36"}
    data = requests.get(url, headers=head)
    targets = bs4.BeautifulSoup(data.text, "html.parser")
    depth = targets.find_all_next("td", class_="td-01\nranktop")
    for each in depth:
        depths.append(each.text)
    depth = 51
'''
def main():
    shuzi = []
    for i in range(1, 52):
        shuzi.append(str(i))

    url = "https://s.weibo.com/top/summary?Refer=top_hot&topnav=1&wvr=6"
    text = []
    resou = getdata(url)
    urls = geturl(url)
    depth = 51
    for i in range(depth):
        text.append(shuzi[i]+"."+resou[i])
    with open("微博热搜.html", "w", encoding="utf-8") as f:
        f.write("<!DOCTYPE html>\n<html>\n<head>\n<meta\ncharset='utf-8'>\n<title>微博热搜by\nMIGHTYJOI</title><style type='text/css'>\na:link{color:black;}\na:visited{color:grey;}\n</style></head>\n<body>")
        f.write("<h1 style='text-align: center;'>微博热搜</h1>")
        for i in range(len(text)):
            s = str(text[i]).replace('[', '').replace(']', '')  # 去除[],这两行按数据不同，可以选择
            s = s.replace("'", '').replace(',', '') + '\n'  # 去除单引号，逗号，每行末尾追加换行符
            s = "<p style='text-align: center;font-family:arial;color: black;font-size: 20px;'><a\nhref=" + '"' + urls[i] + '"' + ">" + s + "</a></p>"
            f.write(s)
            f.write("")
            f.write("</body>\n</html>")
        f.close()


if __name__ == "__main__":
    main()
