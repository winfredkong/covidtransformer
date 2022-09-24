# Covid Transformer Model

## Introduction

This project aims to use the Autoregressive Transformer Model to predict future COVID-19 deaths using the data of past cases, deaths and vaccinations.

## Motivation and Methodology

This project is primarily motivated by [[1]](#1) and uses Spatial embedding (Country), the standard sinusoidal temporal positional encoding [[2]](#2) and variable embedding in order to predict future COVID-19 deaths. Roughly speaking we should expect death cases to be a function of previous cases  (which deteriorate) and also a function of vaccination numbers. However, since different countries have different healthcare capabilities and have different testing rates, we should expect the predictor function to differ greatly across different countres. Hence, we employ a Spatial Embedding layer in order for our model to learn the differences between countries. 

## Dataset

We scraped public data from https://github.com/owid/covid-19-data and https://covid19.who.int/data, and took the intersection of countries which had data in all 3 variables (cases, vacc, deaths). 



## References
<a id="1">[1]</a> 
Long-Range Transformers for Dynamic Spatiotemporal Forecasting, 2022 https://arxiv.org/abs/2109.12218

<a id="2">[2]</a>
Attention Is All You Need, 2017 https://arxiv.org/abs/1706.03762
