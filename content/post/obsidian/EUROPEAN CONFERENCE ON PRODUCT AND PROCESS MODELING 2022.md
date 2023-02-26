---
date: 2022-09-18 16:30:39 +02:00
tags:  [2022-09]
title: EUROPEAN CONFERENCE ON PRODUCT AND PROCESS MODELING 2022
TOC: true
---


# EUROPEAN CONFERENCE ON PRODUCT AND PROCESS MODELING 2022
https://www.ecppm2022.org/
## BIM
BIM (building information modeling), a digitalization trend in construction area. And I think in that logic, our work can be called "product information/knowledge modeling". I think it is useful to learn how they balance the platform and problem domain in their job and how their work contributes to the platform.

## 讨论后的一些收获
- IFC (Industry Fundimental Class) 不能涵盖施工阶段的信息，因为施工阶段的有些信息不是显式的，不易表达，还有一些与设计与结构阶段耦合，所以难以全部数字化。
- 对于系列化的、较标准的建筑，设计与结构阶段已有较高的数字化，似乎也有一定程度的自动化，但似乎还是没有大规模应用，因为还有一些阶段并没有集成起来。
- 我的机械产品设计流程数字化可以提供一个平台，将各个操作数字化、规范化，能自动化的就自动化、不能的交给人工，人工处理完后继续回到自动化流程。
- 通过问卷得到机械产品的设计工作流。
- 规则分类：![[EUROPEAN CONFERENCE ON PRODUCT AND PROCESS MODELING 2022#A multi-representation method of building rules for automatic code compliance checking]]


## Ontology-related BIM
the Ontology building process in the third paper is undergoing.
They also see Ontology as tagged data, and the important part is also the algorithm part.

==Some of the calculation is moved into SPARQL query part.==


## 对部分论文的认识
### 基于GAN的塔吊布局优化
解是256X256的位图，解析该位图得到合适的塔吊位置。塔吊设计除了选址还有工作路径设计。

### 3D点云目标识别
类似于图片内容识别。

### 基于NLP的自然语言项目文本信息提取
无监督学习的accuracy不高，适合要求不高的场景，本文主要目标是给文档分类、加标签，方便日后查找。对于accuracy要求高的需要用有监督学习的方法。

### A ontology-based experty system for quality inspection planning in the construction execution
4 levels automation rule inspection, rule design and validation is very important step.

### Plenary 2: Arto Kiviniemi  - AECOO Education – Do we need a new teaching paradigm?
- move from traditional hand drawing to digitalised design.
- main benefits require collabration
    - greatly helps the employee training
    - More BIM use brings less change orders?
- design stage determines a lot of value in the following stages.
- performance of a building: salaries/92%, running cost 6%, capital cost 2%, energy 1.3%.
- document centric process = incoherent document, different views and different abilities to understand.
- better communication and shared understanding
- What is a model? 
    - A model represents reality for the given purpose; the model is ==an abstraction of reality in the sense that it cannot represent all aspects of reality==. Jeff Rothenberg "Al, Simulation & Modeling" 1989
    - Different domains (architectural design, structural and HVAC engineering, construction tasks, FM...) have different models ==because they perform different tasks==. 
    - The shared models must cover (at least) the parts necessary for the desired purpose(s) such as design coordination.
    - However, defining the content and representation in a homogeneous way is not a simple task because the content and representations in different domain models are very different.
- Conclusions: identify the valuable core of the different professions and separate it from "old rubbish". We must digitalise the documents.

### EAPPM Assembly Meeting (Hybrid)
- sustainability related topic, entails inforamtion exchange.
- The EAPPM is an organisation for institutions from academia, research and industry that promote the application of Computer Science and Information Technology Methods in the Building Industry.

### GreenBIM - fundamentals for the integration of building greening in openBIM projects.
to plant green plants in the buildings. 
BIM software: Autodesk Revit is a building information modelling software tool for architects, landscape architects, structural engineers, mechanical, electrical, and plumbing (MEP) engineers, designers and contractors. 

### Artificially Intelligent Classification of Structural Plans for Automated Schematic Design of Reinforced Concrete Structures
- Why AI? 
many feasible solutions; many constraints; complicated for classical programming; Data structured as graphs -> GNNs
- Research goal
showing feasibility of automated preliminary structural design using GNNs and engineers' experience.
- Roadmap
structural plans (PDF) -> floor models (Revit) -> engineers' chanllenges (Web) -> graph raw data (CSV)
- Survey to get human engineers' comments on the elements in the PDF. https://alonf4.wixsite.com/aipdorcs/engineers-survey
- How to represent the complex model?
    - model: graph ID, number of floors, Gross height, Building types
    - ==structural element==: element ID, deoth, width, length, comments, comment score, concrete volume
    - column, wall, beam, slabs. model data, model manipulation
- Structural plans classification
        - feedback from engineers
        - data consolidation
        - graphs from CSVs
        - Graph Neutral Networks
        - Automatic classfication
        - new design generation
- Question, what is the catalog? the score, good/bad design?

### Plenary 4: Ardeshir Mahdavi - Why we need "HIM" in BIM
该教授强调Ontology是个schema，强调reuse、以及与其他Ontology的联系。

### An ontology-supported case-based reasoning approach for damage assessment
- How computer-based damage assessment should be?
Drones or other sensors collect the data in fields, and humans assess in the office.
- Problems:
    - huge amount of training needed
    - current approach focus on detection only consider explict damage attributes.
- ==Abox and Tbox==
    - https://en.wikipedia.org/wiki/Abox
    - TBox statements are sometimes associated with object-oriented classes and ABox statements associated with instances of those classes.
    - Tbox: BOT, BEO, DOT, AOI
- knowledge vs experience (ML)
    - develop the knowledge is time-consuming, while preparation of the data is the same.
- case-based reasoning as hybrid solution
    - retrieve similar cases via SPARQL
    - reuse of most similar case and adaption of it to the current problem.
    - revise solved case via checking rules
    - retain solved and checked case into case base
- How?
    - define Tbox
    - define similarity: `A x B -> [0,1]`
```sparql
SELECT ?damage ?similarity
WHERE {
    ?beam rdf:type beo:beam.
    ?beam dot:hasDamage ?damage.
    ?damage rdf:type cdo:Crack.
    ?damage cdo:crackLengh ?cl.
    BIND (ABS(?cl-0.58)*0.2) AS ?weightedCL.
    ?damage cdo:crackWidth ?cw.
    BIND (ABS(?cackWidh-0.47)*0.3) AS ?weightedCW.
    ?damage cdo:cracAngle ?crackAngle.
    BIND (ABS((?crackAngle-85.62)*0.2) AS ?weightedCA. 
    BIND (ABS(?weightedCL * ?weightadCW * ?weightedCA AS ?similarity).
}
ORDER BY ASC(?similarity)
```

- Conlusions:
    - case base is structured as OWL ontology
    - No static structure and differentation between problem and solution
    - problem and solution are defined in SPAQL queries

### Predicting semantic building information (BIM) with Recurrent Neural Networks
predict the current and consective design phase? also temparal parameters.

### A multi-representation method of building rules for automatic code compliance checking
- 将rules分类：能自动翻译的、不能自动翻译的，rule complexity。class1: 基于少量显式数据;class2: 基于简单的推导数据;class3: 基于拓展的数据结构;class4: corrective actions and solutions; 每种rule的合适的表达形式是什么？
- 新分类
    - manual checking: related to quality or aesthetics
    - auto checking: ?
- use a complex "rule"(multi-representation) to represent the rules.
- now only focus the ones that are labelled as "can be auto-checked"

### Training on digitized building regulations for automated rule extraction
规则自动翻译成一种形式语言

### Process-based building permit review – a knowledge engineering approach
一种形式语言，将评审过程表达出来，以图自动推理。

### Closing Session
- a professor postion available: digitalization of construction processes.
- people integrating product and process modelling
- data-driven decisions
