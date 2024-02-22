# AnalogNAS

## Description
**AnalogNas** is a modular and flexible framework to facilitate the implementation of Analog-aware Neural Architecture Search. It offers high-level classes to define: the search space, the accuracy evaluator, and the search strategy. It leverages [the aihwkit framework](https://github.com/IBM/aihwkit) to apply hardware-aware training with analog non-idealities and noise included. **AnalogNAS** obtained architectures are more robust during inference on Analog Hardware. We also include two evaluators trained to rank the architectures according to their analog training accuracy. 

> :warning: This library is currently in beta and under active development.
> Please be mindful of potential issues and monitor for improvements,
> new features, and bug fixes in upcoming versions.

[**Setup**](#setup)
| [**Usage**](#usage)
| [**Docs**](https://github.com/IBM/analog-nas/blob/main/starter_notebook.ipynb)
| [**References**](#references)

## Features 
AnalogaiNAS package offers the following features: 

* A customizable resnet-like search space, allowing to target CIFAR-10, Visual Wake Words, and Keyword Spotting 
* A configuration space object allows to add any number or type of architecture and training hyperparameters to the search 
* An analog-specific evaluator which includes: 
  * An 1-day accuracy ranker 
  * An 1 month accuracy variation estimator 
  * A 1-day standard deviation estimator 
* A flexible search algorithm, enabling the implementation and extension of state-of-the-art NAS methods. 

## Structure 
In a high-level AnalogAINAS consists of 4 main building blocks which (can) interact with each other:

* Configuration spaces (```search_spaces/config_space.py```): a search space of architectures targeting a specific dataset.
* Evaluator (```evaluators/base_evaluator.py```): a ML predictor model to predict: 
    * 1-day Accuracy: the evaluator models the drift effect that is encountered in Analog devices. The accuracy after 1 day of drift is then predicted and used as an objective to maximize. 
    * The Accuracy Variation for One Month (AVM): The difference between the accuracy after 1 month and the accuracy after 1 sec. 
    * The 1-day accuracy standard deviation: The stochasticity of the noise induces different variation of the model's accuracy depending on its architecture. 
    
    The weights of these models are provided in (```evaluators/weights```).
* Optimizer (```search_algorithms/```): a optimization strategy such as evolutionary algorithm or bayesian optimization. 
* Worker (```search_algorithms/worker.py```): A global object that runs the architecture search loop and the final network training pipeline
>>>>>>> public/main

## Setup 
While installing the repository, creating a new conda environment is recomended.

Firstly, refer to [AIHWKit installation](https://aihwkit.readthedocs.io/en/latest/install.html) to install Pytorch and the AIHWKit toolkit. 

Install the additional requirements, using:
```
pip install -r requirements.txt 
```

Afterwards, install AnalogNAS by running the ```setup.py``` file:
``` 
python setup.py install 
```

Alternatively, you can also download the package through pip: 
```
pip install analogainas
```

## Example 

```python
from analogainas.search_spaces.config_space import ConfigSpace
from analogainas.evaluators.xgboost import XGBoostEvaluator
from analogainas.search_algorithms.ea_optimized import EAOptimizer
from analogainas.search_algorithms.worker import Worker

CS = ConfigSpace('CIFAR-10') # define search space, by default a resnet-like search space 
evaluator = XGBoostEvaluator() # load the evaluators 
optimizer = EAOptimizer(evaluator, population_size=20, nb_iter=10)  # define the optimizer with its parameters 

NB_RUN = 2
worker = Worker(CS, optimizer=optimizer, runs=NB_RUN) # The global runner 

worker.search() # start search

worker.result_summary() # print results 

``` 

## Usage
To get started, check out ```nas_search_demo.py``` and ```starter_notebook.ipynb``` to ensure the installation went well. 

## Authors 
AnalogNAS has been developed by IBM Research, 

with Hadjer Benmeziane, Corey Lammie, Irem Boybat, Malte Rasch, Manuel Le Gallo, 
Smail Niar, Hamza Ouarnoughi, Ramachandran Muralidhar, Sidney Tsai, Vijay Narayanan, 
Abu Sebastian, and Kaoutar El Maghraoui

You can contact us by opening a new issue in the repository. 

## How to cite?

In case you are using the _AnalogNas_ toolkit for
your research, please cite the IEEE Edge 2023 paper that describes the toolkit:

> Hadjer Benmeziane, Corey Lammie, Irem Boybat, Malte Rasch, Manuel Le Gallo,
> Hsinyu Tsai, Ramachandran Muralidhar, Smail Niar, Ouarnoughi Hamza, Vijay Narayanan,
> Abu Sebastian and Kaoutar El Maghraoui
> "AnalogNAS: A Neural Network Design Framework for Accurate Inference with Analog In-Memory Computing" (2023 IEEE INTERNATIONAL CONFERENCE ON EDGE
> COMPUTING & COMMUNICATIONS (IEEE Edge))

> https://arxiv.org/abs/2305.10459


## Awards and Media Mentions 

* We are proud to share that AnalogNAS open source project the prestigious **IEEE OPEN SOURCE SCIENCE** in 2023 at the [IEEE 2023 Services Computing Congress](https://conferences.computer.org/services/2023/awards/).
  
 <img width="809" alt="image" src="https://github.com/IBM/analog-nas/assets/7916630/730120f7-7ca1-4ddb-a432-c992470322bc">
 
* AnalogNAS paper received the **Best Paper Award** at [2023 IEEE EDGE (INTERNATIONAL CONFERENCE ON EDGE COMPUTING & COMMUNICATIONS)](https://conferences.computer.org/edge/2023/)

  <img width="796" alt="image" src="https://github.com/IBM/analog-nas/assets/7916630/922a655f-b5fd-4131-80d2-c5b8761c572e">

  


## References
* [Hardware-aware Neural Architecture Search: Survey and Taxonomy](https://www.ijcai.org/proceedings/2021/592)
* [AIHWKit](https://ieeexplore.ieee.org/abstract/document/9458494)
* [AIHW Composer](https://aihw-composer.draco.res.ibm.com)

## License
This project is licensed under [Apache License 2.0].

[Apache License 2.0]: LICENSE.txt
