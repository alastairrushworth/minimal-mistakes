
### Doing machine learning in R

I recently spoke to the [EdinbR](http://edinbr.org) R user group about predictive modelling and machine learning in R.  The slides are attached available to download <a href="/assets/images/talk_amr.pdf" download>here</a>.  The talk was a very high-level overview of how to actually train predictive models in R, and I'd probably summarise the following as the key points:

*  You can fit most commonly used machine learning tools in R: you don't have to learn Python
*  Many individual machine learning algorithms in R exist in separate packages, with varying syntax and input / output, making training painful for multiple models
*  Meta-packages (CRAN terminology) like __caret__ and __mlr__ provide high-level interfaces to a huge variety of ML algorithms
*  __caret__ streamlines the process of cross-validation, prediction and calculating accuracy


<figure class="half">
    <a href="/assets/images/wordle.png"><img src="/assets/images/wordle.png"></a>
    <a href="/assets/images/meta.png"><img src="/assets/images/meta.png"></a>
</figure>
