---
title       : Causal inference
subtitle    : 
author      : Jeffrey Leek
job         : Johns Hopkins Bloomberg School of Public Health
logo        : bloomberg_shield.png
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow   # 
url:
  lib: ../../librariesNew
  assets: ../../assets
widgets     : [mathjax]            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
---





## Pro tip

Learn to say no politely. The primary source of motivation in academics (and in many other places) is guilt. To prevent total insanity you need to:

* Say no to most things and focus
* Do it in a way that doesn't offend people
* Do it in a way that doesn't make you feel guilty

This is an art and requires years to master. [Some never do](http://www.bcaffo.com/). 

---

## Paper of the day

[Causal inference in statistics, an overview](http://ftp.cs.ucla.edu/pub/stat_ser/r350.pdf)

---

## Today's slide credits

Some usesful resources

* Cosma Shalizi
  * http://www.stat.cmu.edu/~cshalizi/ADAfaEPoV/ADAfaEPoV.pdf
* Elizabeth Stuart
  * https://github.com/jtleek/jhsph753and4/blob/master/lectures/stuartcausal1.pdf
  * https://github.com/jtleek/jhsph753and4/blob/master/lectures/stuartcausal2.pdf
* Judea Pearl
  * http://ftp.cs.ucla.edu/pub/stat_ser/r350.pdf
* Eric Xing
  * http://www.cs.cmu.edu/~epxing/Class/10708-05/Slides/causality.pdf

---

## Recall levels of analysis

__In approximate order of difficulty__
* Descriptive
* Exploratory
* Inferential
* Predictive
* </rt>Causal</rt>
* </rt>Mechanistic</rt>

---

## About causal analysis

__Goal__: To find out what happens to one variable when you make another variable change. 

* Usually randomized studies are required to identify causation
* There are approaches to inferring causation in non-randomized studies, but they are complicated and sensitive to assumptions
* Causal relationships are usually identified as average effects, but may not apply to every individual
* Causal models are usually the "gold standard" for data analysis

---

## Causal analysis

<img class=center src="../../assets/img/01_DataScientistToolbox/feces.png" height=400 />


[van Nood et al. (2013) NEJM](http://www.nejm.org/doi/full/10.1056/NEJMoa1205037?query=featured_home)

---

## About mechanistic analysis

__Goal__: Understand the exact changes in variables that lead to changes in other variables for individual objects.

* Incredibly hard to infer, except in simple situations
* Usually modeled by a deterministic set of equations (physical/engineering science)
* Generally the random component of the data is measurement error
* If the equations are known but the parameters are not, they may be inferred with data analysis

---

## Mechanistic analysis

<img class=center src="../../assets/img/01_DataScientistToolbox/mechanistic.png" height=400 />

[http://www.fhwa.dot.gov/resourcecenter/teams/pavement/pave_3pdg.pdf](http://www.fhwa.dot.gov/resourcecenter/teams/pavement/pave_3pdg.pdf)

---

## Illustrating the main problem: shoe size and literacy

<img class=center src=../../assets/img/dexandjeff.png height=500>

---

## Most well known, but least understood idea

<img class=center src=../../assets/img/corcause.png height=300>

---

## An outsider's perspective on causal inference

1. With randomized trials -> straightforwardish (we'll see why in a minute)
2. With observational data -> much, much harder
  * Main problem is confounding
  * The key idea are the assumptions
3. It is all about missing data
  * Missing confounders
  * Missing counterfactual observations
4. Causal inference is not easily described in standard statistical/probabilistic notation. 

---

## This is one of *the hottest topics* 

<img class=center src=../../assets/img/causegene.png height=500>

---

## This is one of *the hottest topics* 

<img class=center src=../../assets/img/planout.png height=500>

---

## Representing causal relatinships

* Rubin model:
  * $Y(0), Y(1)$ or $Y_x(u)$
  * Outcome Y, had the individual, u, received treatment 0,1 or X.
* Pearl model:
  * $Pr(Y = y | do(X=x))$
  * Distribution when setting X=x
* Graphical models
<img class=center src=../../assets/img/shoesize.png height=150>


---

## What do those arrows mean? 

<img class=center src=../../assets/img/shoesize.png height=150>


* Intuitively
  * an arrow $S \rightarrow L$ means S "influences" L 
* Bayes Networks/Graphical models (requires DAG)
  * Conditional probabilities e.g. $Pr(S,L,A) = Pr(A)Pr(S|A)Pr(L|A)$
* Causal networks (requires DAG)
  * Variables connected on an unblocked path [d-separated](http://www.andrew.cmu.edu/user/scheines/tutor/d-sep.html)
  * Basically as long as two arrows don't run into each other on the path [collider](http://en.wikipedia.org/wiki/Collider_(epidemiology))
  * For all distributions represented by the graph  if sets of vertices X and Y are d-separated by a set Z in the DAG G, then X and Y are independent conditional on Z in P (this is complicated!)


---

## Remember: these variables are causally related


```r
x = rnorm(100); y = rnorm(100,mean=0.1*x)
plot(x,y,pch=19)
```

<div class="rimage center"><img src="fig/unnamed-chunk-1.png" title="plot of chunk unnamed-chunk-1" alt="plot of chunk unnamed-chunk-1" class="plot" /></div>


---

## Causal inference the Rubin way

Key concepts:

* Treatments
  * The intervention we could apply or withhold
  * Think heavy drug use versus light drug use
  * Difficult with things you can't assign 
* Units
  * Who you apply the treatment to
  * Could be genes, people, schools, etc.
* Potential outcomes
  * What would have happend in both treatment scenarios
  * Typically denoted $Y(0), Y(1)$

Slides on this framework via: 

https://github.com/jtleek/jhsph753and4/blob/master/lectures/stuartcausal2.pdf

---

## What we'd like

Eﬀect of heavy adolescent drug use (T) on earnings at age 40:

<img class=center src="../../assets/img/completedata.png" height=400 />


---

## What we get

Causal inference as missing data problem:

<img class=center src="../../assets/img/obsdata.png" height=400 />

---

## We estiamte average effects

* Can’t estimate individual-level causal eﬀects
* _ATE_: average treatement effect
  * $ATE = \frac{1}{N} \sum_{i=1}^N Y_i(1) - Y_i(0)$
  * eﬀect of drug use on everyone, if forced everyone to use drugs
* _ATT_: average treatement effect among treated
  * $ATT = \frac{1}{N} \sum_{i=1}^N (Y_i(1) - Y_i(0) | T_i = 1)$
  * eﬀect of drug use on people who actually use drugs

---

## Randomized experiments as ideal

* In a randomized experiment, units randomly assigned to treatment or
control groups
* Mathematically, this means that average of control group outcomes
an unbiased estimate of average outcome under control for whole
population (and same for the treatment group)
  * $E(\bar{y}_{T_i = 0}) = \bar{Y(0)}$, $E(\bar{y}_{T_i = 1}) = \bar{Y(1)}$
  * So $E(\bar{y}_{T_i = 0} - \bar{y}_{T_i = 1}) = \bar{Y(0)} - \bar{Y(1)}$
* Intuition is randomization ensures "balance" of covariates

---

## Randomization is best but may cause problems

* People don't do what they're told
  * Noncompliance
* Randomization isn't always feasible
  * Random mating in human populations
* Might have to wait a long time to see outcome
* Randomization may not estimate effects for the group we care about


---

## Ways of performing causal inference outside of experiments

* Stratification/regression
  * Put people in groups with same covariates
  * But some covariates may be missing
  * Limitations of sample size if many covariates aren't there

* Instrumental variables
  * Find an instrument that aﬀects the treatment of real interest, but does
not directly aﬀect the outcomes
  * Vietnam draft lottery as instrument for military service
  * physician prescribing preferences as instrument for taking drug A vs. drug B
  * Need a good instrument
  * Implies a set of other assumptions (monotonicity, exclusion restrictions, etc.)
  
---

## Propensity scores


* Propensity scores
  * Try to replicate features of randomized experiments (create groups that look only randomly different, don't use the outcome when setting up the design)
  * Basically trying to "balance" the data set

---

## Ideal matching scenario -exact match

* For each treated find a control with _exact same covariates_
* Easy with 1 covariate but harder with more
* Software for doing this:
  * http://gking.harvard.edu/cem
  * http://cran.r-project.org/web/packages/MatchIt/index.html
* Want similar covariate distributions in treatment/control after matching
* One diagnostic is "standardization bias"
  * Difference in means, divided by standard deviation
  * Like an effect size for covariates
  * Should hopefully be small


---

## Example diagnostic plot

<img class=center src=../../assets/img/sbias.png height=500>

---

## Propensity scores

$$e_i = P(T_i = 1 | X_i)$$

* Key features
  * Balancing score - at each value the distribution is the same in the treated/control groups.     
  * Intuitvely people with similar probabilities should be assigned similar treatments other than randomness. 
  * If treatment is independent of potential outcomes given covariates, also independent of ptoential outcomes given propensity score (no unmeasured confounders)
* Can be used to match, instead of on covariates individually
* Central goal is to achieve balance


---

## Unconfoundedness assumption

* Assumes that there are no unobserved diﬀerences between the
treatment and control groups, given the observed variables
  * Other ways of saying essentially the same thing: No unobservedconfounders, no hidden bias, “ignorable”
  * Could be a problem if, e.g., people start smoking marijuana because they are getting bad grades and we don’t have grades measured
  * Can help make unconfoundedness assumption more realistic if think about it during data collection
  * Can also do sensitivity analyses to assess how sensitive results are toviolation of this assumption 


---

## If assumption holds we get the nice property

$$P(T | X, Y(0), Y(1)) = P(T | X) \implies P(T |X, Y(0),Y(1)) = P(T|e(X))$$

* This is what allows us to match just on propensity score; don’t need
to deal with all the covariates individually

---

## Types of matching

* k-nearest neighbors 
  * For each treated unit select k similar controls
* Stratification/binning
  * Group individuals with similar propensity scores
* Weighting adjustments
  * Inverse probability of exposure weights (IPTW)


---

## Double robustness

* You can combine propensity score with regression adjustment
  * regression adjustment in 1:1 matched samples
  * weighted regression adjustment
* The two methods work together and if you get one or the other right you are "ok"
* This is an active area of research

---

## Instrumental vairables: another approach

* Sometimes we can't randomize the thing we want
  * Gene expression levels
  * Taking a drug
* But we can randomize something related
  * Genetic background
  * Treatment assignment
* The thing we can randomize is "variable"
* Randomization is the "instrument"
* Good instruments 
  * Easy to randomize
  * Closely related to the variable you care about

---

## Instrumental variables: yeast example

<img class=center src=../../assets/img/yeast1.png height=500>

---

## Randomize with meiosis

<img class=center src=../../assets/img/yeast2.png height=500>

---

## Is it random?

<img class=center src=../../assets/img/yeast3.png height=400>

---

## How do we use this?

<img class=center src=../../assets/img/yeast4.png height=300>

* Try to infer connections between genes (or genes and phenotype)
* Commonly called "Mendelian randomization"
* Hard to do in humans because instrument (randomization) isn't great: population structure for example

---

## Another example: Sommer and Zeger

* Study estimating the eﬀect of Vitamin A on child mortality
* Carried out in Indonesia
* Villages randomized to receive vitamin A supplements or not
* Not all children in Vitamin A villages actually got Vitamin A
* No children in control villages got Vitamin A (not available)
* For simplicity, will ignore village clustering and treat as if children
randomly assigned (village indicators not available anyway)

---

## Compliance and non-compliance

* Can think of there being two types of people:
  * Compliers: take the treatment if assigned to, don’t take it if not
assigned to
  * Non-compliers: don’t take the treatment either way
* Observe compliance status in treatment group
* Don’t observe compliance status in control group
* We don’t know what the controls would have done if they had been in
the treatment group

---

## The data

<img class=center src=../../assets/img/zegersommerdata.png height=400>

---

## How to deal with noncompliance

* Intent to treat (ITT) - ignore compliance
  * Gives unbiased estimates of causal effect of being assigned to group
  * Not what we care about
* As treated - compare people who get treatment who get control
  * Ignores randomization
  * Not a valid estimate of causal effect
* Per protocol analysis
  * Compares people who appeared to comply with their assigned treatment
  * Not a valid estimate of a causal effect


---

## Intent to treat

<img class=center src=../../assets/img/itt.png height=450>

---

## As treated

<img class=center src=../../assets/img/astreated.png height=450>

---

## Per compliance

<img class=center src=../../assets/img/perprotocol.png height=450>

---

## How do we do this with instrumental variables

* What we really want: the biologic effect of taking Vitman A on Mortality
* We can use instrumental variables to get at this
* Define the average effect among compliers (CACE) and noncompliers (NACE)
* Then we have that $$ACE = p_c \times CACE + (1-p_c) \times NACE$$
* Here we change what we are interested in to be "complier average causal effect"

---

## We can estimate the CACE

* ITT provides unbiased estimate of the total causal effect
* We can estimate the proportions of compliers (missing data problem again!)
  * $p_c$ is proportion who took treatment (in Zeger/Sommer data 0.8)
* Now we have an estimation problem with two unknowns $$ACE = p_c \times CACE + (1-p_c) \times NACE$$ $$-0.0025 = 0.8 \times CACE + 0.2 \times NACE$$
* Assume that being assigned to treatment doesn't effect outcome if you don't take it (NACE =0)
* You can now solve $CACE = -0.0025/0.8 = -0.0031$
* This is the IV estimate for CACE

---

## Can extend this idea to 2-sided

* Suppose that the control group has access to treatment 
  * Flu shots - encouragement
  * Diet - information 
* Now we have three kinds of "non-compliers"
  * Defiers
  * Always takers
  * Never takers
* Main interest is still the CACE but things get more complicated
* Fundamental idea still the same: $$ ACE = p_c \times CACE + p_d \times DACE + p_{at} \times AACE + p_{nt} \times NACE$$

---

## Three conditions for an instrument (Hernan and Robins 2006)

* Has a causal effect on the treatment of interest ($p_c > 0$)
  * (there exist some compliers)
* Affects the outcome only through the treatment received
  * (exclusion restrictions)
* Does not share common causes with the outcome
  * instrument (encouragement) assigned randomly

---

## Flu example


<img class=center src=../../assets/img/fluencouragement.png height=300>


---

## How do we estimate CACE in the more complicated case

$$ ACE = p_c \times CACE + p_d \times DACE + p_{at} \times AACE + p_{nt} \times NACE$$

* No defiers $\implies p_d = 0$
* Exclusion restrictions $AACE = NACE = 0$
* People in the treatment goroup must be never takers $\implies p_{nt} = 1-0.307 = 0.693$
* People in the control group who get the flu shot must be always takers $\implies p_{at} = 0.19$
* The remaining people must be compliers $p_c = 1 - 0.693 - 0.19 = 0.12$ 
* $ITT$ estimates $ACE$: $ITT = -0.014$
* $ACE = p_c * CACE \implies CACE = ACE/p_c \implies CACE = -0.014/0.12$


---

## If you liked this learn from people who actually know stuff

* Causal mailing list: https://groups.google.com/forum/#!forum/jhu-causal

* 3rd Quarter, course by Elizabeth Stuart, who won Golden Apple award; focus is introducing methods and their application: http://www.jhsph.edu/courses/course/140.664/01/2014/20016/

* 4th Quarter, theoretical foundations to provide background for developing new causal inference methods (taught by Frangakis and
Rosenblum): http://www.jhsph.edu/courses/course/140.665/01/2013/18324/
