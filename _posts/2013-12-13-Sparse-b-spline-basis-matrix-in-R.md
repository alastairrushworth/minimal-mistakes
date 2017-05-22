
This is slightly old news now, but as of R 2.15.1, the functions spline.des and splineDesign which construct design matrices for sets of b-spline basis functions, now have the option of outputing such design matrices in sparse format, and constructing them more efficiently. The results are in the compressed column oriented format (class `dgCMatrix)` used by the `Matrix` package.  This is natural, since b-splines are non-zero over only a small interval and in many applications are used in large numbers, requiring the construction of large model matrices.

You'll need to fire up the `splines` and `Matrix` packages first.

```
library(splines)
library(Matrix)
```

Then generate lots of data, say 100000 standard normal draws, and then use this simulated data to construct dense and sparse matrices.

```
x<-rnorm(100000)
```

```
Bdense<-spline.des(x, knots = seq(min(x),  max(x), length.out = 100), outer.ok = T)$design
Bsparse<-spline.des(x, knots = seq(min(x),  max(x), length.out = 100), outer.ok = T, sparse = T)$design
```

There is a speed up in constructing the above,

```
system.time(spline.des(x, knots = seq(min(x),  max(x), length.out = 100), outer.ok = T))
system.time(spline.des(x, knots = seq(min(x),  max(x), length.out = 100), outer.ok = T, sparse = T))
```

But the main thing is how much faster things are from here on with `Bsparse`.  Suppose we wish to calculate

```
t(Bdense) %*% Bdense
```

Well this is faster either way using `crossprod`, and blazingly fast for the sparse version:

```
system.time(t(Bdense) %*% Bdense)
system.time(crossprod(Bdense))
system.time(crossprod(Bsparse))
```

So far so good.  The dense output takes up 73.2Mb compared to only 4.6Mb in the sparse version in this admittedly contrived example (such examples definitely can and do arise, particularly varying coefficient models where many basis functions are needed to describe a parameter’s smooth trajectory as another variable changes, such as with time for example).

```
object.size(Bdense)/1024^2
object.size(Bsparse)/1024^2
```


Something that I've found handy is the ability to swap between different sparse matrix formats in R.  For example, if I'd like to manipulate `Bsparse` using the `spam` package, which has some very useful tools for working with Gaussian Markov random fields, the summary function can be used on `Matrix` objects to extract the triplet form, which can then easily be used to construct a spam object:

```
library(spam)
trip.Matrix<-summary(Bsparse)
Bsparse.spam<-spam(list(i = trip.Matrix[,1], j = trip.Matrix[,2], trip.Matrix[,3]),nrow = nrow(Bsparse), ncol = ncol(Bsparse))
class(Bsparse.spam)
all.equal(as.matrix(Bsparse.spam), as.matrix(Bsparse), check.attributes = F)
```

