## PlantUML Diagrams

```plantuml
@startuml
checkversion
@enduml
```

---
#### Use Case Diagram
```plantuml
@startuml
skinparam usecase {
  ArrowColor Black
  BackgroundColor WhiteSmoke
  BorderColor Silver
  ActorBackgroundColor WhiteSmoke
  ActorBorderColor Silver
}
skinparam ActorBorderColor DimGray
skinparam ActorBackgroundColor WhiteSmoke

left to right direction
actor customer
actor clerk
rectangle checkout {
  customer -- (checkout)
  (checkout) .> (payment) : include
  (help) .> (checkout) : extends
  (checkout) -- clerk
}
@enduml
```

```plantuml
@startuml
skinparam usecase {
  ArrowColor Black
  ActorBorderColor Silver
  ActorBackgroundColor WhiteSmoke
  BackgroundColor WhiteSmoke
  BorderColor Silver
}
skinparam ActorBorderColor Brown
skinparam ActorBackgroundColor Cornsilk
skinparam handwritten true

User <<Human>>
:Main Database: as MySql <<Application>>
(Start) <<One Shot>>
(Use the application) as (Use) <<Main>>
User -> (Start)
User --> (Use)
MySql --> (Use)
@enduml
```


---
#### Activity Diagram
```plantuml
@startuml
skinparam activity {
  ArrowColor black 
  BackgroundColor WhiteSmoke
  BorderColor Gray
  StartColor DimGray
  EndColor DimGray
  BarColor Yellow
  DiamondBorderColor Gray
  DiamondBackgroundColor WhiteSmoke
}
skinparam handwritten true

start
:ClickServlet.handleRequest();
:new page;
if (Page.onSecurityCheck) then (true)  
:Page.onInit();
if (isForward?) then (no)
:Process controls;
if (continue processing?) then (no)
stop
endif

if (isPost?) then (yes)
:Page.onPost();
else (no)
:Page.onGet();
endif
:Page.onRender();
endif
else (false)
endif

if (do redirect?) then (yes)
:redirect process;
else
if (do forward?) then (yes)
:Forward request;
else (no)
:Render page template;
endif
endif

stop
@enduml
```


---
#### Sequence Diagram
```plantuml
@startuml
skinparam sequence {
  ArrowColor Black
  ActorBackgroundColor WhiteSmoke
  ActorBorderColor Gray
  GroupBackgroundColor WhiteSmoke
  LifeLineBackgroundColor WhiteSmoke
  LifeLineBorderColor Gray
  ParticipantBackgroundColor WhiteSmoke
  ParticipantBorderColor Gray
}

title Sequence - Servlet Container

actor User #Orange
participant "First Class" as A  
participant "Second Class" as B #Yellow
participant "Last Class" as C

autonumber 1
User -[#darkred]> A: DoWork
activate A

A -> B: Create Request
activate B #Pink

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

---
#### Class Diagram
```plantuml
@startuml
skinparam class {
  BackgroundColor WhiteSmoke 
  ArrowColor DimGray
  BorderColor Silver
}

skinparam stereotype {
  CBackgroundColor WhiteSmoke
  IBackgroundColor Azure
  ABackgroundColor FloralWhite
  EBackgroundColor GhostWhite
}

title Class Diagrams

abstract class AbstractList #Beige
abstract AbstractCollection
interface List
interface Collection

class MyApp {
  name
  date
  run()
  startup()
}

class Product #Moccasin {
}

class House {
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


---
## PlantUML File List
#### `scriptgraphs/pu-class.puml`
@import "scriptgraphs/pu-class.puml"

---
#### `scriptgraphs/pu-sequence.puml`
@import "./scriptgraphs/pu-sequence.puml"

---
#### `scriptgraphs/pu-activity.puml`
@import "./scriptgraphs/pu-activity.puml"

---
#### `scriptgraphs/pu-usecase.puml`
@import "./scriptgraphs/pu-usecase.puml"
