## Misc (PlantUML)

#### Archimate Diagram

@import "plantuml.puml"


```plantuml {code_block: true}
@startuml
archimate #Technology "VPN Server" as vpnServerA <<technology-device>>

rectangle GO #lightgreen
rectangle STOP #red
rectangle WAIT #orange

@enduml
```

**Defining Junctions**
```plantuml {code_block: true}
@startuml
sprite $bProcess jar:archimate/business-process
sprite $aService jar:archimate/application-service
sprite $aComponent jar:archimate/application-component

rectangle "Handle claim"  as HC <<$bProcess>> #yellow
rectangle "Capture Information"  as CI <<$bProcess>> #yellow
rectangle "Notify\nAdditional Stakeholders" as NAS <<$bProcess>> #yellow
rectangle "Validate" as V <<$bProcess>> #yellow
rectangle "Investigate" as I <<$bProcess>> #yellow
rectangle "Pay" as P <<$bProcess>> #yellow

HC *-down- CI
HC *-down- NAS
HC *-down- V
HC *-down- I
HC *-down- P


CI -right->> NAS
NAS -right->> V
V -right->> I
I -right->> P



rectangle "Scanning" as scanning <<$aService>> #A9DCDF
rectangle "Customer admnistration" as customerAdministration <<$aService>> #A9DCDF
rectangle "Claims admnistration" as claimsAdministration <<$aService>> #A9DCDF
rectangle Printing  <<$aService>> #A9DCDF
rectangle Payment  <<$aService>> #A9DCDF

scanning -up-> CI
customerAdministration  -up-> CI
claimsAdministration -up-> NAS
claimsAdministration -up-> V
claimsAdministration -up-> I
Printing -up-> V
Printing -up-> P
Payment -up-> P

rectangle "Document\nManagement\nSystem" as DMS <<$aComponent>> #A9DCDF
rectangle "General\nCRM\nSystem" as CRM <<$aComponent>> #A9DCDF
rectangle "Home & Away\nPolicy\nAdministration" as HAPA <<$aComponent>> #A9DCDF
rectangle "Home & Away\nFinancial\nAdministration" as HFPA <<$aComponent>> #A9DCDF

DMS .up.|> scanning
DMS .up.|> Printing
CRM .up.|> customerAdministration
HAPA .up.|> claimsAdministration
HFPA .up.|> Payment

legend left
Example from the "Archisurance case study" (OpenGroup).
See
==
<$bProcess> :business process
==
<$aSrv> : application service
==
<$aComp> : appplication component
endlegend
@enduml
```


**List possible sprites**
```plantuml {code_block: true}
@startuml
listsprite
@enduml
```


#### Ditaa

```plantuml {code_block: true}
@startditaa
+--------+   +-------+    +-------+
|        +---+ ditaa +--> |       |
|  Text  |   +-------+    |diagram|
|Document|   |!magic!|    |       |
|     {d}|   |       |    |       |
+---+----+   +-------+    +-------+
    :                         ^
    |       Lots of work      |
    +-------------------------+
@endditaa
```

```plantuml {code_block: true}
@startuml
ditaa(--no-shadows, scale=0.8)
/--------\   +-------+
|cAAA    +---+Version|
|  Data  |   |   V3  |
|  Base  |   |cRED{d}|
|     {s}|   +-------+
\---+----/
@enduml
```




#### Timing Diagram
```plantuml
@startuml
title Timing Diagram

robust "Web Browser" as WB
concise "Web User" as WU

WB is Initializing
WU is Absent

@WB
0 is idle
+200 is Processing
+100 is Waiting
WB@0 <-> @50 : {50 ms lag}

@WU
0 is Waiting
+500 is ok
@200 <-> @+150 : {150 ms}

@enduml
```
