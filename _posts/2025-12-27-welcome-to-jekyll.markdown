---
layout: post
title:  "The Beauty of R's pnorm"
date:   2025-12-27 17:19:54 +0800
categories: statistics programming
---

Earlier this year, I set out to learn a new programming language. I settled on [Elixir](https://elixir-lang.org/), which is a dynamic, functional language that runs on the Erlang VM. 

Since Elixir doesn't currently have any recently-maintained statistics libraries (along the lines of `scipy-stats` or base R), I decided that it would be a fun challenge to implement one. Soon I was reading up on how these languages chose to implement functions like `pnorm`, `pbeta`, which involve complicated integrals, like the [error function](https://en.wikipedia.org/wiki/Error_function) or the [regularized incomplete beta function](https://en.wikipedia.org/wiki/Beta_function). 

Users of statistics libraries are spoiled for choice. Students learn about the normal distribution in school, where, during exams, we pull up [lookup tables for Z-scores](https://math.arizona.edu/~rsims/ma464/standardnormaltable.pdf). Fortunately, we really only ever need 0.95053 (Z-score: 1.65) and 0.97500 (Z-score 1.96). We then promptly trade it in for R or Python: `qnorm`, `pnorm`, `scipy.stats.norm`.

But how do these functions run, under the hood? In school, these are reduced to computations. During exams, we're allowed to leave them as unsimplified integrals. But what happens when we need an actual value? A decimal representation, say, that lives concretely on the real number line...

To compute the cumulative density function of the standard normal, there are a few approaches, like an approximation by [rational Chebyshev functions](https://en.wikipedia.org/wiki/Chebyshev_rational_functions). This is basically how `pnorm` is [implemented in R](https://github.com/SurajGupta/r-source/blob/master/src/nmath/pnorm.c). 

This was originally implemented as part of a portable FORTRAN package, [Algorithm 715](https://dl.acm.org/doi/abs/10.1145/151271.151273), and later ported over to R's core C libraries. It is a wonder of software engineering. The algorithm is beautiful in its simplicity.

1. Only multiplications and additions are required.
2. The computation branches based only on how big the input is: one set of constants for small values, one set for medium values, one set for very large values.
3. Despite this, the algorithm manages to approximate a complex analytical form involving the square root of pi and a nonlinear exponential integral.

Algorithm 715 has stood the test of time. Its implementation is still used in R and MATLAB. Its creator, [W.J. Cody](https://history.siam.org/oralhistories/cody.htm), was also involved in the standardization of IEE754 hardware floating point. 

Not much is known about Cody, but he retired in 1991 and has since passed away. I managed to find an [interview that SIAM conducted](https://history.siam.org/pdfs2/Cody_returned_SIAM.pdf):

> INTERVIEWER: If you had to pick one, to chisel on your gravestone or whatever, what you would most like to remembered for? 

> CODY: Family man.... I never put the professional considerations ahead of the family... I turned down opportunities to speak, to travel to various places, if it conflicted with what I considered to be my primary concern, which was my family. 

I found this strikingly tender. A humble man, who put his family first, who painstakingly derived the rational floating point numbers used to approximate a function that frequently has irrational output. It's not glamorous work, and much of its provenance, including its creator, lost to time.

I wonder what Cody might say, now that Algorithm 715 is used daily by statisticians everywhere.
