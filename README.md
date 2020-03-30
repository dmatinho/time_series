# Time Series

## Yellowstone is one of the most visited parks in the US with more than 4 million visitors a year. This translates in several problems: overcrowded spaces and insufficient resources. The goal of this project is to forecast the number of visitors per year to increase visitor’s satisfaction and, if possible better allocation of the park’s budget.

### Group Project:  composed by myself, Daniela Matinho, Claire Zhang, Sneha Vasudevan and Tim Chang

### Data description
The data is from 1979 to 2018 and it is yearly data. There is a tiny upward trend and strong seasonality as we could expect from a national park. We split our data into train (39 years) and test (12 months) for the forecasts (why one year: this is a medium-term data so one to two years forecast should be enough).

### EDA
We start by analyzing the data in a decomposed way looking at the overall data, the trend, seasonality and the residuals. Applying decomposition in additive and multiplicative time series, we can see our data is better represented in this way. Especially, we see the residuals are more random when applying multiplicative. July is the month with the highest number of visitors and November through April is the lowest season. Lambda is applied to capture the variation of the data. In this case we see that once we apply box-cox the size of the variation is pretty much at the same level in all the years.

### Holt-Winters
1.	Holt: only takes care of the trend so this model is not good for our data
2.	HW additive: tested but we quickly moved to multiplicative as we knew from the decomposition
3.	 HW multiplicative: tested with and without damped. In this case damped performed because as this parameter helps take care of the one constant trend existing in the data. We used AICC (best measure to compare models within the same category) to compared the best model as well as MAPE (mean absolute percentage error is a measure of prediction accuracy of the forecast) and RMSE (root mean square error: the most accurate measure). 
4.	Looking at the check residuals, first there are not white noise as the p-values is lower than 0.05 and the distribution has a long right tail. We can also see seasonality in the residuals not capture by the HW.
5.	Coefficients: alpha (level, the closer to 1, more value is given to recent values); beta (trend, the higher the value, the more the trend changes over time); gamma (seasonal, the smaller the value, the less the variation over time).

### ETS
1.	ETS: weighted sum of the past observations but the model uses exponential decreasing weighted for the past obvs. (meaning: the most recent observations have more weight than the oldest ones).
2.	(MNM; ANA): LEVEL | TREND | SEASONALITY
3.	In this case we applied ets with and without lambda. Without lambda the model is showed as multiplicative and once applied, it turns to additive as the variation of the data was taken care by the box.cox transformation. AICC is about half however looking at the errors, the difference is not very significative. Once again, we checked the residuals: there are not white noise but the distribution is closer to normal than the previous model. Strong seasonality in the residuals meaning that some information of the data may not have been capture by this model. Perhaps, the following models will provide better results.
4.	Parameters: alpha (higher value gives more weight to recent value), gamma (higher value gives more weight to the recent seasonal period)

### sARIMA
1.	sARIMA requires stationarity of data and applying ADF and KPSS.test we have different information. However, visually looking at the data we can see that the residuals are clearly not white noise. 
2.	Differencing is a way of turning our data stationary. We start by applying normal differencing and we see improvements on the residuals with less positive spikes and way better with differencing = 2. However, when we apply seasonal differencing, the results are way better with only one spike in lag 12. Rechecking the stationarity of data, it is now stationary.
3.	Exponential decay on the partial autocorrelation function (12, 24, 36).
4.	 As we know our data is highly seasonal, we did not apply arima and went ahead applying sARIMA (Autoregression, differencing, Moving average), (p,d,q): non-seasonal part of the model (P,D,Q): seasonal part of the model, s = lag
5.	We start by testing our model with seasonal order 1 and kept that till the end. As suggested, we avoid more than order 2 and decided taking in account AICC and MAPE and RMSE. In this case, the best model is model 6where AICC is the a bit higher than model 7 but the rmse is lower and it is crucial for us to have a good forecast of the visitors.
6.	Looking ta the residuals we still have 2significant spikes but the residuals are white noise. Forecast of the increase of visitors in 2019.

### Gas and weather data
We decided to add 2 more variables in order to help us forecast the number of visitors of the park. We ask ourselves if weather as well as the gas price could have an impact on the visitors. This could maybe help us to better forecast and serve visitors. Temperature data average is around 0 to 60 and the price of the gas increased significantly until 2008 and after that the price has had several variations with now being around 2.5.
Looking at the correlation of the variables, temperature seems to be highly correlated with the number of visitors but the same does not apply to the price of gas. We checked the residuals for 3 models (1: using 2 variables and model 2 and 3 using one variable each). The residuals show seasonality is all the cases but visitors and temperature model seems to be better in terms of the distribution of residuals. 

### ARIMAX
1.	ARIMAX is an extended model of arima that includes independent variables (data stationary, non-seasonal arima models) and the model assumes the future values dependent on the past.
2.	ARMAX (lagged dependent variable – relatively slow moving, time series approach) vs Regression with ARMA errors (dependent variables are not moving slowly, errors are observed on ARMA model -> not time series model, can be used after ARMA model fitted residuals) 
3.	ARMAX: effect of change in x is propagated over time; ARMA regression: effect of x is only felt one time

4.	Regression with ARIMA errors: include other information (variables) for forecast. CHECK: all the variables stationary and white noise of the residuals to determine model to apply (only ARIMA errors are assumed to be white noise).
5.	We started by using the ordinary regression and analyzing the structure of the residuals. In this case, the residuals are not white noise and the model does not take care of the seasonality and trend presents in the data. We proceed with ARIMA errors for this model. 
6.	Forecast of the model: regression part of the model and arima part of the model.
7.	Looking at the values of the rmse, mape and aicc, the best model is visitors with temperature as a predictor. Here the residuals are white noise and there are still small significant spikes.

### VAR 
1.	VAR: special case of more general VARMA models used for multivariate time series: the structure of each variable is linear function of the past lags of itself and past lags of other variables (capture linear interdependency among multiple time series).
2.	They model and influence each other equally.
3.	VAR(1), VAR(2) the lag 2 values for all variables are added to the right side of equations: meaning 4 predictors
4.	AICC gets the lower at lag 12 and we decided to procced with this one. Again, we test using gas and weather and like in the previous model we saw only gas impacts the number of visitors coming to the park.

### Evaluation
Cross-validation cannot be directly used when dealing with time series data because we assume no relationship between variables.  time series cross validation yes
Expanding: cumulative; Sliding: a window uses each time for the forecast
Forecast: We evaluated each model using AICC and we compare models among different categories using RMSE and MAE (mean absolute error). In this case, best model is ARIMA (MAE) and VAR (RMSE). As we consider rmse a more accurate measure, the picked this model as the best one. 

### Budget
Taking in account the forecast done, we note the number of visitors per month and we see that about 80% of visit the park during June-September, allowing us to say that the resources and money allocations may be according to this percentage.
Findings and Future work: The data is seasonal, and we found out that gasoline may not have an impact in the number of visitors as expected. Predictions are less accurate during winter months dur to less quantity of visitors. In the future, we can always try to find other variables and test on a weekly basis as well as find other ways to increase profits taking in account accurate forecasts. 
