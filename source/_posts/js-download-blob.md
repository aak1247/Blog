title: 使用js进行文件下载的方法

category: 前端

tags:
 - js
 - blob
 - 下载

date: 2017/12/10
---

&nbsp;&nbsp;&nbsp;&nbsp;今天学到了一种使用js实现文件下载的方法,相比用表单进行下载要安全很多,不会有跳转问题,而且便于掌握下载进度.

## 大致思路

1. 首先,使用ajax下载文件内容,读到内存中去. 这一步需要使用Blob对象存储文件内容;
2. 然后建立一个隐藏的a标签,其链接指向内存中的地址. 这一步使用URL.createObjectUrl()方法拿到内存中的地址;
3. 点击下载(直接调用a标签的click()方法即可),即从内存中下载到磁盘中去;


## Blob对象的用法

### 构造函数

``Blob(Content:array, options:Object)``

#### Options

``{type: type}``: blob对象的MIME类型, 比如``text/html``或``application/octet-binary``等等.

#### 示例

```javascript
var blob = new Blob(["test"], {type: "application/csv"})//MIME类型为csv的Blob对象
``` 

## 获取URL并实现下载

### 示例

```Javascript
var blob = new Blob(["test"], {type: "application/csv"});
var a = document.createElement("a");
a.href = URL.createObjectURL(blob);//创建指向此blob的链接
a.download = "test.csv";
//输出类似于blob:http://$website/fsadjlfsdjl 这样的字符串
document.body.append(a);
a.click();//实现下载
a.remove();
```

## 从远端下载

如果要从远端下载文件,先使用ajax下载数据, 在拿到数据以后再使用blob做处理就好.

### 示例代码

```javascript
//使用jquery
$.get(url, function(data){
    var blob = new Blob([data], {type: "application/csv"});//加一个方括号,data会直接转为字符串
    var a = document.createElement("a");
    a.href = URL.createObjectURL(blob);
    a.download = "download.csv";
    document.body.appendChild(a);
    a.click();
    a.remove();
});
```


## 其他用法

还可以使用此方法来动态加载图片, 只需要把blob对象的URL给到img的src里就好, 其他资源也一样.