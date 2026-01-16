
- **爬虫**（Web Scraping）就是用程序自动从网站上抓取数据的工具，比如下载图片、收集新闻、分析价格等。
- 注意：爬虫要**遵守法律和道德**！不要爬取受保护的数据（如个人信息），尊重robots.txt协议，避免高频请求导致网站崩溃。否则可能被封IP或面临法律风险。
- 爬虫的本质，就是“**欺骗**”服务器，让服务器以为通过 Python 发送的代码，是一个真实的人在使用浏览器访问。

# **分析网络请求**

- **原理：** 网页上的每一个字、每一张图、每一段视频，本质上都是一次“请求（Request）”。

# **构造网络请求**

- 浏览器访问网站时，会携带“身份证”（Headers）。Python 的 `requests` 库默认是“裸奔”的，会被一眼识破。
- **核心三要素：**
	1. **URL (地址)**：你要去哪里？
	2. **Method (方式)**：GET（查）还是 POST（改/提交）？
	3. **Headers (请求头)**：你是谁？
		- `User-Agent`: 告诉服务器我是浏览器，不是脚本。
		- `Referer`: 告诉服务器我从哪里点过来的（防盗链核心）。
		- `Cookie`: 告诉服务器我的账号是谁（会员/高清权限核心）。
```python
# 要爬的url
url = "xxx"
# 构造请求头
header = {
	"User-Agent": "xxx"
	"Cookie": "xxxx"
	"Referer": "xxx"
}
# 获取请求
resp = request.get(url,headers = header)
```
# 提取（**解析数据**）


- 拿到服务器返回的一大堆乱码（HTML 或 JSON）后，找到你要的那行字
- **JSON 解析**（最简单，最推荐）：
	- **适用场景：** API 接口返回的数据（像字典一样）。
- **正则表达式 (Re)**（最快，但难读）：
	- **适用场景：** 数据藏在一段复杂的文字里，或者提取特定的字符串（如 BV 号、URL）。
	- **技能点：** 记住 `.*?` 这个万能公式（意思是：提取中间所有的东西）。
- **HTML 解析 (BeautifulSoup / XPath)**（传统爬虫必备）：
	- **适用场景：** 数据直接写在 HTML 标签里（比如 `<h1>标题</h1>`）。



# 响应的基本用法

| **属性/方法**              | **拿到的是什么？**      | **什么时候用？**     | **之前的案例**     |
| ---------------------- | ---------------- | -------------- | ------------- |
| **`resp.text`**        | **字符串** (String) | 爬网页 HTML、小说、文字 | **豆瓣电影**      |
| **`resp.content`**     | **二进制** (Bytes)  | 爬视频、图片、音频      | **存 .mp4 文件** |
| **`resp.json()`**      | **字典** (Dict)    | 爬 API 接口数据     | **解析 API**    |
| **`resp.status_code`** | **数字** (Int)     | 检查请求是否成功       | 调试报错时         |


# Beautifulsoup

`BeautifulSoup` 的作用，就是**把这串HTML代码字符串变成一个“对象树”**（Object Tree），让你能用简单的 Python 语法去“查找”和“提取”。

- 基本步骤：
	1. 初始化soup对象
	2. 找到**所有**符合条件的标签
	3. 提取（拿你要的数据）

```python
from bs4 import BeautifulSoup

# 1.初始化soup对象
soup = BeautifulSoup(html_doc, 'html.parser')

# 2.查找
link = soup.find('a') # 找第一个 <a> 标签 
titles = soup.find_all('a', class_='title') # 找所有 class="title" 的标签

# 3.提取
el = soup.find('a')
print(el.text)         # 获得里面的字
print(el['href'])      # ['属性名'] 拿链接/图片地址
```


# 保存数据

## 写入文件的两种方式

#### 1. 文本模式 (`'w'`)：需要“翻译”

当你用 `'w'` (write text) 模式打开文件时，Python 会默认你写入的是**字符串 (String)**。
- **动作**：Python 会把你的字符串（比如 "你好"）按照编码格式（如 UTF-8）**翻译**成 0 和 1，然后存入硬盘。
- **适用**：`.txt`, `.html`, `.json`, `.csv`, `.py` 等人类可读的文件。
    
#### 2. 二进制模式 (`'wb'`)：拒绝“翻译”，原样搬运

当你爬取图片、视频、音频时，服务器发给你的是已经编码好的**原生字节流 (Bytes)**。
- **动作**：Python 直接把接收到的 `0x89`, `0xFF` 等数据原封不动地写入硬盘，**不做任何编码转换**。
- **风险**：如果你用 `'w'` 模式去保存图片，Python 会试图把图片的像素数据当成文字去“翻译”，结果只有两个：要么翻译失败报错，要么文件被破坏（图片花屏、无法打开）。

