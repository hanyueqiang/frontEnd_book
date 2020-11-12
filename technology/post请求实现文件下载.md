#### 场景

该文件下载要给后端传一坨东西，有对象，有数组，例如：

```js
const params = {
  list: ["asss", "dfefe", "rerer"],
  pagination: {
    size: 100,
    pageSize: 1,
  },
  content: "vefs",
};
```

#### 解决方案

- 使用 a 链接，把参数传到地址上，对象，数组要进行 stringify，后端要经行 parse 解析,缺点字符串后 url 参数过长，后端需改造，不推荐
- 使用 post Form 表单提交下载，参数放入 key,value 内，value 也是字符串类型，也需要 stringify 不推荐
- <strong>使用 post 获取二进制流文件，用 a 链接下载, 推荐</strong>

#### 实现

```js
async function download(params) {
  // 发送post请求, 返回一个Blob 对象
  const blobData = await Service.exportCaseList(params);
  // 创建a标签
  const link = document.createElement('a');
  // 创建一个 DOMString，其中包含一个表示参数中给出的对象的URL,这个新的URL 对象表示指定的 File 对象或 Blob 对象
  const blobUrl = window.URL.createObjectURL(blobData);

  link.href = blobUrl;
  link.setAttribute('download', '病案统计.xls');
  // a标签添加到document内
  document.body.appendChild(link);
  // 模拟点击触发
  link.click();
  // 移除a标签
  document.body.removeChild(link);
  // 释放生成在内存中的URL 对象
  window.URL.revokeObjectURL(blobUrl);
}

// 注意：该请求的responseType 设置为blob
export(url, params = {}) {
  return Request({
    method: 'post',
    url,
    data: JSON.stringify(params),
    responseType:'blob'
  });
}
```
