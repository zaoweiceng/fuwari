---
title: python中requests的基本用法
published: 2024-02-12
description: 'python爬虫中常用的网页解析库及基本用法。'
image: './liuying.png'
tags: [python, 学习, 爬虫]
category: 'python'
draft: false 
---


# requests

- 发起的是同步网络请求

## 导入

```python
import requests
```

##  请求

### get

`requests.get(...)`

传递参数

```py
data = {
	'name':'amy',
	'age':15
}
headers = {
	'...':'...'
}
res = requests.get(url, params=data, headers=headers)
res.json() # 转为字典，若返回不是json格式将报错
```

- 若网站的SSL出错，则可以不进行SSL验证`requests.get(url, verify=False)`
- 超时设置`requests.get(url, timeout=1)` 单位为：秒。(设置为None表示永久等待)
- 登录验证：`requests.get(url, auth=(username, password))`

获取二进制数据（图片、音频、视频等）

```PY
res = requests.get('...../a.png', params=data)
with open('a.png', 'wb') as f:
	f.write(res.content)
```

### post

类似于get

## session

`session = requests.session()`

`session.get(...)`

------

## 响应

`res.status_code`

`res.headers`

`res.cookies`

`res.url` 

`res.history` 请求历史
