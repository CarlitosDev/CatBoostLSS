# CatBoostLSS - An extension of CatBoost to probabilistic forecasting
We propose a new framework of CatBoost that predicts the entire conditional distribution of a univariate response variable. In particular, **CatBoostLSS** models all moments of a parametric distribution, i.e., mean, location, scale and shape (LSS), instead of the conditional mean only. Choosing from a wide range of continuous, discrete and mixed discrete-continuous distribution, modelling and predicting the entire conditional distribution greatly enhances the flexibility of CatBoost, as it allows to gain additional insight into the data generating process, as well as to create probabilistic forecasts from which prediction intervals and quantiles of interest can be derived. In the following, we provide a short walk-through of the functionality of **CatBoostLSS**.

## Examples

### Simulation

We start with a simulated a data set that exhibits heteroscedasticity where the interest lies in predicting the 5% and 95% quantiles. Details on the data generating process can be found [*here*](https://github.com/StatMixedML/XGBoostLSS). The dots in red show points that lie outside the 5% and 95% quantiles, which are indicated by the black dashed lines.

![Optional Text](../master/plots/CatBoostLSS_sim_data.png)

Let's fit a **CatBoostLSS** to the data. In general, the syntax is similar to the original CatBoost implementation. However, the user has to make a distributional assumption by specifying a family in the function call. As the data has been generated by a Normal distribution, we use the Normal as a function input.

```python
# Import library
from catboostlss import *
import shap

# Data for CatBoostLSS
train_cblss = Pool(data = train_data,
                   label = train_labels,
                   has_header = True)

test_cblss = Pool(data = test_data,
                  label = test_labels,
                  has_header = True)

# Fit model
cblss = CatBoostLSSRegressor(family = "NO",
                             leaf_estimation_method = "Newton",
                             logging_level = "Silent",
                             random_seed = 123,
                             thread_count = -1)

cblss.fit(train_cblss)
```
As CatBoost yields state-of-the-art prediction results without extensive data training typically required by other machine learning methods, we estimate the model with its default parameter settings. Once the model is trained, we can predict all parameters of the distribution.

```python
preds_cblss = cblss.predict(test_cblss)  
```
As **CatBoostLSS** allows to model the entire conditional distribution, we can draw random samples from the predicted distribution, which allows us to create prediction intervals and quantiles of interest. The below image shows the predictions of **CatBoostLSS** for the 5% and 95% quantile in blue.

![Optional Text](../master/plots/CatBoostLSS_sim.png)
