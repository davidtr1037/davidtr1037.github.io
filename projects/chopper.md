---
layout: page
title: Chopper
permalink: /chopper/
---
_Chopped symbolic execution_ is a technique that allows the user to skip functions during the analysis.
To preserve soundness, the technique relies on static pointer analysis to check whether a function can be skipped entirely or not.
If not, then the skipped function is re-executed, while applying static slicing to ignore irrelevant instructions.
The tool is implemented using three open source projects: 
- KLEE
- SVF
- DG

The code is available [here](https://github.com/davidtr1037/chopper).  
The instructions for replicating our experiments are available [here](https://srg.doc.ic.ac.uk/projects/chopper/artifact.html).
