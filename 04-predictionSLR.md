---
source: Rmd
title: Making predictions from a simple linear regression model
teaching: 10
exercises: 10
---



::::::::::::::::::::::::::::::::::::::: objectives

- Calculate a prediction from a simple linear regression model using parameter estimates given by the model output.
- Use the predict function to generate predictions from a simple linear regression model.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How can predictions be manually obtained from a simple linear regression model?
- How can R be used to obtain predictions from a simple linear regression model?

::::::::::::::::::::::::::::::::::::::::::::::::::

One of the features of linear regression is prediction: a model presents predicted mean values for the outcome variable for any values of the explanatory variables. We have already seen this in the previous episodes through our `effect_plot()` outputs, which showed mean predicted responses as straight lines (episode 2) or individual points for levels of a categorical variable (episodes 3). Here, we will see how to obtain predicted values and the uncertainty surrounding them.

## Calculating predictions manually

First, we can calculate a predicted value manually. From the `summ()` output associated with our `Weight_Height_lm` model from episode 2, we can write the model as $E(\text{Weight}) = \beta_0 + \beta_1 \times \text{Height} = -70.194 + 0.901 \times \text{Height}$. The output can be found again below. If we take a height of 165 cm, then our model predicts an average weight of $-70.194 + 0.901 \times 165 = 78.471$ kg.


```r
Weight_Height_lm <- dat %>%
  filter(Age > 17) %>%
  lm(formula = Weight ~ Height)

summ(Weight_Height_lm)
```

```{.output}
MODEL INFO:
Observations: 6177 (320 missing obs. deleted)
Dependent Variable: Weight
Type: OLS linear regression 

MODEL FIT:
F(1,6175) = 1398.22, p = 0.00
R² = 0.18
Adj. R² = 0.18 

Standard errors: OLS
-------------------------------------------------
                      Est.   S.E.   t val.      p
----------------- -------- ------ -------- ------
(Intercept)         -70.19   4.06   -17.29   0.00
Height                0.90   0.02    37.39   0.00
-------------------------------------------------
```

:::::::::::::::::::::::::::::::::::::::  challenge

## Exercise

Given the `summ` output from our `BPSysAve_AgeMonths_lm` model,
the model can be described as

$E(\text{BPSysAve}) = \beta_0 + \beta_1 \times \text{Age (months)} = 101.812 + 0.033 \times \text{Age (months)}$.

What level of average systolic blood pressure does the model predict, on average,
for an individual with an age of 480 months?

:::::::::::::::  solution

## Solution

$101.812 + 0.033 * 480 = 117.652 \text{mmHg}$.

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

## Making predictions using `make_predictions()`

Using the `make_predictions()` function brings two advantages. First, when calculating multiple predictions, we are saved the effort of inserting multiple values into our model manually and doing the calculations. Secondly, `make_predictions()` returns 95% confidence intervals around the predictions, giving us a sense of the uncertainty around the predictions.

To use `make_predictions()`, we need to create a `tibble` with the explanatory variable values for which we wish to have mean predictions from the model. We do this using the `tibble()` function. Note that the column name must correspond to the name of the explanatory variable in the model, i.e. `Height`. In the code below, we create a `tibble` with the values 150, 160, 170 and 180. We then provide `make_predictions()` with this `tibble`, alongside the model from which we wish to have predictions. By default, 95% confidence intervals are returned.

We see that the model predicts an average weight of 64.88 kg for an individual with a height of 150 cm, with a 95% confidence interval of [63\.9kg, 65.9kg].


```r
Heights <- tibble(Height = c(150, 160, 170, 180))

make_predictions(Weight_Height_lm, new_data = Heights)
```

```{.output}
# A tibble: 4 × 4
  Height Weight  ymin  ymax
   <dbl>  <dbl> <dbl> <dbl>
1    150   64.9  63.9  65.9
2    160   73.9  73.3  74.5
3    170   82.9  82.4  83.4
4    180   91.9  91.2  92.6
```

:::::::::::::::::::::::::::::::::::::::  challenge

## Exercise

1. Using the `make_predictions()` function, obtain the expected mean average systolic blood pressure levels predicted by the `BPSysAve_AgeMonths_lm` model for individuals with an age of 300, 400, 500 and 600 months.
2. Obtain 95% confidence intervals for these predictions.
3. How are these confidence intervals interpreted?

:::::::::::::::  solution

## Solution


```r
BPSysAve_AgeMonths_lm <- dat %>% 
 filter(Age > 17) %>%
 lm(formula = BPSysAve ~ AgeMonths)

ages <- tibble(AgeMonths = c(300, 400, 500, 600))

make_predictions(BPSysAve_AgeMonths_lm, new_data = ages)
```

```{.output}
# A tibble: 4 × 4
  AgeMonths BPSysAve  ymin  ymax
      <dbl>    <dbl> <dbl> <dbl>
1       300     112.  111.  113.
2       400     115.  114.  116.
3       500     118.  118.  119.
4       600     121.  121.  122.
```

Recall that 95% of 95% confidence intervals are expected to contain the
population mean.
Therefore, we can be fairly confident that the true population means lie
somewhere between the bounds of the intervals, assuming that our model is good.

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: keypoints

- Predictions of the mean in the outcome variable can be manually calculated using the model's equation.
- Predictions of multiple means in the outcome variable alongside 95% CIs can be obtained using the `make_predictions()` function.

::::::::::::::::::::::::::::::::::::::::::::::::::

