# A Data Science Approach to Understand NYC Subway Data 

Author: Li Chang <br> Date pulished: 9/28/2014

## Introduction

This article is the capstone project culminating the Udacity class, [Introduction to Data Science](https://www.udacity.com/course/ud359). The purpose of this project is to examine the patterns of New York City(NYC) subway ridership and explore whether we can use weather conditions to predict the number of entrants. Therefore, the research questions I try to address are as follows:
1. How does the weather condition affect the subway ridership in New York?
2. How can we predict the number of people entering the subway by using weather and other factors such as weekday?

By answering these questions, it will help Metro Transit Authority(MTA), the authority responsible for NYC subway, understand the effect of weather on the ridership and take proactive mesures to ensure safety and smooth operation — for example, increasing trains, putting up precaution signs to warn people of the slippery stairs, and setting up the underground drainage systems, etc.

## Background Information

I curretnly live in NYC and subway has been an integral part of my life. If you are curious about what the turnstile of the NYC subway looks like, below is a picture from [Capital New York](http://www.capitalnewyork.com/article/politics/2012/09/6537282/why-new-york-still-stuck-metrocard). <br>
My substantive subway-riding experience tells me that if you want a easy ride without elbowing other people, avoid taking subway on weekdays, especially during the rush hours between 8-10 a.m. and 4-6 p.m. On a soggy rainy day, you actually see far less commuters on the subway probably because people just hail a cab to work or school. But is my observation right? Let the data speak!


    %matplotlib inline
    from IPython.display import Image
    Image(url='http://www.capitalnewyork.com/files/Turnstiles400.jpg')




<img src="http://www.capitalnewyork.com/files/Turnstiles400.jpg"/>



## Method

### 1. Data

I use the NYC subway dataset provided by Udacity (link below). It contains more than 130k rows of hourly entries in the month of 2011 May. <br> 
https://www.dropbox.com/s/7sf0yqc9ykpq3w8/weather_underground.csv

The first five rows of data and descriptive statistics are displayed below. Descriptive statistics allows us to perform a simple sanity check on the raw data and get a general sense of variables and their disctributions. We can decide whether to recode, transform, or normalize data. For example, we need to append 'DATEn' and 'TIMEn' to a new variable with the correct time format. 


    import pandas as pd
    import numpy as np
    from datetime import datetime
    
    # import data and combined DATEn and TIMEn to datetime
    df = pd.read_csv('turnstile_data_master_with_weather.csv')
    # Recode variables
    df.rain = df.rain.astype(int)
    df["Raining?"] = np.where(df["rain"] == 1, "Raining", "Not raining")
    df['DATEn_TIMEn'] = df['DATEn'] + " " + df['TIMEn']
    df['DATEn_TIMEn'] = pd.to_datetime(df.DATEn_TIMEn)
    df['Weekday'] = df['DATEn'].apply(lambda d: datetime.strptime(d, '%Y-%m-%d').strftime('%A'))
    df['WeekdayN'] = df['DATEn'].apply(lambda d: datetime.strptime(d, '%Y-%m-%d').weekday())
    df.head()




<div style="max-height:1000px;max-width:1500px;overflow:auto;">
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>UNIT</th>
      <th>DATEn</th>
      <th>TIMEn</th>
      <th>Hour</th>
      <th>DESCn</th>
      <th>ENTRIESn_hourly</th>
      <th>EXITSn_hourly</th>
      <th>maxpressurei</th>
      <th>maxdewpti</th>
      <th>...</th>
      <th>meanwindspdi</th>
      <th>mintempi</th>
      <th>meantempi</th>
      <th>maxtempi</th>
      <th>precipi</th>
      <th>thunder</th>
      <th>Raining?</th>
      <th>DATEn_TIMEn</th>
      <th>Weekday</th>
      <th>WeekdayN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td> 0</td>
      <td> R001</td>
      <td> 2011-05-01</td>
      <td> 01:00:00</td>
      <td>  1</td>
      <td> REGULAR</td>
      <td>    0</td>
      <td>    0</td>
      <td> 30.31</td>
      <td> 42</td>
      <td>...</td>
      <td> 5</td>
      <td> 50</td>
      <td> 60</td>
      <td> 69</td>
      <td> 0</td>
      <td> 0</td>
      <td> Not raining</td>
      <td>2011-05-01 01:00:00</td>
      <td> Sunday</td>
      <td> 6</td>
    </tr>
    <tr>
      <th>1</th>
      <td> 1</td>
      <td> R001</td>
      <td> 2011-05-01</td>
      <td> 05:00:00</td>
      <td>  5</td>
      <td> REGULAR</td>
      <td>  217</td>
      <td>  553</td>
      <td> 30.31</td>
      <td> 42</td>
      <td>...</td>
      <td> 5</td>
      <td> 50</td>
      <td> 60</td>
      <td> 69</td>
      <td> 0</td>
      <td> 0</td>
      <td> Not raining</td>
      <td>2011-05-01 05:00:00</td>
      <td> Sunday</td>
      <td> 6</td>
    </tr>
    <tr>
      <th>2</th>
      <td> 2</td>
      <td> R001</td>
      <td> 2011-05-01</td>
      <td> 09:00:00</td>
      <td>  9</td>
      <td> REGULAR</td>
      <td>  890</td>
      <td> 1262</td>
      <td> 30.31</td>
      <td> 42</td>
      <td>...</td>
      <td> 5</td>
      <td> 50</td>
      <td> 60</td>
      <td> 69</td>
      <td> 0</td>
      <td> 0</td>
      <td> Not raining</td>
      <td>2011-05-01 09:00:00</td>
      <td> Sunday</td>
      <td> 6</td>
    </tr>
    <tr>
      <th>3</th>
      <td> 3</td>
      <td> R001</td>
      <td> 2011-05-01</td>
      <td> 13:00:00</td>
      <td> 13</td>
      <td> REGULAR</td>
      <td> 2451</td>
      <td> 3708</td>
      <td> 30.31</td>
      <td> 42</td>
      <td>...</td>
      <td> 5</td>
      <td> 50</td>
      <td> 60</td>
      <td> 69</td>
      <td> 0</td>
      <td> 0</td>
      <td> Not raining</td>
      <td>2011-05-01 13:00:00</td>
      <td> Sunday</td>
      <td> 6</td>
    </tr>
    <tr>
      <th>4</th>
      <td> 4</td>
      <td> R001</td>
      <td> 2011-05-01</td>
      <td> 17:00:00</td>
      <td> 17</td>
      <td> REGULAR</td>
      <td> 4400</td>
      <td> 2501</td>
      <td> 30.31</td>
      <td> 42</td>
      <td>...</td>
      <td> 5</td>
      <td> 50</td>
      <td> 60</td>
      <td> 69</td>
      <td> 0</td>
      <td> 0</td>
      <td> Not raining</td>
      <td>2011-05-01 17:00:00</td>
      <td> Sunday</td>
      <td> 6</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 26 columns</p>
</div>




    df.describe()




<div style="max-height:1000px;max-width:1500px;overflow:auto;">
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>Hour</th>
      <th>ENTRIESn_hourly</th>
      <th>EXITSn_hourly</th>
      <th>maxpressurei</th>
      <th>maxdewpti</th>
      <th>mindewpti</th>
      <th>minpressurei</th>
      <th>meandewpti</th>
      <th>meanpressurei</th>
      <th>fog</th>
      <th>rain</th>
      <th>meanwindspdi</th>
      <th>mintempi</th>
      <th>meantempi</th>
      <th>maxtempi</th>
      <th>precipi</th>
      <th>thunder</th>
      <th>WeekdayN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951.000000</td>
      <td> 131951</td>
      <td> 131951.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>  65975.000000</td>
      <td>     10.896158</td>
      <td>   1095.348478</td>
      <td>    886.890838</td>
      <td>     30.031894</td>
      <td>     57.241302</td>
      <td>     48.259013</td>
      <td>     29.892714</td>
      <td>     52.703526</td>
      <td>     29.965077</td>
      <td>      0.167100</td>
      <td>      0.334245</td>
      <td>      5.543065</td>
      <td>     56.169775</td>
      <td>     64.269729</td>
      <td>     71.769968</td>
      <td>      0.172276</td>
      <td>      0</td>
      <td>      2.983820</td>
    </tr>
    <tr>
      <th>std</th>
      <td>  38091.117022</td>
      <td>      6.892084</td>
      <td>   2337.015421</td>
      <td>   2008.604886</td>
      <td>      0.125689</td>
      <td>      8.770891</td>
      <td>     11.305312</td>
      <td>      0.146384</td>
      <td>      9.943590</td>
      <td>      0.130461</td>
      <td>      0.373066</td>
      <td>      0.471728</td>
      <td>      1.982441</td>
      <td>      6.338875</td>
      <td>      6.568289</td>
      <td>      7.627218</td>
      <td>      0.429005</td>
      <td>      0</td>
      <td>      2.080339</td>
    </tr>
    <tr>
      <th>min</th>
      <td>      0.000000</td>
      <td>      0.000000</td>
      <td>      0.000000</td>
      <td>      0.000000</td>
      <td>     29.740000</td>
      <td>     39.000000</td>
      <td>     22.000000</td>
      <td>     29.540000</td>
      <td>     31.000000</td>
      <td>     29.640000</td>
      <td>      0.000000</td>
      <td>      0.000000</td>
      <td>      1.000000</td>
      <td>     46.000000</td>
      <td>     55.000000</td>
      <td>     58.000000</td>
      <td>      0.000000</td>
      <td>      0</td>
      <td>      0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>  32987.500000</td>
      <td>      5.000000</td>
      <td>     39.000000</td>
      <td>     32.000000</td>
      <td>     29.960000</td>
      <td>     50.000000</td>
      <td>     38.000000</td>
      <td>     29.840000</td>
      <td>     45.000000</td>
      <td>     29.910000</td>
      <td>      0.000000</td>
      <td>      0.000000</td>
      <td>      5.000000</td>
      <td>     52.000000</td>
      <td>     60.000000</td>
      <td>     65.000000</td>
      <td>      0.000000</td>
      <td>      0</td>
      <td>      1.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>  65975.000000</td>
      <td>     12.000000</td>
      <td>    279.000000</td>
      <td>    232.000000</td>
      <td>     30.030000</td>
      <td>     57.000000</td>
      <td>     51.000000</td>
      <td>     29.910000</td>
      <td>     54.000000</td>
      <td>     29.960000</td>
      <td>      0.000000</td>
      <td>      0.000000</td>
      <td>      5.000000</td>
      <td>     54.000000</td>
      <td>     63.000000</td>
      <td>     71.000000</td>
      <td>      0.000000</td>
      <td>      0</td>
      <td>      3.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>  98962.500000</td>
      <td>     17.000000</td>
      <td>   1109.000000</td>
      <td>    847.000000</td>
      <td>     30.100000</td>
      <td>     64.000000</td>
      <td>     55.000000</td>
      <td>     29.970000</td>
      <td>     60.000000</td>
      <td>     30.050000</td>
      <td>      0.000000</td>
      <td>      1.000000</td>
      <td>      6.000000</td>
      <td>     60.000000</td>
      <td>     68.000000</td>
      <td>     78.000000</td>
      <td>      0.100000</td>
      <td>      0</td>
      <td>      5.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td> 131950.000000</td>
      <td>     23.000000</td>
      <td>  51839.000000</td>
      <td>  45249.000000</td>
      <td>     30.310000</td>
      <td>     70.000000</td>
      <td>     66.000000</td>
      <td>     30.230000</td>
      <td>     68.000000</td>
      <td>     30.270000</td>
      <td>      1.000000</td>
      <td>      1.000000</td>
      <td>     12.000000</td>
      <td>     70.000000</td>
      <td>     78.000000</td>
      <td>     86.000000</td>
      <td>      2.180000</td>
      <td>      0</td>
      <td>      6.000000</td>
    </tr>
  </tbody>
</table>
</div>



### 2. Approach

Here are my approaches to explore the relationship between the subeway ridership and weather conditions:
* Visualizing the NYC subway data to get a general idea of the paterns by weather type and time
* Using T-test to determine whether the subway ridership is statistically different between rainy days and non-rainy days
* Building Ordinary Least Square(OLS) model to see how the relationship interact with other factors, such as week(end) day
* Exploring how to apply MapReduce if we receive a ginormous dataset. Note the current dataset contains data of 2011 May only. 

## Discussion

### 1. Visualizing Data

First, I start with visualization as it's a quick way to see the distribution of entrants/exits by day and time. 

> A note: If you are using IPython for the first time as I am, you may want to install more packages by using pip. I was bogged down in the beginning when the error keeps saying no such module could be found. 

> !pip install -U ggplot


    import matplotlib.pyplot as plt
    import matplotlib.dates as mdates
    from matplotlib.dates import MO, TU, WE, TH, FR, SA, SU
    
    df['DATEn_TIMEn2'] = df['DATEn'] + " " + df['TIMEn']
    new_x = df['DATEn_TIMEn2'].apply(lambda d: mdates.datestr2num(d))
    fig = plt.figure()
    fig.set_size_inches(18,10)
    ax = fig.add_subplot(111)
    
    line1 = plt.plot(new_x , df['ENTRIESn_hourly'], color = 'r', lw = 0.1)
    line2 = plt.plot(new_x , df['EXITSn_hourly'], color = 'b', lw = 0.1)
    ax.set_ylim(0,55000)
    ax.set_title('Total entries and exits by day')
    ax.set_ylabel('Volume')
    ax.set_xlabel('Day')
    ax.xaxis.set_major_locator(mdates.WeekdayLocator(byweekday=SU))
    myFmt = mdates.DateFormatter('%m/%d, %a')
    ax.xaxis.set_major_formatter(myFmt)
    ax.legend((line1[0], line2[0]), ('Entries','Exits'))
    plt.show()


![png](Introduction%20to%20Data%20Science%20Final%20Project%20Submission_files/Introduction%20to%20Data%20Science%20Final%20Project%20Submission_18_0.png)


'Total entries and exits by day and time' shows that the patterns of entries and exits are roughly the same and both exhibit a huge volatility by day and week. For example, it's easy to see that the numbers of entries/exits on Sundays are significantly less than weekdays. Below are two visualizations of the total entries by weekday and hour respectively. Revealing the relationship between entries and factors other than weather helps me build the statitical model.


    byDay = df.set_index(['WeekdayN'])
    byDay = df.groupby('WeekdayN', as_index = False).aggregate(np.sum)
    
    
    fig = plt.figure()
    ax = fig.add_subplot(111)
    N = 7
    ind = np.arange(N)
    width = 0.35
    bar1 = ax.bar(ind,byDay['ENTRIESn_hourly'],width,
            color = 'r',
            alpha = 0.5)
    bar2 = ax.bar(ind+width,byDay['EXITSn_hourly'],width,
            color = 'b',
            alpha = 0.5)
    ax.set_xlabel('Weekday')
    ax.set_ylabel('Total entries')
    ax.set_title('Total entries and exits by weekday')
    ax.set_xlim(-width,len(ind)+width)
    xTickmarks = ['Mon.','Tue.', 'Wed.', 'Thur.', 'Fri.', 'Sat.', 'Sun.']
    ax.set_xticks(ind+width)
    ax.set_xticklabels(xTickmarks)
    ax.legend((bar1[0], bar2[0]), ('Entries','Exits'))
    plt.show()


![png](Introduction%20to%20Data%20Science%20Final%20Project%20Submission_files/Introduction%20to%20Data%20Science%20Final%20Project%20Submission_20_0.png)


'Total entries and exits by eekday' shows that entries decrease dramatically during the weekends. It's pretty explanatory - no school, no work on weekends, so fewer people take subway.


    byHour = df.set_index(['Hour'])
    byHour = df.groupby('Hour', as_index = False).aggregate(np.sum)
    
    plt.bar(byHour['Hour'],byHour['ENTRIESn_hourly'],
            width = 0.7, 
            align = 'center',
            color = 'b',
            alpha = 0.5)
    plt.xlabel('Hour')
    plt.ylabel('Total entries')
    plt.title('Total entries by hour')
    plt.xlim(-0.5,24)
    plt.xticks(np.arange(0, 24, 2))




    ([<matplotlib.axis.XTick at 0x109f82990>,
      <matplotlib.axis.XTick at 0x109f33b50>,
      <matplotlib.axis.XTick at 0x10908b790>,
      <matplotlib.axis.XTick at 0x10908bdd0>,
      <matplotlib.axis.XTick at 0x109094550>,
      <matplotlib.axis.XTick at 0x109094c90>,
      <matplotlib.axis.XTick at 0x10909f410>,
      <matplotlib.axis.XTick at 0x10909fb50>,
      <matplotlib.axis.XTick at 0x1090a82d0>,
      <matplotlib.axis.XTick at 0x1090a8a10>,
      <matplotlib.axis.XTick at 0x109c5d190>,
      <matplotlib.axis.XTick at 0x1084d3090>],
     <a list of 12 Text xticklabel objects>)




![png](Introduction%20to%20Data%20Science%20Final%20Project%20Submission_files/Introduction%20to%20Data%20Science%20Final%20Project%20Submission_22_1.png)


"Total entries by hour" indicates that there are significantly more entrants during certain time windows, for example, 8-9 a.m.(morning rush hour), 12-1p.m. (Mm...surprisingly lunch hour?), 4-5 p.m.(rush hour), 8-9 p.m. (Mm...the end of happy hour?). 

### 2. T-test

Below are the descriptive statisitics of total entries by weather conditions. During non-rainy hours, 'ENTRIESn_hourly' has a mean value of 1090 and median number is 278, while the mean value of rainy hours is 1105 and median number is 282. Simply comparing the mean value, 278 vs. 282, can we say that there are fewer people taking subway on a non-rainy day than a rainy day? Hmm......This is different from my initial thought. To answer this question, I will first use T-test to test whether the difference between two groups is statistically significant.


    byRain = df.groupby('Raining?')
    byRain['ENTRIESn_hourly'].describe()




    Raining?          
    Not raining  count    87847.000000
                 mean      1090.278780
                 std       2320.004938
                 min          0.000000
                 25%         38.000000
                 50%        278.000000
                 75%       1111.000000
                 max      43199.000000
    Raining      count    44104.000000
                 mean      1105.446377
                 std       2370.527674
                 min          0.000000
                 25%         41.000000
                 50%        282.000000
                 75%       1103.250000
                 max      51839.000000
    dtype: float64



But before using T-test, let's look at the distribution of 'ENTRIESn_hourly'. Because the data do not follow a normal distribution, I chose to use non-parametric test, [Mann-Whitney U Test](http://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.mannwhitneyu.html) rather than [Welch's T-test](http://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.ttest_ind.html). 



    from ggplot import *
    f = ggplot(df, aes(x='ENTRIESn_hourly', color = 'Raining?')) 
    f + geom_density() + \
        ggtitle('Distribution of Entries by Raining Weather') + \
        xlab('Entries') +\
        facet_grid('Raining?')


![png](Introduction%20to%20Data%20Science%20Final%20Project%20Submission_files/Introduction%20to%20Data%20Science%20Final%20Project%20Submission_28_0.png)





    <ggplot: (293408525)>




    import scipy.stats
    Rain = df[(df.rain == 1)]
    No_Rain = df[(df.rain == 0)]
    u, p = scipy.stats.mannwhitneyu(Rain.ENTRIESn_hourly, No_Rain.ENTRIESn_hourly)
    print "p-value: %.2f" %(p * 2)

    p-value: 0.05


The reported p-value under Mann Whitney test is one-sided, so I multiply by two to get the two-sided p-value. The result shows that the difference in total entrie between raining days and non-raining days is statistically significant with the p-value at 0.05. Now, I can proceed with modeling.

### 3. Modeling

#### Fitting OLS model

To predict the subway entries, I use an OLS model where $\hat{y}$ is the dependent variable — 'ENTRIESn_hourly', $X_{i(i=1...)}$ are dummy-coded independent variables—'rain', 'Hour', 'Weekday' and 'UNIT', $\beta_{i(i=1...)}$ are co-efficients, $\alpha$ is the constant, and $e_i$ is the random error.

$\hat{y} = \alpha + \beta_i * X_i + e_i $

From the descriptive statistics above we know that 'ENTRIESn_hourly' has a mean value of 1095 and standard deviation of 2337. So instead of using raw units, I normalized 'ENTRIESn_hourly' by log-transforming this variable. A shown below, the dependent variable (y) has a mean value of 2.2 and standard deviation of 1.1.


    y = df['ENTRIESn_hourly']
    y = np.log10(y+1)
    y.describe()




    count    131951.000000
    mean          2.222001
    std           1.104727
    min           0.000000
    25%           1.602060
    50%           2.447158
    75%           3.045323
    max           4.714665
    dtype: float64




    import statsmodels.formula.api as sm
    import scipy
    df['Eins'] = np.ones((len(df), )) 
    X = df[['rain','Eins']]
    dummy_hours = pd.get_dummies(df['Hour'], prefix = 'hour')
    X = X.join(dummy_hours)
    dummy_days = pd.get_dummies(df['Weekday'])
    X = X.join(dummy_days)
    dummy_units = pd.get_dummies(df['UNIT'],prefix='unit')
    X = X.join(dummy_units)
    result = sm.OLS(y, X).fit()


    print result.summary()

                                OLS Regression Results                            
    ==============================================================================
    Dep. Variable:        ENTRIESn_hourly   R-squared:                       0.648
    Model:                            OLS   Adj. R-squared:                  0.647
    Method:                 Least Squares   F-statistic:                     490.5
    Date:                Fri, 17 Oct 2014   Prob (F-statistic):               0.00
    Time:                        15:47:42   Log-Likelihood:            -1.3143e+05
    No. Observations:              131951   AIC:                         2.638e+05
    Df Residuals:                  131456   BIC:                         2.687e+05
    Df Model:                         494                                         
    ==============================================================================
                     coef    std err          t      P>|t|      [95.0% Conf. Int.]
    ------------------------------------------------------------------------------
    rain          -0.0169      0.005     -3.337      0.001        -0.027    -0.007
    Eins        2.955e+10      1e+10      2.947      0.003      9.89e+09  4.92e+10
    hour_0     -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_1     -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_2     -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_3     -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_4     -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_5     -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_6     -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_7     -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_8     -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_9     -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_10    -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_11    -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_12    -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_13    -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_14    -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_15    -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_16    -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_17    -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_18    -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_19    -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_20    -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_21    -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_22    -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    hour_23    -8.782e+08   2.19e+09     -0.400      0.689     -5.18e+09  3.42e+09
    Friday      2.423e+10   1.14e+10      2.121      0.034      1.84e+09  4.66e+10
    Monday      2.423e+10   1.14e+10      2.121      0.034      1.84e+09  4.66e+10
    Saturday    2.423e+10   1.14e+10      2.121      0.034      1.84e+09  4.66e+10
    Sunday      2.423e+10   1.14e+10      2.121      0.034      1.84e+09  4.66e+10
    Thursday    2.423e+10   1.14e+10      2.121      0.034      1.84e+09  4.66e+10
    Tuesday     2.423e+10   1.14e+10      2.121      0.034      1.84e+09  4.66e+10
    Wednesday   2.423e+10   1.14e+10      2.121      0.034      1.84e+09  4.66e+10
    unit_R001   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R002   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R003   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R004   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R005   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R006   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R007   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R008   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R009   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R010   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R011   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R012   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R013   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R014   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R015   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R016   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R017   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R018   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R019   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R020   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R021   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R022   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R023   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R024   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R025   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R027   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R028   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R029   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R030   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R031   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R032   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R033   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R034   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R035   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R036   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R037   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R038   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R039   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R040   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R041   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R042   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R043   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R044   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R045   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R046   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R047   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R048   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R049   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R050   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R051   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R052   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R053   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R054   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R055   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R056   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R057   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R058   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R059   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R060   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R061   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R062   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R063   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R064   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R065   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R066   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R067   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R068   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R069   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R070   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R079   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R080   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R081   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R082   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R083   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R084   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R085   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R086   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R087   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R088   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R089   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R090   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R091   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R092   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R093   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R094   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R095   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R096   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R097   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R098   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R099   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R100   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R101   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R102   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R103   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R104   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R105   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R106   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R107   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R108   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R109   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R110   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R111   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R112   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R113   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R114   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R115   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R116   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R117   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R118   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R119   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R120   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R121   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R122   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R123   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R124   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R125   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R126   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R127   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R128   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R129   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R130   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R131   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R132   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R133   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R134   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R135   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R136   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R137   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R138   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R139   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R140   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R141   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R142   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R143   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R144   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R145   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R146   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R147   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R148   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R149   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R150   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R151   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R152   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R153   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R154   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R155   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R156   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R157   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R158   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R159   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R160   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R161   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R163   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R164   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R165   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R166   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R167   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R168   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R169   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R170   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R171   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R172   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R173   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R174   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R175   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R176   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R177   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R178   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R179   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R180   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R181   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R182   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R183   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R184   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R185   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R186   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R187   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R188   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R189   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R190   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R191   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R192   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R193   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R194   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R195   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R196   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R197   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R198   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R199   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R200   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R201   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R202   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R203   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R204   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R205   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R206   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R207   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R208   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R209   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R210   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R211   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R212   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R213   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R214   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R215   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R216   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R217   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R218   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R219   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R220   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R221   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R222   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R223   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R224   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R225   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R226   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R227   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R228   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R229   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R230   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R231   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R232   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R233   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R234   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R235   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R236   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R237   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R238   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R239   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R240   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R241   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R242   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R243   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R244   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R246   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R247   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R248   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R249   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R250   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R251   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R252   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R253   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R254   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R255   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R256   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R257   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R258   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R259   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R260   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R261   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R262   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R263   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R264   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R265   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R266   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R267   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R268   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R269   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R270   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R271   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R272   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R273   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R274   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R275   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R276   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R277   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R278   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R279   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R280   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R281   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R282   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R283   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R284   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R285   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R286   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R287   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R288   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R289   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R290   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R291   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R292   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R293   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R294   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R295   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R296   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R297   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R298   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R299   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R300   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R301   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R302   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R303   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R304   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R306   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R307   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R308   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R309   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R310   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R311   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R312   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R313   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R314   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R315   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R316   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R317   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R318   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R319   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R320   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R321   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R322   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R323   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R324   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R325   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R326   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R327   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R328   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R329   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R330   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R331   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R332   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R333   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R334   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R335   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R336   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R337   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R338   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R339   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R340   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R341   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R342   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R343   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R344   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R345   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R346   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R347   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R348   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R349   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R350   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R352   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R353   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R354   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R355   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R356   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R357   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R358   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R359   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R360   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R361   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R362   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R363   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R364   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R365   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R366   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R367   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R368   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R369   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R370   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R371   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R372   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R373   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R374   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R375   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R376   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R377   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R378   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R379   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R380   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R381   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R382   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R383   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R384   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R385   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R386   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R387   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R388   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R389   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R390   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R391   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R392   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R393   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R394   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R395   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R396   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R397   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R398   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R399   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R400   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R401   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R402   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R403   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R404   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R405   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R406   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R407   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R408   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R409   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R411   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R412   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R413   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R414   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R415   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R416   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R417   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R418   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R419   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R420   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R421   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R422   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R423   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R424   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R425   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R426   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R427   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R428   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R429   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R430   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R431   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R432   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R433   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R434   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R435   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R436   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R437   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R438   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R439   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R440   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R441   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R442   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R443   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R444   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R445   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R446   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R447   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R448   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R449   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R450   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R451   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R452   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R453   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R454   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R455   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R456   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R459   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R460   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R461   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R462   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R463   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R464   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R468   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R469   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R535   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R536   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R540   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R541   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R542   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R543   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R544   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R545   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R546   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R547   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R548   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R549   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R550   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R551   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    unit_R552   -5.29e+10   1.34e+10     -3.937      0.000     -7.92e+10 -2.66e+10
    ==============================================================================
    Omnibus:                    21151.136   Durbin-Watson:                   1.551
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):            57528.531
    Skew:                          -0.875   Prob(JB):                         0.00
    Kurtosis:                       5.721   Cond. No.                          nan
    ==============================================================================
    
    Warnings:
    [1] The smallest eigenvalue is -3.82e-12. This might indicate that there are
    strong multicollinearity problems or that the design matrix is singular.


A note on feature selection: My gut feeling of modelling is to throw as many independent variables as possible. That's why in the beginning I used 'fog','thunder','meanwindspdi','precipi','Hour','meantempi','meanpressure'. But as the Udacity coach pointed out,  'precipi' is highly correlated with 'rain', which is not good. Also, the dummy coded 'Hour's in my current model have really high p-values. So shall I take it out? Once I remove 'Hour', the r-square value reduces to 0.536 and the confidence  intervals of dummy coded 'UNIT's become very wide. These all relate to the topic of feature selection, which the course doesn't address a lot; nonetheless, I will proceed with current model and hope I will dive deeper into this problem in other classes. Here is [an easy-to-read article](http://www.thejuliagroup.com/blog/?p=1405) that helps you understand the problem of multicollinearity. 

Whew, since I made dummy variables for the UNIT, the summary is extremely long.

The r-square under this model is 0.648, meaning that my model can explain 64.8% of the total variation.With a p-value at 0.001, there is strong evidence that the coefficient of 'rain' is different from 0. Further, the co-efficient (-0.0169) suggests a negative relationship between raining days and total entries; in other words, there are fewer entrants on a rainy day than a non-rainy day. This is very different from what the simple descriptive statistics show above where non-raining days have lower mean and median values than raining days. 

#### Plotting the real data and predicted values

Note that I log-transformed 'ENTRIES_hourly' when building OLS model, so now I need to transform the predictions to the original scale for better comparison. To compare the predicted values with the real data, I plot both of them below:


    predicted = 10**result.fittedvalues  
    fig = plt.figure()
    fig.set_size_inches(18,10)
    ax = fig.add_subplot(111)
    plot1 = ax.plot(new_x, df['ENTRIESn_hourly'], 'ro')
    plot2 = ax.plot(new_x,predicted, 'bo')
    ax.set_ylim(0,55000)
    ax.set_ylabel('Total Entries')
    ax.set_xlabel('Date & Time')
    ax.set_title('Real entries vs. prediction')
    ax.xaxis.set_major_locator(mdates.WeekdayLocator(SU))
    myFmt = mdates.DateFormatter('%m/%d, %a')
    ax.xaxis.set_major_formatter(myFmt)
    ax.legend((plot1[0], plot2[0]), ('Real entries','Predicted value'))
    plt.show()


![png](Introduction%20to%20Data%20Science%20Final%20Project%20Submission_files/Introduction%20to%20Data%20Science%20Final%20Project%20Submission_43_0.png)


From the graph, it looks like the predicted values (blue dots) are able to emulate the volitility by hours in a given day; yet, my model consistenly underpredicts the peak entries. A better method to decide the fitness of the model is to study the distribution of the residuals.

#### Examning residuals


    plt.hist(result.norm_resid())
    plt.ylabel('Count')
    plt.xlabel('Normailzed residual')
    k2, p2 = scipy.stats.normaltest(result.norm_resid())
    print "p-value of normal distribution test: %.2f" %p2

    p-value of normal distribution test: 0.00



![png](Introduction%20to%20Data%20Science%20Final%20Project%20Submission_files/Introduction%20to%20Data%20Science%20Final%20Project%20Submission_46_1.png)


I use the normal distribution test and obtain the p-value less than 0.05, which indicates that the residuals are unlikely to come from normal distribution. It means that my OLS model is biased and needs to improve. The current dataset only contains weather information, time value and turnstile, it would be great if we can have more information such as holiday and street condition to help improve the model.

### 4. Discussing MapReduce

Note that the current dataset includes 2011 May only and it already comprises 130K rows. If we need to analyze a dataset of much bigger size, say MTA entries in the past 10 years, the dataset will be conflated so much that it may take too long for one single computer to do the calculations. In this case, we can explore MapReduce by dividing data into several small chunks and make multiple computers  process the data at the same time. <br>

## Limitation

The above analyese may be improved by:
* Exploring other types of model such as polynominal. Since the residuals are unlikely to come from a normal distribution, it indicates that the OLS model needs adjustment. 
* Collecting more data on other features, for example, whether it's a big holiday (say, St. Patrick Day) when many people are on the move, or whether there is big construction work near the subway stations.

## Conclusion

To sum up, this project primarily explores how the weather condition affects the number of people entering/exiting the NYC subway station. The p-value obtained through Mann Whitney-U test is no more than 0.05, indicating that there is statistically significant difference between rainy and non-rainy days. The OLS model suggests that other factors (hour, day in the week, or unit) being the same, there are likely fewer subway entrants on a rainy day than a non-rainy day. We also know from the visualizatoin that there are more entrants during weekdays than weekends; certain time slots such as 8-9 a.m., 12-1p.m., 4-5 p.m., 8-9 p.m. have more entrants as well. 

Although it's not necessary to increase train schedules during rainy days, MTA needs to assign more cleaning staff, or at least put up the caution signs in the slippery areas.


    Image(url='http://www.safetysign.com/images/catlog/product/large/E5356.png')




<img src="http://www.safetysign.com/images/catlog/product/large/E5356.png"/>



## A Finishing Note

I came from the social science background and have been using R and Stata for the past few years. The course is very well-paced and hands-on(Yes!). All the exercises throughout the classes can apply directly to this final project. It not only exposed me to the field of data science but also led me to explore other cool things such as IPython Notebook. It feels so amazing to combine blogging and programming at the same time. <br>

While working on this project, I also stumbled upon a few  useful tutorials to brush up my knowledge in Python. <br>
* Add CSS style to IPython Notebook:<br>
http://nbviewer.ipython.org/github/fperez/blog/blob/master/120907-Blogging%20with%20the%20IPython%20Notebook.ipynb
* Chart graphs using multiple packages: <br>
Although ggplot was used during the class, I realize that it can't be customized as much as matplot lib especially within IPython, as referenced here https://github.com/yhat/ggplot/issues/223. For that reason, I mostly use matplotlib for this project. That said, ggplot certainly looks better in terms of color and layout and I will definitely explore more in the days ahead.
