**下载文件 blob**



**第一种方式：**

```
import _ from 'lodash';

import { saveAs } from 'file-saver';



export async function formatBlobResponse(response, params = {}) {

  const contentDisposition = _.get(response, 'headers.content-disposition');

  const fileName = _.chain(contentDisposition)

​    .words(/filename=([^;]+)/)

​    .nth(1)

​    .defaultTo(params.defaultFileName)

​    .value();

  const blob = await _.get(response, ['data']);

  return { data: blob, name: decodeURI(fileName) };

}



export async function downloadBlobResponse(response, params) {

  const result = await formatBlobResponse(response, params);

  try {

​    const url = window.URL.createObjectURL(

​      new Blob([result.data], { type: 'application/octet-stream' }),

​    );

​    saveAs(url, result.name);

​    window.URL.revokeObjectURL(url);

  } catch (e) {

​    console.error(e);

  }

}
```



直接调用 downloadBlobResponse（接口返回的数据，{

​       defaultFileName: '默认的文件名',

 }）



**另一种方式：**

*/***

 ** 下载文件，基于xhr请求，保持登录状态，最长下载时间60分钟，超出则断开*

 **/*

```
export *const* downloadFile = (*options*) => {

 *const* { url, fileName, fileSuffix } = options;

 request({

  url,

  responseType: 'blob',

  independent: true,

  timeout: 60 * 60 * 1000,

 }).then((*res*) => {

  *const* date = safeMomentFormat(new Date(), 'YYYYMMDDhhmmss');

  *const* name = `${fileName}-${date}${fileSuffix}`;

  *const* { data } = res;

  *// 针对IE浏览器*

  if (window.navigator.msSaveOrOpenBlob) {

   navigator.msSaveOrOpenBlob(data, name);

  } else {

   *const* a = document.createElement('a');

   *const* fileUrl = URL.createObjectURL(data);

   a.href = fileUrl;

   a.download = name;

   a.click();

   window.URL.revokeObjectURL(url);

  }

 });

};
```



使用方式： downloadFile({ url, fileName: '高端险线下制卡人员清单', fileSuffix: '.xlsx' });



