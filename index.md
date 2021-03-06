贝叶斯AB测试

https://towardsdatascience.com/conducting-bayesian-inference-in-python-using-pymc3-d407f8d934a5

https://tungprime.com/2020/08/31/a-b-testing-with-probabilistic-programming-and-pymc3-part-i/

http://mathamy.com/using-pymc-to-analyze-ab-testing-data.html


2.1 Option 1: Analytic solution


It is often believed that Bayesian methods require more computational power than Frequentist methods. This is a fairly accurate statement, except that in some cases it is possible to solve the Bayesian problem analytically - making the Bayesian solution on par, in terms of speed, with the Frequentist approach.

In order to be able to solve this problem analytically, the prior distributions have to be chosen carefully depending on the form of the likelihood function. For a standard “conversion rate” A/B test, the likelihood function has a particularly simple form: a Bernoulli distribution.

Likelihood function

Most A/B tests involve comparing conversions for users in the Control bucket vs. users in the Treatment bucket. This means that the data will contain, for each user, whether he/she converted or not during the duration of the experiment, like so:


Figure. After the usual ETL (Extract-Transform-Load) phase, the experimental data should contain two arrays, one for the Control group and one for the Treatment group. Each entry of the array will represent whether a certain user has converted (value=1) or not (value=0).
Figure. After the usual ETL (Extract-Transform-Load) phase, the experimental data should contain two arrays, one for the Control group and one for the Treatment group. Each entry of the array will represent whether a certain user has converted (value=1) or not (value=0).


This distribution of ones and zeros has a name: it is a Bernoulli distribution.

Now let’s call the two arrays of binary values as 
d
A
 for the Control group and 
d
B
 for the Treatment group. Furthermore, let’s say that the Control group has a probability 
p
A
 of converting, while the Treatment group has a probability 
p
B
 of doing so. Then, the joint likelihood 
P
(
d
|
H
)
 that enters into Bayes’ theorem can be evaluated as

P
(
d
|
H
)
=
P
(
d
A
,
d
B
|
p
A
,
p
B
)
=
P
(
d
A
|
p
A
)
P
(
d
B
|
p
B
)
=
p
c
A
A
(
1
−
p
A
)
n
A
−
c
A
p
c
B
B
(
1
−
p
B
)
n
B
−
c
B
  
(2)
 

where we have assumed that the results in the A and B buckets are independent. The quantity in eq. (
2
) represents the probability of observing the data 
d
A
 and 
d
B
, assuming that the probability of converting are 
p
A
 and 
p
B
.

Choosing the priors


For specific distributions of the likelihood it is possible to find specific forms of the priors that make the subsequent evaluation of Bayes’ theorem very simple. For the purpose of A/B testing, given the expression (
2
) this “specific form” of the prior is represented by the Beta distribution, which is a continuous distribution defined as a function of 
0
≤
x
≤
1
 with parameters 
α
>
0
 and 
β
>
0
:

f
(
x
;
α
,
β
)
=
x
α
−
1
(
1
−
x
)
β
−
1
B
(
α
,
β
)
  
(3)
 

where
B
(
α
,
β
)
=
Γ
(
α
)
Γ
(
β
)
Γ
(
α
+
β
)
,

in which 
Γ
 is the Gamma function.

This will sound rather obscure, but the point is that given our choice of the prior eq. (
3
) it turns out that the posterior distribution is also a Beta distribution! Because the prior and posterior distributions belong to the same family of Beta distributions, the prior of eq. (
3
) is called a conjugate prior of the likelihood function eq. (
2
).

We will see just below the exact expression of the posterior distribution.

Finding the posterior distribution

Given our expressions for the likelihood eq. (
2
) and for the priors eq. (
3
), it is possible to show that the posterior distribution has the following form:

P
(
H
|
d
)
=
f
(
x
;
α
+
c
A
,
β
+
(
n
A
−
c
A
)
)
f
(
x
;
α
+
c
B
,
β
+
(
n
B
−
c
B
)
)
  
(4)
 

We will see in a moment that this expression agrees perfectly well with the numerical solution (or, rather, the numerical solution agrees with the analytic one!).
