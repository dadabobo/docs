## Markdown 语法
* [Markdown 语法说明](http://wowubuntu.com/markdown/)
* [Markdown语法说明（详解版）](http://www.ituring.com.cn/article/504)


#### 标题
分为六级
````
# 一级标题
## 二级标题
...
###### 六级标题
````

#### 锚点
[页内链接](#user-content-1);

#### 引用
> 这是第一级引用。
>
>> 这是第二级引用。  
>
> 现在回到第一级引用。

#### 列表
- Red
- Green
- Blue

列表
* 红
* 绿
* 蓝

有序列表
1. Red
2. Green
3. Blue

代办列表: 表示列表是否勾选状态
- [ ] 不勾选
- [x] 勾选

#### 复选框
使用 `- [ ]` 和 `- [x]` 语法可以创建复选框，实现 todo-list 等功能。例如：

- [x] 已完成事项
- [ ] 待办事项1
- [ ] 待办事项2


#### 代码
```ruby
require 'redcarpet'
markdown = Redcarpet.new("Hello World!")
puts markdown.to_html
```

<span id="user-content-1"></span>
#### 强调
在Markdown中，可以使用 `*` 和 `_` 来表示斜体和加粗。

*这是斜体*  
_这是斜体_   

**这是加粗**
__这是加粗__


#### 自动链接
方括号显示说明，圆括号内显示网址， Markdown 会自动把它转成链接，例如：
`[超强大的云开发平台Coding](http://coding.net)`

[超强大的云开发平台Coding](http://coding.net)

或者也可以直接用< >，将网址或者邮箱地址放在中间，也能将地址直接转成链接：
`<support@coding.net>`  
<support@coding.net>

#### 表格
```
First Header | Second Header | Third Header
------------ | ------------- | ------------
Content Cell | Content Cell  | Content Cell
Content Cell | Content Cell  | Content Cell
```

First Header | Second Header | Third Header
------------ | ------------- | ------------
Content Cell | Content Cell  | Content Cell
Content Cell | Content Cell  | Content Cell

或者也可以让表格两边内容对齐，中间内容居中，例如：
```
First Header | Second Header | Third Header
:----------- | :-----------: | -----------:
Left         | Center        | Right
Left         | Center        | Right
```

First Header | Second Header | Third Header
:----------- | :-----------: | -----------:
Left         | Center        | Right
Left         | Center        | Right

#### 分割线
---

#### 上下标
`^`表示上标, `_`表示下标。如果上下标的内容多于一个字符，要用`{}`把这些内容括起来当成一个整体。上下标是可以嵌套的，也可以同时使用。 例如：

x_y

hello[^hello]

[^hello]: hi


#### LaTeX 公式
可以创建行内公式，例如 `$\Gamma(n) = (n-1)!\quad\forall n\in\mathbb N$` $\Gamma(n) = (n-1)!\quad\forall n\in\mathbb N$ 。或者块级公式：

```
$$	x = \dfrac{-b \pm \sqrt{b^2 - 4ac}}{2a} $$

\[
\xi^2+\eta^2+\zeta^2={\rm R}^2
\]
```
$$	x = \dfrac{-b \pm \sqrt{b^2 - 4ac}}{2a} $$

\[
\xi^2+\eta^2+\zeta^2={\rm R}^2
\]


#### 流程图
```
st=>start: Start
e=>end
op=>operation: My Operation
cond=>condition: Yes or No?

st->op->cond
cond(yes)->e
cond(no)->op
```
```flow
st=>start: Start
e=>end
op=>operation: My Operation
cond=>condition: Yes or No?

st->op->cond
cond(yes)->e
cond(no)->op
```

以及时序图:
```
Alice->Bob: Hello Bob, how are you?
Note right of Bob: Bob thinks
Bob-->Alice: I am good thanks!
```
```sequence
Alice->Bob: Hello Bob, how are you?
Note right of Bob: Bob thinks
Bob-->Alice: I am good thanks!
```

[1]: http://maxiang.info/
[2]: https://chrome.google.com/webstore/detail/kidnkfckhbdkfgbicccmdggmpgogehop
[3]: http://adrai.github.io/flowchart.js/
[4]: http://bramp.github.io/js-sequence-diagrams/
[5]: https://dev.yinxiang.com/doc/articles/enml.php


------
## 欢迎使用 Cmd Markdown 编辑阅读器
我们理解您需要更便捷更高效的工具记录思想，整理笔记、知识，并将其中承载的价值传播给他人，**Cmd Markdown** 是我们给出的答案 —— 我们为记录思想和分享知识提供更专业的工具。 您可以使用 Cmd Markdown：
> * 整理知识，学习笔记
> * 发布日记，杂文，所见所想
> * 撰写发布技术文稿（代码支持）
> * 撰写发布学术论文（LaTeX 公式支持）

![cmd-markdown-logo](https://www.zybuluo.com/static/img/logo.png)

除了您现在看到的这个 Cmd Markdown 在线版本，您还可以前往以下网址下载：

#### [Windows/Mac/Linux 全平台客户端](https://www.zybuluo.com/cmd/)
> 请保留此份 Cmd Markdown 的欢迎稿兼使用说明，如需撰写新稿件，点击顶部工具栏右侧的 <i class="icon-file"></i> **新文稿** 或者使用快捷键 `Ctrl+Alt+N`。

------
### 什么是 Markdown
Markdown 是一种方便记忆、书写的纯文本标记语言，用户可以使用这些标记符号以最小的输入代价生成极富表现力的文档：譬如您正在阅读的这份文档。它使用简单的符号标记不同的标题，分割不同的段落，**粗体** 或者 *斜体* 某些文字，更棒的是，它还可以

#### 1. 制作一份待办事宜 [Todo 列表](https://www.zybuluo.com/mdeditor?url=https://www.zybuluo.com/static/editor/md-help.markdown#13-待办事宜-todo-列表)
- [ ] 支持以 PDF 格式导出文稿
- [ ] 改进 Cmd 渲染算法，使用局部渲染技术提高渲染效率
- [x] 新增 Todo 列表功能
- [x] 修复 LaTex 公式渲染问题
- [x] 新增 LaTex 公式编号功能

#### 2. 书写一个质能守恒公式[^LaTeX]
`$$E=mc^2$$`

$$E=mc^2$$

#### 3. 高亮一段代码[^code]
```python
@requires_authorization
class SomeClass:
    pass

if __name__ == '__main__':
    # A comment
    print 'hello world'
```

#### 4. 高效绘制 [流程图](https://www.zybuluo.com/mdeditor?url=https://www.zybuluo.com/static/editor/md-help.markdown#7-流程图)
```flow
st=>start: Start
op=>operation: Your Operation
cond=>condition: Yes or No?
e=>end

st->op->cond
cond(yes)->e
cond(no)->op
```

#### 5. 高效绘制 [序列图](https://www.zybuluo.com/mdeditor?url=https://www.zybuluo.com/static/editor/md-help.markdown#8-序列图)
```seq
Alice->Bob: Hello Bob, how are you?
Note right of Bob: Bob thinks
Bob-->Alice: I am good thanks!
```

#### 6. 高效绘制 [甘特图](https://www.zybuluo.com/mdeditor?url=https://www.zybuluo.com/static/editor/md-help.markdown#9-甘特图)
```gantt
    title 项目开发流程
    section 项目确定
        需求分析       :a1, 2016-06-22, 3d
        可行性报告     :after a1, 5d
        概念验证       : 5d
    section 项目实施
        概要设计      :2016-07-05  , 5d
        详细设计      :2016-07-08, 10d
        编码          :2016-07-15, 10d
        测试          :2016-07-22, 5d
    section 发布验收
        发布: 2d
        验收: 3d
```

#### 7. 绘制表格
| 项目        | 价格   |  数量  |
| --------   | -----:  | :----:  |
| 计算机     | \$1600 |   5     |
| 手机        |   \$12   |   12   |
| 管线        |    \$1    |  234  |

#### 8. 更详细语法说明

想要查看更详细的语法说明，可以参考我们准备的 [Cmd Markdown 简明语法手册][1]，进阶用户可以参考 [Cmd Markdown 高阶语法手册][2] 了解更多高级功能。

总而言之，不同于其它 *所见即所得* 的编辑器：你只需使用键盘专注于书写文本内容，就可以生成印刷级的排版格式，省却在键盘和工具栏之间来回切换，调整内容和格式的麻烦。**Markdown 在流畅的书写和印刷级的阅读体验之间找到了平衡。** 目前它已经成为世界上最大的技术分享网站 GitHub 和 技术问答网站 StackOverFlow 的御用书写格式。


[1]: https://www.zybuluo.com/mdeditor?url=https://www.zybuluo.com/static/editor/md-help.markdown
[2]: https://www.zybuluo.com/mdeditor?url=https://www.zybuluo.com/static/editor/md-help.markdown#cmd-markdown-高阶语法手册