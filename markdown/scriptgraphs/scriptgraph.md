
## Script Graphs

Contents
* [概述](#summary)
* [Mermaid Diagrams](#mermaid-example)
* [PlantUML Diagrams](#plantuml-example)
* [Wavedrom Diagrams](#wavedrom-example)
* [Graphviz Diagrams](#graphviz-example)

Online
* [Graphviz](http://www.graphviz.org/)
* [yEd](http://www.yworks.com/products/yed)
* [PlantUML](http://plantuml.com/)
* [mermaid](http://knsv.github.io/mermaid/index.html)
* [yUML](https://yuml.me/)
* [脚本绘图工具总结](http://www.cnblogs.com/hbccdf/p/Script_drawing_tool_summary.html)
* [使用Graphviz绘制流程图和关系图](http://www.tuicool.com/articles/qeqeuyb)
* [使用 Sublime + PlantUML 高效地画图](http://www.cnblogs.com/codingWarrior/p/5147183.html)
* [mermaid简介](http://www.cnblogs.com/wangyaning/p/6131468.html)
* [markdown-preview-enhanced](https://atom.io/packages/markdown-preview-enhanced)
* [CSDN-markdown语法之如何使用LaTeX语法编写数学公式](http://it.taocms.org/03/7247.htm)
* [KaTeX and MathJax Comparison Demo](http://www.intmath.com/cg5/katex-mathjax-comparison.php)

---
<span id="summary"></span>
### 概述
#### PlantUML
* [PlantUML](http://www.plantuml.com/)
* [Using PlantUML](http://plantuml.com/running)
* [Use it with Atom](https://atom.io/packages/plantuml)
* [PlantUML language package for Atom](https://atom.io/packages/language-plantuml)

文件扩展名 `.pu` Atom 中预览 `ctrl-alt-p`。

#### Graphviz
* [Graphviz](http://www.graphviz.org/)
* [graphviz-preview](https://atom.io/packages/graphviz-preview)
* [graphviz-preview-plus](https://atom.io/packages/graphviz-preview-plus)
* [language-dot](https://atom.io/packages/language-dot)

安装 Graphviz，设置  `GRAPHVIZ_DOT` 为 `C:\winapp\graphviz\bin\dot.exe`   

建议使用 `graphviz-preview-plus`
Write and preview GraphViz dot. Shortcut: `ctrl-shift-V`.
Enabled for `.dot` and `.gv` files

#### Mermaid
* [atom-mermaid](https://atom.io/packages/atom-mermaid)
* [markdown-preview-enhanced](https://atom.io/packages/markdown-preview-enhanced)

#### WaveDrom
* [Hitchhiker's Guide to the WaveDrom](http://wavedrom.com/tutorial.html)
* [drom/wavedrom](https://github.com/drom/wavedrom)


----
### Diagrams

<span id="mermaid-example"></span>
#### Mermaid 
流程图(flowchart)  
```mermaid
graph LR;
  A-->B;
  A-->C;
  B-->D;
  C-->D;
```

时序图(sequence diagram)
```mermaid
sequenceDiagram
  participant Alice
  participant Bob
  Alice->John:Hello John, how are you?
  loop Healthcheck
    John->John:Fight against hypochondria
  end
  Note right of John:Rational thoughts <br/>prevail...
  John-->Alice:Great!
　John->Bob: How about you?
　Bob-->John: Jolly good!
```


甘特图(GANNT Diagram)
```mermaid
gantt
  dateFormat　YYYY-MM-DD
  title Adding GANTT diagram functionality to mermaid
  section A section
  Completed task　　:done, des1, 2014-01-06,2014-01-08
  Active task 　　　　:active, des2, 2014-01-09, 3d
  future task 　　　　:　　　  des3, after des2, 5d
  future task2　　　　:　　　  des4, after des3, 5d
  section Critical tasks
  Completed task in the critical line　:crit, done, 2014-01-06,24h
  Implement parser and json　　　　　　:crit, done, after des1, 2d
  Create tests for parser　　　　　　　:crit, active, 3d
  Future task in critical line　　　　　:crit, 5d
  Create tests for renderer　　　　　　:2d
  Add to ,mermaid　　　　　　　　　　　:1d
```

<span id="plantuml-example"></span>
#### PlantUML
Class Digram
```puml
@startuml
abstract class AbstractList
abstract AbstractCollection
interface List
interface Collection

class MyApp {
  name
  date
  run()
  startup()
}

class Product {
  id
  name
  description
}

Product <|-- House
Product <|-- Park
Product <|-- Area

List <|-- AbstractList
Collection <|-- AbstractCollection

Collection <|- List
AbstractCollection <|- AbstractList
AbstractList <|-- ArrayList

class ArrayList {
  Object[] elementData
  size()
}

enum TimeUnit {
  DAYS
  HOURS
  MINUTES
}
@enduml
```

<span id="wavedrom-example"></span>
#### Wavedrom
```wavedrom
{ signal: [
  { name: "CK",   wave: "P.......",                                              period: 2  },
  { name: "CMD",  wave: "x.3x=x4x=x=x=x=x", data: "RAS NOP CAS NOP NOP NOP NOP", phase: 0.5 },
  { name: "ADDR", wave: "x.=x..=x........", data: "ROW COL",                     phase: 0.5 },
  { name: "DQS",  wave: "z.......0.1010z." },
  { name: "DQ",   wave: "z.........5555z.", data: "D0 D1 D2 D3" }
]}
```

<span id="graphviz-example"></span>
#### Graphviz
```viz
digraph G {
subgraph cluster0 {
node [style=filled,color=white];
style=filled;
color=lightgrey;
a0 -> a1 -> a2 -> a3;
label = "process #1";
}
subgraph cluster1 {
node [style=filled];
b0 -> b1 -> b2 -> b3;
label = "process #2";
color=blue
}
start -> a0;
start -> b0;
a1 -> b3;
b2 -> a3;
a3 -> a0;
a3 -> end;
b3 -> end;
start [shape=Mdiamond];
end [shape=Msquare];
}
```
