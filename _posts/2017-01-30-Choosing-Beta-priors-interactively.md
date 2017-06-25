Choosing Beta prior distributions can be difficult when using the default parameterisation because the shape parameters are difficult to interpret. To get around this, I’ve written a simple `rshiny` app for teaching purposes, where the user can play with sliders to change the mean and spread of a Beta distribution and visualise the shape at the same time.  Of course, the app translates the mean and spread back to alpha and beta parameters so that once a good prior has been found, it can be implemented in R.  

[https://alastairrushworth.shinyapps.io/beta_prior_picker/](https://alastairrushworth.shinyapps.io/beta_prior_picker/)


<figure>
<a href="/assets/images/beta_app.png"><img src="/assets/images/beta_app.png"></a>
</figure>
