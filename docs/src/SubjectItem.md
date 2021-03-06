````julia
julia> using DataFrames, MixedModels, RData

julia> const dat = convert(Dict{Symbol,DataFrame}, load(Pkg.dir("MixedModels", "test", "dat.rda")));

````



````julia
julia> mm1 = fit!(lmm(@formula(Y ~ 1+S+T+U+V+W+X+Z+(1+S+T+U+V+W+X+Z|G)+(1+S+T+U+V+W+X+Z|H)), dat[:kb07]))
Linear mixed model fit by maximum likelihood
 Formula: Y ~ 1 + S + T + U + V + W + X + Z + ((1 + S + T + U + V + W + X + Z) | G) + ((1 + S + T + U + V + W + X + Z) | H)
     logLik       -2 logLik         AIC            BIC      
 -1.4293159×10⁴  2.8586318×10⁴  2.8748318×10⁴ 2.91930056×10⁴

Variance components:
              Column     Variance   Std.Dev.    Corr.
 G        (Intercept)   90715.0184 301.189340
          S              5180.3901  71.974927 -0.43
          T              5543.1348  74.452232 -0.47  0.08
          U              7584.8816  87.091226  0.21 -0.20  0.41
          V              8832.9843  93.983958  0.20 -0.76 -0.54 -0.20
          W              1821.9809  42.684668  0.47 -0.53 -0.11 -0.44  0.28
          X              7417.1453  86.122850 -0.10  0.13 -0.05 -0.86 -0.06  0.70
          Z              3801.0318  61.652509 -0.47  0.41 -0.39 -0.09  0.18 -0.78 -0.39
 H        (Intercept)  129690.6871 360.125932
          S              1856.9765  43.092650 -0.34
          T             62370.7020 249.741270 -0.68 -0.45
          U              2950.1553  54.315332  0.20 -0.03 -0.18
          V              1042.0598  32.280950  0.57 -0.76  0.02  0.02
          W              1620.5108  40.255569  0.28 -0.03 -0.27  0.44 -0.21
          X              4703.6870  68.583431  0.08 -0.24  0.21 -0.13 -0.26  0.02
          Z              4821.2335  69.435103  0.04 -0.47  0.32 -0.68  0.65 -0.69 -0.10
 Residual              399627.0136 632.160592
 Number of obs: 1790; levels of grouping factors: 56, 32

  Fixed-effects parameters:
             Estimate Std.Error  z value P(>|z|)
(Intercept)   2180.63   76.7856  28.3989  <1e-99
S              -66.99   19.3346 -3.46478  0.0005
T            -333.881   47.6587 -7.00566  <1e-11
U              78.987    21.235  3.71967  0.0002
V             22.1518   20.3368  1.08925  0.2760
W            -18.9243   17.5061 -1.08101  0.2797
X             5.26182   22.4216 0.234677  0.8145
Z             -23.951   21.0197 -1.13946  0.2545


````



````julia
julia> mm1.optsum
Initial parameter vector: [1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 1.0, 0.0, 1.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 1.0, 0.0, 0.0, 1.0, 0.0, 1.0]
Initial objective value:  30014.36976860626

Optimizer (from NLopt):   LN_BOBYQA
Lower bounds:             [0.0, -Inf, -Inf, -Inf, -Inf, -Inf, -Inf, -Inf, 0.0, -Inf, -Inf, -Inf, -Inf, -Inf, -Inf, 0.0, -Inf, -Inf, -Inf, -Inf, -Inf, 0.0, -Inf, -Inf, -Inf, -Inf, 0.0, -Inf, -Inf, -Inf, 0.0, -Inf, -Inf, 0.0, -Inf, 0.0, 0.0, -Inf, -Inf, -Inf, -Inf, -Inf, -Inf, -Inf, 0.0, -Inf, -Inf, -Inf, -Inf, -Inf, -Inf, 0.0, -Inf, -Inf, -Inf, -Inf, -Inf, 0.0, -Inf, -Inf, -Inf, -Inf, 0.0, -Inf, -Inf, -Inf, 0.0, -Inf, -Inf, 0.0, -Inf, 0.0]
ftol_rel:                 1.0e-12
ftol_abs:                 1.0e-8
xtol_rel:                 0.0
xtol_abs:                 [1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10, 1.0e-10]
initial_step:             [0.75, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.75, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.75, 1.0, 1.0, 1.0, 1.0, 1.0, 0.75, 1.0, 1.0, 1.0, 1.0, 0.75, 1.0, 1.0, 1.0, 0.75, 1.0, 1.0, 0.75, 1.0, 0.75, 0.75, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.75, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.75, 1.0, 1.0, 1.0, 1.0, 1.0, 0.75, 1.0, 1.0, 1.0, 1.0, 0.75, 1.0, 1.0, 1.0, 0.75, 1.0, 1.0, 0.75, 1.0, 0.75]
maxfeval:                 -1

