
# Singular covariance estimates in random regression models

This notebook explores the occurrence of singularity in the estimated covariance matrix of random regression models.
These are mixed-effects models with vector-valued random effects.

First, fit a model to the `sleepstudy` data from [`lme4`](https://github.com/lme4/lme4).

## Fitting a linear mixed-model to the sleepstudy data

Load the required packages

````julia
julia> using DataFrames, FreqTables, Gadfly, MixedModels, RData

julia> using Gadfly.Geom: density, histogram, point

julia> using Gadfly.Guide: xlabel, ylabel

julia> const dat = convert(Dict{Symbol,DataFrame}, load(Pkg.dir("MixedModels", "test", "dat.rda")));

````





The model specification for the Julia `lmm` function is similar to that for the R function `lmer`.
However, the `lmm` function only creates the model structure.
An explicit call to `fit!` is required to fit the model.
As is customary (though not required) in Julia, a function whose name ends in `!` is a _mutating function_ that modifies one or more of its arguments.

An optional second argument of `true` in the call to `fit!` produces verbose output from the optimization.

````julia
julia> sleepm = fit!(lmm(@formula(Y ~ 1 + U + (1+U|G)), dat[:sleepstudy]), true)
f_1: 1784.6423 [1.0, 0.0, 1.0]
f_2: 1790.12564 [1.75, 0.0, 1.0]
f_3: 1798.99962 [1.0, 1.0, 1.0]
f_4: 1803.8532 [1.0, 0.0, 1.75]
f_5: 1800.61398 [0.25, 0.0, 1.0]
f_6: 1798.60463 [1.0, -1.0, 1.0]
f_7: 1752.26074 [1.0, 0.0, 0.25]
f_8: 1797.58769 [1.18326, -0.00866189, 0.0]
f_9: 1754.95411 [1.075, 0.0, 0.325]
f_10: 1753.69568 [0.816632, 0.0111673, 0.288238]
f_11: 1754.817 [1.0, -0.0707107, 0.196967]
f_12: 1753.10673 [0.943683, 0.0638354, 0.262696]
f_13: 1752.93938 [0.980142, -0.0266568, 0.274743]
f_14: 1752.25688 [0.984343, -0.0132347, 0.247191]
f_15: 1752.05745 [0.97314, 0.00253785, 0.23791]
f_16: 1752.02239 [0.954526, 0.00386421, 0.235892]
f_17: 1752.02273 [0.935929, 0.0013318, 0.234445]
f_18: 1751.97169 [0.954965, 0.00790664, 0.229046]
f_19: 1751.9526 [0.953313, 0.0166274, 0.225768]
f_20: 1751.94852 [0.946929, 0.0130761, 0.222871]
f_21: 1751.98718 [0.933418, 0.00613767, 0.218951]
f_22: 1751.98321 [0.951544, 0.005789, 0.220618]
f_23: 1751.95197 [0.952809, 0.0190332, 0.224178]
f_24: 1751.94628 [0.946322, 0.0153739, 0.225088]
f_25: 1751.9467 [0.947124, 0.0148894, 0.224892]
f_26: 1751.94757 [0.946497, 0.0154643, 0.225814]
f_27: 1751.94531 [0.946086, 0.0157934, 0.224449]
f_28: 1751.94418 [0.945304, 0.0166902, 0.223361]
f_29: 1751.94353 [0.944072, 0.0172106, 0.222716]
f_30: 1751.94244 [0.941271, 0.0163099, 0.222523]
f_31: 1751.94217 [0.939, 0.015899, 0.222132]
f_32: 1751.94237 [0.938979, 0.016548, 0.221562]
f_33: 1751.94228 [0.938863, 0.0152466, 0.222683]
f_34: 1751.9422 [0.938269, 0.015733, 0.222024]
f_35: 1751.94131 [0.938839, 0.0166373, 0.222611]
f_36: 1751.94093 [0.938397, 0.0173965, 0.222817]
f_37: 1751.94057 [0.937006, 0.0180445, 0.222534]
f_38: 1751.94018 [0.934109, 0.0187354, 0.22195]
f_39: 1751.94008 [0.932642, 0.0189242, 0.221726]
f_40: 1751.94027 [0.931357, 0.0190082, 0.221309]
f_41: 1751.9415 [0.932821, 0.0206454, 0.221367]
f_42: 1751.93949 [0.931867, 0.0179574, 0.222564]
f_43: 1751.93939 [0.929167, 0.0177824, 0.222534]
f_44: 1751.9394 [0.929659, 0.0177721, 0.222508]
f_45: 1751.93943 [0.929193, 0.0187806, 0.22257]
f_46: 1751.93935 [0.928986, 0.0182366, 0.222484]
f_47: 1751.93949 [0.928697, 0.0182937, 0.223175]
f_48: 1751.93936 [0.928243, 0.0182695, 0.222584]
f_49: 1751.93934 [0.929113, 0.0181791, 0.222624]
f_50: 1751.93934 [0.929191, 0.0181658, 0.222643]
f_51: 1751.93935 [0.929254, 0.0182093, 0.222621]
f_52: 1751.93935 [0.929189, 0.0181298, 0.222573]
f_53: 1751.93934 [0.929254, 0.0181676, 0.22265]
f_54: 1751.93934 [0.929214, 0.0181717, 0.222647]
f_55: 1751.93934 [0.929208, 0.0181715, 0.222646]
f_56: 1751.93934 [0.929209, 0.018173, 0.222652]
f_57: 1751.93934 [0.929221, 0.0181684, 0.222645]
Linear mixed model fit by maximum likelihood
 Formula: Y ~ 1 + U + ((1 + U) | G)
   logLik   -2 logLik     AIC        BIC    
 -875.96967 1751.93934 1763.93934 1783.09709

Variance components:
              Column    Variance  Std.Dev.   Corr.
 G        (Intercept)  565.51067 23.780468
          U             32.68212  5.716828  0.08
 Residual              654.94145 25.591824
 Number of obs: 180; levels of grouping factors: 18

  Fixed-effects parameters:
             Estimate Std.Error z value P(>|z|)
(Intercept)   251.405   6.63226 37.9064  <1e-99
U             10.4673   1.50224 6.96781  <1e-11


````





The variables in the optimization are the elements of a lower triangular matrix, $\Lambda$, which is the relative covariance factor of the random effects.
The corresponding parameter vector is called $\theta$.

````julia
julia> Λ = getΛ(sleepm)[1]
2×2 Array{Float64,2}:
 0.929221   0.0     
 0.0181684  0.222645

````





The matrix $\Lambda$ is the left (or lower) Cholesky factor of the covariance matrix of the unconditional distribution of the vector-valued random effects, relative to the variance, $\sigma^2$, of the per-observation noise.
That is
\begin{equation}
    \Sigma = \sigma^2\Lambda\Lambda'
\end{equation}

In terms of the estimates,

````julia
julia> s² = varest(sleepm)    # estimate of the residual variance
654.9414511016189

````



````julia
julia> s² * Λ * Λ'   # unconditional covariance matrix of the random effects
2×2 Array{Float64,2}:
 565.511  11.057 
  11.057  32.6821

````





The estimated correlation of the random effects can, of course, be evaluated from the covariance matrix.
Writing out the expressions for the elements of the covariance matrix in terms of the elements of Λ shows that many terms cancel in the evaluation of the correlation, resulting in the simpler formula.

````julia
julia> Λ[2, 1] / sqrt(Λ[2, 1]^2 + Λ[2, 2]^2)
0.08133212358418719

````





For a $2\times 2$ covariance matrix it is not terribly important to perform this calculation in an efficient and numerically stable way.
However, it is a good idea to pay attention to stability and efficiency in a calculation that can be repeated tens of thousands of times in a simulation or a parametric bootstrap.
The `norm` function evaluates the (geometric) length of a vector in a way that controls round-off better than the naive calculation.
The `view` function provides access to a subarray, such as the second row of $\Lambda$, without generating a copy.
Thus the estimated correlation can be written

````julia
julia> Λ[2, 1] / norm(view(Λ, 2, :))
0.08133212358418719

````





### Optimization with respect to θ

As described in section 3 of the 2015 _Journal of Statistical Software_ [paper](https://www.jstatsoft.org/index.php/jss/article/view/v067i01/v67i01.pdf) by Bates, Maechler, Bolker and Walker, using the relative covariance factor, $\Lambda$, in the formulation of mixed-effects models in the [`lme4`](https://github.com/lme4/lme4) and [`MixedModels`](https://github.com/dmbates/MixedModels) packages and using the vector $\theta$ as the optimization variable was a conscious choice.
Indeed, a great deal of effort went into creating this form so that the profiled log-likelihood can be easily evaluated and so that the constraints on the parameters, $\theta$, are simple "box" constraints.
In fact, the constraints are simple lower bounds.

````julia
julia> show(lowerbd(sleepm))
[0.0, -Inf, 0.0]
````





In contrast, trying to optimize the log-likelihood with respect to standard deviations and correlations of the random effects
would be quite difficult because the constraints on the correlations when the covariance matrix is larger than $2\times 2$ are quite complicated.
Also, the correlation itself can be unstable.
Consider what happens to the expression for the correlation if both $\Lambda_{2,1}$ and $\Lambda_{2,2}$ are small in magnitude.
Small perturbations in $\Lambda_{2,1}$ that result in sign changes can move the correlation from near $-1$ to near $+1$ or vice-versa.

Some details on the optimization process are available in an `OptSummary` object stored as the `optsum` field of the model.

````julia
julia> sleepm.optsum
Initial parameter vector: [1.0, 0.0, 1.0]
Initial objective value:  1784.6422961924686

Optimizer (from NLopt):   LN_BOBYQA
Lower bounds:             [0.0, -Inf, 0.0]
ftol_rel:                 1.0e-12
ftol_abs:                 1.0e-8
xtol_rel:                 0.0
xtol_abs:                 [1.0e-10, 1.0e-10, 1.0e-10]
initial_step:             [0.75, 1.0, 0.75]
maxfeval:                 -1

Function evaluations:     57
Final parameter vector:   [0.929221, 0.0181684, 0.222645]
Final objective value:    1751.9393444647078
Return code:              FTOL_REACHED


````





### Convergence on the boundary

Determining if an estimated covariance matrix is singular is easy when using the $\theta$  parameters because singularity corresponds to points on the boundary of the allowable parameter space.
In other words, if the optimization converges to a vector in which either or both of $\theta_1$ or $\theta_3$ are zero, the covariance matrix is singular.
Otherwise it is non-singular.

The $\theta_1$ parameter is the estimated relative standard deviation of the random intercepts.
If this is zero then the correlation is undefined and reported as `NaN`.
If $\theta_3$ is zero and $\theta_2$ is non-zero then the estimated correlation is $\pm 1$ with the sign determined by the sign of $\theta_2$.
If both $\theta_2$ and $\theta_3$ are zero the correlation is `NaN` because the standard deviation of the random slopes will be zero.

Singular covariance matrices larger than $2\times 2$ do not necessarily result in particular values, like ±1, for the correlations.

Users of `lmer` or `lmm` are sometimes taken aback by convergence on the boundary if this produces correlations of `NaN` or $\pm 1$.
Some feel that this is a sign of model failure.
Others consider such estimates as a sign that Bayesian methods with priors that pull singular covariance matrices away from the boundary should be used.

This type of value judgement seems peculiar.
An important property of maximum likelihood estimates is that these estimates are well-defined once the probability model for the data has been specified.
It may be difficult to determine numerical values of the estimates but the definition itself is straightforward.
If there is a direct method of evaluating the log-likelihood at a particular value of the parameters, then, by definition, the mle's are the parameter values that maximize this log-likelihood.
Bates et al. (2015) provide such a method of evaluating the log-likelihood for a linear mixed-effects model.
Indeed they go further and describe how the fixed-effects parameters and one of the variance components can be profiled out of the log-likelihood evaluation, thereby reducing the dimension of the nonlinear, constrained optimization problem to be solved.

If the mle's correspond to a singular covariance matrix, this is a property of the model and the data.
It is not a mistake in some way.
It is just the way things are.
It reflects the fact that often the distribution of the estimator of a covariance matrix is diffuse.
It is difficult to estimate variances and covariances precisely.
A search for papers or books on "covariance estimation" will produce many results, often describing ways of regularizing the estimating process because the data themselves do not provide precise estimates.

For the example at hand a parametric bootstrap is one way of evaluating the precision of the estimates.

## The bootstrap function

The `MixedModels` package provides a `bootstrap` method to create a parametric bootstrap sample from a fitted model.

For reproducibility, set the random number seed to some arbitrary value.

````julia
julia> srand(1234321);

````





Arguments to the `bootstrap` function are the number of samples to generate and the model from which to generate them.
By default the converged parameter estimates are those used to generate the samples.
Addition, named arguments can be used to override these parameter values, allowing `bootstrap` to be used for simulation.

`bootstrap` returns a `DataFrame` with columns:
- `obj`: the objective (-2 loglikelihood)
- `σ`: the standard deviation of the per-observation noise
- `β₁` to `βₚ`: the fixed-effects coefficients
- `θ₁` to `θₖ`: the covariance parameter elements
- `σ₁` to `σₛ`: the estimates standard deviations of random effects.
- `ρ₁` to `ρₜ`: the estimated correlations of random effects

The `ρᵢ` and `σᵢ` values are derived from the `θᵢ` and `σ` values.

````julia
julia> sleepmbstrp = bootstrap(10000, sleepm);

julia> show(names(sleepmbstrp))
Symbol[:obj, :σ, :β₁, :β₂, :θ₁, :θ₂, :θ₃, :σ₁, :σ₂, :ρ₁]
````





Recall that the constrained parameters are $\theta_1$ and $\theta_3$ which both must be non-negative.
If either or both of these are zero (in practice the property to check is if they are "very small", which here is arbitrarily defined as less than 0.00001) then the covariance matrix is singular.

````julia
julia> issmall(x) = x < 0.00001   # defines a one-liner function in Julia
issmall (generic function with 1 method)

````



````julia
julia> freqtable(issmall.(sleepmbstrp[:θ₁]), issmall.(sleepmbstrp[:θ₃]))
2×2 Named Array{Int64,2}
Dim1 ╲ Dim2 │ false   true
────────────┼─────────────
false       │  9685    308
true        │     7      0

````





Here the covariance matrix estimate is non-singular in 9,686 of the 10,000 samples, has an zero estimated intercept variance in 6 samples and is otherwise singular (i.e. correlation estimate of $\pm 1$) in 308 samples.

Empirical densities of the θ components are:

![](./assets//SingularCovariance_14_1.svg)

![](./assets//SingularCovariance_15_1.svg)



A density plot is typically a good way to visualize such a large sample.
However, when there is a spike such as the spike at zero here, a histogram provides a more informative plot.

![](./assets//SingularCovariance_16_1.svg)

![](./assets//SingularCovariance_17_1.svg)



### Reciprocal condition number

The definitve way to assess singularity of the estimated covariance matrix is by its _condition number_ or, alternatively, its _reciprocal condition number_.
In general the condition number, $\kappa$, of a matrix is the ratio of the largest singular value to the smallest.
For singular matrices it is $\infty$, which is why it is often more convenient to evaluate and plot $\kappa^{-1}$.
Because $\kappa$ is a ratio of singular values it is unaffected by nonzero scale factors.
Thus
\begin{equation}
\kappa^{-1}(s^2\Lambda\Lambda') = \kappa^{-1}(\Lambda\Lambda') =
[\kappa^{-1}(\Lambda)]^2
\end{equation}

````julia
function recipcond(bstrp::DataFrame)
    T = eltype(bstrp[:θ₁])
    val = sizehint!(T[], size(bstrp, 1))
    d = Matrix{T}(2, 2)
    for (t1, t2, t3) in zip(bstrp[:θ₁], bstrp[:θ₂], bstrp[:θ₃])
        d[1, 1] = t1
        d[1, 2] = t2
        d[2, 2] = t3
        v = svdvals!(d)
        push!(val, v[2] / v[1])
    end
    val
end
rc = recipcond(sleepmbstrp)
````



![](./assets//SingularCovariance_19_1.svg)



$\kappa^{-1}$ is small if either or both of $\theta_1$ or $\theta_3$ is small.

````julia
julia> sum(issmall, rc)
315

````





The density of the estimated correlation

![](./assets//SingularCovariance_21_1.svg)

````julia
julia> sum(isfinite, sleepmbstrp[:ρ₁])  # recall that ρ = NaN in 7 cases
9993

````



````julia
julia> sum(x -> x == -1, sleepmbstrp[:ρ₁])  # number of cases of rho == -1
2

````



````julia
julia> sum(x -> x == +1, sleepmbstrp[:ρ₁])  # number of cases of rho == +1
306

````





In this case the bootstrap simulations that resulted in $\rho = -1$ were not close to being indeterminant with respect to sign.
That is, the values of $\theta_2$ were definitely negative.

````julia
julia> sleepmbstrp[:θ₂][find(x -> x == -1, sleepmbstrp[:ρ₁])]
2-element Array{Float64,1}:
 -0.265855
 -0.254484

````





## The Oxboys data

In the `nlme` package for R there are several data sets to which random regression models are fit.
The `RCall` package for Julia provides the ability to run an embedded R process and communicate with it.
The simplest form of writing R code within Julia is to use character strings prepended with `R`.
In Julia strings are delimited by `"` or by `"""`.
With `"""` multi-line strings are allowed.

````julia
julia> using RCall

julia> R"""
library(nlme)
plot(Oxboys)
"""
RCall.RObject{RCall.VecSxp}


````



````julia
julia> oxboys = rcopy(R"Oxboys");

julia> show(names(oxboys))
Symbol[:Subject, :age, :height, :Occasion]
````



````julia
julia> oxboysm = fit!(lmm(@formula(height ~ 1 + age + (1+age | Subject)), oxboys))
Linear mixed model fit by maximum likelihood
 Formula: height ~ 1 + age + ((1 + age) | Subject)
   logLik   -2 logLik     AIC        BIC    
 -362.98384  725.96769  737.96769  758.69962

Variance components:
              Column     Variance   Std.Dev.    Corr.
 Subject  (Intercept)  62.78847365 7.92391782
          age           2.71167720 1.64671710  0.64
 Residual               0.43545514 0.65989024
 Number of obs: 234; levels of grouping factors: 26

  Fixed-effects parameters:
             Estimate Std.Error z value P(>|z|)
(Intercept)   149.372   1.55461 96.0833  <1e-99
age           6.52547  0.329768 19.7881  <1e-86


````



````julia
julia> show(getθ(oxboysm))
[12.0079, 1.60161, 1.91366]
````





As seen in the plot and by the estimate $\widehat{\theta_1} = 12.0$, the estimated standard deviation of the random intercepts is much greater than the residual standard deviation.
It is unlikely that bootstrap samples will include singular covariance estimates.

````julia
julia> srand(4321234);

julia> oxboysmbtstrp = bootstrap(10000, oxboysm);

````





In this bootstrap sample, there are no singular estimated covariance matrices for the random effects.

````julia
julia> freqtable(issmall.(oxboysmbtstrp[:θ₁]), issmall.(oxboysmbtstrp[:θ₃]))
1×1 Named Array{Int64,2}
Dim1 ╲ Dim2 │ false
────────────┼──────
false       │ 10000

````





The empirical density of the correlation estimates shows that even in this case the correlation is not precisely estimated.

![](./assets//SingularCovariance_32_1.svg)

````julia
julia> extrema(oxboysmbtstrp[:ρ₁])
(-0.048724310572162526, 0.9352854720073266)

````





The reciprocal condition number

````julia
julia> rc = recipcond(oxboysmbtstrp);

julia> extrema(rc)
(0.06152489984238238, 0.36869107666318435)

````





does not get very close to zero.

![](./assets//SingularCovariance_35_1.svg)



## The Orthodont data

````julia
julia> R"plot(Orthodont)"
RCall.RObject{RCall.VecSxp}


````





The subject labels distinguish between the male and the female subjects.  Consider first the female subjects only.

````julia
julia> orthfemale = rcopy(R"subset(Orthodont, Sex == 'Female', -Sex)");

julia> orthfm = fit!(lmm(@formula(distance ~ 1 + age + (1 + age | Subject)), orthfemale))
Linear mixed model fit by maximum likelihood
 Formula: distance ~ 1 + age + ((1 + age) | Subject)
   logLik   -2 logLik     AIC        BIC    
  -67.25463  134.50927  146.50927  157.21441

Variance components:
              Column    Variance   Std.Dev.    Corr.
 Subject  (Intercept)  2.97201061 1.72395203
          age          0.02152085 0.14669986 -0.30
 Residual              0.44656043 0.66825177
 Number of obs: 44; levels of grouping factors: 11

  Fixed-effects parameters:
             Estimate Std.Error z value P(>|z|)
(Intercept)   17.3727  0.725217 23.9552  <1e-99
age          0.479545 0.0631368 7.59533  <1e-13


````



````julia
julia> srand(1234123)
MersenneTwister(UInt32[0x0012d4cb], Base.dSFMT.DSFMT_state(Int32[1849428804, 1072710534, 1722234079, 1073299110, 2058053067, 1072801015, 18044541, 1072957251, 668716466, 1073001711, 1294148366, 1073392756, -246176162, 1073384180, 1439312554, 1073174261, 59840039, 1072796106, -1045579541, 1073468618, 388453685, 1073735140, -1275427225, 1072814875, -1971017192, 1072928173, -46910975, 1073472366, 860582882, 1073252538, 1154181238, 1073295647, -619955804, 1072833188, -1324140313, 1073322743, -1114678961, 1073257910, -1799686755, 1073464531, 276417507, 1073419406, 1970196725, 1073308789, -13922601, 1073313708, 640462978, 1073086920, 1821228270, 1073662023, 1531855689, 1072742242, -1437706174, 1073735270, -1006506637, 1072741588, 1732322399, 1072760605, -1923004190, 1072885547, -1622002665, 1073684278, 1022696057, 1072984566, -1521755664, 1073171884, 1273712108, 1073144109, 678390109, 1073200698, 2068056517, 1073357621, 210620177, 1073475212, 488577462, 1073611580, -1963669263, 1072724838, 1344926338, 1073220633, -2128026924, 1073025422, -920440580, 1072784275, -1091866162, 1073473678, -59369667, 1073554514, -2071907959, 1073025925, -1246947456, 1072920630, -1670610045, 1073254614, -1052964581, 1073455201, -1791712339, 1073535443, -704243129, 1073671224, -506703901, 1072889748, -1310651959, 1073169973, -537425724, 1073283538, 1793991520, 1073642067, -973437862, 1072747472, 1232110785, 1073611144, -1914344300, 1073038604, 759504088, 1073209170, -1976196993, 1072824746, 163828606, 1073250587, 2060443751, 1073334027, -1885928194, 1072757818, -1493065553, 1073557997, -832397309, 1073454123, 1467839181, 1073466932, 319269955, 1073693486, 1325787130, 1073523241, -896041432, 1072893329, -1878247912, 1072874719, 1190522197, 1072858280, -121108225, 1073117078, -16965821, 1073043162, -809336891, 1073273048, 1607485827, 1073643584, -1350170179, 1073431809, -1510996351, 1072752005, 1353691010, 1072846043, -1129116352, 1072755857, 1706625622, 1073084338, -243809912, 1073203323, -1119406567, 1072877086, -965616213, 1073162341, 1296869492, 1073023599, 884757660, 1073436321, 324346295, 1072746761, 1646872759, 1073453656, 1659590315, 1072993205, 1848094888, 1073608742, -1389293461, 1072930572, -842024947, 1073208906, 424909026, 1072894317, -1942412030, 1073663928, -1161059380, 1073353002, -1315723161, 1073132351, 2146816118, 1073481050, 1699536517, 1072856916, -647709167, 1072745038, 781257507, 1072962005, 558844032, 1072794643, -1827676894, 1072970089, 1391216679, 1073061922, 1468938343, 1073507066, -50573609, 1072965147, 550534557, 1073041040, 1111331492, 1073272372, 1854735545, 1073415603, 1481741113, 1072996097, -84629236, 1073496481, 2108607834, 1073652638, 1450897993, 1072705903, 22081081, 1072773460, -792905833, 1073321136, -1241866505, 1073266969, 1572339858, 1073553347, -544093909, 1072864034, -905361989, 1072994198, 597868072, 1072858235, -148690660, 1072726596, 2092121974, 1073392702, 1001020932, 1073359943, -664205090, 1073635439, 798487126, 1073739043, 95108301, 1073017923, -2102978354, 1073084683, 460916246, 1072970867, 1301312504, 1072884577, 1181661833, 1072703029, -1438526844, 1072836215, 1357881049, 1073238811, 407395602, 1072959641, -563671799, 1073328832, 1574312152, 1072868062, -1168474889, 1072956406, -1507947262, 1073337316, -1177970445, 1073075252, -1950883307, 1073730687, 1808826949, 1073712051, -673307179, 1073086883, 1234568777, 1073669206, 1086777039, 1072738451, 1176717639, 1073569439, -1108603235, 1073381359, -302636537, 1073410600, -1258968951, 1073366825, -1847183597, 1073005725, -820495507, 1073032147, -539222979, 1072797912, -1129119277, 1073126019, 29819990, 1073296701, -289856999, 1073118270, 1602752003, 1072898571, -436188404, 1073540928, -1736482116, 1073273284, 1183979036, 1073053117, 1407863340, 1073249816, 1515988008, 1073412855, 1427635406, 1073613554, -1552911431, 1073711931, -457593369, 1073395042, 583376543, 1072858985, 120403097, 1073336895, -546791565, 1072766143, -1945250252, 1073447776, 2092782457, 1073720655, -1361962500, 1073635827, 1098177616, 1072958526, 635047031, 1073130602, -2036676095, 1073586385, -1447984508, 1072822028, -1860097375, 1073668999, -734215219, 1073123323, 203113175, 1073423649, 12118255, 1072996726, -1847134656, 1073293818, -346685271, 1072728058, 1948116565, 1073720651, -1214839800, 1072949179, 1051019791, 1073646630, -1430462444, 1072698994, 683397666, 1073198427, 939105378, 1073371092, 1946538405, 1073672877, -131298527, 1073302859, -2006906568, 1073439839, 406137672, 1073273025, 323668036, 1073725260, -1520237861, 1073730982, 1630848572, 1073337302, 1908852154, 1073331846, 1757852111, 1073484221, 907450426, 1073250426, -429586529, 1073356068, 28909651, 1073285139, -1005714440, 1073633989, 735528808, 1073466649, -1287098702, 1073619338, -1999098996, 1073150981, 1742118585, 1073226201, 1056909257, 1073499160, 412542419, 1073609260, 1987033414, 1073426151, 373301362, 1073464813, -2072480688, 1073435334, -1275681021, 1072748215, 1979340606, 1072844123, 196469936, 1072761978, 250282766, 1072900592, 1046193758, 1073042979, 688446650, 1073676901, 484492819, 1073565985, 545232556, 1073062928, 1401725408, 1073481360, 1786249255, 1072767542, 342133395, 1073012367, 764190343, 1072838025, 834188368, 1073514097, 293469320, 1073320924, -129203618, 1072869113, -426121097, 1073393475, 443358151, 1073606557, 1057390546, 1073557435, -865292617, 1072993798, 165383692, 1073606073, 1896949958, 1073397960, 879983282, 1073707666, 536638657, 1073155831, -300176276, 1073113112, -400371866, 1073130583, -489576627, 1073044960, -1951713158, 1072917492, -1286700939, 1073478481, -783793209, 1073058458, -506161217, 1073421779, -1890992377, 1072746360, -1115406913, 1073614546, -162887009, 1073650333, 613307192, 1073504939, -304427970, 1073547012, -1940510584, 1072816758, -815665977, 1073287216, -2016029525, 1072754541, 1762115438, 1072974567, 717520751, 1073322548, -680193101, 1073004733, 34262767, 1073536000, 1400792921, 1073025746, -1842573561, 1073622161, -2124608432, 1072887936, 1075593791, 1073388138, -140230141, 1073354549, -1159659754, 1073645587, -863858741, 1073644778, 2134298558, 1073300168, -269344418, 1072821644, 1873828269, 1072792826, 1775543533, 1072947554, 586115388, 1072854654, 1436908889, 1072883723, -1533449616, 1072913043, 1124041442, 1072770958, -1621716787, 1073660676, -1242581393, 1073319921, -2101113220, 1072764611, 1897101736, 1073069969, -2051615881, 1072972945, -1139078463, 1072998784, -1325332950, 1072886743, -1112830824, 1073203480, 1947115707, 1073736193, 1682907517, 1073563739, 290460745, 1073097143, 85772401, 1072776213, -695753600, 1073729211, -1369847030, 1073684013, -1910559124, 1072979931, 1292463358, 1073595166, -269306072, 1073700601, 626399620, 1072699381, -1520057283, 1073322071, 792094481, 1073553556, -952075680, 1073670157, -518022544, 1073113445, 262171394, 1072967794, -401181589, 1073325461, 1897911001, 1073156198, 808299321, 1073238034, -1782147221, 1073046155, -1446909839, 1073134391, 886080174, 1073707227, -1108449298, 1072757675, 1916674698, 1073733168, -993331321, 1072794479, -73077411, 1073384707, -215330224, 1073662090, -1059500391, 1072776564, -991963568, 1072977065, -337793736, 1073707187, -635009394, 1073717248, -158432757, 1072838042, 524172367, 1073633605, -844798861, 1073721735, -74603517, 1073314254, -1792052474, 1073357247, -1665833071, 1073380480, -1320849645, 1073631090, 1600122228, 1073062140, 287155168, 1073446461, 1413166997, 1073632239, -1654370157, 1073564114, -1444845157, 1073208110, 2141164054, 1073616196, -1835755974, 1073563243, 36069359, 1072918616, -840815020, 1073711493, 1454018272, 1073136051, 551271058, 1073399877, -1809923840, 1073041572, 189180410, 1073256860, 995460455, 1073020843, -598226223, 1073361044, -1337535558, 1073546973, -1402851934, 1073052123, -1522889678, 1073158231, -1686951621, 1073000689, 38560395, 1073065622, -2123206258, 1072929243, 293987068, 1073714036, -783742036, 1073181345, -251423067, 1073222902, 1438164842, 1073479914, 653202388, 1072947503, 1576890362, 1073145194, -2127091864, 1073578927, 813994073, 1072811723, -538524903, 1073394838, -850020419, 1073345584, 923056093, 1073182638, -1712988658, 1072954406, 660416299, 1072739015, 1515592894, 1073664970, 299474099, 1072933157, -1877373193, 1072824159, 1954615641, 1072985757, 639258178, 1073117106, -1835827910, 1073347886, -1133029688, 1072928231, 461762273, 1072801274, -529589171, 1073089766, -444679559, 1072950048, 396342591, 1073739438, 1461622329, 1073056155, 1864686526, 1073711443, 365455052, 1073331975, 468459086, 1073028730, 1941567735, 1073559901, -1956440732, 1073625817, 1095988623, 1073653617, 1169091901, 1073677684, -1414753292, 1073263574, -2109827946, 1073530397, -846504813, 1072748417, 1735247338, 1072725749, 518053453, 1073505452, -36067822, 1073212775, -343350984, 1073391452, -1820369291, 1072882278, 1297201948, 1072820954, 278667265, 1072730773, -1439793814, 1073094809, -657226778, 1073567866, 1480483272, 1073740640, 1172949624, 1073031749, -2087268881, 1073183471, -1632841116, 1072797806, -303045015, 1073654302, 979350334, 1072975010, -393602063, 1073070555, -1261194796, 1072711071, 321440136, 1073513463, -126423279, 1073051373, -1153221639, 1073553062, 1653158638, 1073411494, 780501209, -2117144994, -394908522, -1446490633, 382, 0]), [1.39169, 1.01459, 1.69258, 1.91239, 1.6891, 1.69523, 1.38824, 1.00049, 1.93803, 1.94006, 1.90659, 1.63501, 1.91715, 1.48091, 1.10972, 1.80163, 1.26269, 1.50306, 1.68199, 1.57417, 1.81124, 1.68689, 1.18151, 1.53163, 1.16967, 1.44419, 1.23956, 1.59694, 1.85841, 1.60524, 1.78611, 1.46478, 1.98423, 1.72108, 1.66429, 1.70762, 1.39824, 1.78871, 1.81995, 1.49487, 1.24669, 1.74591, 1.22775, 1.65195, 1.91041, 1.45178, 1.65043, 1.49824, 1.47671, 1.93257, 1.13513, 1.76829, 1.26258, 1.95549, 1.72951, 1.93126, 1.66836, 1.74831, 1.4289, 1.62828, 1.69934, 1.8498, 1.13332, 1.9979, 1.38677, 1.70394, 1.67686, 1.24935, 1.00772, 1.41226, 1.48639, 1.45156, 1.40043, 1.91376, 1.88813, 1.68674, 1.96842, 1.70133, 1.53853, 1.58489, 1.7293, 1.76468, 1.87832, 1.13767, 1.4087, 1.44116, 1.31574, 1.86853, 1.93417, 1.94502, 1.21288, 1.96916, 1.08907, 1.55288, 1.40131, 1.12543, 1.21405, 1.67817, 1.17983, 1.99058, 1.99955, 1.021, 1.36419, 1.83445, 1.60462, 1.71884, 1.46441, 1.78621, 1.45319, 1.98843, 1.55662, 1.80779, 1.11781, 1.95475, 1.96495, 1.64982, 1.08662, 1.72398, 1.30742, 1.03123, 1.46261, 1.89265, 1.99604, 1.95782, 1.95518, 1.54616, 1.50369, 1.60303, 1.63555, 1.1163, 1.65027, 1.13288, 1.32219, 1.74818, 1.23475, 1.69259, 1.88469, 1.05621, 1.37915, 1.51552, 1.56632, 1.52667, 1.20825, 1.51445, 1.73014, 1.29619, 1.33702, 1.35274, 1.59769, 1.73476, 1.91596, 1.41436, 1.33519, 1.91299, 1.45369, 1.66721, 1.77278, 1.34307, 1.00324, 1.40304, 1.74047, 1.70785, 1.77585, 1.6332, 1.79741, 1.69388, 1.3171, 1.67221, 1.35684, 1.06447, 1.12468, 1.54196, 1.06154, 1.41146, 1.86812, 1.52971, 1.1563, 1.23706, 1.16389, 1.01507, 1.62896, 1.55833, 1.69745, 1.00809, 1.30821, 1.32629, 1.65988, 1.42734, 1.21396, 1.11986, 1.69339, 1.82858, 1.31863, 1.1525, 1.76046, 1.70297, 1.42043, 1.63997, 1.56172, 1.95448, 1.75838, 1.24174, 1.44877, 1.25432, 1.90751, 1.78323, 1.62425, 1.86824, 1.35815, 1.14457, 1.9352, 1.85055, 1.29918, 1.94123, 1.24999, 1.40297, 1.3838, 1.45346, 1.36219, 1.0115, 1.02739, 1.89383, 1.26001, 1.16968, 1.85881, 1.40997, 1.00037, 1.50492, 1.40556, 1.86905, 1.23646, 1.68106, 1.31574, 1.74962, 1.00291, 1.92415, 1.87567, 1.27393, 1.74007, 1.0389, 1.33037, 1.16264, 1.67845, 1.53356, 1.43217, 1.16943, 1.6584, 1.72777, 1.43911, 1.36428, 1.40679, 1.1439, 1.96602, 1.56063, 1.03341, 1.43778, 1.80941, 1.77579, 1.73572, 1.82685, 1.2145, 1.52935, 1.03663, 1.35444, 1.53689, 1.03897, 1.66977, 1.71988, 1.95437, 1.40077, 1.15403, 1.50026, 1.56632, 1.8615, 1.41536, 1.73983, 1.52685, 1.72446, 1.95235, 1.19034, 1.75043, 1.23639, 1.71512, 1.88732, 1.65388, 1.97635, 1.6025, 1.13761, 1.85906, 1.46835, 1.07552, 1.74679, 1.68907, 1.74825, 1.5992, 1.45209, 1.77065, 1.51303, 1.94757, 1.04173, 1.43018, 1.43237, 1.19421, 1.65903, 1.68714, 1.52056, 1.81748, 1.48228, 1.10787, 1.57301, 1.23938, 1.11674, 1.0279, 1.46869, 1.64583, 1.81857, 1.68066, 1.6342, 1.24168, 1.15932, 1.86415, 1.13741, 1.99294, 1.44182, 1.15146, 1.83543, 1.95767, 1.04504, 1.5624, 1.88916, 1.69743, 1.75566, 1.07714, 1.81709, 1.12531, 1.37952, 1.18183, 1.15131, 1.52327, 1.12375, 1.28035, 1.403, 1.47955, 1.53547, 1.76864, 1.15059, 1.43026, 1.59596, 1.41254, 1.26003, 1.69594, 1.73538, 1.92577, 1.41879, 1.51559, 1.61853, 1.07281, 1.67011, 1.44911, 1.58715, 1.17107, 1.72297, 1.19498, 1.57364, 1.39339, 1.57009, 1.08744, 1.16391, 1.32093, 1.30841, 1.43297, 1.47044, 1.97007, 1.85482, 1.55931, 1.20623, 1.79846, 1.70858, 1.79727, 1.41108, 1.47938, 1.55753], 382)

julia> orthfmbtstrp = bootstrap(10000, orthfm);

````



````julia
julia> freqtable(issmall.(orthfmbtstrp[:θ₁]), issmall.(orthfmbtstrp[:θ₃]))
2×2 Named Array{Int64,2}
Dim1 ╲ Dim2 │ false   true
────────────┼─────────────
false       │  6855   3105
true        │    39      1

````





For this model almost 1/3 of the bootstrap samples converge to singular covariance estimates for the vector-valued random effects.
A histogram of the estimated correlations of the random effects is dominated by the boundary values.

![](./assets//SingularCovariance_40_1.svg)



Even though the estimated correlation in the model is -0.30, more of the boundary values are at +1 than at -1.
This may be an artifact of the optimization routine used.
In some cases there may be multiple optima on the boundary.
It is difficult to determine the global optimum in these cases.

A histogram of the reciprocal condition number is also dominated by the boundary values.




## Early childhood cognitive study

This example from Singer and Willett (2003), *Applied Longitudinal Data Analysis* was the motivation for reformulating the estimation methods to allow for singular covariance matrices. Cognitive scores (`cog`) were recorded at `age` 1, 1.5 and 2 years on 103 infants, of whom 58 were in the treatment group and 45 in the control group.  The treatment began at age 6 months (0.5 years).  The data are available as the `Early` data set in the `mlmRev` package for R.  In the model, time on study (`tos`) is used instead of age because the zero point on the time scale should be when the treatment begins.

````julia
julia> R"""
suppressMessages(library(mlmRev))
library(lattice)
Early$tos <- Early$age - 0.5
Early$trttos <- Early$tos * (Early$trt == "Y")
xyplot(cog ~ tos | reorder(id, cog, min), Early, 
    type = c("p","l","g"), aspect="xy")
"""
RCall.RObject{RCall.VecSxp}


````





Notice that most of these plots within subjects have a negative slope and that the scores at 1 year of age (`tos = 0.5`) are frequently greater than would be expected on an age-adjusted scale.

````julia
julia> R"print(xtabs(cog ~ age + trt, Early) / xtabs(~ age + trt, Early))";
     trt
age           N         Y
  1   108.53333 112.93103
  1.5  95.88889 110.29310
  2    87.40000  97.06897

````





When the time origin is the beginning of the treatment there is not generally a "main effect" for the treatment but only an interaction of `trt` and `tos`.

````julia
julia> early = rcopy(R"subset(Early, select = c(cog, tos, id, trt, trttos))");

julia> earlym = fit!(lmm(@formula(cog ~ 1 + tos + trttos + (1 + tos | id)), early))
Linear mixed model fit by maximum likelihood
 Formula: cog ~ 1 + tos + trttos + ((1 + tos) | id)
   logLik   -2 logLik     AIC        BIC    
 -1185.6369  2371.2738  2385.2738  2411.4072

Variance components:
              Column    Variance   Std.Dev.   Corr.
 id       (Intercept)  165.476124 12.863752
          tos           10.744608  3.277897 -1.00
 Residual               74.946764  8.657180
 Number of obs: 309; levels of grouping factors: 103

  Fixed-effects parameters:
             Estimate Std.Error  z value P(>|z|)
(Intercept)   120.783    1.8178  66.4448  <1e-99
tos           -22.474    1.4878 -15.1055  <1e-50
trttos        7.65205   1.43609   5.3284   <1e-7


````





The model converges to a singular covariance matrix for the random effects.

````julia
julia> getθ(earlym)
3-element Array{Float64,1}:
  1.48591 
 -0.378633
  0.0     

````





The conditional (on the observed responses) means of the random effects fall along a line.

![](./assets//SingularCovariance_46_1.svg)
