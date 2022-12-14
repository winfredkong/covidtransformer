# Autoregressive Transformer Model to predict future COVID-19 deaths 

## Introduction

This project aims to use the Autoregressive Transformer Model to predict future COVID-19 deaths using the data of past cases, deaths and vaccinations.

## Motivation and Methodology

This project is primarily motivated by [[1]](#1) and uses Spatial embedding (Country), the standard sinusoidal temporal positional encoding [[2]](#2) and variable embedding in order to predict future COVID-19 deaths. Roughly speaking we should expect death cases to be a function of previous cases  (which deteriorate) and also a function of vaccination numbers. However, since different countries have different healthcare capabilities and have different testing rates, we should expect the predictor function to differ greatly across different countres. Hence, we employ a Spatial Embedding layer in order for our model to learn the differences between countries. 

## Dataset

We scraped public data from https://github.com/owid/covid-19-data and https://covid19.who.int/data, and took the intersection of countries which had data in all 3 variables (cases, vacc, deaths). Data was extracted around August 2022. 

For our second model, where we ran it on normalised data, we took public data from https://www.kaggle.com/datasets/tanuprabhu/population-by-country-2020?resource=download , as well as manually scraped population data from some countries. We did not place a focus on accuracy of population data (given that our data also spans across 2 to 3 years, in which the population might have changed) as it was  just a rough normalising constant. 

We shuffled and used the standard ratio of (0.8, 0.1, 0.1) for (train, valid, test) for each country then added it to the full dataset. This was to ensure that each country would be equally represented in train, valid and test data. 

## Model Architecture

For our model, we take the `past=14` days of data (in all 3 variables) and seek to `pred=3` days of data in deaths. A simplified diagram of the model architecture can be seen below with `N=1` layers.

![Simplified Model Architecture](images/model_architecture.png?raw=True)

We took the dimension of the model to be `d_model=512`. Our Spatial (which country) and Variable (which variable) embedding we both output into `d_model-1` dimensions which was then stacked with the actual value (of cases/vacc/death) to give `d_model`. Finally we used the standard sinusoidal relative positional encoding and added it to the result. The motivation behind stacking the Spatial and Variable embedding is because the standard sinusoidal relative positional encoding makes small variations in each dimension, which does not overshadow differences in values, whereas we should have no such expectations for our Spatial and Variable embeddings. A simplified diagram of `d_model=4` can be seen below. 

![Simplified Embedding Architecture](images/embedding_architecture.png?raw=True)

For optimisation, we first ran our model with MSE loss, but noted that there was huge variation in losses (between batches). We hypothesized that this was due to different population sizes and since MSE loss scaled quadratically with this loss, this resulted in large loss for large populations and small loss for small populations. This could result in overfitting for large countries. 

Thus, we ran a second model with all data divided by country population.

## Results
After training for approximately 50 epochs, we achieved a validation loss of 6368.7090 (RMSE of about 80), and a test loss of 2908.0791 (RMSE of about 40). For the model trained on normalised data, we achieved a validation loss of 7.82e-7 (RMSE of about 8.84e-4) and a test loss of 7.81e-7 (RMSE of about 8.83e-4).

As explained earlier our first model likely performs better on countries with larger populations, while our second model is a more stable general model. 

## Further extensions

It is to be noted that in [[1]](#1), they implemented a time2vec embedding which allows for capturing of seasonal data in predicting weather. While we did not implement this, it could be something worth implementing due to differences in temperatures/seasons changing the spread of disease. 

## Credit

This project was made possible by the many online references and tutorials like https://nlp.seas.harvard.edu/annotated-transformer/ as well as the freely available GPUs provided by Google Colab.

## References
<a id="1">[1]</a> 
Long-Range Transformers for Dynamic Spatiotemporal Forecasting, 2022 https://arxiv.org/abs/2109.12218

<a id="2">[2]</a>
Attention Is All You Need, 2017 https://arxiv.org/abs/1706.03762
