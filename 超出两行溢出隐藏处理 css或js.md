#### 纯css处理：

```javascript
overflow: hidden;
text-overflow: ellipsis;
display: -webkit-box;
 -webkit-line-clamp: 2;
/*! autoprefixer: off */
 -webkit-box-orient: vertical;
 /* autoprefixer: on */
word-break: break-all;
```



#### react hook写法处理：

```javascript
useEffect(()*=>*{

  *let* dom = document.querySelector('文本盒子的class或id')

  if (description && dom) {

   *let* domattr = window.getComputedStyle(dom)

   *const* totalTextLen = description.length

   *const* lineNum = 2;

   *const* baseWidth = domattr.width.slice(0, -2);

   *const* baseFontSize = domattr.fontSize.slice(0, -2);

   *const* lineWidth = baseWidth.slice(0, -2);

   

   // 所计算的strNum为元素内部一行可容纳的字数(不区分中英文)

   *const* strNum = Math.floor(lineWidth / baseFontSize);



   // 多行可容纳总字数

   *const* totalStrNum = Math.floor(strNum * lineNum);



   if (totalTextLen > totalStrNum) {

​    // 设置隐藏操作，比如增加溢出隐藏的css的class

   }

  }

 }, [文本变量：description])
 ```
