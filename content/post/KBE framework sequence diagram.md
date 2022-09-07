---
date: "2022-08-08"
tags: ["2022-08"]
title: "A low-code KBE framework"
toc: true
----


# A low-code KBE framework

## Some reference
The low code webinar and a brochure.
How the Composable Enterprise Will Accelerate Digital Transformation in the Automotive Industry[EB/OL]//Mendix. [2022-08-30]. https://www.mendix.com/resources/how-the-composable-enterprise-will-accelerate-digital-transformation-in-the-automotive-industry/.


### Digitalisation personnel
Gartner reports that 
- business technologists—employees who report outside of IT departments and create technology or analytics capabilities for work—now make up ==41%== of digital technologists.
- Another ==49%== are technology end users, 
- leaving only ==10%== inside the IT department.

### How does the manufacturing team create its personalized application? 

The low-code platform provides assembly tools for this task: 
- ==Templates== are prepackaged frameworks that provide a starting point for the application. In a few simple visual steps, the template can be customized to address a specific need. 
- ==App services== provide domain-specific building blocks packaged in cloud services. 
- ==Integrations== enable users to reach specific data and pull it into a workflow or experience.

## The purpose
A low-code framwwork to automate the design process as much as possible, e.g.:
- rapid generate the variants of series products,
- integrate different tools,
- rapid generate the secondary components after the main components are determined,
- ...

## Some expected features
- Flexible GUI, or low-code coordinator of orchestration
- SOA for integration of different tools
- Knowledge base for unified data storage
- ...

## What knowledge to capture?
Before writing the application, what do the engineers have?
These are called raw files:
- the CAD model files (DFA file), 
- some calaculation (excel, matlab/python scripts), 
- CAE model files (Ansys, abaqus),
- some design reports.

Geometric and non-geometric knowledge are contained in the raw files.

## How to formalize? What does the knowledge in KB look like?
Make the knowledge in those files **semantic**.
Maybe organised/ classified by sub-system or working group.
The knowledge is connected to each component.

## Some assumptions
- the CAD models are established in parametric formats, e.g. DFA file.
- the non-geometric parameters are stored in reports or ?

## The difficulties
1. The magic 1
- How to formalise the knowledge in raw files.
    - The parameters:
        - the geometric parameters in CAD files
        - the non-geometric parameters in reports ?
    - The rules(calculation) used to determine the parameters.
- What forms are the knowledge stored? Or how to organise the knowledge?
    [[# How to formalise? What does the knowledge in KB look like?]]

2. The magic 2
- How do the users use this application? How does the application interact with the user?


## The architecture
https://app.diagrams.net/#G1xU_qLjZD-mErccbckfeYHtZaPTjxmR3K

## The UML sequence diagram
```plantuml
@startuml "A KBE framework scenario"
' skinparam backgroundColor #EEEBDC
skinparam handwritten false
skinparam style strictuml

'to request GUI for partX
actor       "Developer/Engineer"    as developer
actor       "User/Engineer"    as user
participant "Web browser"    as client
participant "http service\n(generate HTML of GUI)"    as httpServer
participant "application service\n(calculators)"    as appServer
participant "file service\n(calculators)"    as fileServer
participant "Knowledge base\n(Fuseki)"    as KB

'developing engineer prepare the knowledge
'extract the parameters from files e.g., CAD and reports?
developer -> appServer  : Upload the raw files of the prototype, (CAD models, reports?).
appServer -> KB: (The magic 1) The formalisation service extracts/formalises the knowledge, \n including geometric and non-geometric knowledge.
developer -> appServer  : Convert the calculation to web services.
developer -> httpServer: (The magic 2) Prepare the HTML template, \n working as the coordinator of orchestration.

'user rquest GUI
user -> client  :  Request GUI URL: /designer/partX.
client -> httpServer    :   GET /designer/partX.
httpServer -> KB    :   Send SPARQL to get \n the parameters for \n current GUI (/designer/partX).
activate KB
KB -> httpServer    : Return the parameters \n (name and demo value) for current GUI.
deactivate KB

httpServer -> httpServer : Generate the HTML for /designer/partX.
httpServer -> client : HTML for /designer/partX.
client -> user: Webpage(GUI) for /designer/partX.

'user clicks "calculate"
user -> client  :   User clicks "calculate/optimise".
client -> httpServer    :   POST the parameters \n (name and input value) \n and the name of calculation.

httpServer -> appServer: [SOAP] Call the calculation service \n using the parameter values.

activate appServer
appServer -> appServer    :  Do the calculation.
appServer -> fileServer : The useful files generated, \n e.g. DFA file, preview figure, report.
appServer -> KB : Send SPARQL to write \n the parameters and result \n into current product.
appServer -> httpServer : [SOAP] Send the result.
deactivate appServer

httpServer -> httpServer : Generate the HTML for /designer/result.
httpServer -> client : HTML for /designer/result.
client -> user: Webpage(GUI) for /designer/result.

user -> client: Click "download XX files", e.g. DFA file, preview figure, report.
client -> fileServer: GET XX files.
fileServer -> client: The files.

'user add new parameters for the parametric model
user -> KB : Send SPARQL to add new parameters and properties into KB. \n The properties are meta-data, related to GUI and calculation. This is why it is flexible: user not the developer can edit.

@enduml
```

--------------------
- The order making scenario
```plantuml
@startuml "The order making scenario"
' skinparam backgroundColor #EEEBDC
skinparam handwritten false
skinparam style strictuml

actor       Customer    as customer
participant "Client\n(Web browser\n&CAD)"    as client
participant "Design server"    as server
participant "Knowledge base\n(Fuseki)"    as KB

customer -> client  :   http://127.0.0.1:1234\nhttp://127.0.0.1:1234/pipe_router
client -> server    :   GET /pipe_router
activate server
server -> server    :   Do_GET(self)
server --> client   :   HTML for /pipe_router
deactivate server
client --> customer :   web page for /pipe_router

customer -> client  :   Input and submit parameter values
client -> server    :   POST /pipe_router\n ?paras=InputValues
activate server
server -> server    :   Do_POST(self)
server -> server    :   parse parameters
server -> server    :   call GA to generate\n the best solution
server -> KB++      :   POST /update\n?paras=parametric model
KB -> KB            :   SPARQL\n update
return result for\nSPARQL update
server -> server    :   generate the models \nrepresented in \nDFA, MAC, javaScript\nfrom template\nand return the webpage
server --> client   :   viewer HTML for /pipe_router\n containing the model in \nDFA, MAC and JavaScript
deactivate server
client --> customer :   The visualized model\n and DFA, MAC file

customer -> client  :   http://127.0.0.1:1234/viewer\n?productID=p001
client -> server    :   GET /viewer\n ?productID=p001
activate server
server -> KB++      :   POST /query\n ?paras=productID
KB -> KB            :   SPARQL\n query
return result for SPARQL query
server -> server    :   parse the result\n to generate the model
server --> client   :   viewer HTML for /pipe_router \ncontaining the model in \nDFA, MAC and JavaScript
deactivate server
client --> customer :   The visualized model\n and DFA, MAC file
@enduml
```
