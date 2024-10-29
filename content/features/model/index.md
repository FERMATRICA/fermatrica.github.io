+++
author = "FERMATRICA"
title = "Model & Optimizer"
date = "2024-10-16"
description = "Model & Optimizer"
tags = [
    "modeling",
	"optimising"
]
series = ["Features Guide"]
+++

![Modeling and optimizing](/features/model/intro.jpg)

## 1. Key ideas

FERMATRICA separates the model definition into two contours:

- Inner contour - generally a linear model.

- Outer contour - transformations that occur before building/executing the linear model and/or after it.


By splitting the model into two parts, we avoid the issue of adjusting parameters/coefficients for a complex nonlinear model directly. Nonlinearity is moved to a separate contour with appropriate algorithms, effectively representing parameterized feature engineering. The inner contour retains the transformed variables with included nonlinearity, allowing for the use of simple and fast linear algorithms like OLS/LME.

The specificity of FERMATRICA is a broader interpretation of the outer contour. The classical approach allows building models with only one type of dependency of the target variable on factors - either additive or multiplicative. In FERMATRICA, you can add multiplicative factors to additive ones by specifying them in the LHS transformations. Such additional factors will be optimized using the outer contour alongside the transformation parameters.

But it is only the beginning. In addition to multiplicative factors, other transformations can be added to the outer contour, including auxiliary models for correcting both Y and X.


## 2. Architecture and Transformations

Let's consider model architectures available in FERMATRICA.

### 2.1. Variable Transformations

The first aspect to focus on is the transformations of the (independent) variables. Solving a nonlinear regression problem with a large number of parameters and coefficients represents a complex and resource-intensive task. But what if we divide the problem into two parts? Instead of describing nonlinearities directly in the regression equation, we can move them to an outer contour that executes prior to the regression build, while keeping a simple linear model — OLS or LME — in the internal contour.

```Raw Data -> Transformed Data -> Linear Model```

It may seem that variable transformations are merely a classic case of feature engineering. Before inclusion in the model, we adjust the raw data to achieve the best scoring results. The main difference is that the transformations used in modeling the marketing mix are parameterized, and the parameter search is a crucial part of model building rather than just preparation — it's part of the model's outer contour.

FERMATRICA natively supports the following key transformations. A complete list of transformations can be found in the [guide](/fermatrica/guides/FERMATRICA_and_MMM_instruction.html) and [documentation](/fermatrica/api/fermatrica/model/transform.html).

1. **Temporal**
   - Lag
   - Moving Average
   - Geometric Adstock
   - Adstock / Weibull Transformation
2. **Saturation**
   - Exponential
   - Logistic Curve
   - Gompertz Curve
   - Power Transformation
3. **Relative and Business-Specific**
   - Share within a Slice
   - Relative Price
   - Age of Entity
4. **Other**
   - Gaussian
   - Lorentzian
   - Log-Gaussian

The framework supports writing custom transformations of (nearly) any complexity, including those containing auxiliary models.

### 2.2. Outer Contour (more)

FERMATRICA views the outer contour of the model more broadly than the basic version of marketing mix modeling. If there is a mechanism for parameter tuning for nonlinear transformations, why not apply it to related tasks?

One of the principal difficulties in marketing mix modeling (MMM) is the inability to combine additive and multiplicative factors. The model is either additive (in standard form) or multiplicative (log-log model). But what to do if marketing activities are additive while seasonality, trends, and price influences are multiplicative (elasticities)? One of the standard approaches suggests removing seasonality from the dependent variable (LHS) before building an additive model. But how can we be sure that the seasonality has been removed correctly if this step precedes the actual modeling?

FERMATRICA allows the inclusion of left-hand side (LHS) transformations in the model:

```Full Y -> Direct LHS Transformation -> Cleaned Y```

```Cleaned Y Forecasted -> Inverse LHS Transformation -> Full Y Forecasted```

LHS transformations are parameterized similarly to the transformations of independent variables, allowing for the discovery of parameters and coefficients for applying multipliers within the overall optimization of the model’s scoring. This ensures that the fit-predict can be compared not only on the cleaned Y but also on the full Y.

### 2.3. Panel Models

Incorporating elasticities into the model removes barriers to actively building panel models. While in time series models, elasticities can often be approximated using additive factors, in a panel model this would effectively mean creating a separate factor for each entity. Moving multipliers to the outer contour means that this is no longer necessary.

Panel models are effective for at least the following tasks:

1. **Modeling with granularity down to sales channels/formats and, especially, regions.** Building separate models for each region is incredibly labor-intensive and resource-demanding; panel modeling allows for a significant reduction in effort.

2. **Modeling with short series and under data scarcity.** Panels can qualitatively increase the dataset, for example, by incorporating competitor data, and build a robust model even with monthly data.

3. **Separating market trends from the trends of the brand being studied.** When distinct trends are present, panel models allow for their separation.

Of course, the ability to build time series models remains fully available in FERMATRICA.

