#### **可编辑div**



**获取光标，并插入自定义的内容，插入的内容不可编辑，不可相邻插入参数**

下面的思路大部分来自这个博主的文章，https://www.shuzhiduo.com/A/qVdepY0g5P/



大家根据各自的业务线去修改。

切记盲目搬运，一定要沉下心来理解，这样才能更好地去实现。



下面的代码是根据我自己本身的项目，将这个功能的函数进行抽离。

```react
react写法：

const contentEditableBox = () => {
     const [lastEditRange, setLastEditRange] = useState<any>();
     const [templateText, setTemplateText] = useState()
     // 获取当前光标的位置，可用于判断是否相邻插入内容
     const [position, setPosition] = useState({
        end: 0,
        preElement: null,
      });
       // 编辑框点击事件
      const editH = () => {
        const selection = getSelection();
        // 设置最后光标对象
        setLastEditRange(selection && selection.getRangeAt(0));
        getPointPosition();
      };
     	// 触发keyup时，保存每次的光标位置
      const editHonkeyup = () => {
        const selection = getSelection();
        // 设置最后光标对象
        setLastEditRange(selection && selection.getRangeAt(0));
      };
     
      	//获取光标位置
       const getPointPosition = useCallback(() => {
        let range;
        if (window.getSelection) {
          range = window.getSelection().getRangeAt(0); // 创建range
        } else if (document.selection) {
          range = document.selection.createRange(); // 创建选择对象
        }
        let end = range.endOffset;
        let preElement = range.endContainer;
        while (preElement.previousSibling) {
          if (preElement.previousSibling.innerHTML) {
            end += preElement.previousSibling.innerHTML.length;
          } else {
            end += preElement.previousSibling.textContent.length;
          }
          preElement = preElement.previousSibling;
        }
        setPosition({
          end,
          preElement,
        });
        return end;
      }, []);
     
     const handleMessageChange = () => {
       getPointPosition();
       ......
     }
       
     /**
       * @description 处理输入框超出内容、保存光标位置
       */
      const handleContent = () => {
        const obj = document.getElementById('edit');
        obj?.addEventListener('keyup', () => {
          editHonkeyup(); // 保存光标
          if (obj?.innerText.length >= 300) {
            obj.innerText = obj.innerText.substring(0, 300);
            return false;
          }
          return true;
        });
        obj?.addEventListener('paste', (e) => {
          if (obj?.innerText.length >= 300) {
            e.preventDefault();
            obj.innerText = obj.innerText.substring(0, 300);
            return false;
          }
          return true;
        });
      };

    // 在光标位置，插入参数
    const rangNameHandle = (name) => {
        const selection: any = getSelection();
        // 插入参数
        const item = `～{{${name}}}～`;
        if (lastEditRange) {
          // 存在最后光标对象，选定对象清除所有光标并添加最后光标还原之前的状态
          selection.removeAllRanges();
          selection.addRange(lastEditRange);
        }
        // 判断选定对象范围是编辑框还是文本节点
        if (selection.anchorNode.nodeName !== '#text') {
          // 如果是编辑框范围。则创建表情文本节点进行插入
          const emojiText = document.createTextNode(item);
          const edit: any = document.getElementById('edit');
          if (edit.childNodes.length > 0) {
            // 如果文本框的子元素大于0，则表示有其他元素，则按照位置插入表情节点
            for (let i = 0; i < edit.childNodes.length; i += 1) {
              if (i === selection.anchorOffset) {
                edit.insertBefore(emojiText, edit.childNodes[i]);
              }
            }
          } else {
            // 否则直接插入一个表情元素
            edit.appendChild(emojiText);
          }
          // 创建新的光标对象
          const range = document.createRange();
          // 光标对象的范围界定为新建的表情节点
          range.selectNodeContents(emojiText);
          // 光标位置定位在表情节点的最大长度
          range.setStart(emojiText, emojiText.length);
          // 使光标开始和光标结束重叠
          range.collapse(true);
          // 清除选定对象的所有光标对象
          selection.removeAllRanges();
          // 插入新的光标对象
          selection.addRange(range);
        } else {
          // 如果是文本节点则先获取光标对象
          const range = selection.getRangeAt(0);
          // 获取光标对象的范围界定对象，一般就是textNode对象
          const textNode = range.startContainer;
          // 获取光标位置
          const rangeStartOffset = range.startOffset;
          // 文本节点在光标位置处插入新的表情内容
          textNode.insertData(rangeStartOffset, item);
          // 光标移动到到原来的位置加上新内容的长度
          range.setStart(textNode, rangeStartOffset + obj.name.length + 2);
          // 光标开始和光标结束重叠
          range.collapse(true);
          // 清除选定对象的所有光标对象
          selection.removeAllRanges();
          // 插入新的光标对象
        }
      };

     /**
       * @description 选择参数回调函数
       * @param { regParam } 需要用html替换的参数 ～～用于只能替换一次
       * @param { newHtml } 最终html文件
       */
      const onOk = (name) => {
        const regParam = `～{{${name}}}～`;
        rangNameHandle(name);
        const editBox = document.querySelector('#edit') as HTMLDivElement;
        const domeMeg = editBox.innerHTML;
        const newHtml = domeMeg.replace(
          regParam,
          `<span contentEditable='false'>{${name}}</span>`,
        );
        setTemplateText(newHtml)
      };
       
     useEffect(() => {
        handleContent();
        return () => {
          handleContent();
        };
      }, []);
         
         
     return (
     	<div
            id="edit"
            onClick={editH}
            onInput={handleMessageChange}
            dangerouslySetInnerHTML={{ __html: createFormData.templateText }}
          />
         <button onCLick={onOk('sdsdsdsd')}>插入参数</button>
     )
 }
```

