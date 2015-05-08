pyirt
=====
[![Build Status](https://img.shields.io/travis/17zuoye/pyirt/master.svg?style=flat)](https://travis-ci.org/17zuoye/pyirt)
[![Coverage Status](https://coveralls.io/repos/17zuoye/pyirt/badge.svg)](https://coveralls.io/r/17zuoye/pyirt)
[![Health](https://landscape.io/github/17zuoye/pyirt/master/landscape.svg?style=flat)](https://landscape.io/github/17zuoye/pyirt/master)
[![Download](https://img.shields.io/pypi/dm/pyirt.svg?style=flat)](https://pypi.python.org/pypi/pyirt)
[![License](https://img.shields.io/pypi/l/pyirt.svg?style=flat)](https://pypi.python.org/pypi/pyirt)



A python library of IRT algorithm designed to cope with sparse data structure.

- Current version is in early development stage. Use at your own peril.


I.Model Specification
===================

## MMLE
The current version supports MMLE algorithm and unidimension two parameter
IRT model. There is a backdoor method to specify the guess parameter but there
is not active estimation.

The prior distribution of theta is uniform rather than beta.

There is no regularization in alpha and beta estimation. Therefore, the default
algorithm uses boundary on the parameter to prevent over-fitting and deal with
extreme cases where almost all responses to the item is right.

## Theta estimation
The package offers two methods to estimate theta, given item parameters: Bayesian and MLE. <br>
The estimation procedure is quite primitive. For examples, see the test case.  

II.What's New
==========

IRT model is developed for offline test that has few missing data. However,
when try to calibrate item parameters for online testing bank, such assumption
breaks down and the algorithm runs into sparse data problem, as well as severe
missing data problem.

## "Big" Data
EM algorithm requires two essential dictionaries for analysis routine. One maps
item to user and the other maps user to item. Python dictionary is not memory
efficient so pyirt uses hard disk dbm instead. The limit of data size is
about 1/4 of the hard drive size. I doubt any dataset will be that large.

The performance will suffer greatly by using the dbm. For a 10 million record
dataset, the loading time increases by about 5 times and the computation time
increase by about 3 times. Putting the temp folder in memory does not reduces
the time by 10%.

User be aware when invoke the 'dbm' mode.

## Missing Data

As for now, missing data are assumed to be ignorable.

III.Default Config
===========
## Exposed
The theta paramter range from [-4,4] and a step size of 0.8 by default.

Alpha is bounded by [0.25,2] and beta is bounded by [-2,2], which goes with the user ability
specifiation. 

Guess parameter is set to 0 by default, but one could supply a dictionary with eid as key and c as value.


## Hidden
The default solver is L-BFGS-G. 

The default max iteration is 10.

The stop condition threshold is 1e-3 by default. The algorithm computes the
average likelihood per log at the end of the iteration. If the likelihood
increament is less than the threshold, stops.

IV.Data Format
=========
The file is expected to be comma delimited. 

The three columns are uid, eid, result-flag.

Currently the model only works well with 0/1 flag but will NOT raise error for
other types.



V.Example
=========
```python
from pyirt import *

src_fp = open(file_path,'r')

# alternatively, pass in list of tuples in the format of [(uid, eid, atag),...]


# (1)Run by default
item_param, user_param = irt(src_fp)

# (2)Supply bnds
item_param, user-param = irt(src_fp, theta_bnds = [-5,5], beta_bnds = [-3,3])

# (3)Supply guess parameter
guessParamDict = {1:{'c':0.0}, 2:{'c':0.25}}

item_param, user_param = irt(src_fp, in_guess_param = guessParamDict)
```


VI.Performance Check
=======
The likelihood is not stable in terms of converging. 
To use max iteration improve the performance compared to the MIRT/LTM package

Simulation shows that (with proper bounds), the pyirt is more stable in
estimating the 2PL model when the item parameters are constrained.

The performance improves because it prevents alpha and beta going too
large when the item is too easy. 



VII.ToDos
===========

## Models
(1) The solver cannot handle polytomous answers.

(2) The solver cannot handle multi-dimensional data.

(3) The solver cannot handle group constraints.

## Algorithm
(1) Introduce parallel computing

(2) Scipy optimize routine is not as good as the matlab fmincon, consider use a
c-plugin from matlab


VIII.Acknowledgement
==============
The algorithm is described in details by Bradey Hanson(2000), see in the
literature section. I am grateful to Mr.Hanson's work.

The python implementation is benefited greatly from the comments and suggestions from Chaoqun Fu and Dawei Chen.

