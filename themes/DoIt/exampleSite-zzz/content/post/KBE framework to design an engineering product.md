---
aliases: null
date: 2022-04-04 16:32:00 +02:00
tags: [2022-04]
title: KBE framework to design an engineering product
TOC: true
---

#KBE 
## framework in KBE approach
the problems identification -> steps to solve the problems -> tools/method can be used

## possible objectives
- model represetation:
    - geometric model
    - non-geometric parameters
    - CAD model
    - CAE model
 - ==rule represetation:==
    - the rules/functions in "steps"
- steps:
    - analysis algorithm
    - optimization algorithm
    - data storage and retrieve
    - software integration
    - report generation

## supporting tech
 - KBE app as a ==web application architecture==.
     - database
     - application server
     - user interface: http server
 - KBE app as a =="single big program"==, there should be
    - data structure (the parametric model);
        - parameters + constructor -> a sharable format
    - function declarations: sth. like `c++ virtual function table`;
        - a declaration list -> a sharable format
        - Service-oriented architecture (SoA)
 - model represetation:
    - parameters in knowledge base
    - constructor code in application server
- possible way to ==rule represetation==:
    - function list in knowledge base
    - function code in application server?

## measurement-1: extendibility and interoperability
“International Standard ISO/IEC/IEEE 24765 \[8\] defines 
1. extendibility as “the ==ease== with which a system or component can be modified to ==increase its storage or functional capacity==.”, and 
2. interoperability as “the capability to communicate, execute programs, and transfer data ==among various functional units== in a manner that requires the user to have ==little or no knowledge of the unique characteristics of those units==.”” ([Andrei, p. 2](zotero://select/library/items/X43JKV9N)) ([pdf](zotero://open-pdf/library/items/5BXK7RPF?page=2&annotation=IUTGVT2L))

## KBE framework brainstorm
Like XAML, represent the model in tree, or graph?
KBML: Knowledge-based markup language
There is an interpreter for the model script.
namespace: math, engineering, algorithm and etc.
And the class code behind KBML.
Ontology as "KBML"?
Less render-related code, better.

Use ontology to define a set of interface