> **一句话原则**：只要这个文件**不能**用记事本直接打开看懂（如图片、视频、压缩包、甚至 Word 文档），就必须用 `'wb'`。

## 流模式

- **默认模式** (`stream=False`)：**立即加载** (Eager Loading)
	- 当你发起请求时，`requests` 库会通过底层的 TCP Socket 接收数据。默认情况下，它会**持续阻塞**，直到把**整个 Body** 全部下载并加载到你电脑的 **RAM（内存）** 中，才把 `resp` 对象交给你。
	- **后果**：如果你下载一个 5GB 的电影，你的 Python 脚本会瞬间申请 5GB 内存。如果你电脑只有 4GB 内存，程序直接崩溃（MemoryError）。
	    

-  **流模式** (`stream=True`)：**延迟加载** (Lazy Loading)
	- 开启此模式后，`requests` 库在接收到 **Headers** 后，就会立即断开与 Body 自动下载的连接逻辑，将控制权交还给你的代码。
	- **状态**：此时，TCP 连接保持打开（Keep-Alive），数据（Body）还在服务器或操作系统的 TCP 缓冲区（TCP Buffer）里排队，并没有进入 Python 的内存。
	- **后果**：无论文件多大，Python 此时占用的内存几乎为零。

### 下载过程

拆解为 5 个标准的 **I/O（输入/输出）阶段**。
#### 阶段 1：握手与头部解析 (Handshake & Header Parsing)

- **动作**：`resp = requests.get(url, stream=True)`
- **底层**：客户端与服务器建立 TCP 连接（三次握手），发送 HTTP Request。服务器返回 HTTP Response。
- **关键点**：Python 此时只读取了 **Headers**。它解析出 `Content-Length`（总大小）和 `Content-Type`（类型），然后挂起连接。此时文件内容尚未下载。

#### 阶段 2：构建迭代器 (Iterator Construction)

- **动作**：`resp.iter_content(chunk_size=1024)`
- **原理**：你创建了一个生成器（Generator）。这就像在水管上装了一个“定量阀门”。你告诉程序：“我不想要全部数据，我每次只想要 `1024 bytes`（1KB）。”
    

#### 阶段 3：套接字读取 (Socket Read - I/O Blocking)

- **动作**：`for chunk in ...`
- **底层**：
    1. 当循环开始时，Python 通过系统调用（System Call）去询问操作系统的 **TCP 接收缓冲区 (Recv Buffer)**。
    2. 如果缓冲区有数据，操作系统把 1024 字节的数据 **复制 (Copy)** 到 Python 进程的内存空间中（即 `chunk` 变量）。
    3. 如果缓冲区空了，网络会阻塞等待服务器发送更多数据包 (Packets)。
        
#### 阶段 4：缓冲区写入 (Buffer Write)

- **动作**：`f.write(chunk)`
- **底层**：
    1. Python 将 `chunk` 数据从用户态内存（User Space）发送到内核态的文件系统缓冲区（Page Cache）。
    2. **注意**：此时数据通常还**没有**真正写到硬盘的物理磁道上，而是停留在操作系统的写入缓存中。
        

#### 阶段 5：刷盘 (Flush to Disk)

- **动作**：循环结束，`with` 语句退出，文件关闭。
- **底层**：操作系统执行 `fsync` 操作，将缓存中的数据强制物理写入硬盘（SSD/HDD）。此时文件才算真正“落袋为安”。

### 代码示例

```python
# stream=True: 仅获取响应头，保持 TCP Socket 连接打开，不立即读取 Body
resp = requests.get(url, headers=header, stream=True)

# Context Manager (with语句): 确保文件句柄在使用后正确关闭，释放文件描述符(File Descriptor)
with open(file_path, 'wb') as f:
    # Iterator: 从 Socket 缓冲区按 1MB 为单位“懒加载”读取数据
    for chunk in resp.iter_content(chunk_size=1024*1024):
        if chunk: # 过滤掉 Keep-Alive 数据包可能产生的空块
            # I/O Write: 将数据块写入系统的 Page Cache
            f.write(chunk)
            # 此时内存中永远只保留这 1MB 的数据，旧的 chunk 会被垃圾回收(GC)
```

## 普通模式

- 不用流 (`stream=False`)：一次性倾倒，这是 `requests` 的默认模式。
- **流程**：
    1. **下载**：Python 会等待服务器把**整个文件**发完。
    2. **缓存**：把这整个文件全部塞进你的**内存 (RAM)** 里，生成 `resp.content`。
    3. **写入**：你再一次性把内存里的数据写入硬盘。
