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
<img src="https://latex.codecogs.com/svg.image?I" /> : Input, $i\in I$  
<img src="https://latex.codecogs.com/svg.image?J" /> : Output, $j\in J$  
<img src="https://latex.codecogs.com/svg.image?Q" /> : Pollutants, $q\in Q$  
<img src="https://latex.codecogs.com/svg.image?K" /> : States in US, $k\in K$

#### Parameters
<img src="https://latex.codecogs.com/svg.image?X_{ik}" /> : $i$th input of firm k  
<img src="https://latex.codecogs.com/svg.image?Y_{jk}" /> : $j$th good output of firm k  
<img src="https://latex.codecogs.com/svg.image?B_{qk}" /> : $q$th bad output of firm k

<img src="https://latex.codecogs.com/svg.image?g^{Y_j}" /> : direction of $j$th good output  
<img src="https://latex.codecogs.com/svg.image?g^{B_q}" /> : direction of $q$th bad output

#### Decision Variables

<img src="https://latex.codecogs.com/svg.image?\lambda" /> : intensity weights representing the convex combination between firms  
<img src="https://latex.codecogs.com/svg.image?\mu" /> : intensity weights representing the convex combination between firms  
<img src="https://latex.codecogs.com/svg.image?\eta" /> : efficiency  

#### DDF Model

Below is a general form of DDF in the paper.
(補充說明)

![](https://i.imgur.com/O80gA0P.png =200x)

And in this study, we want to focus on one input (Coal), one output (Electricity), and three bad outputs(CO2,SO2,NOx) here, so we can simplify the model as:

$\max \eta\\
s.t.\ \sum_k (\lambda_k+\mu_k)X_k\leq X_r\\
\qquad \sum_k \lambda_k Y_k \geq Y_r+\eta g^Y\\
\qquad \sum_k \lambda_k B_{qk} = B_{qr} -\eta g^{B_q} ,\forall q \in Q\\
\qquad \sum_k (\lambda_k + \mu_k) = 1\\
\qquad \lambda_k,\mu_k \geq 0, \eta\ is\ free$

With the efficiency $\eta$, we can project raw data to the frontier line. That is, the frontier data will be ${X_i,Y_j + \eta g^Y,B_q-\eta g^B}$, which is the projection of raw data ${X_i,Y_j,B_q}$ to the frontier line.

### Directional Marginal Productivity(DMP)
(補充說明)
#### Parameters

$X_{ik}$ : $i$th input of firm k  
$Y_{jk}$ : $j$th good output of firm k  
$B_{qk}$ : $q$th bad output of firm k

$X^{Max}_{i}$ : max value of $i$th input  
$Y^{Max}_{j}$ : max value of $j$th good output  
$B^{Max}_{q}$ : max value of $q$th bad output

$g^{Y_j}$ : direction of $j$th good output  
$g^{B_q}$ : direction of $q$th bad output

$g^{Y_j}$ and $g^{B_q}$ are assign by us. How to give $g^{Y_j}$ and $g^{B_q}$ to calculate the v depends on what direction of marginal productivity we want to find. For example, if we want to find the desirable output Electricity, then we will set $g^{Y_{Elec}}$ as 1, and all the other directions as 0. We will give the details of how to set the value in the DMP calculation formula.

###### Note that the $g^{Y_j}$ and $g^{B_q}$ here are different from the DDF model. We just want to show that these parameter denotes the direction of outputs. In the DDF model, we want to find frontier data, so the direction is for raw data to project to the frontier line. But in the DMP model, the direction is determined by what marginal productivity of output we want to focus on. You can get more detail of that in the paper, but here we just give a simple explainable of direction $g^{Y_j}$ and $g^{B_q}$. Just remember they are totoally different things in DDF and DMP model.

#### Decision Variables
$v_i$ : Dual multipliers of the input constraints  
$u_j$ : Dual multipliers of the output constraints  
$w_q$ : Dual multipliers of the undesirable output constraints  
$u_0$ : Dual multipliers of convex-combination constraints  

#### Calculate v
(補充說明)

![](https://i.imgur.com/AqspGSq.png =200x)

> use frontier data in constraint (1), and raw data in the others

#### DMP
Having the v of each firm, DMP can be calculated with the formula:

$(g^{Y_j} Y^{Max}_j,-g^{B_q}B^{Max}_q)\times v_{i^*}/X^{Max}_{i^*}$

#### GMP

Set $g^Y=1, \sum_q g^{B_q}=0:

$GMP = g^{Y_j} Y^{Max}_j\times v_{i^*}/X^{Max}_{i^*}$

#### BMP

Set $g^Y=0, \sum_q g^{B_q}=1$:

$BMP=-g^{B_q}B^{Max}_q\times v_{i^*}/X^{Max}_{i^*}$

### Directional Shadow Price(DSP)

$p_{b_q} = p_{y_j}\left(\frac{\partial y_j}{\partial x_i}/\frac{\partial b_q}{\partial x_i}\right)$

The $\frac{\partial y_j}{\partial x_i}$ is the MP of desirable output, and the $\frac{\partial b_q}{\partial x_i}$ is the undesirable output. They are the GMP and BMP we have from the DMP model. Thus, make the DSP calculation a more simple formation as:

$DSP_{B_q} = Price_{y_j}\times\dfrac{GMP_{y_j}}{BMP_{B_q}}$

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

<img src="https://latex.codecogs.com/png.image?(g^{B_{CO_2}},\ g^{B_{SO_2}},\ g^{B_{NO_x}})=(.048,\ .508,\ .444)" />

    (g_C, g_S, g_N) = (.048, .508, .444)

We use this vector to project our raw data on the efficient frontier. Note that we should calculate the shadow price by efficient power plant. **Projecting step is very important.** 
After projecting step, we get the frontier data, and we need to plug frontier data into the first constraint (frontier constraint) in model (3).

### Tutorial step by step
哈哈哈哈哈

### Result of panel data analysis

The table below shows the statiscal information of shadow price during these two decades.

|Shadow price|CO2|SO2|NOx|
|:-|-:|-:|-:|
|MIN|$ 125|$ 5,222|$ 16,969|
|MEAN|624|17,866|68,118|
|MAX|1,229|57,987|186,211|

> Hint: We exclude the 2018 result, because it is an outlier and dominance all other years.

This figure shows the historical shadow prices of CO<sub>2</sub>. No obivous trend during two decades.

![](https://i.imgur.com/d4myGVk.jpg)

This figure shows SO<sub>2</sub>'s. The shadow prices before 2012 we calculate are more steady compared to those after 2012. Our guess is because of electricity prices go bumpy after that year.

![](https://i.imgur.com/ldPz6ka.jpg)

Finally, it's NO<sub>x</sub> turn. The shadow prices before 2012 we observed increased slowly.

![](https://i.imgur.com/Jxy7k5n.jpg)

## 4 Comments

After showing our empirical study, we have some topics need to discussion.

### Free disposal hull problem
是Free Disposable Hull？

### Outlier 2018

As we mentioned above, the shadow prices of three pollutants are outliers. The problem is because the spot price of coal from 5 main mining basin in U.S. rose dramatically that year. From the beginning of 2018 to ending, the coal price in U.S. increased almost 40%.

![](https://i.imgur.com/HAGXsOg.jpg)

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
