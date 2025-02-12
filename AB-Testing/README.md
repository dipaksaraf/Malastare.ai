## Malastare AI
## RIHAD VARIAWA, Data Scientist
## AB Testing

<p align="center">
  <img src="./fig/logo.png"/>
</p>

Received: 05 June 2019; Accepted: 19 November 2019; Published: 01 December 2019


## Experiment Design

There are two options a user can choose for Udacity courses. The first is a "free trial", the second is "access course materials". When they click on the first option, they will be asked to enter their payment data and enrolled in a 14 days trial period. After that, they will be charged until they cancel their subscription.

For the second option, they are able to view all course material for free, but they will not receive coaching and cannot submit their final projects (and get a course certificate).

Udacity tested how a change of the free trial period would change the users behavior. After clicking on the "free trial" button, the users were asked how much time they would be able to spent on the course. If that was 5 hours or more, they were shown they were forwarded to the page where they have to enter their payment data. If they entered less than 5 hours, a warning was displayed that the course usually requires a commitment of more than 5 hours and accessing the free course material might be the better option. Still, they had the option to enroll in the free trial instead.

The hypothesis was that this would reduce the number of frustrated students who left the free trial because they realized they didn't have enough time, but the number of students who would continue past the free trial would not be reduced.

As a unit of diversion cookies were used. After students enrolled, they can be identified by their user-id. It's not possible to enroll in the same free trial more than once.

### Metric Choice

I will first list the metrics with an explanation for each. Then I will present my choices for invariant and evaluation metrics.

* Number of cookies: That is, number of unique cookies to view the course overview page
* Number of user-ids: That is, number of users who enroll in the free trial.
* Number of clicks: That is, number of unique cookies to click the "Start free trial" button (which happens before the free trial screener is trigger)
* Click-through-probability:  That is, number of unique cookies to click the "Start free trial" button divided by number of unique cookies to view the course overview page
* Gross conversion: That is, number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the "Start free trial" button.
* Retention: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by number of user-ids to complete checkout.
* Net conversion: That is, number of user-ids to remain enrolled past the 14-day
boundary (and thus make at least one payment) divided by the number of unique
cookies to click the "Start free trial" button.

#### Invariant metrics

Invariant metrics shouldn't change across the experiment and the control group. They can be used to check the integrity of the measurement after the experiment.

The main goal of this experiment is to see how users react to the new query message displayed after they want to join for the trial. Therefore, the following variables do not change:

* Number of cookies: The number represents the unique visitors of the website.
    + The aim is to split them between the experiment and the control group. For this reason, the cookie is an invariant metric.
    + The number of cookies is not an evaluation metric, because it should not be different between the control and the experiment group.
* Number of clicks: The experiment is focusing on the part after the "Start free trial" button has been pressed.
    + For this reason, the number of clicks should be the same across the experiment and the control group.
    + As it will not be possible to meassure a difference with this metric, it is not an evaluation metric.
* Click-through-probability: This probability is the number of clicks divided by the number of cookies.
    + As both of these variables are invariant (see above for the explanation), the click-through-probability is invariant as well.

#### Evaluation metrics

Evaluation metrics are expected to show the changes across the experiment and the control group. For this experiment we have the following evaluation metrics:

* Gross conversion: This is the number of user-ids to complete the checkout divided by the number of cookies who click the "Free trial"-button.
    + The hypothesis is that the gross conversion for the experiment group is lower than the gross conversion for the control group.
* Net conversion: The net conversion is the evaluation metric representing the number of students who keep subscribed after the free trial period.
    + The hypothesis is that it doesn't decrease for the experiment group. For that reason, it is an evaluation metric.
    + For the same reason, net conversion cannot be a a invariant metric.

#### Metrics not used here

* Number of user-ids: The number of user-ids represents the number of people who enroll in the free trial period.
    + The number of user-ids can be considered as an evaluation metric because we could compare the absolute difference between the experimental and the control group. However, the gross conversion is a much better metric because it is the relative difference.
    + The number of user-ids is not an invariant metric, as we expect it to be different between the groups.
