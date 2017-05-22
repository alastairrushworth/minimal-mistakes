### What is knitr?

Knitr [http://yihui.name/knitr/](http://yihui.name/knitr/) is an `R` package that allows straightforward integration of R code for writing reports and is developed by Yihui Xie. It is very powerful, much easier to get started with than Sweave and has potentially a lot of uses. My interest in lies in how easy it is to save the results of an analysis and use them in different documents types with little extra effort – for example including the same plots and tables in papers and in talk slides. Better yet, it all works in RStudio without the need for calling any additional commands to produce the PDFs (assuming you can already compile .tex documents in RStudio).

This mini tutorial is very basic and makes use of RStudio, and highlights a selection the main features in knitr, but hopefully illustrates how easy they are to use. Yihui's website and resources therein are great for info on all of the full capabilities of knitr, the manual is here [http://yihui.name/knitr/demo/manual/](http://yihui.name/knitr/demo/manual/). The LateX code below can be download from here: testKnitr.Rnw. To compile this using RStudio's "Compile PDF" button, you might have to first set "RStudio -> Tools -> Options -> Sweave -> weave Rnw files using" to "knitr". Also make sure you have the R package `knitr` installed too!

```
\documentclass{article}

\begin{document}

\section*{\textbf{0 to knitr in 60 seconds}}

\noindent All code in \texttt{knitr} is run in grouped 'chunks' of
code that perform specific parts of the analysis. Typically an
analysis might start with loading packages, and so the following
chunk does that.

<<loadPackages>>=
library(xtable)
@

\noindent Inside the \verb+<<...>>=+, options can be set that
control how and what is to be displayed within the chunk. In this
case, the only thing provided is the chunk name that has been set
to \texttt{loadPackages}', although chunk names are not strictly
required.\\

\noindent Next, some data is generated and a linear model is
fitted. The chunk will be called \texttt{doLinearModel}.

<<doLinearModel, cache = TRUE>>=
x<-rnorm(100)
y<-3*x + 0.5 + rnorm(100)
lm1<-lm(y~x)
@

\noindent The \texttt{cache = TRUE} option tell knitr that you want
 to store the results of the R code inside the chunk. This will
result in a new directory called 'cache' being created inside the
current working directory where all of the chunk data will be put.
 When the document is subsequently compiled again, the cached R
data will be used provided that neither the chunk contents or
options have not changed since the last compile. If any changes
have been made, the cache will be rebuilt. \textbf{Important note}:
 \textit{any} small change to a chunk (including insertion of space
s) will result in cache rebuilds, so it is important to avoid
making accidental changes to time consuming chunks. We can evaluate
 R objects and perform simple operations in the text body, for exam
ple the largest values of \texttt{x} using \Sexpr{max(x)}.\\

\noindent Plotting in \texttt{knitr} is straightforward:

<<plotLinearModel, dependson ='doLinearModel', cache = TRUE>>=
plot(y~x)
abline(lm1)
@

\noindent This chunk will print a plot. Note the addition of the
option dependson = 'doLinearModel' - this option indicates that
some of the objects in plotLinearModel were created or modified
by the doLinearModel chunk. If changes are made to doLinearModel,
then the the plotLinearModel cache will be rebuilt too - this way,
each time code changes are made, the changes propagate through
subsequent pieces of code that depend on the modifications. The
plotting command have been placed in a seperate chunk from the
linear model fitting so that changes can be made to the plotting
code without affecting the cache that contains \texttt{x} and
\texttt{y} (this very important if the code in doLinearModel
takes a while to execute). The cached plots are saved into a
separate cache directory called 'figure'.\\

<<tableCoefficients, dependson ='doLinearModel', echo = FALSE, results = 'asis', cache = TRUE>>=
print(xtable(summary(lm1)$coefficients, align = 'l|cccc', digits = 3))
@

\noindent This final chunk will produce a table. Because printing
an \texttt{xtable} object results in a bit of latex code, we need
to supply the option results = 'asis' to tell knitr to treat the
output as such. Note in this chunk we have suppressed the printing
of the code by using the echo = FALSE argument.

\end{document}
```
