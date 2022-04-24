## Linear Regression: 
Involves predicting a numerical response variable like in KNN Regression. The prediction is made by fitting a line of 'best-fit' through the training data and then looking up the value to predict 

**Recipe:** takes in the prediction/response variable, followed by at least 1 predictor variable (separated by +), and then the data set to be used (usually training). 


```R
lm_recipe <- recipe(price ~ sqft, data = sacramento_train)
```


    Error in recipe(price ~ sqft, data = sacramento_train): could not find function "recipe"
    Traceback:



**Model Specification:** Always the same for linear regression - engine = "lm", mode = "regression"


```R
lm_spec <- linear_reg() |>
  set_engine("lm") |>
  set_mode("regression")

```


    Error in set_mode(set_engine(linear_reg(), "lm"), "regression"): could not find function "set_mode"
    Traceback:



**Training the regression model:** Uses workflow, adding recipe, mode, then fitting to the _training data_. The testing data is only used during prediction. 


```R
lm_fit <- workflow() |>
  add_recipe(lm_recipe) |>
  add_model(lm_spec) |>
  fit(data = sacramento_train)

lm_fit
```


    Error in fit(add_model(add_recipe(workflow(), lm_recipe), lm_spec), data = sacramento_train): could not find function "fit"
    Traceback:



Unlike in KNN regression, we do not need to scale or center as it does not affect the fit.  

To assess the performance of the model:

**predict:** Takes in the fit and the _testing data_. 

**bind_cols:** Attaches predicted values as new column with name ".pred" to the testing data

**metrics**: truth is the actual value of the response variable, estimate is the predicted value (always set to .pred)



```R
lm_test_results <- lm_fit |>
  predict(sacramento_test) |>
  bind_cols(sacramento_test) |>
  metrics(truth = price, estimate = .pred)

lm_test_results
```


    Error in metrics(bind_cols(predict(lm_fit, sacramento_test), sacramento_test), : could not find function "metrics"
    Traceback:



In particular, to extract the RMSE/RMSPE as a numerical value, we use:


```R
lm_rmspe <- lm_test_results %>%
          filter(.metric == "rmse") %>%
          select(.estimate) %>%
          pull()
        
```


    Error in lm_test_results %>% filter(.metric == "rmse") %>% select(.estimate) %>% : could not find function "%>%"
    Traceback:



**RMSE vs RMSPE**: RMSE is the root-mean-squared error of the model when predicting on the training set, RMSPE is the root-mean-squared _prediction_ error when predicting on the testing set.

**Advantages and Disadvantages of Linear Regression compared to KNN:** 
Advantages: Linear regression is much more computationally efficient, makes better predictions on data outside of the training-data range and is easily interpretable (from coefficents of regression equation)

Disadvantages: Assumes that the data follows a linear model/trend

**Multivariable Linear Regression**: No need to standardise of perform cross-validation (because there are no parameters). 

Predictions from linear regression _always_ form a flat plane, whereas with KNN-regression in multiple variables, we will get a wiggly/flexible surface

#### Issues when performing Linear Regression: ####

**Outliers:** Points that do not follow the usual pattern of the rest of the data can have too much influence on the line of best fit. Typically has less of an impact if the number of outliers is very small compared to the size of the training data

**Multicollinearity:** If you include multiple predictors which are strongly linearly related to each other, small changes to the data can result in large changes to the coefficents of the model. In particular, it becomes hard to distinguish which predictor is causing what effect on the response variable

Note: Always remember to set the seed to some value, to make the analysis reproducible!

## Chunks of Code ##

**Splitting the dataset**


```R
marathon <- read_csv("filename.csv")
marathon_split <- initial_split(marathon, prop = 0.75, strata = time_hrs)
marathon_training <- training(marathon_split)
marathon_testing <- testing(marathon_split)
```


    Error in read_csv("filename.csv"): could not find function "read_csv"
    Traceback:



**Training & Fitting the Model**


```R
lm_recipe <- recipe(price ~ sqft, data = sacramento_train)
lm_spec <- linear_reg() %>%
  set_engine("lm") %>%
  set_mode("regression")

lm_fit <- workflow() %>%
  add_recipe(lm_recipe) %>%
  add_model(lm_spec) %>%
  fit(data = sacramento_train)

lm_fit
```


    Error in recipe(price ~ sqft, data = sacramento_train): could not find function "recipe"
    Traceback:



**Testing the model on testing data**


```R
lm_test_results <- lm_fit %>%
  predict(sacramento_test) %>%
  bind_cols(sacramento_test) %>%
  metrics(truth = price, estimate = .pred)


lm_rmspe <- lm_test_results %>%
          filter(.metric == "rmse") %>%
          select(.estimate) %>%
          pull()

lm_rmspe
```


    Error in lm_fit %>% predict(sacramento_test) %>% bind_cols(sacramento_test) %>% : could not find function "%>%"
    Traceback:



**Visualising Simple Linear Regression on a graph**



```R
lm_predictions <- ggplot(marathon_training, aes(x = max, y = time_hrs)) + 
    geom_point() + 
    geom_smooth(method = "lm", se = FALSE) + 
    labs(x = "Maximum distance ran per week during training", y = "Race time in hours")+ 
    ggtitle("Race time vs Max distance ran per week during training")
```


    Error in ggplot(marathon_training, aes(x = max, y = time_hrs)): could not find function "ggplot"
    Traceback:



**Exploring the data/pairs of predictors using ggpairs**


```R
ggpairs(training_data)
```


    Error in ggpairs(training_data): could not find function "ggpairs"
    Traceback:




```R

```
