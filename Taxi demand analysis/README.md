<h2>Introduction</h2>
<p>Exploiting an understanding of taxi demand could increase the efficiency of the city’s taxi system.In this challenge we are given historical grab booking data to predict the real time taxi demand.</p>

<p>Raw data structure:</p>
<li><b>Geohash6</b> : Geohash is a public domain geocoding system which encodes a geographic location into a short string of letters and digits with arbitrary precision</li>
<li><b>Timestamp</b>:
Start time of 15-minute intervals, in the following format: (hour:minute), where hour ranges from 0 to 23 and minute is either one of (0, 15, 30, 45) </li>
<li><b>Day </b>: Day, where the value indicates the sequential order and not a particular day of the month</li>
<li><b>Demand</b> : Aggregated demand normalised to be in the range [0,1]</li><br>
<p>The information in this raw dataset including taxi pick-up day, timestamp, locations, which contains 4 million observations</p>
<h2>Data preprocessing</h2>
<b>Time</b>
<p> Since we want to predict the amount of demand at a given timestamp, we transfer the time into bins(intervals) of 15 minutes and divide origin"day" column by 7 and retrieve its remainder.</p>
<li><b>timebin_no : </b> Transfer the whole time into 15-min time bin ex: 24*4 = 96 bins</li>
<li><b>map_day :</b> "day" % 7 </li>
<br>
<b>Location</b>
<p>Next, we use Googlemap api to see location of this dataset based on latitude and longitude decoded from the Geohash6 given, it is obvious that the origin Geohash6 code had been encrypted, because the latitude & longitude in this dataset are all located in Indian Ocean.<br><br>
It is desired to predict taxi demand in the small area so that the taxi driver know exactly where to go. However, learning to predict taxi demand in small area is difficult .Therefore, choosing an area size which is both easy to predict and sufficiently accurate for the drives. Geohash can divide a geographical area into smaller subareas with arbitrary precision. Geohash is a geocoding system that has a
hierarchical spatial data structure which subdivides space into buckets of grid shape. The size of the grid is determined by the number of characters used in the Geohash code.Fortunately, Geohash code is already given in this dataset.</p>
<b>Final format</b>
<p>
Then we aggregate the number of taxi demands during every time-step length.That is to say, we group by the raw taxi data by each time bin, Geohash code and the map_day. In such a way, historical taxi data in each area becomes the aggregated number of demand data sequences.We get an observation that there are some time_bins that do not have any demand so we fill a value of zero for every bins where no demand data is present.
</p>


<h2>Modelling</h2>

<b>Random Forest</b>
<p>The tree regression model is capable of representing complex decision boundaries, so we firstly fed the data sequences into Random Forest model and we get the following result:</p>

```
RMSE (train) = 0.084025, RMSE (test) = 0.104904
```

<p>However, we should notice that this is a time series problem, Random Forests don’t fit very well for increasing or decreasing trends which are usually encountered when dealing with time-series analysis, such as seasonality! An idea to further improve predictions using Random Forests is to flatten the data.<br><br>
<h4>Time Series to Supervised</h4>
A time series must be transformed into samples with input and output components. The transform both informs what the model will learn and how you intend to use the model in the future when making predictions, e.g. what is required to make a prediction (X) and what prediction is made (y). </p>

```
X,              y
sample input,   sample output
sample input,   sample output
sample input,   sample output
...
```

<p>For example, a univariate time series is represented as a vector of observations:</p>

```
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

<p>The above 10-step univariate series can be expressed as a supervised learning problem with three time steps for input and one step as output, as follows:</p>

```
X,          y
[1, 2, 3], [4]
[2, 3, 4], [5]
[3, 4, 5], [6]
...
```

<p>After finishing up the above method, we later fed our data sequences into model again.</p>

<b>Random Forest</b>
<p>This time we get the following result:</p>

```
RMSE (train) = 0.070520, RMSE (test) = 0.091750
```
<p> After transforming the data we got the much better result compared with the previous one. </p>

<b>XGB</b>
<p>XGBoost is an advanced gradient boosting algorithm. It is a highly sophisticated algorithm, powerful enough to deal with all sorts of irregularities of data. We get the following result from our XGBoost model:</p>

```
RMSE (train) = 0.102508, RMSE (test) = 0.088560
```

<b>LSTM</b>
<p>The sequential nature of taxi demand data leads us to the
choice of a model that can handle time series data. Recurrent
neural networks (RNNs) are one of the most popular models
that can process sequential data very well. Currently, the most commonly used type of RNNs are Long Short Term Memory networks (LSTMs). LSTMs are a special
kind of RNN, capable of learning long-term dependencies due
to their gating mechanism. We got the following result from the LSTM model:</p>

```
Test RMSE: 0.086824
```
<h2>Conclusion</h2>
Overall, the LSTM model perform best, likely due to its ability to perform time series problem.
