## Markdown Diagram

#### 流程图
```
st=>start: Start:>http://www.bing.com[blank]
e=>end:>http://www.bing.com
op1=>operation: My Operation
sub1=>subroutine: My Subroutine
cond=>condition: Yes or No?:>http://www.bing.com
io=>inputoutput: catch something...

st->op1->cond
cond(yes)->io->e
cond(no)->sub1(righy)->op1
```

```flow
st=>start: Start:>http://www.bing.com[blank]
e=>end:>http://www.bing.com
op1=>operation: My Operation
sub1=>subroutine: My Subroutine
cond=>condition: Yes or No?:>http://www.bing.com
io=>inputoutput: catch something...

st->op1->cond
cond(yes)->io->e
cond(no)->sub1(right)->op1
```

#### 时序图
```
Alice->Bob: Hello Bob, how are you?
Note right of Bob: Bob thinks
Bob-->Alice: How are you?
Alice->>Bob: I am good thanks!
```

```sequence 
Alice->Bob: Hello Bob, how are you?
Note right of Bob: Bob thinks
Bob-->Alice: How are you?
Alice->>Bob: I am good thanks!
```

```sequence {theme="hand"}
Alice->Bob: Hello Bob, how are you?
Note right of Bob: Bob thinks
Bob-->Alice: How are you?
Alice->>Bob: I am good thanks!
```

### Mermaid
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

#### PlantUML
Class Digram
```puml
@startuml
abstract class AbstractList
abstract AbstractCollection
interface List
interface Collection

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

Sequence Digram
```puml
@startuml
skinparam monochrome true
actor User
participant "First Class" as A
participant "Second Class" as B
participant "Last Class" as C

User -> A: DoWork
activate A
A -> B: Create Request
activate B
B -> C: DoWork
activate C
C --> B: WorkDone
destroy C
B --> A: Request Created
deactivate B
A --> User: Done
deactivate A
@enduml
```

Timing Digram
@import "./scriptgraphs/puml-timing.puml"

#### WaveDrom
```wavedrom
{ signal: [
  { name: "clk",         wave: "p.....|..." },
  { name: "Data",        wave: "x.345x|=.x", data: ["head", "body", "tail", "data"] },
  { name: "Request",     wave: "0.1..0|1.0" },
  {},
  { name: "Acknowledge", wave: "1.....|01." }
]}
```

#### GraphViz
@import "./scriptgraphs/graphviz.dot" {as=viz}