Function evaluations:     2829
Final parameter vector:   [0.476444, -0.0495227, -0.0557742, 0.0295214, 0.0292097, 0.0319541, -0.0139336, -0.0463229, 0.102521, -0.0170701, -0.0165868, -0.110967, -0.0243266, 0.0126983, 0.0218126, 0.102316, 0.0781059, -0.0943707, 0.00505476, -0.0135029, -0.0650083, 0.108318, 0.00550319, -0.0540444, -0.134248, 0.0516112, 0.0, 0.000111037, 9.89607e-5, -0.000120772, 0.0, 0.000144849, -0.000171985, 0.0, 1.49074e-5, 0.0, 0.569675, -0.0234122, -0.266913, 0.0173413, 0.0290032, 0.0178621, 0.00844082, 0.0049074, 0.0640206, -0.288127, 0.00329602, -0.0307006, 0.00437284, -0.0241439, -0.053426, 0.0425733, -0.0139551, -0.0162798, -0.0184925, 0.0991966, -0.00479098, 0.0829213, -0.00678668, 0.0223168, 0.000985977, -0.0775749, 0.0226446, -0.0535127, -0.0355039, 0.0560694, 0.00364195, -0.00381326, -0.00114592, 0.0, -5.49465e-5, 0.0]
Final objective value:    28586.31798310179
Return code:              FTOL_REACHED


````



````julia
julia> mm1.trms[1].Λ
8×8 LowerTriangular{Float64,Array{Float64,2}}:
  0.476444     ⋅           ⋅            ⋅            ⋅             ⋅            ⋅           ⋅ 
 -0.0495227   0.102521     ⋅            ⋅            ⋅             ⋅            ⋅           ⋅ 
 -0.0557742  -0.0170701   0.102316      ⋅            ⋅             ⋅            ⋅           ⋅ 
  0.0295214  -0.0165868   0.0781059    0.108318      ⋅             ⋅            ⋅           ⋅ 
  0.0292097  -0.110967   -0.0943707    0.00550319   0.0            ⋅            ⋅           ⋅ 
  0.0319541  -0.0243266   0.00505476  -0.0540444    0.000111037   0.0           ⋅           ⋅ 
 -0.0139336   0.0126983  -0.0135029   -0.134248     9.89607e-5    0.000144849  0.0          ⋅ 
 -0.0463229   0.0218126  -0.0650083    0.0516112   -0.000120772  -0.000171985  1.49074e-5  0.0

````



````julia
julia> mm1.trms[2].Λ
8×8 LowerTriangular{Float64,Array{Float64,2}}:
  0.569675      ⋅            ⋅            ⋅             ⋅           ⋅            ⋅           ⋅ 
 -0.0234122    0.0640206     ⋅            ⋅             ⋅           ⋅            ⋅           ⋅ 
 -0.266913    -0.288127     0.0425733     ⋅             ⋅           ⋅            ⋅           ⋅ 
  0.0173413    0.00329602  -0.0139551    0.0829213      ⋅           ⋅            ⋅           ⋅ 
  0.0290032   -0.0307006   -0.0162798   -0.00678668    0.0226446    ⋅            ⋅           ⋅ 
  0.0178621    0.00437284  -0.0184925    0.0223168    -0.0535127   0.00364195    ⋅           ⋅ 
  0.00844082  -0.0241439    0.0991966    0.000985977  -0.0355039  -0.00381326   0.0          ⋅ 
  0.0049074   -0.053426    -0.00479098  -0.0775749     0.0560694  -0.00114592  -5.49465e-5  0.0

````