- **适用场景**：图片、网页源码、小型 JSON 数据（通常 < 10MB）。
- **优点**：代码简单，速度极快（因为内存读写比硬盘快）。
- **缺点**：如果文件是 5GB 的电影，你的内存会瞬间爆炸，程序崩溃。
```python
# 小文件推荐写法（图片/短音频）
resp = requests.get(url)  # 默认 stream=False
with open('image.jpg', 'wb') as f:
    f.write(resp.content) # 一次性拿所有数据
```
- 保存到特定目录下(**构造文件路径即可**)
```python
dir_name = "xxx"
file_name = 'xxx'
path = os.path.join(dir_name, file_name)
with open(path,'wb') as f:
        f.write(img_resp.content)
```


|     **Header 字段名**      |                             **含义与用途**                              |                   **示例值 (String)**                   |                                                    **Python 获取与处理方法**                                                     |
| :---------------------: | :----------------------------------------------------------------: | :--------------------------------------------------: | :-----------------------------------------------------------------------------------------------------------------------: |
|   **Content-Length**    |             **文件大小**<br>单位是字节(Bytes)。用于制作进度条或校验文件完整性。              |                     `'10485760'`                     |             `total_size = int(resp.headers.get('Content-Length', 0))`<br><br>  <br><br>_注意：获取到的是字符串，必须转 int。_             |
|    **Content-Type**     |      **MIME 类型**<br><br>告诉客户端这是一个什么文件（HTML, MP3, JPG, JSON）。       | `'audio/mpeg'`<br><br>  <br><br>`'application/json'` |                `ctype = resp.headers.get('Content-Type', '')`<br><br>  <br><br>`if 'audio' in ctype: pass`                |
| **Content-Disposition** |              **文件名**<br><br><br>服务器建议的保存文件名。常用于附件下载。               |         `'attachment; filename="music.mp3"'`         | **需正则提取：**<br><br><br>`import re`<br><br>`fname = re.findall('filename="(.+)"', resp.headers.get('Content-Disposition'))` |
|        **Date**         |              **服务器时间**<br><br>  <br><br>响应生成的 GMT 时间。              |          `'Thu, 15 Jan 2026 12:00:00 GMT'`           |                                         `server_time = resp.headers.get('Date')`                                          |
|     **Set-Cookie**      | **身份凭证**<br><br>  <br><br>服务器要求客户端保存的 Cookie（如下次访问需带上的 SessionID）。 |             `'SESSDATA=xyz...; Path=/'`              |                 `cookies = resp.headers.get('Set-Cookie')`<br><br>  <br><br>_(通常 requests 会自动处理，很少需手动解析)_                 |
|    **Last-Modified**    |       **最后修改时间**<br><br>  <br><br>用于判断文件是否更新，实现“断点续传”或增量爬取。        |          `'Wed, 21 Oct 2025 07:28:00 GMT'`           |                                      `last_mod = resp.headers.get('Last-Modified')`                                       |
|      **Location**       |      **重定向地址**<br><br>  <br><br>当状态码为 301/302 时，告诉客户端去哪里找资源。       |          `'https://www.new-url.com/a.mp3'`           |         `new_url = resp.headers.get('Location')`<br><br>  <br><br>_(requests 默认会自动跳转，除非设置 allow_redirects=False)_         |
|  **Content-Encoding**   |         **压缩方式**<br><br>  <br><br>如果值为 gzip，说明 Body 是压缩过的。         |                       `'gzip'`                       |              `encoding = resp.headers.get('Content-Encoding')`<br><br>  <br><br>_(requests 会自动解压，通常无需手动处理)_               |

# 代码示例

## 爬取wallheaven壁纸

```python
import requests
from bs4 import BeautifulSoup
import os
# 设置要爬取的url
url = "https://wallhaven.cc/w/pol7qe"
dir_name = "images"  # 你想保存的文件夹名字

# exist_ok=True 的意思是：如果文件夹已经存在，不要报错，直接用就行
if not os.path.exists(dir_name):
    os.makedirs(dir_name)
    print(f"文件夹不存在，已创建: {dir_name}")
else:
    print(f"文件夹已存在: {dir_name}")
# 构造请求头
header = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36 Edg/143.0.0.0"
}
# 发送请求
resp = requests.get(url,headers=header,stream=True)
# 获取图片标签
soup = BeautifulSoup(resp.text,'lxml')
images = soup.find_all('img',id = 'wallpaper')

# 保存数据
for img in images:
    print(img['src'])
    img_url = img['src']
    file_name = img_url.split('/')[-1]
    path = os.path.join(dir_name, file_name)
    print(f"正在下载：{file_name}")
    img_resp = requests.get(img_url,headers=header)
    with open(path,'wb') as f:
        f.write(img_resp.content)
    print(f"保存成功！文件名: {file_name}")
```

## 爬取音乐

