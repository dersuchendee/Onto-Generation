<div align="center">
 <img src="Images/ontology-generation-with-llms-high-resolution-logo.png" style="width: 40%; height: auto;"/>
 <H1>Ontology Generation with LLMs</H1>

[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit)](https://github.com/pre-commit/pre-commit)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![Imports: isort](https://img.shields.io/badge/%20imports-isort-%231674b1?style=flat&labelColor=ef8336)](https://pycqa.github.io/isort/)

</div>


This repository contains supplementary materials for the submitted paper in ESWC 2025 - Research Track. Our work improved ontology generation performance using two new prompting techniques. In this repository you can find details of the prompts, the dataset and other material for the evaluation and some details that could not be fit in the page count : )




---



![alt text](Images/digram.png)
_Illustration of the two OWL generation settings and the four evaluations for assessing the generated OWL. The top one generates an OWL concerning only one CQ. The middle one generates an OWL concerning CQs associated with one narrative. At the bottom, four OWL evaluations setups including OOPS!, proportion of modelled CQs, statistics of superfluous components and expert evaluation are shown._




## Contents of the directory

|   | Title                                                      |                         Description                         |
|:-:|:-----------------------------------------------------------|:---------------------------------------------------------:|
| 1 | [Dataset for Ontology generation](/Dataset_OntoGen)          |   The dataset used in this work.                                  |
| 2 | [Experiment Results](/Experiment Results)  |           The results of the experiments for what concerns the evaluation.            |
|3 | [Generated OWLs](/Generated OWLs)       |             Generated OWL files for Ontogenia and MemlessCQbyCQ.              |
|4| [Prompting Techniques](/Prompting Techniques)|              The code to execute the two prompting techniques.              |
|5|  [Images](/Images)      |                     Images used for the README and the paper.   

## Prompting techniques
The prompting techniques used in this work can be found [here](/PromptingTechniques)


## Dataset
The dataset is publicly available for future ontology generation processes. To prevent LLMs from including this dataset in their training data, it is zipped and locked with a publicly available password.

password for zipped files: 28mRFhW6wVnu7Wh


## GPT-4 hyperparameters
Our work used GPT-4 API version 1106, with the model's hyperparameters (frequency\_penalty, presence\_penalty, and temperature) set to zero. In fact, these hyperparameters manage the model's output. The frequency\_penalty penalizes frequently seen words during training, while the presence\_penalty penalizes words present in the current context when generating the next token. High temperature forces the model to be creative. This is to make sure if a prefix/class/property is defined in the ontology, in the future, LLM can use it again and not penalized.

# License
<p>
  This software is licensed under the
  <a href="https://opensource.org/licenses/MIT" target="_blank">MIT License</a>.
</p>
<a href="https://opensource.org/licenses/MIT" target="_blank">
  <img src="https://img.shields.io/badge/License-MIT-blue.svg" alt="MIT License">
</a
