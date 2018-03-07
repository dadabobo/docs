## PlantUML Diagrams

---
#### scriptgraphs/pu-version.puml
@import "scriptgraphs/pu-version.puml"
@import "scriptgraphs/pu-version.puml" {code_block=true class="line-numbers"}

---
#### Use Case
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

```plantuml {code_block=true class="line-numbers"}
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
#### scriptgraphs/pu-class.puml
@import "scriptgraphs/pu-class.puml"
@import "scriptgraphs/pu-class.puml" {code_block=true class="line-numbers"}

---
#### scriptgraphs/pu-sequence.puml
@import "scriptgraphs/pu-sequence.puml"
@import "scriptgraphs/pu-sequence.puml" {code_block=true class="line-numbers"}

---
#### scriptgraphs/pu-activity.puml
@import "scriptgraphs/pu-activity.puml"
@import "scriptgraphs/pu-activity.puml" {code_block=true class="line-numbers"}

---
#### scriptgraphs/pu-usecase.puml
@import "scriptgraphs/pu-usecase.puml"
@import "scriptgraphs/pu-usecase.puml" {code_block=true class="line-numbers"}