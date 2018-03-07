# mermaid

## 示例
流程图(flowchart)  
```mermaid
graph LR;
  A-->B;
  A-->C;
  B-->D;
  C-->D;
```

```mermaid {code_block: true}
graph LR;
  A-->B;
  A-->C;
  B-->D;
  C-->D;
```

时序图 (Sequence Diagram)
```mermaid
sequenceDiagram
  Alice->John: Hello John, how are you ?
  John-->Alice:Great!
  Alice->>John: dont borther me !
  John-->>Alice:Great!
  Alice-xJohn: wait!
  John--xAlice: Ok!
```

GANNT Diagram
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

箭头形连接
```mermaid
graph LR
A==>B
```

图表 (带样式)
```mermaid
graph LR
  id1(Start)-->id2(Stop)
  style id1 fill:#f9f,stroke:#333,stroke-width:4px;
  style id2 fill:#ccf,stroke:#f66,stroke-width:2px,stroke-dasharray:5,5;
```

## 导入外部文件 
`@import *.mermaid`
```
@import "mermaid-flow.mermaid"
@import "mermaid-flow.mermaid" {code_block=true class="line-numbers"}
```

#### 流程图 (`mermaid-flow.mermaid`)
@import "mermaid-flow.mermaid"
@import "mermaid-flow.mermaid" {code_block=true class="line-numbers"}

#### 时序图 (`mermaid-sequence.mermaid`)
@import "mermaid-sequence.mermaid"
@import "mermaid-sequence.mermaid" {code_block=true class="line-numbers"}

#### 甘特图 (`mermaid-gantt.mermaid`)
@import "mermaid-gantt.mermaid"
@import "mermaid-gantt.mermaid" {code_block=true class="line-numbers"}

#### 项目计划
@import "mermaid-gantt-E01.mermaid"