* Retention: Retention is the number of user-ids enrolled after the trial period divided by the user-ids completing the checkout.
    + This metric can be considered as an evaluation metric as we expect it to be different between the groups. For this reason, it cannot be a invariant metric.
    + However, I will not use retention as an evaluation metric in this experiment because we only have a small amount of data for payments and enrollments. We would have to collect more, which would make the experiment last longer.

### Measuring Standard Deviation

* Unique cookies to view page per day:	40000
* Unique cookies to click "Start free trial" per day:	3200
* Enrollments per day:	660
* Click-through-probability on "Start free trial":	0.08
* Probability of enrolling, given click:	0.20625
* Probability of payment, given enroll:	0.53
* Probability of payment, given click:	0.1093125

In the experiment, we predict we need 5000 cookies oer day in each group. With the given numbers above and a scale factor of ``r 5000/40000``, this results in ``r 3200*0.125`` users who click the "Start free trial"-button and ``r 660*0.125`` enrollments.

##### Gross conversion

```{r eval=FALSE}
gconv_sd <- sqrt(0.2063 * (1-0.2063)/400)
gross_sd <- round(gconv_sd,4)
```

The SD for gross conversion is `r gross_sd`.

##### Net conversion

```{r eval=FALSE}
clicks_sd <- sqrt(0.1093 * (1-0.1093)/400)
clicks_sd <- round(clicks_sd,4)
```

The SD for net conversion is `r clicks_sd`.

The unit of diversion in this experiment is a cookie, which is also the unit of analysis for both metrics. Therefore, the analytic and empirical variability are expected to be comparable

### Sizing

#### Number of Samples vs. Power

##### Gross conversion
* Baseline conversion rate:	20.625%
* Minimum effect: 1%
* $\alpha$: 0.05
* $\beta$: 80%
* Sample size: 25.835
* click-through-probability: 0.08

```{r, comment= ""}
25835/0.08*2
```

##### Net conversion

* Baseline conversion rate:	10.93%
* Minimum effect: 0.75%
* Sample size: 27.411

```{r, comment= ""}
27411/0.08*2
```

As the project should have appropriate numbersfor both metrics, we have to use the higher number of pageviews. So we have to get 685.275 pageviews.

#### Duration vs. Exposure

According to the numbers given above, there are 40000 unique visitors per day. To have 685.275 pageviews, the experiment will have to run for ``r ceiling(685275/40000)`` days.

I do not think that the experiment is risky in any way. Users who plan to invest only a small amount of time on the course receive a warning. They can still join the course.

The experiment is not ethically problematic and the data is not sensitive. It might be possible to draw some conclusions from the information. For example, if users are able to spend more than 30 hours on the course, it's unlikely that they have a full-time job. But this information is not very confidential.

There is a risk that net conversion will decrease significantly, which would mean a loss of money for Udacity. The implementation is also not problematic as no new hardware or extensive changes of the website are required.

## Experiment Analysis

### Sanity Checks

```{r eval=FALSE, echo=FALSE}
control <- read.table("Control.csv", sep=",", header = TRUE)
experiment <- read.table("Experiment.csv", sep=",", header = TRUE)
```

#### Number of cookies

```{r eval=FALSE, echo=FALSE}
cViews <- sum(control$Pageviews)
eViews <- sum(experiment$Pageviews)
sd_cookies <- round(sqrt((0.5*0.5)/(cViews+eViews)),4)
me_cookies <- sd_cookies*1.96
lowerBound_cookies <- 0.5 - me_cookies
upperBound_cookies <- 0.5 + me_cookies
observed_cookies <- round(cViews/(cViews+eViews),4)
```

* Lower bound: `r round(lowerBound_cookies ,4)`
* Upper bound: `r round(upperBound_cookies ,4)`
* Confidence interval: [`r round(0.5- me_cookies,4)`, `r round(0.5 + me_cookies,4)`]
* Observed probability: `r observed_cookies`

