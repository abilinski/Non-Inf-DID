# Don't Test for Parallel Pre-trends: A Non-Inferiority Approach to Difference-in-Differences

In this vignette, we walk through implementing a non-inferiority approach to difference-in-differences, as described in [Bilinski and Hatfield (2019)](https://arxiv.org/abs/1805.03273).  R code is under construction but available on request.  For more about diff-in-diff, you can also visit the Health Policy Data Science Lab's [website](https://diff.healthpolicydatascience.org/).

## I thought I should test whether pre-trends are parallel.  You're telling me not to?
In many cases, a traditional parallel trends test has low power and conflates this with "no violation" of the parallel trends assumption.  But if you're well-powered, you might see a significant trend difference when it is unimportant.  Testing for pre-trends is not a reliable indicator of meaningful violations, either when a significant effect is observed or not.

## So I think trends are parallel -- how do I estimate my model?
You should run a base model that includes differential linear pre-trends.  Because you only want to fit differential trends on pre-intervention data, you should include each post-intervention time period as a dummy variable in your model, and average these to obtain an average treatment effect.  For example, without covariates, the specification would be:

<p style="text-align: center">
<a href="https://www.codecogs.com/eqnedit.php?latex=y_{it}&space;=&space;\beta_0&space;&plus;&space;\sum_{k=T_0}^T&space;\beta_{k}&space;\mathbb{I}(t&space;=k&space;\cap&space;d_i&space;=&space;1)&space;&plus;&space;\theta&space;d_i&space;t&space;&plus;&space;\alpha_i&space;&plus;&space;\gamma_t&space;&plus;&space;\epsilon_{it}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?y_{it}&space;=&space;\beta_0&space;&plus;&space;\sum_{k=T_0}^T&space;\beta_{k}&space;\mathbb{I}(t&space;=k&space;\cap&space;d_i&space;=&space;1)&space;&plus;&space;\theta&space;d_i&space;t&space;&plus;&space;\alpha_i&space;&plus;&space;\gamma_t&space;&plus;&space;\epsilon_{it}" title="y_{it} = \beta_0 + \sum_{k=T_0}^T \beta_{k} \mathbb{I}(t =k \cap d_i = 1) + \theta d_i t + \alpha_i + \gamma_t + \epsilon_{it}" /></a>,

<a href="https://www.codecogs.com/eqnedit.php?latex=\beta&space;=&space;\frac{1}{T-T_0&space;+&space;1}&space;\sum_{k&space;=&space;T_0}^T&space;\beta_{k}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\beta&space;=&space;\frac{1}{T-T_0&space;+&space;1}&space;\sum_{k&space;=&space;T_0}^T&space;\beta_{k}" title="\beta = \frac{1}{T-T_0 + 1} \sum_{k = T_0}^T \beta_{k}" /></a>
</p>

where time is indexed by t, the intervention begins at t=T, <a href="https://www.codecogs.com/eqnedit.php?latex=d_i" target="_blank"><img src="https://latex.codecogs.com/gif.latex?d_i" title="d_i" /></a> is an indicator that an observation is in the treatment group, <a href="https://www.codecogs.com/eqnedit.php?latex=\alpha_i" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\alpha_i" title="\alpha_i" /></a> is a unit fixed effect, <a href="https://www.codecogs.com/eqnedit.php?latex=\gamma_t" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\gamma_t" title="\gamma_t" /></a> is a time fixed effect.  The average treatment effect is <a href="https://www.codecogs.com/eqnedit.php?latex=\beta" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\beta" title="\beta" /></a>.

## But I said I believe trends are parallel.  Why should I include differential trends?
If trends are parallel, your treatment effect is still unbiased.  If there are small (but substantively unimportant) trend differences, this specification reduces bias.

## But won't this hurt power?  What if I only have a few pre-periods?
We show in our paper that power to detect a treatment effect in a model with differential linear pre-trends is roughly the same as power to rule out a violation of parallel trends the same size as your treatment effect.  If you can no longer detect your effect in a more complex model, you probably couldn't provide much statistical evidence about the parallel trends assumption.

In this case, you might try to increase power by adding time-invariant covariates -- which reduce standard error because they are correlated with the outcome but not treatment or the trend difference.  You can also consider showing that groups are similar in other ways (e.g. covariates of interest) or conduct sensitivity analysis to place bounds on expected bias.  But because this type of analysis relies on contextual knowledge that would be obtained from pre-trends, we think diff-in-diffs are stronger if authors can provide statistical evidence for stable pre-trends.

## Okay, done with that bit.  What else should I do?
We also recommend providing information about how much your treatment effect from this model with differential trends differs from a model that enforces parallel trends.  Even if you see a significant effect in this base model, if a large portion of it is driven by differential trends, we still wouldn't trust the parallel trends assumption and might not trust that our treatment group provides a good counterfactual.

Our approach can do this either as a difference or ratio between treatment effects in simpler and more complex models.  Generally, if you can't rule out a violation of parallel trends more than half the size of the treatment effect (e.g., ratio < 0.5), you might be concerned about the validity of the parallel trends assumption.

(See our paper for more discussion on choosing a threshold.  If you would only report results if they fell within this threshold, you can adjust your standard errors to reflect this.). 

## Actually, my trends don't seem very parallel.  Or maybe I believed they weren't parallel from the start.  What next?
You can choose an alternative data-generating process, like parallel growth.  If you do this, you should present your treatment effect estimate from a model with more complex data-generating process, like a restricted spline.  You can then compare treatment effects or provide a ratio, compared to parallel growth. 

You could also model potential mechanisms for differential trends as time-varying covariates and see if this explains so of the difference you see.  Finally, we note that you might not have enough power to say much about the parallel trends assumption.  That's okay, as long as this isn't translated into "passing" a parallel trends test.

## My intervention has staggered start times.  How do I incorporate this?
This is an area of active research.  [This page](https://andrewcbaker.netlify.com/2019/09/25/difference-in-differences-methodology/) does a great job of summarizing different approaches, and most estimators can be adapted to allow for trend differences. 

## What plots help me in all of this?
We make two main plots.  First, we like a standard plot that shows us the unadjusted treatment and comparison groups.  This gives us a general sense of the data and tells us a bit about how levels vary between treatment and comparison groups.

We also recommend an event-study plot.   This type of plot estimates a “treatment effect” at each time point relative to the last time point before the intervention. It thus illustrates pre-intervention trends, adjusted for covariates in the model. Our preferred version of the event-study plot extrapolates pre-intervention trends into the post-intervention period, alowing readers to visualize the impact of potential trend differences and think through the reasonableness of extrapolations.  (The example below is from our paper.)

![img](https://github.com/abilinski/Non-Inf-DID/blob/master/esp_reformatted.png?style=centerme)
