# AI对话光标跟随效果



[https://juejin.cn/post/7554221661992861742](https://juejin.cn/post/7554221661992861742)



```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      .blinking-dot {
        width: 15px;
        height: 15px;
        background-color: #000;
        /* 圆点颜色 */
        border-radius: 50%;
        position: fixed;
        /* 圆形 */
        animation: blink 0.8s infinite;
        /* 动画设置 */
        box-shadow: 0 0 10px rgba(255, 0, 0, 0.5);
        /* 可选的光晕效果 */
      }

      /* 闪烁动画定义 */
      @keyframes blink {
        0% {
          opacity: 1;
          /* 完全显示 */
          transform: scale(1);
          /* 正常大小 */
        }

        50% {
          opacity: 0.3;
          /* 半透明 */
          transform: scale(0.8);
          /* 稍微缩小 */
        }

        100% {
          opacity: 1;
          /* 恢复完全显示 */
          transform: scale(1);
          /* 恢复大小 */
        }
      }
    </style>
  </head>

  <body>
    <!-- 内容容器 -->
    <div class="wrapper"></div>

    <script>
      // 要显示的文本内容
      const str = `核心对比：现代公共性观赏 vs 古代私人雅集式观赏。 
        开头引用民间说法和《本草纲目》，指出大蒜对眼睛有害。
        接着从临床经验、现代医学、中医理论多角度解释为什么有害。
        然后不仅讲对眼睛的害处，还提到蒜是发物，会刺激加重其他疾病（如肠炎）。
        作者态度是：
        现代公共观赏是主流，若强装古人雅集式观赏会被嘲笑。
        选项分析：
        历史越往后发展，艺术品越具有公共性——文段
        中国人艺术修为在不断进化——文段没有谈修为进化然后不仅讲对眼睛的害处，还提到蒜是发物，会刺激加重其他疾病（如肠炎）。
        我们分析一下文段结构：
        开头引用民间说法和《本草纲目》，指出大蒜对眼睛有害。然后不仅讲对眼睛的害处，还提到蒜是发物，会刺激加重其他疾病（如肠炎）。
        接着从临床经验、现代医学、中医理论多角度解释为什么有害。
        然后不仅讲对眼睛的害处，还提到蒜是发物，会刺激加重其他疾病（如肠炎）。
        `;

      // 获取内容容器元素
      const wrapper = document.querySelector(".wrapper");
      // 用于存储闪烁圆点的引用
      let dot = null;

      /**
       * 延迟函数，返回一个Promise，在指定时间后resolve
       * @param {number} duration 延迟时间（毫秒）
       * @returns {Promise} 延迟完成的Promise
       */
      function delay(duration) {
        return new Promise((resolve) => {
          setTimeout(() => {
            resolve();
          }, duration);
        });
      }

      /**
       * 将文本转换为HTML段落
       * @param {string} str 要转换的文本
       * @returns {string} 转换后的HTML字符串
       */
      function transformTag(str) {
        // 按换行符分割文本，每行用<p>标签包裹
        return str
          .split("\n")
          .map((t) => `<p>${t}</p>`)
          .join("");
      }

      /**
       * 异步渲染内容，实现打字机效果
       */
      async function renderContent() {
        // 逐个字符显示文本
        for (let i = 0; i < str.length; i++) {
          // 获取当前要显示的文本部分
          const text = str.slice(0, i);
          // 转换为HTML格式
          const html = transformTag(text);
          // 更新容器内容
          wrapper.innerHTML = html;
          // 更新光标位置
          updateCursor();
          // 延迟一段时间，控制打字速度
          await delay(180);
        }
      }

      /**
       * 递归查找DOM节点中的最后一个文本节点
       * @param {Node} node 要查找的节点
       * @returns {Node|null} 找到的文本节点或null
       */
      function getLastTextNode(node) {
        // 如果当前节点是文本节点，直接返回
        if (node.nodeType === Node.TEXT_NODE) {
          return node;
        }

        // 获取所有子节点并转换为数组
        const childNodeList = Array.from(node.childNodes);

        // 从后往前遍历子节点
        for (let i = childNodeList.length - 1; i >= 0; i--) {
          const child = childNodeList[i];
          // 递归查找子节点中的最后一个文本节点
          const res = getLastTextNode(child);
          if (res) {
            return res;
          }
        }

        // 如果没有找到文本节点，返回null
        return null;
      }

      /**
       * 更新光标位置
       */
      function updateCursor() {
        // 查找最后一个文本节点
        const lastTextNode = getLastTextNode(wrapper);
        // 创建光标节点（竖线符号）
        const curSorNode = document.createTextNode("|");

        // 如果找到文本节点，将光标插入其后
        if (lastTextNode) {
          lastTextNode.after(curSorNode);
        } else {
          // 如果没有文本节点，将光标添加到容器末尾
          wrapper.appendChild(curSorNode);
        }

        // 创建Range对象用于获取光标位置
        const range = document.createRange();
        range.setStart(curSorNode, 0); // 设置Range起点
        range.setEnd(curSorNode, 0); // 设置Range终点
        // 获取光标位置信息
        const rect = range.getBoundingClientRect();
        // 获取容器位置信息
        const wrapperRect = wrapper.getBoundingClientRect();

        // 计算相对于容器的位置
        const left = rect.left - wrapperRect.left;
        const top = rect.top - wrapperRect.top;
        console.log("光标位置:", rect);

        // 创建或更新闪烁圆点
        if (!dot) {
          // 如果圆点不存在，创建新元素
          dot = document.createElement("span");
          dot.className = "blinking-dot";
          document.body.appendChild(dot);
        }

        // 获取圆点尺寸
        const dotRect = dot.getBoundingClientRect();
        // 设置圆点位置：水平位置与光标对齐，垂直位置与光标中心对齐
        dot.style.left = rect.left + "px";
        dot.style.top = rect.top + rect.height / 2 - dotRect.height / 2 + "px";

        // 移除临时光标节点
        curSorNode.remove();
      }

      // 页面加载完成后开始渲染内容
      window.addEventListener("DOMContentLoaded", () => {
        renderContent();
      });
    </script>
  </body>
</html>
```