#### Number of clicks

```{r eval=FALSE, echo=FALSE}
cClicks <- sum(control$Clicks)
eClicks <- sum(experiment$Clicks)
sd_clicks <- round(sqrt((0.5*0.5)/(cClicks+eClicks)),4)
me_clicks <- sd_clicks*1.96
lowerBound_clicks <- 0.5 - me_clicks
upperBound_clicks <- 0.5 + me_clicks
observed_clicks <- cClicks/(cClicks+eClicks)
```

* Lower bound: `r round(lowerBound_clicks,4)`
* Upper bound: `r round(upperBound_clicks,4)`
* Confidence interval: [`r round(0.5- me_clicks,4)`, `r round(0.5 + me_clicks,4)`]
* Observed probability: `r round(observed_clicks,4)`

#### Click-through-probability

```{r eval=FALSE, echo=FALSE}
click_throughCTRL <- cClicks/cViews
sd_clthrough <- sqrt(click_throughCTRL*(1-click_throughCTRL)/cViews)
me_clickthrough <- sd_clthrough*1.96
lowerBound_clickthrough <- 0.5 - me_clickthrough
upperBound_clickthrough <- 0.5 + me_clickthrough
observed_clickthrough <- eClicks/eViews
```

* Lower bound: `r round(lowerBound_clickthrough,4)`
* Upper bound: `r round(upperBound_clickthrough,4)`
* Confidence interval: [`r round(click_throughCTRL - me_clickthrough,4)`, `r round(click_throughCTRL + me_clickthrough,4)`]
* Observed probability: `r round(observed_clickthrough,4)`

All observed values fall in the expected range, so all metrics pass the sanity check.

### Result Analysis

#### Effect Size Tests

```{r eval=FALSE, echo=FALSE}
control_na <- control[complete.cases(control),]
experiment_na <- experiment[complete.cases(experiment),]

NcEnrollment <- sum(control_na$Enrollments, na.rm=T)
NeEnrollment <- sum(experiment_na$Enrollments, na.rm=T)
NcPayments <- sum(control_na$Payments, na.rm=T)
NePayments <- sum(experiment_na$Payments, na.rm=T)
NcClicks <- sum(control_na$Clicks, na.rm=T)
NeClicks <- sum(experiment_na$Clicks, na.rm=T)
```

##### Gross conversion

```{r eval=FALSE, echo=FALSE}
gc <- (NcEnrollment + NeEnrollment)/(NcClicks + NeClicks)
gc_se <- sqrt(gc*(1-gc)*(1/NcClicks+1/NeClicks))
gc_me <- gc_se *1.96
gc_diff <- (NeEnrollment/NeClicks) - (NcEnrollment/NcClicks)
```

*  Difference: `r round(gc_diff,4)`
* 95% confidence interval: [`r round(gc_diff - gc_me,4)`, `r round(gc_diff + gc_me,4)`]

The difference is statistically significant as the confidence interval does not include Zero. It is also practically significant as it does not include dmin.

##### Net conversion

```{r eval=FALSE, echo=FALSE}
nc <- (NcPayments + NePayments)/(NcClicks + NeClicks)
nc_se <- sqrt(nc*(1-nc)*(1/NcClicks+1/NeClicks))
nc_me <- nc_se *1.96
nc_diff <- (NePayments/NeClicks) - (NcPayments/NcClicks)
```

*  Difference: `r round(nc_diff,4)`
* 95% confidence interval: [`r round(nc_diff - nc_me,4)`, `r round(nc_diff + nc_me,4)`]

The confidence interval includes Zero, so it's not statistically significant. It includes the lower boundary for practical significance, so it's also not practically significant.

#### Sign Tests

To do a sign test we can use the binom.test function in R. I first create columns in the dataframe to make it easier to calculate the difference. I then count the occurences of negative and positive values. 

##### Gross Conversion

