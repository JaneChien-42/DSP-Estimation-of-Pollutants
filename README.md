# Directional Shadow Price Estimation of CO<sub>2</sub>, SO<sub>2</sub>, and NO<sub>x</sub>

## 1 Background and Motivation
### Background

第一次工業革命的開始，因為自動化機械化需要大量的電力，因此人類開始使用燃煤來發電。而在近代，因為地球暖化、環保意識抬頭，對於汙染物的管控變成了一大議題。政府(EPA)為了規範發電廠汙染物的產生，......

Coase Theorem寇斯定理

### Motivation

The only thing that EPA should do is to determine the price of polltants. We hope this research can give a good shadow price estimation of pollutants, and can also give an insight to find the trend of pollutants emission through the panel analysis.

### Objective

Reproduce the Paper:

Lee, Chia-Yen and Peng Zhou, 2015. [Directional Shadow Price Estimation of CO2, SO2 and NOx in the United States Coal Power Industry 1990-2010](http://dx.doi.org/10.1016/j.eneco.2015.08.010). Energy Economics, 51, 493–502.  

### Problem Definition
(可以刪掉?)

## 2 Methodology

We need to follow the step below to find the DSP of pollutants:

1.  Collect Data as **Raw Data**.
2.  Project Raw Data to the frontier line with **Directional Distance Function(DDF)** method (To find the **Frontier Data** needede in the next step).
3.  Find the **Directional Marginal Productivity(DMP)** with Raw Data and Frontier Data.
4.  Find the **Directional Shadow Price(DSP)** based on DMP.

How we collect the data will be introduced in the third part. So following goes into details of the models use in the project.

### Directional Distance Function(DDF)

DDF is one of DEA models. So here we can find the efficiency of each firm with DDF method. With the efficiency, next is to project the raw data to the frontier line, and that is the frontier data we need in the DMP calculation.

#### Indices
<img src="https://latex.codecogs.com/svg.image?i" /> : the input  
<img src="https://latex.codecogs.com/svg.image?j" /> : the output  
<img src="https://latex.codecogs.com/svg.image?q" /> : the undesirable output  
<img src="https://latex.codecogs.com/svg.image?k,r" /> : the firm

#### Sets
<img src="https://latex.codecogs.com/svg.image?I" /> : Input, <img src="https://latex.codecogs.com/svg.image?i\in%20I" />  
<img src="https://latex.codecogs.com/svg.image?J" /> : Output, <img src="https://latex.codecogs.com/svg.image?j\in%20J" />  
<img src="https://latex.codecogs.com/svg.image?Q" /> : Pollutants, <img src="https://latex.codecogs.com/svg.image?q\in%20Q" />  
<img src="https://latex.codecogs.com/svg.image?K" /> : States in US, <img src="https://latex.codecogs.com/svg.image?k\in%20K" />

#### Parameters
<img src="https://latex.codecogs.com/svg.image?X_{ik}" /> : ith input of firm k  
<img src="https://latex.codecogs.com/svg.image?Y_{jk}" /> : jth good output of firm k  
<img src="https://latex.codecogs.com/svg.image?B_{qk}" /> : qth bad output of firm k

<img src="https://latex.codecogs.com/svg.image?g^{Y_j}" /> : direction of jth good output  
<img src="https://latex.codecogs.com/svg.image?g^{B_q}" /> : direction of qth bad output

#### Decision Variables

<img src="https://latex.codecogs.com/svg.image?\lambda" /> : intensity weights representing the convex combination between firms  
<img src="https://latex.codecogs.com/svg.image?\mu" /> : intensity weights representing the convex combination between firms  
<img src="https://latex.codecogs.com/svg.image?\eta" /> : efficiency  

#### DDF Model

Below is a general form of DDF in the paper.
(補充說明)

<img src="https://i.imgur.com/O80gA0P.png" width="200">

In this study, we want to focus on one input (Coal), one output (Electricity), and three bad outputs(CO2,SO2,NOx) here, so we can simplify the model as:

(圖片取代)
$\max \eta\\
s.t.\ \sum_k (\lambda_k+\mu_k)X_k\leq X_r\\
\qquad \sum_k \lambda_k Y_k \geq Y_r+\eta g^Y\\
\qquad \sum_k \lambda_k B_{qk} = B_{qr} -\eta g^{B_q} ,\forall q \in Q\\
\qquad \sum_k (\lambda_k + \mu_k) = 1\\
\qquad \lambda_k,\mu_k \geq 0, \eta\ is\ free$

#### Source Code(python-pulp)

We use python with PuLP to implement the model.  
**Xr**, **Yr** and **Br** are in respect of a state's **coal comsumption**, amount of **electricity productivity** and amount of **pollutant emission**. The other 2 parameters, **gY** and **gB**, represent the direction we want to project.

```python
def DDF(Xr, Yr, Br, gY, gB):
    # Decision variables
    Eff = LpVariable('eff', lowBound=None, upBound=None, cat='Continuous')
    Lambda = LpVariable.dicts('lambda', (K), lowBound=0, upBound=None, cat='Continuous')
    Mu = LpVariable.dicts('mu', (K), lowBound=0, upBound=None, cat='Continuous')

    DDF = LpProblem('DDF_model', LpMaximize)
    
    # Objective function
    DDF += Eff

    # Constraints
    DDF += lpSum((Lambda[k]+Mu[k])*X[k] for k in K) <= Xr # I Constraint
    DDF += lpSum(Lambda[k]*Y[k] for k in K) >= Yr + Eff*gY # GO Constraint
    for q in Q:
        modDDFel1 += lpSum(Lambda[k]*B[k,q] for k in K) <= Br[q] - Eff*gB[q] # BO Constraint
    DDF += lpSum(Lambda[k]+Mu[k] for k in K) == 1 # Convex
    
    DDF.solve()
    return (value(DDF.objective))
```

With the efficiency <img src="https://latex.codecogs.com/svg.image?\eta" />, we can project raw data to the frontier line. That is, the frontier data will be <img src="https://latex.codecogs.com/svg.image?{X_i,Y_j+\eta%20g^Y,B_q-\eta%20g^B}" />, which is the projection of raw data <img src="https://latex.codecogs.com/svg.image?{X_i,Y_j,B_q}" /> to the frontier line.

### Directional Marginal Productivity(DMP)
(補充說明)
#### Parameters

<img src="https://latex.codecogs.com/svg.image?X_{ik}" /> : ith input of firm k  
<img src="https://latex.codecogs.com/svg.image?Y_{jk}" /> : jth good output of firm k  
<img src="https://latex.codecogs.com/svg.image?B_{qk}" /> : qth bad output of firm k

<img src="https://latex.codecogs.com/svg.image?X^{Max}_{i}" /> : max value of ith input  
<img src="https://latex.codecogs.com/svg.image?Y^{Max}_{j}" /> : max value of jth good output  
<img src="https://latex.codecogs.com/svg.image?B^{Max}_{q}" /> : max value of qth bad output

<img src="https://latex.codecogs.com/svg.image?g^{Y_j}" /> : direction of jth good output  
<img src="https://latex.codecogs.com/svg.image?g^{B_q}" /> : direction of qth bad output

<img src="https://latex.codecogs.com/svg.image?g^{Y_j}" /> and <img src="https://latex.codecogs.com/svg.image?g^{B_q}" /> are assign by us. How to give <img src="https://latex.codecogs.com/svg.image?g^{Y_j}" /> and <img src="https://latex.codecogs.com/svg.image?g^{B_q}" /> to calculate the v depends on what direction of marginal productivity we want to find. For example, if we want to find the desirable output Electricity, then we will set <img src="https://latex.codecogs.com/svg.image?g^{Y_{Elec}}" /> as 1, and all the other directions as 0. We will give the details of how to set the value in the DMP calculation formula.

###### Note that the $g^{Y_j}$ and $g^{B_q}$ here are different from the DDF model. We just want to show that these parameter denotes the direction of outputs. In the DDF model, we want to find frontier data, so the direction is for raw data to project to the frontier line. But in the DMP model, the direction is determined by what marginal productivity of output we want to focus on. You can get more detail of that in the paper, but here we just give a simple explainable of direction $g^{Y_j}$ and $g^{B_q}$. Just remember they are totoally different things in DDF and DMP model.

#### Decision Variables
<img src="https://latex.codecogs.com/svg.image?v_i" /> : Dual multipliers of the input constraints  
<img src="https://latex.codecogs.com/svg.image?u_j" /> : Dual multipliers of the output constraints  
<img src="https://latex.codecogs.com/svg.image?w_q" /> : Dual multipliers of the undesirable output constraints  
<img src="https://latex.codecogs.com/svg.image?u_0" /> : Dual multipliers of convex-combination constraints  

#### Calculate v
(補充說明)

<img src="https://i.imgur.com/AqspGSq.png" width="450">

> **Use frontier data in constraint (1), and raw data in the others.**

#### Source Code(python+pulp)

**NX, NY and NB** is normalized raw data. On the other side, **NXr, NftYr, NftBr** are for a state who stands on frontier line.

```python
def DMPv(NX, NY, NB, NXr, NftYr, NftBr, gY, gB):
    v = LpVariable('v', lowBound=0, upBound=None, cat='Continuous')
    u = LpVariable('u', lowBound=0, upBound=None, cat='Continuous')
    u0 = LpVariable('u0', lowBound=None, upBound=None, cat='Continuous')
    w = LpVariable.dicts('w', (Q), lowBound=None, upBound=None, cat='Continuous')

    DMPv = LpProblem('DMPv', LpMinimize)
    
    # Objective function
    DMPv += v

    # Constraints
    DMPv += v*NXr - u*NftYr + lpSum(w[q]*NftBr[q] for q in Q_star) + u0 == 0 # (1)
    for k in K:
        nb = NB[k]
        DMPv += v*NX[k] - u*NY[k] + lpSum(w[q]*nb[q] for q in Q_star) + u0 >= 0 # (2)
        DMPv += v*NX[k] +u0 >= 0 # (3)
    DMPv += u*gY + lpSum(w[q]*gB[q] for q in Q_star) == 1 # (4)

    # solve 
    DMPv.solve()
    return (value(DMPv.objective))
```

#### DMP
Having the v of each firm, DMP can be calculated with the formula:

<img src="https://latex.codecogs.com/svg.image?(g^{Y_j}Y^{Max}_j,-g^{B_q}B^{Max}_q)\times%20v_{i^*}/X^{Max}_{i^*}" />

All firms would like to increase the desirable output(Electricity), while decrease the undesireable output(pollutants). We give the opposite direction of Y and B in the DMP calculation.

#### GMP
If we want to find the marginal productivity of good outputs, we should set <img src="https://latex.codecogs.com/svg.image?\sum_ig^{Y_i}=1" /> and <img src="https://latex.codecogs.com/svg.image?\sum_qg^{B_q}=0" />. Here we have only one desirable output -- Electricity, so we set the direction as:

<img src="https://latex.codecogs.com/svg.image?g^{Y}=1,\sum_qg^{B_q}=0" />

With the direction assigned, we can calculate the v in the [above model](#Calculate-v). After calculating the v, we can find the GMP by the formula:

<img src="https://latex.codecogs.com/svg.image?GMP=g^{Y_j}Y^{Max}_j\times%20v_{i^*}/X^{Max}_{i^*}" />

> We name the desirable output electricity as good output, and the marginal productivity of that is called GMP.

#### BMP

Set <img src="https://latex.codecogs.com/svg.image?g^Y=0,\sum_qg^{B_q}=1" />:

<img src="https://latex.codecogs.com/svg.image?BMP=-g^{B_q}B^{Max}_q\times%20v_{i^*}/X^{Max}_{i^*}" />

### Directional Shadow Price(DSP)

<img src="https://latex.codecogs.com/svg.image?p_{b_q}=p_{y_j}\left(\frac{\partial%20y_j}{\partial%20x_i}/\frac{\partial%20b_q}{\partial%20x_i}\right)" />

The <img src="https://latex.codecogs.com/svg.image?\frac{\partial%20y_j}{\partial%20x_i}" /> is the MP of desirable output, and the <img src="https://latex.codecogs.com/svg.image?\frac{\partial%20b_q}{\partial%20x_i}" /> is the undesirable output. They are the GMP and BMP we have from the DMP model. Thus, make the DSP calculation a more simple formation as:

<img src="https://latex.codecogs.com/svg.image?DSP_{B_q}%20=%20Price_{y_j}\times\dfrac{GMP_{y_j}}{BMP_{B_q}}" />

Finally, remember to average the DSP of different firms.

### Summary

Follow the steps above, we can find the DSP of one certain year.

## 3 Example and Applications

Our objective is **ESTIMATION OF SHADOW PRICE** of by-product which is made by power plant. After building the model, we put real world data in our model to complete the empirical study. Our model have one input (Coal consumption), one good output (Electricity), and three bad output (CO<sub>2</sub>, SO<sub>2</sub>, and NO<sub>x</sub>).

### Data set

We use state level data of coal power plant from USA in 2000-2019. The total number of state is 48, because the data of 2 other state is not applicable. Our data is collect from [U.S. EIA website](https://www.eia.gov/). We merge emissions data (tons), electicity production (MWh), electricity price (dollar per MWh) and coal consumption (tons) as the data set . Please click [here]() for the data. Each worksheet is an annual data with 48 states.

Let's take a look at the raw data in 2019.
|Year|State|Coal|Electicity|CO2 Emissions|SO2 Emissions|NOx Emissions|Price|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|2019|AK   |555,952|683,055|1,399,888|1,928|2,156|202.165|
|2019|AL   |14,249,921|26,655,068|20,839,019|6,204|9,905|98.289|
|.....||||||||

### Choice of direction

Then, let us move to direction. According to C.Y., Lee's masterpiece (2015), the most robust direction vector is (.048, .508, .444).

As a result, we should assign the direction parameters like the block below.

<img src="https://latex.codecogs.com/svg.image?(g^{B_{CO_2}},g^{B_{SO_2}},g^{B_{NO_x}})=(.048,.508,.444)" />

We use this vector to project our raw data on the efficient frontier. Note that we should calculate the shadow price by efficient power plant. **Projecting step is very important.** 
After projecting step, we get the frontier data, and we need to plug frontier data into the first constraint (frontier constraint) in GMPv model.

### Result of panel data analysis

The table below shows the statiscal information of shadow price during these two decades.

|Shadow price|CO2|SO2|NOx|
|:-|-:|-:|-:|
|MIN|$ 125|$ 5,222|$ 16,969|
|MEAN|624|17,866|68,118|
|MAX|1,229|57,987|186,211|

> Note: We exclude the 2018 result, because it is an outlier and dominance all other years.

This figure shows the historical shadow prices of CO<sub>2</sub>. No obivous trend during two decades.

![](https://i.imgur.com/d4myGVk.jpg)

<img src="https://i.imgur.com/d4myGVk.jpg" width="450">

This figure shows SO<sub>2</sub>'s. The shadow prices before 2012 we calculate are more steady compared to those after 2012. Our guess is because of electricity prices go bumpy after that year.

![](https://i.imgur.com/ldPz6ka.jpg)

<img src="https://i.imgur.com/ldPz6ka.jpg" width="450">

Finally, it's NO<sub>x</sub> turn. The shadow prices before 2012 we observed increased slowly.

![](https://i.imgur.com/Jxy7k5n.jpg)

<img src="https://i.imgur.com/Jxy7k5n.jpg" width="450">

## 4 Comments

After showing our empirical study, we have some topics need to discussion.

### Free disposable hull problem

If choose invalid direction to compute DDF effiency, we will get many states ending up with 0 DMP. The reason is that we project data along output direction on the horizontal part of VRS frontier. DMP means the slope. Moreover, a slope of a horizontal line is actually 0. Look at the figure below, the dash line in the cicle is where free disposable hull happens.

![](https://i.imgur.com/y53T02J.jpg)

<img src="https://i.imgur.com/y53T02J.jpg" width="450">

###### Source: ORA 10 DEA slides pp.47 by Dr. Chia-Yen Lee.


### Outlier 2018

As we mentioned above, the shadow prices of three pollutants are outliers. The problem is because the spot price of coal from 5 main mining basin in U.S. rose dramatically that year. From the beginning of 2018 to ending, the coal price in U.S. increased almost 40%.

![](https://i.imgur.com/HAGXsOg.jpg)

###### Source: U.S. EIA

### Distortion after 2012

Back to see our run chart again. No matter the pollutant is CO<sub>2</sub>, SO<sub>2</sub> or NO<sub>x</sub>, their run chart share the same pattern after 2012. Some exogenous variables had led to this distortion. It probably is because that the coal and electricity price had high variance during those years. Remember, according to our model, the shadow price computing is based on electricity price. The coal price influenced the electricity price, and in the end influenced the shadow price of pollutants.

### Our insight in real world

Echo to our beginning of study, the objective is providing insight of emission trading. Europe union has the most advanced and strictly enviroment protection policy. As this result, we compare our shadow price calculated with EU emission taxes.

|EU taxes|Amount ($/ton)|Shadow price|Amount ($/ton)|
|:-|:-:|:-|:-:|
|Carbon|47|Carbon dioxide|434|
|Sulpher|126|Sulpher dioxide|29,141|
|Nitrogen|249|Nitrogen oxide|81,097|

We see there is much room for EU to raise their emission taxes. We recommend them raising the taxes to transfer the bad part of externality into manufacturers' production cost. Another view of shadow prices far lager than taxes is because taxes are paid by one single company. However, the shadow prices of pollutant are bear by the whole social (that means everyone on earth). Actually, the shadow prices capture the numerical impact of these pollutant.

### Future work

The amount of **natural gas** using for power plant has exceed the amount of coal after 2015 in U.S. Due to coal comsumption having enviroment unfriendly result, U.S. has decide phase out the amount of coal use. In the future, the coal power plant percentage will drop to 0. We need to substitute our model input from coal to natural gas. Othewise, we will have some serious bias in our model. 

## Reference

### Data

[Electricity - US Energy Information Administration ](https://www.eia.gov/electricity/)

### Paper 
1. Lee, Chia-Yen and Peng Zhou, 2015. [Directional Shadow Price Estimation of CO2, SO2 and NOx in the United States Coal Power Industry 1990-2010](http://dx.doi.org/10.1016/j.eneco.2015.08.010). Energy Economics, 51, 493–502.
2. Wang, K., Y. Xian, Chia-Yen Lee, Y.-M. Wei, Z. Huang, 2019. [On Selecting Directions for Directional Distance Functions in a Non-parametric Framework: A Review](http://dx.doi.org/10.1007/s10479-017-2423-5). Annals of Operations Research, 278 (1-2), 43–76.
3. Lee, Chia-Yen, 2017. [Directional Marginal Productivity: A Foundation of Meta-Data Envelopment Analysis](http://dx.doi.org/10.1057/s41274-016-0129-8). Journal of the Operational Research Society, 68 (5), 544-555

### Others

1. https://www.eia.gov/todayinenergy/detail.php?id=38132
3. https://www.eia.gov/todayinenergy/detail.php?id=43675
4. https://ec.europa.eu/taxation_customs/tedb/legacy/taxDetail.html;jsessionid=KGQu38CJ0JQOAwelO94vqXvGIgetpB_3QcRiWMlXqnaiejuvDs83!-2112365544?id=361/1424159221&taxType=Other%20indirect%20tax
