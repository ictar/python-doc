原文：[Using Predictive Algorithms to Track Real Time Health Trends](http://blog.algorithmia.com/predictive-algorithms-track-real-time-health-trends/)

--- 

![Create a real time health dashboard with predictive
algorithms](http://blog.algorithmia.com/wp-content/uploads/2016/08/predictive-algorithm-dashboard.jpg)_This is a guest post by Chris Hannam, a professional
Python and Java developer. Want to contribute your own how-to post? Let us
know [contact us here](https://algorithmia.com/contact)._

We've shown how to use predictive algorithms to [track economic
development](http://blog.algorithmia.com/2015/05/tracking-economic-
development-with-open-data-and/). In this tutorial, we're going to build a
real-time health dashboard for tracking a person's blood pressure readings, do
time series analysis, and then graph the trends over time using predictive
algorithms. This tutorial is the starting point for creating your own personal
health dashboard using time series algorithms and predictive APIs.

We'll be creating this dashboard in Python, using the [Withings
API](http://oauth.withings.com/api) for our data, the
[Forecast](https://algorithmia.com/algorithms/TimeSeries/Forecast) and [Simple
Moving
Average](https://algorithmia.com/algorithms/TimeSeries/SimpleMovingAverage)
microservices from [Algorithmia](https://algorithmia.com/), and
[Plotly](https://plot.ly?ref=algorithmia) to graph the data.

**tl;dr** here's the GitHub repo for [running Algorithmia tasks against Withings data using Python](https://github.com/bassdread/AlgorithmiaWithWithings).

## What Gets Measure, Gets Managed

Why blood pressure data? A friend of mine was diagnosed with high blood
pressure and was determined to lower it using data. According to [CDC statisti
cs](http://www.cdc.gov/dhdsp/data_statistics/fact_sheets/fs_bloodpressure.htm)
as many as 1 in 3 Americans suffer from high blood pressure, which can
contribute to a higher risk for [heart
disease](http://www.cdc.gov/heartdisease) and
[stroke](http://www.cdc.gov/stroke).

I’m a Python programmer, and thought I could build a simple, serverless health
dashboard to help my friend measure and understand his blood pressure.

The first step was to establish a routine of measuring the blood pressure and
logging it using a cheap blood pressure monitor and the [Withings
app](https://play.google.com/store/apps/details?id=com.withings.wiscale2).
We'll then use the Withings API to access our data for the health dashboard
(Withings also makes a [wifi-enabled blood pressure
cuff](https://www.withings.com/us/en/products/blood-pressure-monitor) for
those that don't want to manually log their data).

My friend has been logging their heart rate, systolic and diastolic blood
pressure in the morning and night for the last five months. Below is a
snapshot from the dashboard offered by Withings.

![Withings Blood Pressure Graph](http://blog.algorithmia.com/wp-
content/uploads/2016/08/Screen-Shot-2016-07-26-at-13.15.17.png)

The graphs are OK, but we both found them confusing and not very helpful for
tracking trends. I also wanted to be able to use predictive algorithms to
forecast the future based on the past.

Here's how we'll build our own health dashboard instead.

## Python, APIs, and Graphing

I set up a basic [Flask](http://flask.pocoo.org/) app to fetch the blood
pressure data from the Withings API, process the data, and graph it client
side. To access the data, I used a [Withings Python
library](https://github.com/maximebf/python-withings) (available on
[PyPi](https://pypi.python.org/pypi/withings/0.3)). For graphing, I choose
[Plot.ly](https://plot.ly/). In just a few lines in the HTML you can quickly
create powerful graphs.

The first task was to extract the raw data from Withings. Using the Python lib
made this pretty simple. Where it got a little tricky was converting the
fetched into something Plotly could graph. I went for a simple approach to
build a string of text to render in a template using
[Jinja2](http://jinja.pocoo.org/docs/dev/) as part of Flask.

We'll define our function to fetch the data from the Withings API (I've
removed some code for brevity, but [the repo has everything you
need](https://github.com/bassdread/AlgorithmiaWithWithings) to get started).
We call the Withings API to get our measurement data, and then iterate through
the response to sort through the measurement dates and times. We'll build up
both an object to be used for graphing, as well as one of raw data we can pass
to Algorithmia to run their predictive algorithms on.

[code]

    def _fetch_withings():

    

        results = []

        client = WithingsApi(creds)

    

        readings = {

            # analysis of past readings

            'past': {

                'x': '',

                'diastolic': '',

                'systolic': '',

                'pulse': '',

                'simple_moving_average' : {

                    'diastolic': '',

                    'pulse': '',

                    'systolic': '',

                }

            }

        }

    

        measures = client.get_measures()

    

        last_reading_date = measures[-1].date

        counter = 1

    

        raw_readings = {

            'systolic': [],

            'diastolic': [],

            'pulse': [],

        }

    

        for measure in measures:

    

            if measure.systolic_blood_pressure\

               and measure.diastolic_blood_pressure:

    

                next_date = last_reading_date + timedelta(days=counter)

    

                # sort out date times

                readings['past']['x'] += '"' + measure.date.strftime('%Y-%m-%d %H:%M:%S') + '",'

                readings['future']['x'] += '"' + next_date.strftime('%Y-%m-%d %H:%M:%S') + '",'

    

                readings['past']['systolic'] += str(measure.systolic_blood_pressure) + ','

                readings['past']['diastolic'] += str(measure.diastolic_blood_pressure) + ','

    

                # keep ints for for sending to ALGORITHMIA

                raw_readings['systolic'].append(measure.systolic_blood_pressure)

                raw_readings['diastolic'].append(measure.diastolic_blood_pressure)

    

                if measure.heart_pulse and measure.heart_pulse > 30:

                    raw_readings['pulse'].append(measure.heart_pulse)

                    readings['past']['pulse'] += str(measure.heart_pulse) + ','

    

                counter += 1

    

        return readings
[/code]

As with most simple projects, [Bootstrap](http://getbootstrap.com/) made the
perfect tool for rendering the HTML with the graphs embedded into the normal
row layout.

![Blood Pressure and Pulse Simple Moving Average](http://blog.algorithmia.com
/wp-content/uploads/2016/08/newplot.png)

To build the graphs, we create an object in the following format in our Flask
app when fetching the Withings data:

[code]

        readings = {

            # predictions

            'future' : {

                'x': '',

                'diastolic': '',

                'systolic': '',

                'pulse': '',

                'simple_moving_average' : {

                    'diastolic': '',

                    'pulse': '',

                    'systolic': '',

                }

            },

            # analysis of past readings

            'past': {

                'x': '',

                'diastolic': '',

                'systolic': '',

                'pulse': '',

                'simple_moving_average' : {

                    'diastolic': '',

                    'pulse': '',

                    'systolic': '',

                }

            }

        }
[/code]

And then, to generate the graph we pass Plot.ly the x and y coordinates from
our data. We use x as the index, and y as the diastolic, systolic, or pulse
value like so:

[code]

        HISTORIC_FUTURE = document.getElementById('historic_future_graph');

        Plotly.plot(HISTORIC_FUTURE, [

          {

            name: 'Systolic',

            x: [{{readings.past.x|safe}}],

            y: [{{readings.past.systolic|safe}}]

          },

          {

            name: 'Diastolic',

            x: [{{readings.past.x|safe}}],

            y: [{{readings.past.diastolic|safe}}]

          },

          {

            name: 'Pulse',

            x: [{{readings.past.x|safe}}],

            y: [{{readings.past.pulse|safe}}]

          },

          {

            name: 'Systolic Future',

            x: [{{readings.future.x|safe}}],

            y: [{{readings.future.systolic|safe}}]

          },

          {

            name: 'Diastolic Future',

            x: [{{readings.future.x|safe}}],

            y: [{{readings.future.diastolic|safe}}]

          },

          {

            name: 'Pulse Future',

            x: [{{readings.future.x|safe}}],

            y: [{{readings.future.pulse|safe}}]

          }

          ],

          {

            margin: { t: 0 }

          });
[/code]

Now that we have our graphs, we can see that our blood pressure data has some
unpredictable peaks, which makes trends hard to spot. I've used R for time
series data in the past, but have never used anything in Python. This is where
[Algorithmia](http://algorithmia.com/) comes in.

## Adding Predictive Algorithms

I needed to make sense of the data as easily as possible. I explored a few
services that did machine learning and data analysis. Most were limited to
text classification, expensive, or not available as a serverless API.

Then I found Algorithmia, which has a large library of algorithms that run as
microservices on your data. You call the algorithm, pass in your data, they
run the algorithm over it, and return the results in realtime. They have a
[Python library](https://pypi.python.org/pypi/algorithmia/1.0.0), and since
it's an API, it fit perfectly with this serverless project.

I chose two predictive algorithms for this project:

  * [Simple Moving Average](https://algorithmia.com/algorithms/TimeSeries/SimpleMovingAverage)
  * [Forecast](https://algorithmia.com/algorithms/TimeSeries/Forecast)

### Using Simple Moving Average

I used [simple moving
average](https://algorithmia.com/algorithms/TimeSeries/SimpleMovingAverage) to
smooth out the data, and make it easier to spot trends in the graph. Using the
moving average also helped reduce the fluctuations and noise in the raw data.

First, we define our simple moving average function:

[code]

    def _get_simple_moving_average(data):

    

        string = ''

        raw = []

    

        reply = SIMPLE_MOVING_AVERAGE.pipe(data)

        for reading in reply.result:

            string += str(int(reading)) + ','

            raw.append(reading)

    

        string = string[:-1]

    

        return string, raw
[/code]

Then, as part of **_fetch_withings()** from above, we pass our systolic,
diastolic, and pulse data to the function like so:

[code]

        # simple moving average of existing data

        readings['past']['simple_moving_average']['diastolic'], average_diastolic = _get_simple_moving_average(raw_readings['diastolic'])

        readings['past']['simple_moving_average']['systolic'], average_systolic = _get_simple_moving_average(raw_readings['systolic'])

        readings['past']['simple_moving_average']['pulse'], average_pulse = _get_simple_moving_average(raw_readings['pulse'])
[/code]

This creates a smoothed out chart of our vitals data. Then we use the forecast
algorithm to project the trends into the future.

### Using Forecast

This is where things get really fun. I had about five months of data to work
with, and, generally speaking, the more data you have the better!

I first defined my forecast function:

[code]

    def _get_forecast(data):

    

        string = ''

        raw = []

    

        reply = FORECAST.pipe(data)

        for reading in reply.result:

            string += str(int(reading)) + ','

            raw.append(reading)

    

        string = string[:-1]

    

        return string, raw
[/code]

Again, as part of the **_fetch_withings()** function, I pass in the data as an
array to the forecast algorithm. We could stop here, but the forecasted data
would be prone to spikes and fluctuations. So, once that's complete, I run the
moving average algorithm on the forecasted data to smooth the results out:

[code]

        if FORECAST_ON_AVERAGE:

            readings['future']['diastolic'], future_diastolic = _get_forecast(average_diastolic)

            readings['future']['systolic'], future_systolic = _get_forecast(average_systolic)

            readings['future']['pulse'], future_pulse = _get_forecast(average_pulse)

        else:

            # populate the standard graphs and get the raw data to feed into thenext algorithm

            readings['future']['diastolic'], future_diastolic = _get_forecast(raw_readings['diastolic'])

            readings['future']['systolic'], future_systolic = _get_forecast(raw_readings['systolic'])

            readings['future']['pulse'], future_pulse = _get_forecast(raw_readings['pulse'])

    

        # simple moving average of future data

        readings['future']['simple_moving_average']['diastolic'], average_diastolic = _get_simple_moving_average(future_diastolic)

        readings['future']['simple_moving_average']['systolic'], average_systolic = _get_simple_moving_average(future_systolic)

        readings['future']['simple_moving_average']['pulse'], average_pulse = _get_simple_moving_average(future_pulse)
[/code]

Here's the graphed results below showing the five months of systolic,
diastolic, and pulse data, with a five month forecast for each:

![Blood Pressure Averages and Forecast ](http://blog.algorithmia.com/wp-
content/uploads/2016/08/newplot-4.png)

And, here is the output from the forecast algorithm, but this time using the
simple moving average data instead of the raw data:

![Blood pressure forecast results using moving average
data.](http://blog.algorithmia.com/wp-content/uploads/2016/08/newplot-5.png)

Much better! Blood pressure data is hard to work from as can be quite erratic
at times. There are a number of algorithms for smoothing and normalize the
data, which I intend to use to improve the predictions in the future. For
instance, I could have used [Linear
Detrend](https://algorithmia.com/algorithms/TimeSeries/LinearDetrend) to focus
the analysis on the fluctuations in the data, or
[Autocorrelate](https://algorithmia.com/algorithms/TimeSeries/Autocorrelate)
to analyze the seasonality of the time series. I could even use [Outlier
Detection](https://algorithmia.com/algorithms/TimeSeries/OutlierDetection) to
remove unusual data points in the raw data, which could indicate bad readings.

## Conclusion

The main takeaway is that it appears my friends blood pressure isn’t going to
get worse, and should stay within an acceptable range for the next few months.

And, thanks to their new health dashboard, my friend now has a set of graphs
they can take to their doctor when discussing long term treatment. Blood
pressure is something that can be influenced by a range of factors so regular
reviews are important for long term management.

This was my first attempt at making forecasts and understanding the many, many
ideas behind this kind data processing. I have barely scratched the service
with what can be done with the data.

Tools used:

  * [Flask Python Framework](http://flask.pocoo.org/)
  * [Algorithmia](http://algorithmia.com/)
  * [Plotly](http://plot.ly/)