### 2.4. Complex Architecture

In addition to complicating the architecture of a specific model, FERMATRICA allows for the development of an architecture consisting of several interconnected models. This can be achieved in three ways:

1. **Auxiliary Model as a Transformation Function.** If the transformation of an independent variable requires a separate model, it can be integrated into the main model as a custom transformation function.

    ```Auxiliary Model -> Main Model```

2. **Auxiliary Model as Part of the Left-Hand Side (LHS) Transformation.** For example, you can build a model for a product category, the result of which can be used as one of the factors in transforming Y and for additional correction.

3. **Chain of Main Models.** Interconnected models where the output of the preceding model serves as a factor for the next. For example, a knowledge model and a sales model. (Development is ongoing)

    ```Main Model 1 -> Main Model 2```


## 3. Outer countour optimization

The internal contour consists of a linear model (either simple or panel), the solution of which does not pose technical challenges. In this regard, FERMATRICA relies on the well-known package [statsmodels](https://statsmodels.org), using OLS for time series and LME for panel models.

Finding parameters for the outer contour is a significantly more complex task. In the simplest case, the data scientist can manually specify them in the model settings based on their own expert knowledge. Since the model settings are stored in a human-readable XLSX config, this can be done using any software that supports editing simple XLSX files, including IDEs with XLSX editing plugins.

A more advanced approach is numerical optimization algorithms. Numerical optimization allows the determination of outer contour parameters when expert knowledge is limited or absent, and also enables the testing and/or validation of expert representations of specific transformations.

### 3.1. Optimization algorithms

FERMATRICA supports both local and global search algorithms. Global search allows for the identification of the most promising areas within the space of possible solutions, while local search achieves an accurate solution but without guarantees of finding the global optimum.

Local algorithms are further divided into gradient and non-gradient types. Gradient algorithms offer better performance but require knowledge of the analytical gradient, which can be challenging for models with a large number of diverse transformations. Currently, FERMATRICA relies on non-gradient algorithms for local searches.

Out of the box, the following algorithms are supported:

1. **Global**
   - Genetic Algorithm based on the [PyGAD](https://pygad.readthedocs.io) package, with minor technical adjustments.

2. **Local**
   - Non-gradient COBYLA algorithm from the [NLopt implementation](https://nlopt.readthedocs.io/en/latest/NLopt_Algorithms/#cobyla-constrained-optimization-by-linear-approximations). The NLopt implementation is preferred over the [SciPy implementation](https://docs.scipy.org/doc/scipy/reference/optimize.minimize-cobyla.html) due to its more efficient handling of upper and lower bounds.

Several parameter search strategies for the outer contour are possible, depending on modeling style and data volume:

1. **Iterative.** Optimization of individual blocks of parameters, typically logically linked to one another. We start with parameters describing the transformations of the largest factors, then move on to smaller ones, and so forth. This strategy is optimal for large datasets that are difficult to manage with a stochastic approach. The main algorithm used is local.

2. **Stochastic.** This involves performing N independent searches using a global algorithm with all or most of the parameters to be optimized. The resulting solutions are then examined, and one or more are selected for further development using a local algorithm or a global algorithm with narrowed boundaries.

### 3.2. Scoring

The standard metric for regression tasks is the mean squared error (MSE) and its derivatives, primarily the more interpretable RMSE and R². However, minimizing forecast error is not the only challenge faced by the data scientist developing a marketing mix model. For instance, it is equally important to make the model interpretable and minimize the effects of multicollinearity, reliably distinguishing the contributions of marketing factors in as detailed a granularity as possible.

FERMATRICA supports optimization based on a composite scoring system of two types:

1. **Standard Scoring.** A linear combination of several metrics normalized to a common scale of 0 to 1, each with its own weight:
   - R²
   - Adjusted R²
   - T-values of regression coefficients
   - P-values of regression coefficients
   - Mean RMSE by entity
   - Sign correctness of regression coefficients
   - Sign correctness and significance sufficiency (combined)
   - VIF (Variance Inflation Factor)
   - Durbin-Watson Test

2. **Custom Scoring.** An arbitrary arithmetic formula derived from components of standard scoring. This is relevant in cases where a different relationship between scoring components is preferred over a linear combination. For example, a combined metric of sign and significance can be used to penalize the base scoring component.


## 4. Source code and documenation

### 4.1 Source code

FERMATRICA consists of three separate repositories: one for utilities, one for reporting and one for core functionality (model definition, predicting, optimization). Source code of the core FERMATRICA functionality (model definition, predicting, optimising) is in main [FERMATRICA package](https://github.com/FERMATRICA/fermatrica).

### 4.2. Documentation

- [Fermatrica MMM guide](/fermatrica/guides/FERMATRICA_and_MMM_instruction.html)
- [API Reference](/fermatrica/api/index.html)

### 4.3. See also

- [Analysis and reporting](/features/reporting)
- [Customization](/features/customize)







