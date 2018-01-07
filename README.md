# StatsLib &nbsp; [![Build Status](https://travis-ci.org/kthohr/stats.svg?branch=master)](https://travis-ci.org/kthohr/stats) [![Coverage Status](https://codecov.io/github/kthohr/stats/coverage.svg?branch=master)](https://codecov.io/github/kthohr/stats?branch=master)

StatsLib is a templated C++ library designed for fast computation of statistical distribution functions.

Features:
* Header-only library of density functions, cumulative distribution functions, and quantile functions, as well as random variable generation.
* Compile-time enabled computation through effective use of C++11 `constexpr` functions with the [GCE-Math library](https://github.com/kthohr/gcem).
* A simple, R-like syntax.
* Optional vector-matrix functionality built on the [Armadillo C++ linear algebra library](http://arma.sourceforge.net/).

## Available Distributions

cdf, density, and quantile functions, as well as random variable generation, are available for the following distributions:

* Bernoulli
* Beta
* Binomial
* Cauchy
* Chi-squared
* Exponential
* Gamma
* Inverse-Gamma
* Laplace
* Logistic
* Log-Normal
* Normal (Gaussian)
* Student's t
* Uniform

In addition, density and random variable generation are available for:

* inverse-Wishart
* Multivariate Normal
* Wishart

## Compiler Options

For inline-only functionality (no constexpr flags) use
```cpp
#define STATS_GO_INLINE
```

To remove any Armadillo-related functionality use
```cpp
#define STATS_NO_ARMA
```

## Syntax and Examples

Functions are called using a clean R-like syntax.

* density functions: `d*`. For example, the Normal (Gaussian) density is called using
``` cpp
stats::dnorm(<value>,<mean parameter>,<standard deviation>);
```
* cumulative distribution functions: `p*`. For example, the Gamma CDF is called using
``` cpp
stats::pgamma(<value>,<shape parameter>,<scale parameter>);
```
* quantile functions: `q*`. For example, the Beta quantile is called using
``` cpp
stats::qbeta(<value>,<a parameter>,<b parameter>);
```
* randomization: `r*`. For example, a Logistic random variable is generated with
``` cpp
stats::rlogis(<location parameter>,<scale parameter>);
```

All of these functions have vector and matrix equivalents using the Armadillo library.

* The density, CDF, and quantile functions can all take matrix-valued arguments just as easily as scalar (`double`) arguments.
* The randomization functions (`r*`) can output random matrices of arbitrary size. For example,
```cpp
arma::mat beta_cdf_vals = stats::rgamma(100,50,3.0,2.0);
```
will generate a 100 by 50 matrix of iid Gamma-distributed random variables with parameters (3,2).


Examples with code:
```cpp
// evaluate the normal PDF at x = 1, mu = 0, sigma = 1
double dval_1 = stats::dnorm(1.0,0.0,1.0);
 
// evaluate the normal PDF at x = 1, mu = 0, sigma = 1, and return the log value
double dval_2 = stats::dnorm(1.0,0.0,1.0,true);
 
// evaluate the normal CDF at x = 1, mu = 0, sigma = 1
double pval = stats::pnorm(1.0,0.0,1.0);
 
// evaluate the Laplacian quantile at p = 0.1, mu = 0, sigma = 1
double qval = stats::qlaplace(0.1,0.0,1.0);

// generate a t-distributed random variable with dof = 30
double rval = stats::rt(30);

// matrix output
arma::mat beta_rvs = stats::rbeta(100,100,3.0,2.0);
// matrix input
arma::mat beta_cdf_vals = stats::pbeta(beta_rvs,3.0,2.0);
```

## Installation

StatsLib is a header-only library. Simply copy the contents of the include folder and add the header files to your project using `#include "stats.hpp"`.

## Compile-time computation

StatsLib can operate as a compile- or run-time library. Compile-time features are enabled using the ```constexpr``` specifier:
```cpp
#include "stats.hpp"

int main()
{
    
    constexpr double dens_1 = stats::dlaplace(1.0,1.0,2.0);  // answer = 0.25
    constexpr double prob_1 = stats::plaplace(1.0,1.0,2.0);  // answer = 0.5
    constexpr double quant_1 = stats::qlaplace(0.1,1.0,2.0); // answer = -2.218875...

    return 0;
}
```
Assembly code generated by Clang:
```assembly
LCPI0_0:
	.quad	-4611193153885729483    ## double -2.2188758248682015
LCPI0_1:
	.quad	4602678819172646912     ## double 0.5
LCPI0_2:
	.quad	4598175219545276417     ## double 0.25000000000000006
	.section	__TEXT,__text,regular,pure_instructions
	.globl	_main
	.p2align	4, 0x90
_main:                                  ## @main
	.cfi_startproc
## BB#0:
	push	rbp
Lcfi0:
	.cfi_def_cfa_offset 16
Lcfi1:
	.cfi_offset rbp, -16
	mov	rbp, rsp
Lcfi2:
	.cfi_def_cfa_register rbp
	xor	eax, eax
	movsd	xmm0, qword ptr [rip + LCPI0_0] ## xmm0 = mem[0],zero
	movsd	xmm1, qword ptr [rip + LCPI0_1] ## xmm1 = mem[0],zero
	movsd	xmm2, qword ptr [rip + LCPI0_2] ## xmm2 = mem[0],zero
	mov	dword ptr [rbp - 4], 0
	movsd	qword ptr [rbp - 16], xmm2
	movsd	qword ptr [rbp - 24], xmm1
	movsd	qword ptr [rbp - 32], xmm0
	pop	rbp
	ret
	.cfi_endproc
```

## Author

Keith O'Hara

## License

GPL (>= 2)