```python
import requests
import os

song_id = input("请输入id：")
song_name = input("请输入要保存的歌曲名：")

# 这是一个神奇的接口，只要把 ID 填进去，它就会自动重定向到真实的 MP3 地址
url = f'http://music.163.com/song/media/outer/url?id={song_id}.mp3'

header = {
    'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36'
}
print(f"准备下载: {song_name}")
print(f"构造的链接: {url}")

resp = requests.get(url,headers=header,stream=True)

content_type = resp.headers.get('Content-Type', '')
print(f"文件类型: {content_type}")

if 'audio' in content_type:
    # 保存文件
    file_path = f'{song_name}.mp3'
    
    # 检查文件大小
    total_size = int(resp.headers.get('content-length', 0))
    print(f"文件大小: {total_size / 1024 / 1024:.2f} MB")

    if total_size < 10240: # 如果文件小于 10KB，说明可能是假的或者被拦截了
        print("下载失败：文件过小，可能是VIP歌曲或无版权。")
    else:
        with open(file_path, 'wb') as f:
            for chunk in resp.iter_content(chunk_size=1024*1024):
                if chunk:
                    f.write(chunk)
        print("下载成功！")
else:
    print("下载失败：获取到的不是音频文件，可能是 404 或者被拦截页面。")
```

## 爬取bilibili视频

```python
import requests
import re
import json
import os
import subprocess

# ================= 配置区域 =================
# 目标视频
bvid = input("请输入BV号：")
url = f'https://www.bilibili.com/video/{bvid}'
output_name = f'{bvid}.mp4'

# 你的 Cookie (最关键的一步)
# 务必保证 Cookie 是完整的，不要有换行
my_cookie = "请复制你自己的cookie"

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36',
    'Referer': 'https://www.bilibili.com/',
    'Cookie': my_cookie
}
# ===========================================

def get_cid(bvid):
    """第一步：从网页获取视频的内部 ID (CID)"""
    print("正在获取视频 CID...")
    resp = requests.get(url, headers=headers)
    # 正则提取 cid
    match = re.search(r'"cid":(\d+),', resp.text)
    if match:
        cid = match.group(1)
        print(f"获取成功，CID: {cid}")
        return cid
    else:
        raise Exception("未找到 CID，可能是 BV 号错误或网页结构变更")

def get_high_quality_url(bvid, cid):
    """第二步：请求 API 获取真正的 1080p/4K 链接"""
    print("正在请求高清 API...")
    
    # B站官方 API 接口
    api_url = 'https://api.bilibili.com/x/player/playurl'
    
    params = {
        'bvid': bvid,
        'cid': cid,
        'qn': '120',      # 期望画质：120=4K, 80=1080p. 但最终结果取决于 fnval
        'fnval': '4048',  # 关键参数！4048 表示请求 DASH 格式 (必须有这个才能拿高画质)
        'fnver': '0',
        'fourk': '1'      # 允许 4K
    }
    
    resp = requests.get(api_url, params=params, headers=headers)
    data = resp.json()
    
    if data['code'] != 0:
        raise Exception(f"API 请求失败: {data['message']}")
    
    # 获取实际的画质 ID
    quality_id = data['data']['quality']
    # 对应的描述
    quality_desc = data['data']['accept_description'][data['data']['accept_quality'].index(quality_id)]
    
    print(f"API 响应成功！当前获取画质: {quality_desc} (ID: {quality_id})")
    
    # 提取 DASH 链接
    dash = data['data']['dash']
    video_url = dash['video'][0]['baseUrl']
    audio_url = dash['audio'][0]['baseUrl']
    
    return video_url, audio_url

def download_file(url, filename):
    if os.path.exists(filename):
        print(f"{filename} 已存在")
        return
    print(f"下载中: {filename}")
    resp = requests.get(url, headers=headers, stream=True)
    total = int(resp.headers.get('content-length', 0))
    with open(filename, 'wb') as f:
        dl = 0
        for chunk in resp.iter_content(chunk_size=8192):
            if chunk:
                f.write(chunk)
                dl += len(chunk)
                print(f"\r进度: {int(dl/total*100)}%", end="")
    print("\n下载完成")

def merge(v, a, out):
    print("正在合并...")
    subprocess.run(['ffmpeg', '-i', v, '-i', a, '-c', 'copy', '-y', out], 
                   stdout=subprocess.PIPE, stderr=subprocess.PIPE)
    print(f"搞定！文件名为: {out}")
    # 清理垃圾
    os.remove(v)
    os.remove(a)

def main():
    try:
        # 1. 拿 CID
        cid = get_cid(bvid)
        
        # 2. 拿高清直链
        v_url, a_url = get_high_quality_url(bvid, cid)
        
        # 3. 下载
        download_file(v_url, "temp_video.m4s")
        download_file(a_url, "temp_audio.m4s")
        
        # 4. 合并
        merge("temp_video.m4s", "temp_audio.m4s", output_name)
        
    except Exception as e:
        print(f"发生错误: {e}")

if __name__ == "__main__":
    main()
```