---
title: js常用代码片段
description: js常用代码片段
date: 2020-12-08
tags:
  - js常用代码片段
---

## blob 二进制文件流 下载

```js
  const blob = new Blob([res])
  let fileName = '文件名.text'
  const link = document.createElement('a')
  link.download = fileName
  link.target = '_blank'
  link.style.display = 'none'
  link.href = URL.createObjectURL(blob)
  document.body.appendChild(link)
  link.click()
  URL.revokeObjectURL(link.href)
  document.body.removeChild(link)
```