```{r eval=FALSE, echo=FALSE}
experiment_na$gc <- experiment_na$Enrollments/experiment_na$Clicks
control_na$gc <- control_na$Enrollments/control_na$Clicks

gc_values <- experiment_na$gc - control_na$gc
length(gc_values)
length(gc_values[gc_values > 0])
```

There were 23 days recorded and 19 days in which the probability of enrollment for the experiment group is lower than for the control group.

```{r, comment= ""}
binom.test(4,23)
```

The binomical test gives us a p-value = 0.002599, so the result is statistically significant.

##### Net Conversion

```{r eval=FALSE, echo=FALSE}
experiment_na$nc <- experiment_na$Payments/experiment_na$Clicks
control_na$nc <- control_na$Payments/control_na$Clicks

nc_values <- experiment_na$nc - control_na$nc
length(nc_values)
length(nc_values[nc_values > 0])
```

Like above, 23 days were recorded, but here we had only 10 days with an improvement.

```{r, comment= ""}
binom.test(10,23)
```

The p-value = 0.6776, so the difference is not statistically significant.

#### Summary

The main result of the experiment is that the gross conversion did decrease significantly. This supports the hypothesis. However, the net conversion had a confidence interval above and below zero and ist not statically significant for that reason.

For this experiment the Bonferroni correction was not used because all evaluation metrics need to be statistically significant to recommend the changes.

In an experiment like this, the risk of a Type II error decreases compared to an experiment in which only one metric out of all metrics needs to be statistically significant. In the latter, using the Bonferroni correction is recommended because it is more conservative, so the risk of a Type II error is lowre.

### Recommendation

There are two main points we have learned from the experiment:

* Less users enroll in the free trial: the Gross conversion is statistically and practically lower, so we can assume that the warning regarding the required working hours has an effect.
* The results of the Net conversion are neither stastically nor practically significant.
    + Furthermore, the confidence interval includes negative values and the negative of the practical significance bondary, which indicates that the change could even have a negative influence (i.e. a lower amount of paid subscriptions, the second part of our hypothesis).

Therefore, it is *not* recommended to implement the change.

## Follow-Up Experiment

### Description

In this experiment we only focussed on the time a user is able to spend on the course and considered this as the main reason for users to cancel their subscription before the end of the trial period.
However, I think there are many more reasons why users cancel. One, which I consider quite likely for Nanodegree programs is that user are just overchallenged by the long list of course material. They might also have problems to start with a topic because they are missing basics.

My idea would be to contact users after half of the trial period (i.e. 7 days) and invite users to get some expert advice in case there are any problems with the content. This does not necessarily require an individual personal guidance, but an in-depth analysis of the problem. This could be supported by a well-sorted list of additional course material (similar to the recommendations users get in the nanodegrees).
To make sure that users read this invitation, it could be shown when logging in but also send via email.

#### Hypothesis

Sending users emails to invite them to get expert advice will increase the number of students who keep enrolled after the free trial period.

#### Metrics

The metrics would be similar to the metrics used in this experiment. As this experiment takes place after users register, we should not use any metrics involving cookies or clicks. The unit of diversion is the user-id.

##### Invariant metrics

* Number of user-ids: That is, number of users who enroll in the free trial.

##### Evaluation metrics

* Retention: The number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by number of user-ids to complete checkout.

#### Downsides

This experiment setting has some downsides:
It is not completely clear if users actually read and use the material they received as help or if they just ignore them.
If external websites or videos are used, it wouldn't be possibel to track if users read or watch them. However, even if this was possible, this would become a more ethically problematic experiment as it would mean many users would have to be monitored in a very detailed way.

## Ressources

* Udacity course material
* [http://www.stat.berkeley.edu/~mgoldman/Section0402.pdf](http://www.stat.berkeley.edu/~mgoldman/Section0402.pdf)
* [When to use the Bonferroni correction](http://onlinelibrary.wiley.com/doi/10.1111/opo.12131/full)


## Conflicts of Interest: The practitioner declare no conflict of interest.

