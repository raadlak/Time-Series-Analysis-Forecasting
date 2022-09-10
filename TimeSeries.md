# Time Series Analysis

###### Excerpt From Time Series Analysis with Python Cookbook by Tarek A. Atwan

### A. Reading TimeSeries Data in Pandas from Files    

Often we see that time series Data is represented with Indexes. Reason in general to represent DataFrame with Index is to make slicing and dicing operations very intuitive.<br> DatetimeIndex is used for time series as Index which unlocks many features and useful functions esential while working with time series data.

Following are the recipes to efficiently read time series data into a DataFrame.

#### 1. Reading data from CSVs and other delimited files


```python
import pandas as pd
from pathlib import Path
```


```python
_base_location = '/Users/rahuladlakha/Documents/Github/Code_books/TimeSeriesAnalysis_Python/Datasets/Time-Series-Analysis-with-Python-Cookbook./datasets/'
chapter = 'Ch2'
filename = '/movieboxoffice.csv'
filepath = Path(_base_location+chapter+filename)
```

Since the first column of the dataset is Date column, we can convert it to DateTimeIndex and parse it as Date while reading it into a pandas DataFrame.


```python
ts = pd.read_csv(filepath,
            header=0, # by Default it is infer, If csv doesn't contain header, then header=None. 
            #If we prefer to supply custom col names as header, then supply header=0 and overwrite it by providing a list of col to names argument.
            parse_dates=['Date'], #can take list with col positions as well col names. 
            index_col=0, #can take position as well as name of the col. can be used to create MultiIndex, using list of position of col or string as cols.
            infer_datetime_format=True,
            usecols=['Date',
                     'DOW',
                     'Daily',
                     'Forecast',
                     'Percent Diff'])# can take positional indices)
```


```python
ts.sample(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>DOW</th>
      <th>Daily</th>
      <th>Forecast</th>
      <th>Percent Diff</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-04-26</th>
      <td>Friday</td>
      <td>$125,789.89</td>
      <td>$235,036.46</td>
      <td>-46.48%</td>
    </tr>
    <tr>
      <th>2021-07-31</th>
      <td>Wednesday</td>
      <td>$48.06</td>
      <td>$59.98</td>
      <td>-19.87%</td>
    </tr>
    <tr>
      <th>2021-06-03</th>
      <td>Monday</td>
      <td>$753.48</td>
      <td>$1,464.27</td>
      <td>-48.54%</td>
    </tr>
    <tr>
      <th>2021-08-15</th>
      <td>Thursday</td>
      <td>$1.92</td>
      <td>$2.98</td>
      <td>-35.41%</td>
    </tr>
    <tr>
      <th>2021-07-03</th>
      <td>Wednesday</td>
      <td>$409.16</td>
      <td>$740.10</td>
      <td>-44.72%</td>
    </tr>
  </tbody>
</table>
</div>



There are cases where the parse_dates parameter doesn't work, this is where the **date_parser** parameter can be useful.
date_parser will contain a lambda function to convert the date column using pandas **to_datetime** function. <br><br>
Format is added to the date parser to point out the existing format of the date column. <br>
* %d represents, day of the month - 21<br>
* %b represents, month as short, ex. Jan <br>
* %y represents, two digit year, 90
<br> <br>
* %Y represents, year four digit, 2022 <br>
* %B represents, month in full, April <br>
* %m represents, date as two digits, 12 <br>


```python
date_parser = lambda x: pd.to_datetime(x, format="%d-%b-%y")
```


```python
ts2=pd.read_csv(filepath, 
                parse_dates=[0],
                index_col=0,
                date_parser=date_parser,
                usecols=[0,1,3,7,6])
```


```python
ts2.sample(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>DOW</th>
      <th>Daily</th>
      <th>Forecast</th>
      <th>Percent Diff</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-05-04</th>
      <td>Saturday</td>
      <td>$55,900.40</td>
      <td>$62,773.88</td>
      <td>-10.95%</td>
    </tr>
    <tr>
      <th>2021-06-15</th>
      <td>Saturday</td>
      <td>$1,109.08</td>
      <td>$1,424.36</td>
      <td>-22.14%</td>
    </tr>
    <tr>
      <th>2021-05-14</th>
      <td>Tuesday</td>
      <td>$5,192.90</td>
      <td>$5,399.49</td>
      <td>-3.83%</td>
    </tr>
    <tr>
      <th>2021-06-09</th>
      <td>Sunday</td>
      <td>$1,328.70</td>
      <td>$1,957.15</td>
      <td>-32.11%</td>
    </tr>
    <tr>
      <th>2021-06-17</th>
      <td>Monday</td>
      <td>$368.78</td>
      <td>$662.31</td>
      <td>-44.32%</td>
    </tr>
  </tbody>
</table>
</div>




```python
ts.info()
```

    <class 'pandas.core.frame.DataFrame'>
    DatetimeIndex: 128 entries, 2021-04-26 to 2021-08-31
    Data columns (total 4 columns):
     #   Column        Non-Null Count  Dtype 
    ---  ------        --------------  ----- 
     0   DOW           128 non-null    object
     1   Daily         128 non-null    object
     2   Forecast      128 non-null    object
     3   Percent Diff  128 non-null    object
    dtypes: object(4)
    memory usage: 5.0+ KB


Note: Date column is now of type DateTimeIndex. Column Daily and Forecast is of dtype *'object'*, to correct the dtype we would need to remove $ sign and convert them to astype float.


```python
c_df = ts[['Daily', 'Forecast']].apply(lambda x: x.str.replace('[^\d]', '', regex=True)) # replace $ and change dtype
```


```python
c_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Daily</th>
      <th>Forecast</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-04-26</th>
      <td>12578989</td>
      <td>23503646</td>
    </tr>
    <tr>
      <th>2021-04-27</th>
      <td>9937401</td>
      <td>19762255</td>
    </tr>
    <tr>
      <th>2021-04-28</th>
      <td>8220316</td>
      <td>11699126</td>
    </tr>
    <tr>
      <th>2021-04-29</th>
      <td>3353026</td>
      <td>6665265</td>
    </tr>
    <tr>
      <th>2021-04-30</th>
      <td>3010524</td>
      <td>3482819</td>
    </tr>
  </tbody>
</table>
</div>




```python
ts[['Daily', 'Forecast']] = c_df.astype(float)
```


```python
ts.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>DOW</th>
      <th>Daily</th>
      <th>Forecast</th>
      <th>Percent Diff</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-04-26</th>
      <td>Friday</td>
      <td>12578989.0</td>
      <td>23503646.0</td>
      <td>-46.48%</td>
    </tr>
    <tr>
      <th>2021-04-27</th>
      <td>Saturday</td>
      <td>9937401.0</td>
      <td>19762255.0</td>
      <td>-49.72%</td>
    </tr>
    <tr>
      <th>2021-04-28</th>
      <td>Sunday</td>
      <td>8220316.0</td>
      <td>11699126.0</td>
      <td>-29.74%</td>
    </tr>
    <tr>
      <th>2021-04-29</th>
      <td>Monday</td>
      <td>3353026.0</td>
      <td>6665265.0</td>
      <td>-49.69%</td>
    </tr>
    <tr>
      <th>2021-04-30</th>
      <td>Tuesday</td>
      <td>3010524.0</td>
      <td>3482819.0</td>
      <td>-13.56%</td>
    </tr>
  </tbody>
</table>
</div>



#### 2. Reading Data from Excel Files

Excels contain multiple worksheets, so it is essential to explore different options to read Excels.In read_excel(), we will use engine parameter to specify, which library to use for processing Excels. <br>
Ex: openpyxl, xlrd


```python
filename = '/sales_trx_data.xlsx'
```


```python
filepath = Path(_base_location+chapter+filename)
```


```python
excel_ts = pd.read_excel(filepath,
                        engine='openpyxl',
                        index_col=1,
                        sheet_name=[0,1], #can use sheet names, sheet positions or combination of both
                        parse_dates=True)

# ExcelFile - Function provides additional methods and properties, can be used to find sheetname
```


```python
excel_ts[0].info()
```

    <class 'pandas.core.frame.DataFrame'>
    DatetimeIndex: 36764 entries, 2017-01-01 to 2017-12-31
    Data columns (total 4 columns):
     #   Column              Non-Null Count  Dtype 
    ---  ------              --------------  ----- 
     0   Line_Item_ID        36764 non-null  int64 
     1   Credit_Card_Number  36764 non-null  int64 
     2   Quantity            36764 non-null  int64 
     3   Menu_Item           36764 non-null  object
    dtypes: int64(3), object(1)
    memory usage: 1.4+ MB


For the current excel, the two dataframes within the dictionary are identical in terms of schema, we can stack them into one DataFrame with single DateTimeIndex


```python
ts_combined = pd.concat([excel_ts[0], excel_ts[1]])
```


```python
ts_combined.info()
```

    <class 'pandas.core.frame.DataFrame'>
    DatetimeIndex: 74124 entries, 2017-01-01 to 2018-12-31
    Data columns (total 4 columns):
     #   Column              Non-Null Count  Dtype 
    ---  ------              --------------  ----- 
     0   Line_Item_ID        74124 non-null  int64 
     1   Credit_Card_Number  74124 non-null  int64 
     2   Quantity            74124 non-null  int64 
     3   Menu_Item           74124 non-null  object
    dtypes: int64(3), object(1)
    memory usage: 2.8+ MB


#### 3. Reading data from GitHub

To read data from github we need url to the raw content, like (https://media.githubusercontent.com/media/PacktPublishing/Time-Series-Analysis-with-Python-Cookbook./main/datasets/Ch2/AirQualityUCI.csv)


```python
url = 'https://media.githubusercontent.com/media/PacktPublishing/Time-Series-Analysis-with-Python-Cookbook./main/datasets/Ch2/AirQualityUCI.csv'
```


```python
date_parser = lambda x: pd.to_datetime(x, format="%d/%m/%Y")
```


```python
df = pd.read_csv(url,
                delimiter=';',
                index_col = 'Date',
                date_parser=date_parser
                )
```


```python
df.iloc[:3, 1:4]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CO(GT)</th>
      <th>PT08.S1(CO)</th>
      <th>NMHC(GT)</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2004-03-10</th>
      <td>2,6</td>
      <td>1360</td>
      <td>150</td>
    </tr>
    <tr>
      <th>2004-03-10</th>
      <td>2</td>
      <td>1292</td>
      <td>112</td>
    </tr>
    <tr>
      <th>2004-03-10</th>
      <td>2,2</td>
      <td>1402</td>
      <td>88</td>
    </tr>
  </tbody>
</table>
</div>



#### 4. Reading data from public S3 bucket

To read data from public S3 bucket, you don't need to specify the region (us-east-1), we can directly read it using s3 url.


```python
url = 'https://s3.amazonaws.com/tscookbook/AirQualityUCI.xlsx'
```


```python
df_s3 = pd.read_excel(url,
                    index_col='Date',
                    parse_dates=True
                    )
```


```python
df_s3.sample(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Time</th>
      <th>CO(GT)</th>
      <th>PT08.S1(CO)</th>
      <th>NMHC(GT)</th>
      <th>C6H6(GT)</th>
      <th>PT08.S2(NMHC)</th>
      <th>NOx(GT)</th>
      <th>PT08.S3(NOx)</th>
      <th>NO2(GT)</th>
      <th>PT08.S4(NO2)</th>
      <th>PT08.S5(O3)</th>
      <th>T</th>
      <th>RH</th>
      <th>AH</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2005-01-29</th>
      <td>04:00:00</td>
      <td>0.5</td>
      <td>776.00</td>
      <td>-200</td>
      <td>0.519861</td>
      <td>444.50</td>
      <td>36.6</td>
      <td>1459.75</td>
      <td>34.1</td>
      <td>737.25</td>
      <td>330.25</td>
      <td>1.375</td>
      <td>53.750</td>
      <td>0.368660</td>
    </tr>
    <tr>
      <th>2004-08-17</th>
      <td>07:00:00</td>
      <td>2.2</td>
      <td>1117.50</td>
      <td>-200</td>
      <td>12.868432</td>
      <td>1079.25</td>
      <td>135.0</td>
      <td>637.75</td>
      <td>68.0</td>
      <td>1761.25</td>
      <td>1144.75</td>
      <td>22.525</td>
      <td>52.325</td>
      <td>1.410027</td>
    </tr>
    <tr>
      <th>2004-04-17</th>
      <td>19:00:00</td>
      <td>-200.0</td>
      <td>1096.75</td>
      <td>-200</td>
      <td>7.820412</td>
      <td>891.75</td>
      <td>-200.0</td>
      <td>908.50</td>
      <td>-200.0</td>
      <td>1594.25</td>
      <td>833.00</td>
      <td>16.075</td>
      <td>55.975</td>
      <td>1.015072</td>
    </tr>
    <tr>
      <th>2004-11-20</th>
      <td>10:00:00</td>
      <td>2.9</td>
      <td>1086.50</td>
      <td>-200</td>
      <td>17.111654</td>
      <td>1213.25</td>
      <td>661.0</td>
      <td>667.75</td>
      <td>243.0</td>
      <td>1215.50</td>
      <td>1401.50</td>
      <td>11.900</td>
      <td>27.925</td>
      <td>0.388151</td>
    </tr>
    <tr>
      <th>2004-10-29</th>
      <td>03:00:00</td>
      <td>0.6</td>
      <td>868.25</td>
      <td>-200</td>
      <td>2.162700</td>
      <td>596.00</td>
      <td>-200.0</td>
      <td>1092.25</td>
      <td>-200.0</td>
      <td>1275.50</td>
      <td>578.75</td>
      <td>18.675</td>
      <td>73.475</td>
      <td>1.566380</td>
    </tr>
  </tbody>
</table>
</div>



Reading the same file using s3url


```python
s3uri = 's3://tscookbook/AirQualityUCI.csv'
```

#### 4.1 Reading data from private S3 Bucket


```python
import configparser
config = configparser.ConfigParser()
config.read('aws.cfg')

AWS_ACCESS_KEY = config['AWS']['aws_access_key']
AWS_SECRET_KEY = config['AWS']['aws_secret_key']
```

To use AWS config Access Key and Secret Key we need to pass them in the read_csv function as storage_options


```python
df = pd.read_csv(s3uri,
                 index_col = 'Date',
                 parse_dates = True,
                 storage_options = {
                 'key' : AWS_ACCESS_KEY,
                 'secret' : AWS_SECRET_KEY})
```

Alternatively, you can use the AWS SDK for Python (Boto3) to achieve similar results.


```python
import boto3 #Boto3 offers two levels of APIs: client and resource.
bucket = "tscookbook-private"
client = boto3.client("s3",
                      aws_access_key_id = AWS_ACCESS_KEY,
                      aws_secret_access_key = AWS_SECRET_KEY) #client object has many methods specific to the AWS S3 service for creating, deleting and retrieving bucket info, etc.

```

The **client** is a low level service access interface that gives you more granular control, ex. boto3.client("s3). <br>
The **resource** is a high level object-oriented interface (an abstraction layer), for example, boto3.resource("s3")


```python
data = client.get_object(Bucket=bucket, Key='AirQuality.csv')
df = pd.read_csv(data['Body'], # when we call get_object, we receive a key-value pair response, however we are interested in the response body.
                index_col='Date',
                parse_dates=True)
```

#### 5. Reading data from HTML

We can read HTML tables using pandas.read_html() function. We will https://en.wikipedia.org/wiki/COVID-19_pandemic_by_country_and_territory link to access HTML Wikipedia data.


```python
url = "https://en.wikipedia.org/wiki/COVID-19_pandemic_by_country_and_territory"
result = pd.read_html(url)
```


```python
print(len(result)) #returned 70 dataframes
```

    70



```python
df_html = result[15] #Dataframe at index 15 contains summary of COVID-19 cases
```


```python
df_html.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Region[29]</th>
      <th>Total cases</th>
      <th>Total deaths</th>
      <th>Cases per million</th>
      <th>Deaths per million</th>
      <th>Current weekly cases</th>
      <th>Current weekly deaths</th>
      <th>Population millions</th>
      <th>Vaccinated %[30]</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>European Union</td>
      <td>158263205</td>
      <td>1116388</td>
      <td>353803</td>
      <td>2496</td>
      <td>1743773</td>
      <td>4439</td>
      <td>447</td>
      <td>74.9</td>
    </tr>
    <tr>
      <th>1</th>
      <td>North America</td>
      <td>94411188</td>
      <td>1067866</td>
      <td>255991</td>
      <td>2895</td>
      <td>928336</td>
      <td>3180</td>
      <td>369</td>
      <td>74.3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Other Europe</td>
      <td>54061026</td>
      <td>485902</td>
      <td>231385</td>
      <td>2080</td>
      <td>572668</td>
      <td>1592</td>
      <td>234</td>
      <td>60.9</td>
    </tr>
    <tr>
      <th>3</th>
      <td>South America</td>
      <td>61630291</td>
      <td>1288867</td>
      <td>143368</td>
      <td>2998</td>
      <td>402909</td>
      <td>2296</td>
      <td>430</td>
      <td>80.6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Russia and Central Asia</td>
      <td>22414473</td>
      <td>423836</td>
      <td>94658</td>
      <td>1790</td>
      <td>71486</td>
      <td>252</td>
      <td>237</td>
      <td>50.8</td>
    </tr>
  </tbody>
</table>
</div>



Most of the pandas functions accept URL as a path, examples includes following: <br>

* pandas.read_csv() <br>
* pandas.read_excel() <br>
* pandas.read_parquet() <br>
* pandas.read_table() <br>
* pandas.read_pickle() <br>
* pandas.read_orc() <br>
* pandas.read_stata() <br>
* pandas.read_sas() <br>
* pandas.read_json() <br>

The URL needs to be one of the valid URL schemes that pandas supports, which includes http and https, ftp, s3, gs, or the file protocol.
			

#### 6. Reading data from SAS dataset

We can use pandas.read_sas() function to read SAS7BDAT files.


```python
filename = '/DCSKINPRODUCT.sas7bdat'
```


```python
path = Path(_base_location+chapter+filename)
```


```python
df = pd.read_sas(path, chunksize=10000) 
type(df) # returned object is a SAS7BDATReader object. we can retrieve first chunk using the next() method.
```




    pandas.io.sas.sas7bdat.SAS7BDATReader



We will need to iterate through the chunks to do some computations. Since the data in most SAS7BDAT files is huge, this is the way forward. 


```python
results = []
for chunk in df:
    results.append(chunk)
len(results) #There are 16 chunks(DataFrame) in total.
```




    16




```python
df_all = pd.concat(results)
df_all.shape
```




    (152130, 5)




```python
df = pd.read_sas(path, chunksize=10000) #Re-read the chunks, grouping by Date and aggregating using sum and count.
results = []
for chunk in df:
    results.append(
        chunk.groupby('DATE')['Revenue']
             .agg(['sum', 'count']))
len(results)
```




    16




```python
results[0].loc['2013-02-10']
```




    sum      923903.0
    count        91.0
    Name: 2013-02-10 00:00:00, dtype: float64



Pandas is a single core framework, doesn't offer parallel computation. There are specialized libraries that offer parallel processing, ex. Dask.<br> It creates computational graphs and parallelizes small tasks and improves speed and reduces memory overheads, which are generally high if we use just Pandas. <br><br>

There is a Modin library which acts as a wrapper against the Dask/Ray library, it optimizes the pandas code without adding another framework into the code. 


```python
import memory_profiler
filename = '/large_file.csv'
```


```python
%load_ext memory_profiler
l_path = Path(_base_location+chapter+filename)
```


```python
%%time
%memit pd.read_csv(l_path).groupby('label_source').count()

```

    peak memory: 310.29 MiB, increment: 101.82 MiB
    CPU times: user 270 ms, sys: 56.9 ms, total: 327 ms
    Wall time: 820 ms


### B. Reading Time Series Data from Databases

Reading TimeSeries Data from different databases with DatetimeIndex. <br>

Reading data from a relational database <br>
Reading data from Snowflake <br>
Reading data from a document database (MongoDB) <br>
Reading third-party financial data using APIs <br>
Reading data from a time series database (InfluxDB) <br>


#### B.1 Reading Data from relational databases

To read data from relational databases we will start with Postgre SQL. We will be exploring two different methods to connect to PSQL. <br>
1. psycopg2 - python connector for PSQL.
2. SQL Alchemy - an object-relational mapper (ORM), with Pandas.

##### B.1.1 Using psycopg2


```python
import psycopg2
import pandas as pd
params = {"host": "127.0.0.1",
          "database": "postgres",
          "user": "postgres",
          "password": "password"} #parameter values to 
```

Connection can be established by passing the parameters to the .connect() method. object for cursor function can be used to execute SQL queries. The cursor object provides several attributes and methods, including execute() and fetchall().


```python
conn = psycopg2.connect(**params)
cursor = conn.cursor()
```


```python
cursor.execute("""SELECT date, last, volume
                FROM yen_tbl,
                ORDER BY date;""")
cursor.rowcount
```

Note: The returned result set after the query execution will not include a header.


```python
cursor.description # To grab the column names from the cursor object using description attribute.
columns = [col[0] for col in cursor.description]
columns
```


```python
data = cursor.fetchall()
df = pd.DataFrame(data, columns=columns)
df.info()
```


Another way to grab the table with column name is by instructing the cursor to return a **RealDictRow** class to the cursor_factory parameter


```python
from psycopg2.extras import RealDictCursor
cursor = conn.cursor(cursor_factory=RealDictCursor)
cursor.execute("SELECT * from yen_tbl;")
data = cursor.fetchall()
df = pd.DataFrame(data)
cursor.close()
conn.close()
```

psycopg2 connections and cursors can be used with Python's with statement for exceptions handling. when commiting an transaction. The cursor object provides three different fetching functions that is fetchall(), fetchmany() and fetchone(). Fetchone() returns a single tuple.


```python
import psycopg2
url = 'postgresql://postgres:password@localhost:5432'
with psycopg2.connect(url) as conn:
    with conn.cursor() as cursor:
        cursor.excecute("SELECT * FROM yen_tbl")
        data = cursor.fetchall()

```

##### B.1.2 Using SQL Alchemy

SQL Alchemy is ORM (object relational mapper), which provides abstraction layer to use OOP to interact with relational database.<br>
SQL Alchemy integrates well with pandas, and pandas SQL reader and writer functions depend on SQL Alchemy as abstraction layer.<br><br>

Some of the pandas reader function that rely on SQLAlchemy include, **pandas.read_sql(), pandas.read_sql_query() and pandas.read_sql_table()**


```python
import pandas as pd
from sqlalchemy import create_engine
engine = create_engine("postgresql+psycopg2://postgres:password@localhost:5432") #SQLAlchemy uses the dialect and the driver (DBAPI).
query = "SELECT * FROM yen_tbl"
df = pd.read_sql(query,
                 engine,
                 index_col='date',
                 parse_dates={'date': '%Y-%m-%d'})

df['last'].tail(3)
```

The same can be accomplished by using pandas.read_sql_query() function.


```python
df = pd.read_sql_query(query,
                       engine,
                       index_col='date',
                       parse_dates={'date':'%Y-%m-%d'})
df['last'].tail(3)
```

Pandas has another SQL reader function called pandas.read_sql_table() that does noth take a SQL query, instead taking a table name. This can be considered as SELECT * FROM sometable query.


```python
df = pd.read_sql_table('yen_tbl',
                        engine,
                        index_col='date')
df.index[0]
```

AWS Redshift is based on PostgreSQL, hence the same connection (read, dialect and DBAPI) can be used to connect to AWS Redshift warehouse.


```python
from configparser import ConfigParser
config = ConfigParser()
config.read('snow.cfg')
config.sections()

params = dict(config['AWS'])
```


```python
import pandas as pd
from sqlalchemy import create_engine
host = params['host']
port = 5439
database = 'dev'
username = params['username']
chunksize = 1000
password = params['password']
query = "SELECT * FROM yen_tbl"

aws_engine = create_engine(f"postgresql+psycopg2://{username}:{password}@{host}:\
                   {port}/{database}")

df = pd.read_sql(query,
                 aws_engine,
                 index_col='date',
                 parse_dates=True,
                 chunksize=chunksize)

next(df)
```

##### B.2 Reading Data from Snowflake

Installation required: <br>

“conda install -c conda-forge snowflake-sqlalchemy snowflake-connector-python” <br>
“pip install "snowflake-connector-python[pandas]" <br>
pip install --upgrade snowflake-sqlalchemy”



```python
import pandas as pd
from snowflake import connector
from configparser import ConfigParser
```


```python
connector.paramstyle='qmark'
config = ConfigParser()
config.read('snow.cfg')
config.sections()
params = dict(config['SNOWFLAKE'])

```


```python
con = connector.connect(**params)
cursor = con.cursor()
```


```python
query = "select * from ORDERS;"
cursor.execute(query)

df = cursor.fetch_pandas_all()
df.info()
```

From the preceding output, you can see that the DataFrame's Index is just a sequence of numbers and that the O_ORDERDATE column is not a Date field. <br> This can be fixed by parsing the O_ORDERDATE column with a DatetimeIndex and setting it as an index for the DataFrame:<br>




```python
df_ts = (df.set_index(pd.to_datetime(df['O_ORDERDATE']))
            .drop(columns='O_ORDERDATE'))
df_ts.iloc[0:3, 1:5]
```

##### B.3 Reading data from third-party financial data using APIs

pandas-datareader, which provides remote data access to extract data from multiple data sources, including Yahoo Finance, Quandl, and Alpha Vantage, etc. <br>
The library not only fetches the data but also returns the data as a pandas DataFrame and the index as a DatetimeIndex”



```python
import pandas as pd
import datetime
import matplotlib.pyplot as plt
import pandas_datareader.data as web
```


```python
start_date = (datetime.datetime.today() -
        datetime.timedelta(weeks=52*10)).strftime('%Y-%m-%d')
end_date = datetime.datetime.today().strftime('%Y-%m-%d')
tickers = ['MSFT','AAPL']

```


```python
dt = web.DataReader(name=tickers,
                    data_source='yahoo',
                    start=start_date,
                    end=end_date)['Adj Close']
dt.tail(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Symbols</th>
      <th>MSFT</th>
      <th>AAPL</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2022-09-01</th>
      <td>260.399994</td>
      <td>157.960007</td>
    </tr>
    <tr>
      <th>2022-09-02</th>
      <td>256.059998</td>
      <td>155.809998</td>
    </tr>
  </tbody>
</table>
</div>




```python
dt = web.get_data_yahoo(tickers)['Adj Close']
dt.tail(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Symbols</th>
      <th>MSFT</th>
      <th>AAPL</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2022-09-01</th>
      <td>260.399994</td>
      <td>157.960007</td>
    </tr>
    <tr>
      <th>2022-09-02</th>
      <td>256.059998</td>
      <td>155.809998</td>
    </tr>
  </tbody>
</table>
</div>



Additionally, the library provides other high-level functions for many of the data sources, as follows:

* get_data_quandl

* get_data_tiingo

* get_data_alphavantage

* get_data_fred

* get_data_stooq

* get_data_moex



#### B.4 Reading data from time series database (InfluxDB)

A time serie DB, a type of NoSQL database, is optimized for time-stamped or time series data for improved performance, especially when working with large datasets containing IoT data or sensor data. 

Installation required; InfluxDB python SDK

* “conda install -c conda-forge influxdb-client”


```python
from influxdb_client import InfluxDBClient
import pandas as pd
import matplotlib.pyplot as plt
```


```python
token = "WXT1Hkn-Hg3FGvKChg4UQ2IW2c2_zprqbj63A1GntGVVQIZ2wZP8egDSD91MH_56sM8LbheJ1WZjM1iNI_60NQ=="
org = "my-org"
bucket = "noaa"
```


```python
client = InfluxDBClient(url="http://localhost:8086", 
                        token=token,
                        org=org)
```


```python
query_api = client.query_api()
```


```python
query = '''
        from(bucket: "noaa")
            |> range(start: 2019-09-01T00:00:00Z)
            |> filter(fn: (r) => r._measurement == "h2o_temperature")
            |> filter(fn: (r) => r.location == "coyote_creek")
            |> filter(fn: (r) => r._field == "degrees")
            |> movingAverage(n: 120)
        '''
result = client.query_api().query_data_frame( query=query)
```


```python
result.info()
```


```python
result.loc[0:5, '_time':'_value']
```


```python
result.set_index('_time')['_value'].plot()
plt.show()
```


```python
result = query_api.query_data_frame(query=query,                                 
                                    data_frame_index=['_time'])
result['_value'].head()
```

query_api gives us additional methods to interact with our bucket:
			
* query() returns the result as a FluxTable.

* query_csv() returns the result as a CSV iterator (CSV reader).

* query_data_frame() returns the result as a pandas DataFrame.

* query_data_frame_stream() returns a stream of pandas DataFrames as a generator.

* query_raw() returns the result as raw unprocessed data in s string format.

* query_stream() is similar to query_data_frame_stream but returns a stream of FluxRecord as a generator.


### C. Writing timeseries data to files

### D. Writing time series data to Databases

### E. Working with Date and Time in Python

#### E.1 DatetimeIndex

It is critical to understand DatetimeIndex to work with Date and Time datasets.

**pandas.to_datetime()** - it returns Timestamp object, is a powerful function that can intelligently parse different date representations from strings.


```python
import datetime as dt
import numpy as np
```


```python
dates = ['2021-1-1', '2021-1-2']
dates_pd = pd.to_datetime(dates)
```


```python
dates_pd #produces sequence of type DatetimeIndex.
```




    DatetimeIndex(['2021-01-01', '2021-01-02'], dtype='datetime64[ns]', freq=None)



The DatetimeIndex object gives access to many useful properties and methods to extract additional date and time properties. As an example, you can extract <br>
day_name, <br>
month, <br>
year, <br>
days_in_month, <br> 
quarter, <br>
is_quarter_start, <br> 
is_leap_year, <br>
is_month_start, <br>
is_month_end, and <br>
is_year_start.”<br>



```python
dates = ['2021-01-01', # date str format %Y-%m-%d
         '2/1/2021', # date str format %m/%d/%Y
         '03-01-2021', # date  str format %m-%d-%Y
         'April 1, 2021', # date  str format %B %d, %Y
         '20210501', # date str format %Y%m%d
          np.datetime64('2021-07-01'), # numpy datetime64
          dt.datetime(2021, 8, 1), # python datetime
          pd.Timestamp(2021,9,1) # pandas Timestamp
          ]
```


```python
parsed_dates = pd.to_datetime(
                 dates,
                 infer_datetime_format=True,
                 errors='coerce' #pandas.to_datetime() contains error parameter which sets any value it could not parse as NaT indicating a missing value.
                 )

print(parsed_dates)
```

    DatetimeIndex(['2021-01-01', '2021-02-01', '2021-03-01', '2021-04-01',
                   '2021-05-01', '2021-07-01', '2021-08-01', '2021-09-01'],
                  dtype='datetime64[ns]', freq=None)


Error parameter in **pandas.to_datetime** can take one of the three valid string option:
1. raise - which means it will raise exception (error out).
2. coerce - will not cause exception. Instead, it will just replace pd.NaT, indicating a missing datetime value.
3. ignore - will not cause exception, it will pass the original value.


```python
example = pd.to_datetime(['something 2021', 'Jan 1, 2021'], errors='ignore')
example
```




    Index(['something 2021', 'Jan 1, 2021'], dtype='object')




```python
print(f'Name of Day : {parsed_dates.day_name()}')
print(f'Month : {parsed_dates.month}')
print(f'Year : {parsed_dates.year}')
print(f'Days in Month : {parsed_dates.days_in_month}')
print(f'Quarter {parsed_dates.quarter}')
print(f'Quarter Start : {parsed_dates.is_quarter_start}')
print(f'Leap Year : {parsed_dates.is_leap_year}')
print(f'Month Start : {parsed_dates.is_month_start}')
print(f'Month End : {parsed_dates.is_month_end}')
print(f'Year Start : {parsed_dates.is_year_start}')
```

    Name of Day : Index(['Friday', 'Monday', 'Monday', 'Thursday', 'Saturday', 'Thursday',
           'Sunday', 'Wednesday'],
          dtype='object')
    Month : Int64Index([1, 2, 3, 4, 5, 7, 8, 9], dtype='int64')
    Year : Int64Index([2021, 2021, 2021, 2021, 2021, 2021, 2021, 2021], dtype='int64')
    Days in Month : Int64Index([31, 28, 31, 30, 31, 31, 31, 30], dtype='int64')
    Quarter Int64Index([1, 1, 1, 2, 2, 3, 3, 3], dtype='int64')
    Quarter Start : [ True False False  True False  True False False]
    Leap Year : [False False False False False False False False]
    Month Start : [ True  True  True  True  True  True  True  True]
    Month End : [False False False False False False False False]
    Year Start : [ True False False False False False False False]


pandas.date_range() - It is an alternate way to generate DatetimeIndex. <br>
It requires three parameters:  <br>
start, <br>
end,<br>
period, and <br>
freq <br>


```python
pd.date_range(start='2021-01-01', periods=3, freq='D')
```




    DatetimeIndex(['2021-01-01', '2021-01-02', '2021-01-03'], dtype='datetime64[ns]', freq='D')




```python
pd.date_range(start='2021-01-01', periods=3, freq='D')
```




    DatetimeIndex(['2021-01-01', '2021-01-02', '2021-01-03'], dtype='datetime64[ns]', freq='D')




```python
pd.date_range(start='2021-01-01',
               end='2021-01-03',
               freq='D')
```




    DatetimeIndex(['2021-01-01', '2021-01-02', '2021-01-03'], dtype='datetime64[ns]', freq='D')




```python
pd.date_range(start='2021-01-01',
               periods=3)
```




    DatetimeIndex(['2021-01-01', '2021-01-02', '2021-01-03'], dtype='datetime64[ns]', freq='D')



#### E.2  DateTime Formatting

At times date column is stored as string format, to have more control, to ensure date is parsed correctly as date/datetime object we use strptime() method from datetime module.

We will parse four different representations of January 1, 2022 that will produce the same output – datetime.datetime(2022, 1, 1, 0, 0)



```python
dt.datetime.strptime('1/1/2022', '%m/%d/%Y')
```




    datetime.datetime(2022, 1, 1, 0, 0)




```python
dt.datetime.strptime('1/1/2022', '%m/%d/%Y').date()
```




    datetime.date(2022, 1, 1)




```python
dt.datetime.strptime('1 January, 2022', '%d %B, %Y').date()
```




    datetime.date(2022, 1, 1)




```python
dt.datetime.strptime('1-Jan-2022', '%d-%b-%Y').date()
```




    datetime.date(2022, 1, 1)




```python
dt.datetime.strptime('Saturday, January 1, 2022', '%A, %B %d, %Y').date()
```




    datetime.date(2022, 1, 1)



#### E.3 Tranforming a pandas DataFrame to a time series DataFrame


```python
df = pd.DataFrame(
        {'Date': ['January 1, 2022', 'January 2, 2022', 'January 3, 2022'],
         'Sales': [23000, 19020, 21000]}
            )
```


```python
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Sales</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>January 1, 2022</td>
      <td>23000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>January 2, 2022</td>
      <td>19020</td>
    </tr>
    <tr>
      <th>2</th>
      <td>January 3, 2022</td>
      <td>21000</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['Date'] = pd.to_datetime(df['Date'])
df.set_index('Date', inplace=True)
df.info() #since Date is now index
```

    <class 'pandas.core.frame.DataFrame'>
    DatetimeIndex: 3 entries, 2022-01-01 to 2022-01-03
    Data columns (total 1 columns):
     #   Column  Non-Null Count  Dtype
    ---  ------  --------------  -----
     0   Sales   3 non-null      int64
    dtypes: int64(1)
    memory usage: 48.0 bytes


#### E.4 Working with Time Deltas


```python
df = pd.DataFrame(
        {       
        'item': ['item1', 'item2', 'item3', 'item4', 'item5', 'item6'],
        'purchase_dt': pd.date_range('2021-01-01', periods=6, freq='D', tz='UTC')
        }
)
```


```python
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>item</th>
      <th>purchase_dt</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>item1</td>
      <td>2021-01-01 00:00:00+00:00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>item2</td>
      <td>2021-01-02 00:00:00+00:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>item3</td>
      <td>2021-01-03 00:00:00+00:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>item4</td>
      <td>2021-01-04 00:00:00+00:00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>item5</td>
      <td>2021-01-05 00:00:00+00:00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>item6</td>
      <td>2021-01-06 00:00:00+00:00</td>
    </tr>
  </tbody>
</table>
</div>



The **Timedelta** class makes it possible to derive new datetime objects by adding or subtracting at different ranges or increments, such as seconds, daily, and weekly. This includes time zone-aware calculations



```python
df['expiration_dt'] = df['purchase_dt'] + pd.Timedelta(days=30)
```


```python
df['extended_dt'] = df['purchase_dt'] +\
                pd.Timedelta('35 days 12 hours 30 minutes')
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>item</th>
      <th>purchase_dt</th>
      <th>expiration_dt</th>
      <th>extended_dt</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>item1</td>
      <td>2021-01-01 00:00:00+00:00</td>
      <td>2021-01-31 00:00:00+00:00</td>
      <td>2021-02-05 12:30:00+00:00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>item2</td>
      <td>2021-01-02 00:00:00+00:00</td>
      <td>2021-02-01 00:00:00+00:00</td>
      <td>2021-02-06 12:30:00+00:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>item3</td>
      <td>2021-01-03 00:00:00+00:00</td>
      <td>2021-02-02 00:00:00+00:00</td>
      <td>2021-02-07 12:30:00+00:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>item4</td>
      <td>2021-01-04 00:00:00+00:00</td>
      <td>2021-02-03 00:00:00+00:00</td>
      <td>2021-02-08 12:30:00+00:00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>item5</td>
      <td>2021-01-05 00:00:00+00:00</td>
      <td>2021-02-04 00:00:00+00:00</td>
      <td>2021-02-09 12:30:00+00:00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>item6</td>
      <td>2021-01-06 00:00:00+00:00</td>
      <td>2021-02-05 00:00:00+00:00</td>
      <td>2021-02-10 12:30:00+00:00</td>
    </tr>
  </tbody>
</table>
</div>



Time zone conversion from UTC to local time zone of Los Angeles.


```python
df.iloc[:,1:] = df.iloc[: ,1:].apply(
            lambda x: x.dt.tz_convert('US/Pacific')
                )
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>item</th>
      <th>purchase_dt</th>
      <th>expiration_dt</th>
      <th>extended_dt</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>item1</td>
      <td>2020-12-31 16:00:00-08:00</td>
      <td>2021-01-30 16:00:00-08:00</td>
      <td>2021-02-05 04:30:00-08:00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>item2</td>
      <td>2021-01-01 16:00:00-08:00</td>
      <td>2021-01-31 16:00:00-08:00</td>
      <td>2021-02-06 04:30:00-08:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>item3</td>
      <td>2021-01-02 16:00:00-08:00</td>
      <td>2021-02-01 16:00:00-08:00</td>
      <td>2021-02-07 04:30:00-08:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>item4</td>
      <td>2021-01-03 16:00:00-08:00</td>
      <td>2021-02-02 16:00:00-08:00</td>
      <td>2021-02-08 04:30:00-08:00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>item5</td>
      <td>2021-01-04 16:00:00-08:00</td>
      <td>2021-02-03 16:00:00-08:00</td>
      <td>2021-02-09 04:30:00-08:00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>item6</td>
      <td>2021-01-05 16:00:00-08:00</td>
      <td>2021-02-04 16:00:00-08:00</td>
      <td>2021-02-10 04:30:00-08:00</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['exp_ext_diff'] = (
         df['extended_dt'] - df['expiration_dt']
        )
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>item</th>
      <th>purchase_dt</th>
      <th>expiration_dt</th>
      <th>extended_dt</th>
      <th>exp_ext_diff</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>item1</td>
      <td>2020-12-31 16:00:00-08:00</td>
      <td>2021-01-30 16:00:00-08:00</td>
      <td>2021-02-05 04:30:00-08:00</td>
      <td>5 days 12:30:00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>item2</td>
      <td>2021-01-01 16:00:00-08:00</td>
      <td>2021-01-31 16:00:00-08:00</td>
      <td>2021-02-06 04:30:00-08:00</td>
      <td>5 days 12:30:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>item3</td>
      <td>2021-01-02 16:00:00-08:00</td>
      <td>2021-02-01 16:00:00-08:00</td>
      <td>2021-02-07 04:30:00-08:00</td>
      <td>5 days 12:30:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>item4</td>
      <td>2021-01-03 16:00:00-08:00</td>
      <td>2021-02-02 16:00:00-08:00</td>
      <td>2021-02-08 04:30:00-08:00</td>
      <td>5 days 12:30:00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>item5</td>
      <td>2021-01-04 16:00:00-08:00</td>
      <td>2021-02-03 16:00:00-08:00</td>
      <td>2021-02-09 04:30:00-08:00</td>
      <td>5 days 12:30:00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>item6</td>
      <td>2021-01-05 16:00:00-08:00</td>
      <td>2021-02-04 16:00:00-08:00</td>
      <td>2021-02-10 04:30:00-08:00</td>
      <td>5 days 12:30:00</td>
    </tr>
  </tbody>
</table>
</div>



##### E.4.1 Time Delta

pandas.Timedelta is used for capturing the difference between two date or time objects. <br>
pandas.Timedelta and datetime.timedelta are equivalent.


```python
week_td = pd.Timedelta('1W')
print(pd.to_datetime('1 JAN 2022') + week_td)
print(pd.to_datetime('1 JAN 2022') + 2*week_td)
```

    2022-01-08 00:00:00
    2022-01-15 00:00:00



```python
df = pd.DataFrame(
    {
        'item': ['item1', 'item2', 'item3', 'item4', 'item5', 'item6'],
        'purchase_dt': pd.date_range('2021-01-01', periods=6, freq='D', tz='UTC')
    }
)
```


```python
df['1 week'] = pd.Timedelta('1W')
df['1 week_more'] = df['purchase_dt'] + df['1 week']
df['1_week_less'] = df['purchase_dt'] - df['1 week']
```


```python
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>item</th>
      <th>purchase_dt</th>
      <th>1 week</th>
      <th>1 week_more</th>
      <th>1_week_less</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>item1</td>
      <td>2021-01-01 00:00:00+00:00</td>
      <td>7 days</td>
      <td>2021-01-08 00:00:00+00:00</td>
      <td>2020-12-25 00:00:00+00:00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>item2</td>
      <td>2021-01-02 00:00:00+00:00</td>
      <td>7 days</td>
      <td>2021-01-09 00:00:00+00:00</td>
      <td>2020-12-26 00:00:00+00:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>item3</td>
      <td>2021-01-03 00:00:00+00:00</td>
      <td>7 days</td>
      <td>2021-01-10 00:00:00+00:00</td>
      <td>2020-12-27 00:00:00+00:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>item4</td>
      <td>2021-01-04 00:00:00+00:00</td>
      <td>7 days</td>
      <td>2021-01-11 00:00:00+00:00</td>
      <td>2020-12-28 00:00:00+00:00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>item5</td>
      <td>2021-01-05 00:00:00+00:00</td>
      <td>7 days</td>
      <td>2021-01-12 00:00:00+00:00</td>
      <td>2020-12-29 00:00:00+00:00</td>
    </tr>
    <tr>
      <th>5</th>
      <td>item6</td>
      <td>2021-01-06 00:00:00+00:00</td>
      <td>7 days</td>
      <td>2021-01-13 00:00:00+00:00</td>
      <td>2020-12-30 00:00:00+00:00</td>
    </tr>
  </tbody>
</table>
</div>




```python
pd.timedelta_range('1W 2 days', periods=5)
```




    TimedeltaIndex(['9 days', '10 days', '11 days', '12 days', '13 days'], dtype='timedelta64[ns]', freq='D')




```python
df = pd.DataFrame(
        {       
        'item': ['item1', 'item2', 'item3', 'item4', 'item5'],
        'purchase_dt': pd.date_range('2021-01-01', periods=5, freq='D', tz='UTC'),
        'time_deltas': pd.timedelta_range('1W 2 days 6 hours', periods=5)
        }

)
```


```python
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>item</th>
      <th>purchase_dt</th>
      <th>time_deltas</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>item1</td>
      <td>2021-01-01 00:00:00+00:00</td>
      <td>9 days 06:00:00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>item2</td>
      <td>2021-01-02 00:00:00+00:00</td>
      <td>10 days 06:00:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>item3</td>
      <td>2021-01-03 00:00:00+00:00</td>
      <td>11 days 06:00:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>item4</td>
      <td>2021-01-04 00:00:00+00:00</td>
      <td>12 days 06:00:00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>item5</td>
      <td>2021-01-05 00:00:00+00:00</td>
      <td>13 days 06:00:00</td>
    </tr>
  </tbody>
</table>
</div>



#### E.5 Converting Datetime with TimeZone information 

Time-series data requires attention to different time zones, when developing data pipelines, building a data warehouse, or integrating data between systems, dealing with time zones requires attention

There are multiple packages in python to deal with time zone: <br>
pytz <br>
dateutil <br>
zoneinfo <br>


```python
df = pd.DataFrame(
        {       
        'Location': ['Los Angeles', 
                     'New York',
                     'Berlin', 
                     'New Delhi', 
                     'Moscow', 
                     'Tokyo', 
                     'Dubai'],
        'tz': ['US/Pacific', 
               'US/Eastern', 
               'Europe/Berlin', 
               'Asia/Kolkata', 
               'Europe/Moscow', 
               'Asia/Tokyo',
               'Asia/Dubai'],
        'visit_dt': pd.date_range(start='22:00',periods=7, freq='45min'),
        }).set_index('visit_dt')
```


```python
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Location</th>
      <th>tz</th>
    </tr>
    <tr>
      <th>visit_dt</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2022-09-04 22:00:00</th>
      <td>Los Angeles</td>
      <td>US/Pacific</td>
    </tr>
    <tr>
      <th>2022-09-04 22:45:00</th>
      <td>New York</td>
      <td>US/Eastern</td>
    </tr>
    <tr>
      <th>2022-09-04 23:30:00</th>
      <td>Berlin</td>
      <td>Europe/Berlin</td>
    </tr>
    <tr>
      <th>2022-09-05 00:15:00</th>
      <td>New Delhi</td>
      <td>Asia/Kolkata</td>
    </tr>
    <tr>
      <th>2022-09-05 01:00:00</th>
      <td>Moscow</td>
      <td>Europe/Moscow</td>
    </tr>
    <tr>
      <th>2022-09-05 01:45:00</th>
      <td>Tokyo</td>
      <td>Asia/Tokyo</td>
    </tr>
    <tr>
      <th>2022-09-05 02:30:00</th>
      <td>Dubai</td>
      <td>Asia/Dubai</td>
    </tr>
  </tbody>
</table>
</div>



To convert this DataFrame to same time zone as in Tokyo. We can use DataFrame.tz_convert() against the DataFrame, but will get TypeError exception if you do this. <br>
That is because your time-series DataFrame is not time zone-aware. <br>
So, you need to localize it first using tz_localize() to make it time-zone aware. 


```python
df = df.tz_localize('UTC')
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Location</th>
      <th>tz</th>
    </tr>
    <tr>
      <th>visit_dt</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2022-09-04 22:00:00+00:00</th>
      <td>Los Angeles</td>
      <td>US/Pacific</td>
    </tr>
    <tr>
      <th>2022-09-04 22:45:00+00:00</th>
      <td>New York</td>
      <td>US/Eastern</td>
    </tr>
    <tr>
      <th>2022-09-04 23:30:00+00:00</th>
      <td>Berlin</td>
      <td>Europe/Berlin</td>
    </tr>
    <tr>
      <th>2022-09-05 00:15:00+00:00</th>
      <td>New Delhi</td>
      <td>Asia/Kolkata</td>
    </tr>
    <tr>
      <th>2022-09-05 01:00:00+00:00</th>
      <td>Moscow</td>
      <td>Europe/Moscow</td>
    </tr>
    <tr>
      <th>2022-09-05 01:45:00+00:00</th>
      <td>Tokyo</td>
      <td>Asia/Tokyo</td>
    </tr>
    <tr>
      <th>2022-09-05 02:30:00+00:00</th>
      <td>Dubai</td>
      <td>Asia/Dubai</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_hq = df.tz_convert('Asia/Tokyo')
df_hq
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Location</th>
      <th>tz</th>
    </tr>
    <tr>
      <th>visit_dt</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2022-09-05 07:00:00+09:00</th>
      <td>Los Angeles</td>
      <td>US/Pacific</td>
    </tr>
    <tr>
      <th>2022-09-05 07:45:00+09:00</th>
      <td>New York</td>
      <td>US/Eastern</td>
    </tr>
    <tr>
      <th>2022-09-05 08:30:00+09:00</th>
      <td>Berlin</td>
      <td>Europe/Berlin</td>
    </tr>
    <tr>
      <th>2022-09-05 09:15:00+09:00</th>
      <td>New Delhi</td>
      <td>Asia/Kolkata</td>
    </tr>
    <tr>
      <th>2022-09-05 10:00:00+09:00</th>
      <td>Moscow</td>
      <td>Europe/Moscow</td>
    </tr>
    <tr>
      <th>2022-09-05 10:45:00+09:00</th>
      <td>Tokyo</td>
      <td>Asia/Tokyo</td>
    </tr>
    <tr>
      <th>2022-09-05 11:30:00+09:00</th>
      <td>Dubai</td>
      <td>Asia/Dubai</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['local_dt'] = df.index
df['local_dt'] = df.apply(lambda x: pd.Timestamp.tz_convert(x['local_dt'], x['tz']), axis=1)
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Location</th>
      <th>tz</th>
      <th>local_dt</th>
    </tr>
    <tr>
      <th>visit_dt</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2022-09-04 22:00:00+00:00</th>
      <td>Los Angeles</td>
      <td>US/Pacific</td>
      <td>2022-09-04 15:00:00-07:00</td>
    </tr>
    <tr>
      <th>2022-09-04 22:45:00+00:00</th>
      <td>New York</td>
      <td>US/Eastern</td>
      <td>2022-09-04 18:45:00-04:00</td>
    </tr>
    <tr>
      <th>2022-09-04 23:30:00+00:00</th>
      <td>Berlin</td>
      <td>Europe/Berlin</td>
      <td>2022-09-05 01:30:00+02:00</td>
    </tr>
    <tr>
      <th>2022-09-05 00:15:00+00:00</th>
      <td>New Delhi</td>
      <td>Asia/Kolkata</td>
      <td>2022-09-05 05:45:00+05:30</td>
    </tr>
    <tr>
      <th>2022-09-05 01:00:00+00:00</th>
      <td>Moscow</td>
      <td>Europe/Moscow</td>
      <td>2022-09-05 04:00:00+03:00</td>
    </tr>
    <tr>
      <th>2022-09-05 01:45:00+00:00</th>
      <td>Tokyo</td>
      <td>Asia/Tokyo</td>
      <td>2022-09-05 10:45:00+09:00</td>
    </tr>
    <tr>
      <th>2022-09-05 02:30:00+00:00</th>
      <td>Dubai</td>
      <td>Asia/Dubai</td>
      <td>2022-09-05 06:30:00+04:00</td>
    </tr>
  </tbody>
</table>
</div>



It is important to standardize on UTC if you are dealing with different time zones and daylight saving, since UTC is always consistent and never changes (regardless of where you are or if daylight saving time is applied or not). <br>
Once in UTC, converting to other time zones is very straightforward.


```python
df = pd.DataFrame(
        {       
        'Location': ['Los Angeles', 
                     'New York',
                     'Berlin', 
                     'New Delhi', 
                     'Moscow', 
                     'Tokyo', 
                     'Dubai'],
        'tz': ['US/Pacific', 
               'US/Eastern', 
               'Europe/Berlin', 
               'Asia/Kolkata', 
               'Europe/Moscow', 
               'Asia/Tokyo',
               'Asia/Dubai'],
        'visit_dt': pd.date_range(start='22:00',periods=7, freq='45min'),
        }).set_index('visit_dt').tz_localize('UTC').tz_convert('Asia/Tokyo')
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Location</th>
      <th>tz</th>
    </tr>
    <tr>
      <th>visit_dt</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2022-09-05 07:00:00+09:00</th>
      <td>Los Angeles</td>
      <td>US/Pacific</td>
    </tr>
    <tr>
      <th>2022-09-05 07:45:00+09:00</th>
      <td>New York</td>
      <td>US/Eastern</td>
    </tr>
    <tr>
      <th>2022-09-05 08:30:00+09:00</th>
      <td>Berlin</td>
      <td>Europe/Berlin</td>
    </tr>
    <tr>
      <th>2022-09-05 09:15:00+09:00</th>
      <td>New Delhi</td>
      <td>Asia/Kolkata</td>
    </tr>
    <tr>
      <th>2022-09-05 10:00:00+09:00</th>
      <td>Moscow</td>
      <td>Europe/Moscow</td>
    </tr>
    <tr>
      <th>2022-09-05 10:45:00+09:00</th>
      <td>Tokyo</td>
      <td>Asia/Tokyo</td>
    </tr>
    <tr>
      <th>2022-09-05 11:30:00+09:00</th>
      <td>Dubai</td>
      <td>Asia/Dubai</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.index = df.index.strftime('%Y-%m-%d %H:%M %p') # to format datetime to morning AM or evening PM
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Location</th>
      <th>tz</th>
    </tr>
    <tr>
      <th>visit_dt</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2022-09-05 07:00 AM</th>
      <td>Los Angeles</td>
      <td>US/Pacific</td>
    </tr>
    <tr>
      <th>2022-09-05 07:45 AM</th>
      <td>New York</td>
      <td>US/Eastern</td>
    </tr>
    <tr>
      <th>2022-09-05 08:30 AM</th>
      <td>Berlin</td>
      <td>Europe/Berlin</td>
    </tr>
    <tr>
      <th>2022-09-05 09:15 AM</th>
      <td>New Delhi</td>
      <td>Asia/Kolkata</td>
    </tr>
    <tr>
      <th>2022-09-05 10:00 AM</th>
      <td>Moscow</td>
      <td>Europe/Moscow</td>
    </tr>
    <tr>
      <th>2022-09-05 10:45 AM</th>
      <td>Tokyo</td>
      <td>Asia/Tokyo</td>
    </tr>
    <tr>
      <th>2022-09-05 11:30 AM</th>
      <td>Dubai</td>
      <td>Asia/Dubai</td>
    </tr>
  </tbody>
</table>
</div>



#### E.6 Working with date offsets

Offsets are useful in transforming dates into something more meaningful and relatable.


```python
np.random.seed(10)
df = pd.DataFrame(
        {       
        'purchase_dt': pd.date_range('2021-01-01', periods=6, freq='D'),
        'production' : np.random.randint(4, 20, 6)
        }).set_index('purchase_dt')
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>production</th>
    </tr>
    <tr>
      <th>purchase_dt</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-01-01</th>
      <td>13</td>
    </tr>
    <tr>
      <th>2021-01-02</th>
      <td>17</td>
    </tr>
    <tr>
      <th>2021-01-03</th>
      <td>8</td>
    </tr>
    <tr>
      <th>2021-01-04</th>
      <td>19</td>
    </tr>
    <tr>
      <th>2021-01-05</th>
      <td>4</td>
    </tr>
    <tr>
      <th>2021-01-06</th>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['day'] = df.index.day_name() # While getting the day name is useful, better would be to know working days information.
```

pandas.offsets.BDay() - can be used to get weekend/holiday information


```python
df['BusinessDay'] = df.index + pd.offsets.BDay(0) #to populate the last business day
df['BDay Name'] = df['BusinessDay'].dt.day_name() 
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>production</th>
      <th>day</th>
      <th>BusinessDay</th>
      <th>BDay Name</th>
    </tr>
    <tr>
      <th>purchase_dt</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-01-01</th>
      <td>13</td>
      <td>Friday</td>
      <td>2021-01-01</td>
      <td>Friday</td>
    </tr>
    <tr>
      <th>2021-01-02</th>
      <td>17</td>
      <td>Saturday</td>
      <td>2021-01-04</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>2021-01-03</th>
      <td>8</td>
      <td>Sunday</td>
      <td>2021-01-04</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>2021-01-04</th>
      <td>19</td>
      <td>Monday</td>
      <td>2021-01-04</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>2021-01-05</th>
      <td>4</td>
      <td>Tuesday</td>
      <td>2021-01-05</td>
      <td>Tuesday</td>
    </tr>
    <tr>
      <th>2021-01-06</th>
      <td>5</td>
      <td>Wednesday</td>
      <td>2021-01-06</td>
      <td>Wednesday</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.reset_index().groupby(['purchase_dt', 'day']).sum()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>production</th>
    </tr>
    <tr>
      <th>purchase_dt</th>
      <th>day</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-01-01</th>
      <th>Friday</th>
      <td>13</td>
    </tr>
    <tr>
      <th>2021-01-02</th>
      <th>Saturday</th>
      <td>17</td>
    </tr>
    <tr>
      <th>2021-01-03</th>
      <th>Sunday</th>
      <td>8</td>
    </tr>
    <tr>
      <th>2021-01-04</th>
      <th>Monday</th>
      <td>19</td>
    </tr>
    <tr>
      <th>2021-01-05</th>
      <th>Tuesday</th>
      <td>4</td>
    </tr>
    <tr>
      <th>2021-01-06</th>
      <th>Wednesday</th>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.groupby(['BusinessDay', 'BDay Name']).sum() #Monday seems to be the most productive day
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>production</th>
    </tr>
    <tr>
      <th>BusinessDay</th>
      <th>BDay Name</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-01-01</th>
      <th>Friday</th>
      <td>13</td>
    </tr>
    <tr>
      <th>2021-01-04</th>
      <th>Monday</th>
      <td>44</td>
    </tr>
    <tr>
      <th>2021-01-05</th>
      <th>Tuesday</th>
      <td>4</td>
    </tr>
    <tr>
      <th>2021-01-06</th>
      <th>Wednesday</th>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>



To track production monthly (MonthEnd) and quarterly (QuarterEnd). We can use pandas.offsets


```python
df['QuarterEnd'] = df.index + pd.offsets.QuarterEnd(0)
df['MonthEnd'] = df.index + pd.offsets.MonthEnd(0)
df['BusinessDay'] = df.index + pd.offsets.BDay(0)
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>production</th>
      <th>day</th>
      <th>BusinessDay</th>
      <th>BDay Name</th>
      <th>QuarterEnd</th>
      <th>MonthEnd</th>
    </tr>
    <tr>
      <th>purchase_dt</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-01-01</th>
      <td>13</td>
      <td>Friday</td>
      <td>2021-01-01</td>
      <td>Friday</td>
      <td>2021-03-31</td>
      <td>2021-01-31</td>
    </tr>
    <tr>
      <th>2021-01-02</th>
      <td>17</td>
      <td>Saturday</td>
      <td>2021-01-04</td>
      <td>Monday</td>
      <td>2021-03-31</td>
      <td>2021-01-31</td>
    </tr>
    <tr>
      <th>2021-01-03</th>
      <td>8</td>
      <td>Sunday</td>
      <td>2021-01-04</td>
      <td>Monday</td>
      <td>2021-03-31</td>
      <td>2021-01-31</td>
    </tr>
    <tr>
      <th>2021-01-04</th>
      <td>19</td>
      <td>Monday</td>
      <td>2021-01-04</td>
      <td>Monday</td>
      <td>2021-03-31</td>
      <td>2021-01-31</td>
    </tr>
    <tr>
      <th>2021-01-05</th>
      <td>4</td>
      <td>Tuesday</td>
      <td>2021-01-05</td>
      <td>Tuesday</td>
      <td>2021-03-31</td>
      <td>2021-01-31</td>
    </tr>
    <tr>
      <th>2021-01-06</th>
      <td>5</td>
      <td>Wednesday</td>
      <td>2021-01-06</td>
      <td>Wednesday</td>
      <td>2021-03-31</td>
      <td>2021-01-31</td>
    </tr>
  </tbody>
</table>
</div>



Date offsets made it possible to increment, decrement, and transform your dates to a new date range following specific rules. There are several offsets provided by pandas, each with its own rules, which can be applied to your dataset. Here is a list of the common offsets available in pandas:


    BusinessDay or Bday
    MonthEnd
    BusinessMonthEnd or BmonthEnd
    CustomBusinessDay or Cday
    QuarterEnd
    FY253Quarter


BusinessDay (BDay) offset that it did not account for the New Year's Day holiday (January 1). So, what can be done to account for both the New Year's Day holiday and weekends? 

To accomplish this, pandas provides two approaches to handle standard holidays. The first is by defining a custom holiday. <br> The second approach uses an existing holiday offset.


```python
from pandas.tseries.holiday import (
    USFederalHolidayCalendar
)

df = pd.DataFrame(
        {       
        'purchase_dt': pd.date_range('2021-01-01', periods=6, freq='D'),
        'production' : np.random.randint(4, 20, 6)
        }).set_index('purchase_dt')

df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>production</th>
    </tr>
    <tr>
      <th>purchase_dt</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-01-01</th>
      <td>15</td>
    </tr>
    <tr>
      <th>2021-01-02</th>
      <td>16</td>
    </tr>
    <tr>
      <th>2021-01-03</th>
      <td>13</td>
    </tr>
    <tr>
      <th>2021-01-04</th>
      <td>17</td>
    </tr>
    <tr>
      <th>2021-01-05</th>
      <td>4</td>
    </tr>
    <tr>
      <th>2021-01-06</th>
      <td>17</td>
    </tr>
  </tbody>
</table>
</div>



First : Existing offset.<br> For this example, dealing with New Year, you can use the USFederalHolidayCalendar class, which has standard holidays such as New Year, Christmas, and other holidays specific to the United States.



```python
USFederalHolidayCalendar.rules
```




    [Holiday: New Years Day (month=1, day=1, observance=<function nearest_workday at 0x7faac9c1e700>),
     Holiday: Martin Luther King Jr. Day (month=1, day=1, offset=<DateOffset: weekday=MO(+3)>),
     Holiday: Presidents Day (month=2, day=1, offset=<DateOffset: weekday=MO(+3)>),
     Holiday: Memorial Day (month=5, day=31, offset=<DateOffset: weekday=MO(-1)>),
     Holiday: July 4th (month=7, day=4, observance=<function nearest_workday at 0x7faac9c1e700>),
     Holiday: Labor Day (month=9, day=1, offset=<DateOffset: weekday=MO(+1)>),
     Holiday: Columbus Day (month=10, day=1, offset=<DateOffset: weekday=MO(+2)>),
     Holiday: Veterans Day (month=11, day=11, observance=<function nearest_workday at 0x7faac9c1e700>),
     Holiday: Thanksgiving (month=11, day=1, offset=<DateOffset: weekday=TH(+4)>),
     Holiday: Christmas (month=12, day=25, observance=<function nearest_workday at 0x7faac9c1e700>)]




```python
df['USFederalHolidays'] = df.index + pd.offsets.CDay(calendar=USFederalHolidayCalendar())
df
```

    /Users/rahuladlakha/opt/anaconda3/lib/python3.9/site-packages/pandas/core/arrays/datetimes.py:741: PerformanceWarning: Non-vectorized DateOffset being applied to Series or DatetimeIndex
      warnings.warn(





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>production</th>
      <th>USFederalHolidays</th>
    </tr>
    <tr>
      <th>purchase_dt</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-01-01</th>
      <td>15</td>
      <td>2021-01-04</td>
    </tr>
    <tr>
      <th>2021-01-02</th>
      <td>16</td>
      <td>2021-01-04</td>
    </tr>
    <tr>
      <th>2021-01-03</th>
      <td>13</td>
      <td>2021-01-04</td>
    </tr>
    <tr>
      <th>2021-01-04</th>
      <td>17</td>
      <td>2021-01-05</td>
    </tr>
    <tr>
      <th>2021-01-05</th>
      <td>4</td>
      <td>2021-01-06</td>
    </tr>
    <tr>
      <th>2021-01-06</th>
      <td>17</td>
      <td>2021-01-07</td>
    </tr>
  </tbody>
</table>
</div>




```python
from pandas.tseries.holiday import (
			Holiday,
			nearest_workday,
			USFederalHolidayCalendar)

newyears = Holiday("New Years", month=1, day=1, observance=nearest_workday)
newyears
```




    Holiday: New Years (month=1, day=1, observance=<function nearest_workday at 0x7faac9c1e700>)




```python
from calendar import calendar


df['NewYearsHoliday'] = df.index+pd.offsets.CDay(calendar=newyears)
df
```

    /Users/rahuladlakha/opt/anaconda3/lib/python3.9/site-packages/pandas/core/arrays/datetimes.py:741: PerformanceWarning: Non-vectorized DateOffset being applied to Series or DatetimeIndex
      warnings.warn(





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>production</th>
      <th>USFederalHolidays</th>
      <th>NewYearsHoliday</th>
    </tr>
    <tr>
      <th>purchase_dt</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-01-01</th>
      <td>15</td>
      <td>2021-01-04</td>
      <td>2021-01-04</td>
    </tr>
    <tr>
      <th>2021-01-02</th>
      <td>16</td>
      <td>2021-01-04</td>
      <td>2021-01-04</td>
    </tr>
    <tr>
      <th>2021-01-03</th>
      <td>13</td>
      <td>2021-01-04</td>
      <td>2021-01-04</td>
    </tr>
    <tr>
      <th>2021-01-04</th>
      <td>17</td>
      <td>2021-01-05</td>
      <td>2021-01-05</td>
    </tr>
    <tr>
      <th>2021-01-05</th>
      <td>4</td>
      <td>2021-01-06</td>
      <td>2021-01-06</td>
    </tr>
    <tr>
      <th>2021-01-06</th>
      <td>17</td>
      <td>2021-01-07</td>
      <td>2021-01-07</td>
    </tr>
  </tbody>
</table>
</div>



#### E.6 Custom business days


```python
dubai_uae_workdays = "Sun Mon Tue Wed Thu"

# UAE national day
nationalDay = [pd.to_datetime('2021-12-2')]


dubai_uae_bday = pd.offsets.CDay(
    holidays=nationalDay,
    weekmask=dubai_uae_workdays,
)
```


```python
df = pd.DataFrame({'Date': pd.date_range('12-1-2021', periods=10, freq=dubai_uae_bday )})
df['Day_name'] = df.Date.dt.day_name()
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Day_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2021-12-01</td>
      <td>Wednesday</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2021-12-05</td>
      <td>Sunday</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2021-12-06</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2021-12-07</td>
      <td>Tuesday</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2021-12-08</td>
      <td>Wednesday</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2021-12-09</td>
      <td>Thursday</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2021-12-12</td>
      <td>Sunday</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2021-12-13</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2021-12-14</td>
      <td>Tuesday</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2021-12-15</td>
      <td>Wednesday</td>
    </tr>
  </tbody>
</table>
</div>



Working with date offsets but focuses on customizing offsets. pandas provides several offsets that can take a custom calendar, holiday, and weekmask. 

These include the following:

    CustomBusinessDay or Cday
    CustomBusinessMonthEnd or CBMonthEnd
    CustomBusinessMonthBegin or CBMonthBegin
    CustomBusinessHour

#### E.7 Custom Business Day hours


```python
b_hours = pd.offsets.BusinessHour()
b_hours
```




    <BusinessHour: BH=09:00-17:00>




```python
cust_hours = pd.offsets.CustomBusinessHour(
    start="8:30",
    end="15:30",
    holidays=nationalDay,
    weekmask=dubai_uae_workdays)
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Day_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2021-12-01</td>
      <td>Wednesday</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2021-12-05</td>
      <td>Sunday</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2021-12-06</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2021-12-07</td>
      <td>Tuesday</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2021-12-08</td>
      <td>Wednesday</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2021-12-09</td>
      <td>Thursday</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2021-12-12</td>
      <td>Sunday</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2021-12-13</td>
      <td>Monday</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2021-12-14</td>
      <td>Tuesday</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2021-12-15</td>
      <td>Wednesday</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['Date'] + cust_hours * 16
```

    /Users/rahuladlakha/opt/anaconda3/lib/python3.9/site-packages/pandas/core/arrays/datetimes.py:741: PerformanceWarning: Non-vectorized DateOffset being applied to Series or DatetimeIndex
      warnings.warn(





    0   2021-12-06 10:30:00
    1   2021-12-07 10:30:00
    2   2021-12-08 10:30:00
    3   2021-12-09 10:30:00
    4   2021-12-12 10:30:00
    5   2021-12-13 10:30:00
    6   2021-12-14 10:30:00
    7   2021-12-15 10:30:00
    8   2021-12-16 10:30:00
    9   2021-12-19 10:30:00
    Name: Date, dtype: datetime64[ns]



### F. Handling Missing Data

Missing data and outliers are two common problems that need to be dealt with during data cleaning and preparation. <br>
Time series data is no different, and before plugging the data into any analysis or modeling workflow, you must investigate the data first.<br>We will explore techniques to handle missing data through imputation and interpolation.


Steps to be followed for handling missing data: ingest the data into a DataFrame, **identify missing data, impute missing data, evaluate it against the original data**, and finally, visualize **and compare the different imputation techniques**.



```python
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle
import matplotlib.dates as mdates

import matplotlib
import sklearn
import statsmodels as sm
```


```python
def read_dataset(folder, file, date_col=None):
    '''
    Reading the dataset
    folder: is a Path object
    file: the CSV filename in that Path object. 
    date_col: specify a date_col to use as index_col 
    
    returns: a pandas DataFrame with a DatetimeIndex
    '''
    df = pd.read_csv(folder / file, 
                     index_col=date_col, 
                     parse_dates=[date_col])
    return df
```

We will be ploting two DataFrames: one original with no missing data and other with missing / imputed dataframe to compare against.<br>
The function creates multiple time series subplots using the specified response column (col). We will be plotting each imputation technique for visual comparison.


```python
def plot_dfs(df1, df2, col, title=None, xlabel=None, ylabel=None):

    '''	df1: original dataframe without missing data
    df2: dataframe with missing data
    col: column name that contains missing data'''  

    df_missing = df2.rename(columns={col: 'missing'})

    columns = df_missing.loc[:, 'missing':].columns.tolist()
    subplot_size = len(columns)
    fig, ax = plt.subplots(subplot_size+1, 1, sharex=True)
    plt.subplots_adjust(hspace=0.25)
    fig.suptitle = title
    df1[col].plot(ax=ax[0], figsize=(10, 12))
    ax[0].set_title('Original Dataset')
    ax[0].set_xlabel(xlabel)
    ax[0].set_ylabel(ylabel)

    for i, colname in enumerate(columns):
        df_missing[colname].plot(ax=ax[i+1])
        ax[i+1].set_title(colname.upper())
    plt.show()

```

RMSE Score - In addition to compare the the non-missing DataFrame with imputed DataFrame we will need statistical measure.


```python
def rmse_score(df1, df2, col=None):
    '''
    df1: original dataframe without missing data
    df2: dataframe with missing data
    col: column name that contains missing data

    returns: a list of scores
    '''
    df_missing = df2.rename(columns={col: 'missing'})
    columns = df_missing.loc[:, 'missing':].columns.tolist()
    scores = []
    dict = {}
    for comp_col in columns[1:]:
        rmse = np.sqrt(np.mean((df1[col] - df_missing[comp_col])**2))
        scores.append(rmse)
        print(f'RMSE for {comp_col}: {rmse}')
        min_rmse = min(scores)
        dict[comp_col] = [rmse]
        func = min(dict, key=dict.get)
    print(f'Mininum RMSE belongs to {func} is {min_rmse}')
    
    return scores
```

Missing data is inevitable. It is important to formulate right strategy to deal with missing data. <br>

**One approach** could be to remove the missing data, drop the observations. This may not be the best strategy if you have limited data in first place. And if done prematurely then the drawback could be, you'll never know if the missing data was due to censoring or due to bias.

**Second approach** could be to tag the rows with missing data by adding a column, describing or labeling the missing data.

**Third approach** could be to estimate the missing data values. The method could range from simple to more complex techniques leveraging ML and stat models.

One of the most important piece is to measure the accuracy of the estimated values for data missing in the first place.<br>
There are different approaches, emphasizing a through evaluation and validation process to select ideal way for each situation.

**RMSE - Root Mean Squared Error** - To evaluate different imputation techniques

The process to calculate the RMSE can be broken down into a few simple steps: first, computing the error, which is the difference between the actual values and the predicted or estimated values. 
This is done for each observation. <br> Since the errors may be either negative or positive, and to avoid having a zero summation, the errors (differences) are squared. <br>
Finally, all the errors are summed and divided by the total number of observations to compute the mean. This gives you the **Mean Squared Error (MSE)**. RMSE is just the square root of the MSE.

$RMSE$ = $\huge\sqrt{\frac{\sum_{i=1}^{N}(x-\widehat{x_i})^2}{N}}$

$x$ - original value; <br>
$\widehat{x_i}$ - imputed value <br>
$N$ - Number of observations



RMSE is commonly used to measure the performance of predictive models.<br>
A lower RMSE is desirable; it tells us that the model can fit the dataset. It tells us tht the average distance (error) between the predicted value and the actual value. We want this distance minimized for the a well fit model.

When comparing different imputation methods, we want our imputed values to resemble (as close as possible) to the actual data, which contains random effects. This means we are not seeking a perfect prediction and thus a lower RMSE score does not necessarily indicate a better imputation method. <br><br>
RMSE compared with visualization of original and imputation to understand different techniques compare and work.


```python
chapter='ch7/'
```


```python
filename='/co2_missing.csv'
```


```python
filename1='/clicks_missing.csv'
```


```python
folder1 = Path(_base_location+chapter+filename1)
```


```python
folder = Path(_base_location+chapter+filename)
```


```python
co2_df = pd.read_csv(folder, parse_dates=['year'])
ecom_df = pd.read_csv(folder1, parse_dates=['date'])
```


```python
ecom_df.head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>price</th>
      <th>location</th>
      <th>clicks</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2008-04-01</td>
      <td>43.155647</td>
      <td>2</td>
      <td>18784.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2008-04-02</td>
      <td>43.079056</td>
      <td>1</td>
      <td>24738.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2008-04-03</td>
      <td>43.842609</td>
      <td>2</td>
      <td>15209.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2008-04-04</td>
      <td>43.312376</td>
      <td>1</td>
      <td>14018.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2008-04-05</td>
      <td>43.941176</td>
      <td>1</td>
      <td>11974.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2008-04-06</td>
      <td>44.403936</td>
      <td>1</td>
      <td>11007.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2008-04-07</td>
      <td>43.995888</td>
      <td>2</td>
      <td>15214.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2008-04-08</td>
      <td>43.373773</td>
      <td>1</td>
      <td>11333.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2008-04-09</td>
      <td>43.320312</td>
      <td>1</td>
      <td>7026.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2008-04-10</td>
      <td>43.154738</td>
      <td>5</td>
      <td>15677.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
co2_df.isna().sum()
```




    year     0
    co2     25
    dtype: int64




```python
ecom_df.isnull().sum()
```




    date         0
    price        0
    location     0
    clicks      16
    dtype: int64




```python
co2_df.describe(include='all', datetime_is_numeric=True)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>co2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>226</td>
      <td>201.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1906-11-27 01:29:12.212389376</td>
      <td>1.590015</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1750-01-01 00:00:00</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1851-04-02 06:00:00</td>
      <td>0.076400</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>1907-07-02 12:00:00</td>
      <td>0.935100</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>1963-10-01 18:00:00</td>
      <td>2.807600</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2020-01-01 00:00:00</td>
      <td>4.907900</td>
    </tr>
    <tr>
      <th>std</th>
      <td>NaN</td>
      <td>1.644182</td>
    </tr>
  </tbody>
</table>
</div>




```python
ecom_df.describe(include='all', datetime_is_numeric=True)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>price</th>
      <th>location</th>
      <th>clicks</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>135</td>
      <td>135.000000</td>
      <td>135.000000</td>
      <td>119.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>2008-06-07 00:00:00</td>
      <td>43.478978</td>
      <td>1.696296</td>
      <td>9530.336134</td>
    </tr>
    <tr>
      <th>min</th>
      <td>2008-04-01 00:00:00</td>
      <td>42.207018</td>
      <td>1.000000</td>
      <td>2044.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2008-05-04 12:00:00</td>
      <td>43.045714</td>
      <td>1.000000</td>
      <td>6438.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2008-06-07 00:00:00</td>
      <td>43.487069</td>
      <td>1.000000</td>
      <td>8391.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2008-07-10 12:00:00</td>
      <td>43.886875</td>
      <td>2.000000</td>
      <td>11363.500000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2008-08-13 00:00:00</td>
      <td>45.801613</td>
      <td>5.000000</td>
      <td>29505.000000</td>
    </tr>
    <tr>
      <th>std</th>
      <td>NaN</td>
      <td>0.608467</td>
      <td>1.114853</td>
      <td>4687.587507</td>
    </tr>
  </tbody>
</table>
</div>




```python
test = pd.read_csv(folder1, parse_dates=['date'], na_values={'?'}) #specifying the na values based on the summary statistics 
```

#### F.1 Handling missing data with imputation using Pandas

There are two approaches to impute missing data:

    univariate imputation
    multivariate imputation

##### F.1.1 Univariate Imputation

In Univariate Imputation, we will use non-missing values in a single variable (think a column or feature) to impute the missing values for that variable. <br>

For Ex. If have a sales column in the dataset with some missing values, you can use a univariate imputation method to impute missing sales observations using average sales


**Basic Univariate Imputation Techniques** <br>
Imputing using the mean<br>
Imputing using the last observation forward (**forward fill - ffill**). This also referred as Last Observed Carried Forward (LOCF) <br>
Imputing using the next observation backward (**backward fill - bfill**). This also referred as Next Observation Carried Backward (NOCB)


```python
folder = Path(_base_location+chapter)
```


```python
co2_original = read_dataset(folder, 'co2_original.csv', 'year')
co2_missing = read_dataset(folder, 'co2_missing_only.csv', 'year')
clicks_original = read_dataset(folder, 'clicks_original.csv', 'date')
clicks_missing = read_dataset(folder, 'clicks_missing.csv', 'date')
```


```python
plot_dfs(co2_original,
         co2_missing,
         'co2',
         title="Annual co2 emission/capita",
         xlabel="Years",
         ylabel="x100 million tons")
```


    
![png](TimeSeries_files/TimeSeries_247_0.png)
    



```python
co2_missing[co2_missing['co2'].isna()]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>co2</th>
    </tr>
    <tr>
      <th>year</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1847-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1848-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1849-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1850-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1851-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1852-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1853-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1854-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1855-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1856-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1927-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1928-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1929-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1930-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1931-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1932-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1933-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1934-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1935-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1936-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1937-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1974-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1975-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1976-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1977-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1978-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1979-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1980-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1981-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1982-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1983-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1984-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1985-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1986-01-01</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1987-01-01</th>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
plot_dfs(clicks_original,
         clicks_missing,
         'clicks',
         title="Page Clicks per Day",
         xlabel="date",
         ylabel="No. of clicks")
```


    
![png](TimeSeries_files/TimeSeries_249_0.png)
    



```python
clicks_missing[clicks_missing['clicks'].isna()]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>price</th>
      <th>location</th>
      <th>clicks</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2008-05-15</th>
      <td>42.517755</td>
      <td>2</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-16</th>
      <td>44.011009</td>
      <td>2</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-17</th>
      <td>42.530303</td>
      <td>2</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-18</th>
      <td>42.565698</td>
      <td>5</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-19</th>
      <td>43.892996</td>
      <td>2</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-20</th>
      <td>43.247706</td>
      <td>2</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-21</th>
      <td>44.106289</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-22</th>
      <td>43.810714</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-23</th>
      <td>43.620802</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-24</th>
      <td>43.854031</td>
      <td>2</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-25</th>
      <td>43.586710</td>
      <td>2</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-26</th>
      <td>43.670659</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-27</th>
      <td>42.850184</td>
      <td>2</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-28</th>
      <td>43.702147</td>
      <td>5</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-29</th>
      <td>43.872321</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2008-05-30</th>
      <td>44.558011</td>
      <td>5</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



Let's perform **imputation** using **fillna() method**, DataFrame.fillna() is the simplest imputation method. The function can be used in two ways depending on which parameter you are using:

The **value parameter**, where you can pass a scalar value (numeric or string) to use to fill for all missing values <br>
The method parameter, which takes specific string values: <br>
**Backward filling: backfill or bfill**, uses the next observation, after the missing spot(s) and fills the gaps backward <br>
**Forward filling: ffill or pad**, uses the last value before the missing spot(s) and fills the gaps forward” <br>



```python
co2_missing['ffill'] = co2_missing['co2'].fillna(method='ffill')
co2_missing['bfill'] = co2_missing['co2'].fillna(method='bfill')
co2_missing['mean'] = co2_missing['co2'].fillna(co2_missing['co2'].mean())
```


```python
rmse_score(co2_original, co2_missing, 'co2')
```

    RMSE for ffill: 0.05873012599267133
    RMSE for bfill: 0.05550012995280968
    RMSE for mean: 0.7156383637041684
    Mininum RMSE belongs to bfill is 0.05550012995280968





    [0.05873012599267133, 0.05550012995280968, 0.7156383637041684]



bfill and ffill produce better results that when using the mean, both techniques have favorable RMSE scores.


```python
plot_dfs(co2_original, co2_missing, 'co2')
```


    
![png](TimeSeries_files/TimeSeries_255_0.png)
    



```python
clicks_missing['ffil'] = clicks_missing['clicks'].fillna(method='ffill')
clicks_missing['bfill'] = clicks_missing['clicks'].fillna(method='bfill')
clicks_missing['mean'] = clicks_missing['clicks'].fillna(clicks_missing['clicks'].mean())
```


```python
_ = rmse_score(clicks_original, 
                    clicks_missing, 
                    'clicks')
```

    RMSE for ffil: 1034.1210689204554
    RMSE for bfill: 2116.6840489225033
    RMSE for mean: 997.7600138929953
    Mininum RMSE belongs to mean is 997.7600138929953



```python
plot_dfs(clicks_original, clicks_missing, 'clicks')
```


    
![png](TimeSeries_files/TimeSeries_258_0.png)
    


#### F.1.2 Handling missing data with univariate imputation using scikit-learn

**SimpleImputer class from scikit-learn** accepts different values for the strategy parameter, including mean, median and most_frequent.


```python
co2_original = read_dataset(folder, 
                            'co2_original.csv', 'year')
co2_missing = read_dataset(folder, 
                           'co2_missing_only.csv', 'year')
clicks_original = read_dataset(folder, 
                               'clicks_original.csv', 'date')
clicks_missing = read_dataset(folder, 
                              'clicks_missing.csv', 'date')
```


```python
from sklearn.impute import SimpleImputer
```


```python
strategy = [
    ('Mean Strategy', 'mean'),
    ('Median Strategy', 'median'),
    ('Most Frequent Strategy', 'most_frequent')]
```

SimpleImputer accepts a Numpy array, so you will need to use the Series.values property followed by the .reshape(-1,1) method to create a 2D NumPy array.


```python
col2_vals = co2_missing['co2'].values.reshape(-1, 1)
click_vals = clicks_missing['clicks'].values.reshape(-1, 1)

for s_name, s in strategy:
    co2_missing[s_name] = (
        SimpleImputer(strategy=s).fit_transform(col2_vals))
    clicks_missing[s_name] = (SimpleImputer(strategy=s).fit_transform(click_vals))
```


```python
_ = rmse_score(co2_original, co2_missing, 'co2')
```

    RMSE for Mean Strategy: 0.7156383637041684
    RMSE for Median Strategy: 0.8029421606859859
    RMSE for Most Frequent Strategy: 1.1245663822743381
    Mininum RMSE belongs to Mean Strategy is 0.7156383637041684



```python
_ = rmse_score(clicks_original, clicks_missing, 'clicks')
```

    RMSE for Mean Strategy: 997.7600138929953
    RMSE for Median Strategy: 959.3580492530756
    RMSE for Most Frequent Strategy: 1097.6425985146868
    Mininum RMSE belongs to Median Strategy is 959.3580492530756



```python
plot_dfs(co2_original, co2_missing, 'co2')
```


    
![png](TimeSeries_files/TimeSeries_268_0.png)
    



```python
plot_dfs(clicks_original, clicks_missing, 'clicks')
```


    
![png](TimeSeries_files/TimeSeries_269_0.png)
    


We used SimpleImputer class to implement three simple strategies to impute missing values: mean, median and most frequent (mode). This is a univariate technique, since we are only using one feature/column to impute/compute mean, median and mode.

SimpleImputer will impute all occurrences of the missing_values, which you can update with pandas.NA, an integer, float, or a string value.

**strategy**, which defaults to mean, and takes string values. <br>
**fill_value** can be used to replace all instances from missing_values with a specific value. This can either be a string or a numeric value. If the Strategy was set to constant, then you will need to provide your custom fill_value.”



```python
avg = co2_missing['co2'].mean()
co2_missing['pands_fillna'] = co2_missing['co2'].fillna(avg)
```


```python
co2_missing
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>co2</th>
      <th>Mean Strategy</th>
      <th>Median Strategy</th>
      <th>Most Frequent Strategy</th>
      <th>pands_fillna</th>
    </tr>
    <tr>
      <th>year</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1750-01-01</th>
      <td>0.0125</td>
      <td>0.0125</td>
      <td>0.0125</td>
      <td>0.0125</td>
      <td>0.0125</td>
    </tr>
    <tr>
      <th>1760-01-01</th>
      <td>0.0128</td>
      <td>0.0128</td>
      <td>0.0128</td>
      <td>0.0128</td>
      <td>0.0128</td>
    </tr>
    <tr>
      <th>1770-01-01</th>
      <td>0.0150</td>
      <td>0.0150</td>
      <td>0.0150</td>
      <td>0.0150</td>
      <td>0.0150</td>
    </tr>
    <tr>
      <th>1780-01-01</th>
      <td>0.0169</td>
      <td>0.0169</td>
      <td>0.0169</td>
      <td>0.0169</td>
      <td>0.0169</td>
    </tr>
    <tr>
      <th>1790-01-01</th>
      <td>0.0206</td>
      <td>0.0206</td>
      <td>0.0206</td>
      <td>0.0206</td>
      <td>0.0206</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2016-01-01</th>
      <td>4.7496</td>
      <td>4.7496</td>
      <td>4.7496</td>
      <td>4.7496</td>
      <td>4.7496</td>
    </tr>
    <tr>
      <th>2017-01-01</th>
      <td>4.7595</td>
      <td>4.7595</td>
      <td>4.7595</td>
      <td>4.7595</td>
      <td>4.7595</td>
    </tr>
    <tr>
      <th>2018-01-01</th>
      <td>4.8022</td>
      <td>4.8022</td>
      <td>4.8022</td>
      <td>4.8022</td>
      <td>4.8022</td>
    </tr>
    <tr>
      <th>2019-01-01</th>
      <td>4.7582</td>
      <td>4.7582</td>
      <td>4.7582</td>
      <td>4.7582</td>
      <td>4.7582</td>
    </tr>
    <tr>
      <th>2020-01-01</th>
      <td>4.4654</td>
      <td>4.4654</td>
      <td>4.4654</td>
      <td>4.4654</td>
      <td>4.4654</td>
    </tr>
  </tbody>
</table>
<p>226 rows × 5 columns</p>
</div>




```python
cols = ['co2', 'Mean Strategy', 'pands_fillna']
_ = rmse_score(co2_original, co2_missing[cols], 'co2')
```

    RMSE for Mean Strategy: 0.7156383637041684
    RMSE for pands_fillna: 0.7156383637041684
    Mininum RMSE belongs to Mean Strategy is 0.7156383637041684


#### F.1.3 Handling missing data with multivariate imputation

In multivariate imputation we use multiple variables within the dataset to impute missing values. Having more variables within the dataset,chime in to improve the predictability of missing values.

We will use scikit-learn IterativeImputer class for multivariate imputation, as we get to pass a regressor to predict the missing values from other variables


```python
from sklearn.experimental import enable_iterative_imputer
from sklearn.impute import IterativeImputer
from sklearn.ensemble import ExtraTreesRegressor, BaggingRegressor
from sklearn.linear_model import ElasticNet, LinearRegression, BayesianRidge
from sklearn.neighbors import KNeighborsRegressor
```

With IterativeImputer we can test different estimators and compare results. <br>
Create a list of regressors to be used in IterativeImputer


```python
estimators = [
             ('bayesianRidge', BayesianRidge()),
             ('extra_trees', ExtraTreesRegressor(n_estimators=10)),
             ('bagging', BaggingRegressor(n_estimators=10)),
             ('elastic_net', ElasticNet()),
             ('linear_regression', LinearRegression()),
             ('knn', KNeighborsRegressor(n_neighbors=3))
             ]
```

Loop through the estimators and train on the dataset using .fit(), and building different models, and finally apply the imputation using .transform() on variable with missing data.


```python
clicks_vals = clicks_missing.iloc[:, 0:3].values
```


```python
for e_name, e in estimators:
    est = IterativeImputer(
                random_state=15,
                estimator=e).fit(clicks_vals)
    clicks_missing[e_name] = est.transform(clicks_vals)[: , 2]
```

    /Users/rahuladlakha/opt/anaconda3/lib/python3.9/site-packages/sklearn/impute/_iterative.py:685: ConvergenceWarning: [IterativeImputer] Early stopping criterion not reached.
      warnings.warn("[IterativeImputer] Early stopping criterion not"
    /Users/rahuladlakha/opt/anaconda3/lib/python3.9/site-packages/sklearn/impute/_iterative.py:685: ConvergenceWarning: [IterativeImputer] Early stopping criterion not reached.
      warnings.warn("[IterativeImputer] Early stopping criterion not"



```python
_ = rmse_score(clicks_original, clicks_missing, 'clicks')
```

    RMSE for Mean Strategy: 997.7600138929953
    RMSE for Median Strategy: 959.3580492530756
    RMSE for Most Frequent Strategy: 1097.6425985146868
    RMSE for bayesianRidge: 949.4393973455852
    RMSE for extra_trees: 1853.2704226332992
    RMSE for bagging: 1435.306256311665
    RMSE for elastic_net: 945.4075209343099
    RMSE for linear_regression: 938.9419831427186
    RMSE for knn: 1336.8798392251822
    Mininum RMSE belongs to linear_regression is 938.9419831427186



```python
plot_dfs(clicks_original, clicks_missing, 'clicks')
```


    
![png](TimeSeries_files/TimeSeries_285_0.png)
    


The RMSE could be misleading because we did not seek the best score (smallest value) since we are not scoring a prediction model but rather an imputation model to fill for missing data. <br>
We may use the data (with imputed values) to build another model for making predictions (forecasting). <br>
Thus, we do not mind some imperfections to better resemble real data. Additionally, since we may not know the true nature of the missing data, the goal is to get a decent estimate

**Multivariate Imputation by Chained Equation** implementation in InterativeImputer class

The basic idea is to treat each variable with missing values as the dependent variable in a regression, with some or all of the remaining variables as its predictors. <br> 
The MICE procedure cycles through these models, fitting each in turn, then uses a procedure called **“predictive mean matching” (PMM)** to generate random draws from the predictive distributions determined by the fitted models. <br>
These random draws become the imputed values for one imputed data set.

By default, each variable with missing variables is modeled using a linear regression with main effects for all other variables in the data set. <br> Note that even when the imputation model is linear, the PMM procedure preserves the domain of each variable. <br> Thus, for example, if all observed values for a given variable are positive, all imputed values for the variable will always be positive. <br> The user also has the option to specify which model is used to produce imputed values for each variable.


```python
from statsmodels.imputation.mice import MICE, MICEData, MICEResults
import statsmodels.api as sm
```


```python
# create a MICEData object
fltr = ['price', 'location','clicks']
mice_data = MICEData(clicks_missing[fltr], 
                     perturbation_method='gaussian')
# 20 iterations
mice_data.update_all(n_iter=20)

mice_data.set_imputer('clicks', formula='~ price + location', model_class=sm.OLS)
```


```python
clicks_missing['MICE']  = mice_data.data['clicks'].values.tolist()
```


```python
_ = rmse_score(clicks_original, clicks_missing, 'clicks')
```

    RMSE for Mean Strategy: 997.7600138929953
    RMSE for Median Strategy: 959.3580492530756
    RMSE for Most Frequent Strategy: 1097.6425985146868
    RMSE for bayesianRidge: 949.4393973455852
    RMSE for extra_trees: 1853.2704226332992
    RMSE for bagging: 1435.306256311665
    RMSE for elastic_net: 945.4075209343099
    RMSE for linear_regression: 938.9419831427186
    RMSE for knn: 1336.8798392251822
    RMSE for MICE: 1768.0716258325265
    Mininum RMSE belongs to linear_regression is 938.9419831427186



```python
cols = ['clicks','bayesianRidge', 'bagging', 'knn', 'MICE']
plot_dfs(clicks_original, clicks_missing[cols], 'clicks')
```


    
![png](TimeSeries_files/TimeSeries_293_0.png)
    



```python
_ = mice_data.plot_imputed_hist('clicks')
```


    
![png](TimeSeries_files/TimeSeries_294_0.png)
    



```python
mice_data = MICEData(clicks_missing[fltr], 
                     perturbation_method='gaussian')
mice_data.update_all(n_iter=20)
_ = mice_data.plot_fit_obs('clicks')
```


    
![png](TimeSeries_files/TimeSeries_295_0.png)
    


Overall, multivariate imputation techniques generally produce better results than univariate methods.<br> This is true when working with more complex time-series datasets in terms of the number of features (columns) and records. <br> Though univariate imputers are more efficient in terms of speed and simplicity to interpret, there is a need to balance complexity, quality, and analytical requirements.

#### F.1.4 Handling missing data with Interpolation

Commonly used technique for imputing missing values is interpolation. The pandas library provides the `DataFrame.interpolate()` method for more complex univariate imputation strategies. <br>
Each interpolation method will have a different mathematical operation to determine how to fill in for the missing data.

**Techniques in Interpolation:** <br>
**Linear Interpolation**<br>
Linear interpolation can be used to impute missing data by drawing a straight line between the two points surrounding the missing value. <br>
  In time series, this means for a missing data point, it looks at a prior past value and the next future value to draw a line between them.

**Polynomial Interpolation** <br>
Polynimial Interpolation will attempt to draw a curved line between the two points.

We will use the pandas DataFrame.interpolate() function to examine different interpolation methods, including linear, polynomial, quadratic, nearest, and spline.


```python
clicks_missing = clicks_missing.iloc[:,0:3]
co2_missing = co2_missing.iloc[:,0:1]
```


```python
interpolation = ['linear',
                 'quadratic',
                 'nearest',
                 'cubic']
```


```python
'''Loop through each of the interpolation method in the list and use interpolate()'''
for intp in interpolation:
    co2_missing[intp] = co2_missing['co2'].interpolate(method=intp)
    clicks_missing[intp] = clicks_missing['clicks'].interpolate(method=intp)
```

    /var/folders/fn/gllvw1z179nb4gb993sqkg9h0000gn/T/ipykernel_12979/65019824.py:3: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      co2_missing[intp] = co2_missing['co2'].interpolate(method=intp)


There are two additional methods that it would be interesting to test: *spline* and *polynomial*. To use these methods, we need to provide the order parameter.


```python
co2_missing['spline'] = co2_missing['co2'].interpolate(method='spline', order=2)
clicks_missing['spline'] = clicks_missing['clicks'].interpolate(method='spline', order=2)
co2_missing['poly'] = co2_missing['co2'].interpolate(method='polynomial', order=5)
clicks_missing['poly'] = clicks_missing['clicks'].interpolate(method='polynomial', order=5)
```


```python
_ = rmse_score(co2_original, co2_missing, 'co2')
```

    RMSE for linear: 0.05507291327761665
    RMSE for quadratic: 0.08367561505614346
    RMSE for nearest: 0.05385422309469095
    RMSE for cubic: 0.08373627305833138
    RMSE for spline: 0.1878602347541416
    RMSE for poly: 0.06728323553134932
    Mininum RMSE belongs to nearest is 0.05385422309469095



```python
_ = rmse_score(clicks_original, clicks_missing, 'clicks')
```

    RMSE for linear: 1329.1448378562811
    RMSE for quadratic: 5224.641260626974
    RMSE for nearest: 1706.1853705030173
    RMSE for cubic: 6199.304875782833
    RMSE for spline: 5222.922993448641
    RMSE for poly: 56757.29323647128
    Mininum RMSE belongs to linear is 1329.1448378562811



```python
cols = ['co2', 'linear', 'nearest', 'poly']
```


```python
plot_dfs(co2_original, co2_missing[cols], 'co2')
```


    
![png](TimeSeries_files/TimeSeries_310_0.png)
    



```python
cols = ['clicks', 'linear', 'nearest', 'poly', 'spline']
plot_dfs(clicks_original, clicks_missing[cols], 'clicks')
```


    
![png](TimeSeries_files/TimeSeries_311_0.png)
    


### G. Outlier Detection Using Statistical Methods

Types of Outliers: <br>
**Point Outlier** - A data point deviates from the rest of the population—sometimes referred to as a global outlier.<br>
**Contextual Outlier** -  When an observation is considered an outlier based on a particular condition or context, such as deviation from neighboring data points. However, the same observation may not be considered an outlier if the context changes<br>
**Collective Outlier** - groups of observations, differ from the population and don't follow the expected pattern<br>


Outlier Detection and Change point detection are different. <br>

**Change Point Detection (CPD)**, the goal is to anticipate abrupt and impactful fluctuations (increasing or decreasing) in the time series data.
CPD covers specific techniques, such as **CUMSUM** and **Bayesian online change point detection (BOCPD)**. <br>
Example: A machine may break if the internal temperature reaches a certain point or if you're trying to understand whether the discounted price did increase sales or not.


**Statistical Techniques for Outlier Detection** <br>
1. Resampling time series data
2. Detecting outliers using visualization
3. Detecting outliers using the Tukey method
4. Detecting outliers using a z-score
5. Detecting outliers using a modified z-score


```python
import warnings
warnings.filterwarnings('ignore')
plt.rcParams["figure.figsize"] = [16, 3]
```


```python
chapter = 'Ch8'
filename = 'nyc_taxi.csv'
path = Path(_base_location+chapter)
```


```python
nyc_taxi = read_dataset(path, filename, 'timestamp')
```


```python
nyc_taxi.index.freq = '30T' #setting 30 min freq for the index
```


```python
nyc_dates = ["2014-11-01",
             "2014-11-27",
             "2014-12-25",
             "2015-01-01",
             "2015-01-27"]
```


```python
nyc_taxi.plot(title='NYC Taxi', alpha=0.6); #Timeseries with 30 min frequency
```


    
![png](TimeSeries_files/TimeSeries_321_0.png)
    



```python
def plot_outliers(outliers, data, method='KNN',
                  halignment = 'right',
                  valignment = 'bottom',
                  labels = False):

    ax = data.plot(alpha=0.6)

    if labels:
        for i in outliers['value'].items():
            plt.plot(i[0], i[1], 'rx')
            plt.text(i[0], i[1], f'{i[0].date()}', horizontalalignment = halignment,
                                                   verticalalignment = valignment)
    else:
        data.loc[outliers.index].plot(ax=ax, style='rx')

    plt.title(f'NYC Taxi - {method}')
    plt.xlabel('date'); plt.ylabel('# of passengers')
    plt.legend(['nyc taxi', 'outliers'])
    plt.show()

```

Outliers can indicate bad data due to a random variation in the process, known as noise, or due to data entry error, faulty sensors, bad experiment, or natural variation. Outliers are usually undesirable if they seem synthetic, for example, bad data. On the other hand, if outliers are a natural part of the process, you may need to rethink removing them and opt to keep these data points.

Generally, outliers can cause side effects when building a model based on strong assumptions on the data distribution; for example, the data is from a Gaussian (normal) distribution. Statistical methods and tests based on assumptions of the underlying distribution are referred to as parametric methods.

Sometimes you may need to test your model with outliers and again without outliers to understand the overall impact on your analysis. In other words, not all outliers are created, nor should they be treated equally.


There are many well-known methods for outlier detection.

In **statistical methods**, you have different tools that you can leverage:
1. Use of visualizations (boxplots, QQ-plots, histograms, and scatter plots), 
2. z-score, 
3. interquartile range (IQR) and Tukey fences,
4.  and statistical tests such as Grubb's test, the Tietjen-Moore test, or the generalized Extreme Studentized Deviate (ESD) test. 

These are basic, easy to interpret, and effective methods.

#### G.1 Resampling time series data

Resampling implies changing the frequency or level of granularity of the data.

The need for resampling depends upon the requirement of analysis granularity. Sometimes we need to aggregate the data from daily level to weekly level, i.e. called as **downsampling**; for this we need some aggregation function such as mean, sum, min, or max. etc.

However, in some situations we need to resample from daily level to hourly, i.e. called as **upsampling**; this results in null rows, which can be filled using imputation or interpolation.

Resampling is done using **DataFrame.resample()** function.

For downsampling, **D** stands for **Day**, **3B** for **3*Business day**, **W** for **Week**, **M** for **Month end**, **MS** for **Month Start**

For Upsampling, **15T** stands for **15 minutes** and rest same for downsampling


```python
df_downsampled = nyc_taxi.resample('D').mean() #daily downsampling
```


```python
df_downsampled.index.freq # to check frequency of the index
```




    <Day>




```python
df_downsampled = nyc_taxi.resample('3D').sum() #3 day frequency
```


```python
df_downsampled.index.freq
```




    <3 * Days>




```python
df_downsampled = nyc_taxi.resample('3B').sum() #frequency to 3 Business days
```


```python
nyc_taxi.resample('15T').mean().head() #'T' is used for minutes
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>value</th>
    </tr>
    <tr>
      <th>timestamp</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-07-01 00:00:00</th>
      <td>10844.0</td>
    </tr>
    <tr>
      <th>2014-07-01 00:15:00</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2014-07-01 00:30:00</th>
      <td>8127.0</td>
    </tr>
    <tr>
      <th>2014-07-01 00:45:00</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2014-07-01 01:00:00</th>
      <td>6210.0</td>
    </tr>
  </tbody>
</table>
</div>



**NaN** rows created by upsampling, we need to give instruction on how to fill the NaN rows. 


```python
nyc_taxi.resample('15T').fillna('ffill').head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>value</th>
    </tr>
    <tr>
      <th>timestamp</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-07-01 00:00:00</th>
      <td>10844</td>
    </tr>
    <tr>
      <th>2014-07-01 00:15:00</th>
      <td>10844</td>
    </tr>
    <tr>
      <th>2014-07-01 00:30:00</th>
      <td>8127</td>
    </tr>
    <tr>
      <th>2014-07-01 00:45:00</th>
      <td>8127</td>
    </tr>
    <tr>
      <th>2014-07-01 01:00:00</th>
      <td>6210</td>
    </tr>
  </tbody>
</table>
</div>



With downsampling we can provide list of aggregation function, this will create a new col for each aggregation method.


```python
nyc_taxi.resample('W').agg(['mean', 'sum', 'max', 'median', 'max']).head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="5" halign="left">value</th>
    </tr>
    <tr>
      <th></th>
      <th>mean</th>
      <th>sum</th>
      <th>max</th>
      <th>median</th>
      <th>max</th>
    </tr>
    <tr>
      <th>timestamp</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-07-06</th>
      <td>13361.350694</td>
      <td>3848069</td>
      <td>29985</td>
      <td>14428.0</td>
      <td>29985</td>
    </tr>
    <tr>
      <th>2014-07-13</th>
      <td>15365.928571</td>
      <td>5162952</td>
      <td>26873</td>
      <td>17085.0</td>
      <td>26873</td>
    </tr>
    <tr>
      <th>2014-07-20</th>
      <td>15524.455357</td>
      <td>5216217</td>
      <td>27167</td>
      <td>17093.5</td>
      <td>27167</td>
    </tr>
    <tr>
      <th>2014-07-27</th>
      <td>15685.339286</td>
      <td>5270274</td>
      <td>26688</td>
      <td>17420.0</td>
      <td>26688</td>
    </tr>
    <tr>
      <th>2014-08-03</th>
      <td>15071.163690</td>
      <td>5063911</td>
      <td>25969</td>
      <td>16692.0</td>
      <td>25969</td>
    </tr>
  </tbody>
</table>
</div>



#### G.2 Detecting Outliers using Visualization

There are two general approaches for using statistical techniques to detect outliers: **parametric** and **non-parametric** methods. <br> Parametric methods assume you know the underlying distribution of the data. For example, if your data follows a normal distribution. <br> On the other hand, in non-parametric methods, you make no such assumptions.

Histograms and box plots are basic non-parametric techniques that can provide insight into the distribution of the data and the presence of outliers. <br>
More specifically, box plots, also known as box and whisker plots, provide a five-number summary: the minimum, first quartile (25th percentile), median (50th percentile), third quartile (75th percentile), and the maximum. <br>
There are different implementations for how far the whiskers extend, for example, the whiskers can extend to the minimum and maximum values.The whiskers extend to what is called Tukey's lower and upper fences. Any data point outside these boundaries is considered an outlier.


```python
import seaborn as sns
```


```python
tx = nyc_taxi.resample('D').mean() # Downsampling the data to daily frequency.
```


```python
known_outliers = tx.loc[nyc_dates]
```


```python
plot_outliers(known_outliers, tx, 'Known Outliers')
```


    
![png](TimeSeries_files/TimeSeries_346_0.png)
    



```python
sns.histplot(tx)
```




    <AxesSubplot:ylabel='Count'>




    
![png](TimeSeries_files/TimeSeries_347_1.png)
    



```python
# sns.displot(tx, kind='hist', height=3, aspect=4)
```


```python
sns.boxplot(tx['value'])
```




    <AxesSubplot:xlabel='value'>




    
![png](TimeSeries_files/TimeSeries_349_1.png)
    


The width of the box (Q1 to Q3) is called **interquartile range (IQR)** calculated as the difference between the 75th and 25th percentiles **(Q3 – Q1)**. <br> The lower fence is calculated as Q1 - (1.5 x IQR), and the upper fence as Q3 + (1.5 x IQR). Any observation less than the lower boundary or greater than the upper boundary is considered a potential outlier.

There are two more variations for box plots in seaborn **(boxenplot and violinplot)**.

**Boxenplot** is letter-value plot, better suited when working with larger datasets


```python
sns.boxenplot(tx['value']) #The boxen plot, which in literature is referred to as a letter-value plot, better suited when working with larger datasets
```




    <AxesSubplot:xlabel='value'>




    
![png](TimeSeries_files/TimeSeries_352_1.png)
    


In seaborn, this parameter is called k_depth, which can take a numeric value, or you can specify different methods such as tukey, proportion, trustworthy, or full.


```python
for k in ["tukey", "proportion", "trustworthy", "full"]:
    sns.boxenplot(tx['value'], k_depth=k)
    plt.title(k)
    plt.show()
```


    
![png](TimeSeries_files/TimeSeries_354_0.png)
    



    
![png](TimeSeries_files/TimeSeries_354_1.png)
    



    
![png](TimeSeries_files/TimeSeries_354_2.png)
    



    
![png](TimeSeries_files/TimeSeries_354_3.png)
    


Final variation is the violin plot, which you can display using the violinplot function.


```python
sns.violinplot(tx["value"])
```




    <AxesSubplot:xlabel='value'>




    
![png](TimeSeries_files/TimeSeries_356_1.png)
    


**Violen plot** that is a hybrid between a box plot and a **kernel density estimation (KDE)**.

A kernel is a function that estimates the probability density function, the larger peaks (wider area), for example, show where the majority of the points are concentrated. This means that there is a higher probability that a data point will be in that region as opposed to the much thinner regions showing much lower probability.

The number of peaks; in this case, we have one peak, which makes it a unimodal distribution. If there is more than one peak, we call it a multimodal distribution, which should trigger a further investigation into the data.

**Lag Plot** - It is a Scatter plot, but instead of plotting two variables to observe correlation, as an example, we plot the same variable against its lagged version.


```python
from pandas.plotting import lag_plot
lag_plot(tx)
```




    <AxesSubplot:xlabel='y(t)', ylabel='y(t + 1)'>




    
![png](TimeSeries_files/TimeSeries_360_1.png)
    


#### G.3 Detecting Outliers with Tukey Method

The box plot showed the quartiles with whiskers extending to the upper and lower fences. These boundaries or fences were calculated using the Tukey method.


```python
percentiles = [0, 0.05, .10, .25, .5, .75, .90, .95, 1]
tx.describe(percentiles = percentiles)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>215.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>15137.569380</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1937.391020</td>
    </tr>
    <tr>
      <th>min</th>
      <td>4834.541667</td>
    </tr>
    <tr>
      <th>0%</th>
      <td>4834.541667</td>
    </tr>
    <tr>
      <th>5%</th>
      <td>11998.181250</td>
    </tr>
    <tr>
      <th>10%</th>
      <td>13043.854167</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>14205.197917</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>15299.937500</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>16209.427083</td>
    </tr>
    <tr>
      <th>90%</th>
      <td>17279.300000</td>
    </tr>
    <tr>
      <th>95%</th>
      <td>18321.616667</td>
    </tr>
    <tr>
      <th>100%</th>
      <td>20553.500000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>20553.500000</td>
    </tr>
  </tbody>
</table>
</div>



Quartiles divide your distribution into four segments (hence the name) marked as Q1 (25th percentile), Q2 (50th percentile or Median), and Q3 (75th percentile). <br> Percentiles, on the other hand, can take any range from 0 to 100 (in pandas from 0 to 1, while in NumPy from 0 to 100), but most commonly refer to when the distribution is partitioned into 100 segments. These segments are called quantiles.


```python
percentiles = [0, 5, 10, 25, 50, 75, 90, 95, 100]
np.percentile(tx, percentiles)
```




    array([ 4834.54166667, 11998.18125   , 13043.85416667, 14205.19791667,
           15299.9375    , 16209.42708333, 17279.3       , 18321.61666667,
           20553.5       ])



IQR is calculated as the difference between Q3 and Q1 (IQR = Q3 – Q1), which determines the width of the box in the box plot. <br> These upper and lower fences are known as Tukey's fences, and more specifically, they are referred to as inner boundaries. <br> The outer boundaries also have lower Q1 - (3.0 x IQR) and upper Q3 + (3.0 x IQR) fences.


```python
def iqr_outliers(data):
    q1, q3 = np.percentile(data, [25, 75])
    IQR = q3 - q1
    lower_fence = q1 - (1.5 * IQR)
    upper_fence = q3 + (1.5 * IQR)
    return data[(data.value > upper_fence) | (data.value < lower_fence)]
```


```python
outliers = iqr_outliers(tx)
print(outliers)
```

                       value
    timestamp               
    2014-11-01  20553.500000
    2014-11-27  10899.666667
    2014-12-25   7902.125000
    2014-12-26  10397.958333
    2015-01-26   7818.979167
    2015-01-27   4834.541667



```python
plot_outliers(outliers, tx, "Outliers using IQR with Tukey's Fences")
```


    
![png](TimeSeries_files/TimeSeries_369_0.png)
    



```python
known_outliers
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>value</th>
    </tr>
    <tr>
      <th>timestamp</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014-11-01</th>
      <td>20553.500000</td>
    </tr>
    <tr>
      <th>2014-11-27</th>
      <td>10899.666667</td>
    </tr>
    <tr>
      <th>2014-12-25</th>
      <td>7902.125000</td>
    </tr>
    <tr>
      <th>2015-01-01</th>
      <td>14383.479167</td>
    </tr>
    <tr>
      <th>2015-01-27</th>
      <td>4834.541667</td>
    </tr>
  </tbody>
</table>
</div>




```python
plot_outliers(known_outliers, tx, "known outliers")
```


    
![png](TimeSeries_files/TimeSeries_371_0.png)
    


This simple method did a great job at identifying four of the five known outliers. In addition, Tukey's method identified two additional outliers on 2014-12-26 and 2015-01-26.

“The use of 1.5x(IQR) is common when it comes to defining outliers, you can change the default 1.5 value by updating the whis parameter in the boxplot function. <br> **The choice of 1.5 makes the most sense when the data follows a Gaussian distribution (normal)**, but this is not always the case. <br> Generally, the larger the value, the fewer outliers you will capture as you expand your boundaries (fences). Similarly, the smaller the value, the more non-outliers will be defined as outliers, as you are shrinking the boundaries (fences).


```python
def iqr_outliers(data, p):
    q1, q3 = np.percentile(data, [25, 75])
    IQR = q3 - q1
    lower_fence = q1 - (p * IQR)
    upper_fence = q3 + (p * IQR)
    return data[(data.value > upper_fence) | (data.value < lower_fence)]
```


```python
for p in [1.3, 1.5, 2.0, 2.5,  3.0]:
    print(f'with p={p}')
    print(iqr_outliers(tx, p))
    print('-'*15)
```

    with p=1.3
                       value
    timestamp               
    2014-07-04  11511.770833
    2014-07-05  11572.291667
    2014-07-06  11464.270833
    2014-09-01  11589.875000
    2014-11-01  20553.500000
    2014-11-08  18857.333333
    2014-11-27  10899.666667
    2014-12-25   7902.125000
    2014-12-26  10397.958333
    2015-01-26   7818.979167
    2015-01-27   4834.541667
    ---------------
    with p=1.5
                       value
    timestamp               
    2014-11-01  20553.500000
    2014-11-27  10899.666667
    2014-12-25   7902.125000
    2014-12-26  10397.958333
    2015-01-26   7818.979167
    2015-01-27   4834.541667
    ---------------
    with p=2.0
                       value
    timestamp               
    2014-11-01  20553.500000
    2014-12-25   7902.125000
    2015-01-26   7818.979167
    2015-01-27   4834.541667
    ---------------
    with p=2.5
                      value
    timestamp              
    2014-12-25  7902.125000
    2015-01-26  7818.979167
    2015-01-27  4834.541667
    ---------------
    with p=3.0
                      value
    timestamp              
    2014-12-25  7902.125000
    2015-01-26  7818.979167
    2015-01-27  4834.541667
    ---------------


#### G.4 Detecting outliers using a z-score

**z-score** is a common transformation for standardizing the data, common when you want to compare different datasets.

The z-score standardizes the data to be centered around a zero mean and the units represent standard deviations away from the mean.

$\huge{z}$ = $\huge\frac{(x-\mu)}{\sigma}$

z-score is a lossless transformation, which means you will not lose information such as its distribution (shape of data) or the relationship between the observation.

**Shortcoming** - It is a parameteric statistical method, since it assumes a gaussian distribution. In case the data is not normal then we will use modified z-score.


```python
def zscore(df, degree=3):
    data = df.copy()
    data['zscore'] = (data - data.mean())/data.std()
    outliers = data[(data['zscore'] <= -degree) | (data['zscore'] >= degree)]
    return outliers['value'], data
```


```python
threshold = 2.5
outliers, transformed = zscore(tx, threshold)
```


```python
transformed.hist()
```




    array([[<AxesSubplot:title={'center':'value'}>,
            <AxesSubplot:title={'center':'zscore'}>]], dtype=object)




    
![png](TimeSeries_files/TimeSeries_381_1.png)
    



```python
print(outliers)
```

    timestamp
    2014-11-01    20553.500000
    2014-12-25     7902.125000
    2015-01-26     7818.979167
    2015-01-27     4834.541667
    Name: value, dtype: float64



```python
plot_outliers(outliers, tx, "Outliers using z-score")
```


    
![png](TimeSeries_files/TimeSeries_383_0.png)
    


z-score managed to capture 3 out of 5 known outliers.


```python
def plot_zscore(data, d=3):
    n = len(data)
    plt.figure(figsize=(8,8))
    plt.plot(data, 'k^')
    plt.plot([0, n], [d,d], 'r--')
    plt.plot([0, n], [-d,-d], 'r--')    
```


```python
data = transformed['zscore'].values
plot_zscore(data, d=2.5)
```


    
![png](TimeSeries_files/TimeSeries_386_0.png)
    


The z-scores are interpreted as standard deviation units away from the mean, which is the center of the distribution.

There are several test in statsmodels library to test if the data is normal distributed.
1. **Kolmogorov-Smirnov Test** - The null hypothesis is that the data comes from Normal Distribution. The test returns the statistics and a **p-value**, if the p-value is less than 0.05, then you can reject the null hypothesis (data is not normally distributed). Otherwise, we fail to reject null hypothesis (data is normally distributed).

    **ktest_normal - statslibrary**


```python
from statsmodels.stats.diagnostic import kstest_normal

def test_normal(df):
    t_test, p_value = kstest_normal(df)
    if p_value < 0.05:
        print(f"Reject null hypothesis since p_value is {p_value}. Data is not normal")
    else:
        print(f"Fail to reject null hypothesis since p_value is {p_value}. Data is normal")
```


```python
test_normal(tx)
```

    Reject null hypothesis since p_value is 0.0009999999999998899. Data is not normal


#### G.5 Detecting Outliers using a modified z-score

Modified version of z-score comes to work when data is not normally distributed. The main difference between the regular z-score and modified z-score is that we replace the mean with median.

$\large{Modified  Z}$ = $\huge\frac{0.6745(x_i - \tilde{x})} {MAD}$

$\tilde{x}$ - median of the dataset

$MAD$ - Median absolute deviation

$MAD$ = $median(abs(x_i - \tilde{x}))$

$0.6745$ - is the standard deviation unit that corresponds to the 75th percentile (Q3) in gaussian distribution and is used as a normalization factor. It is used to approximate the standard deviation.


```python
import scipy.stats as stats

def modified_zscore(df, degree=3):
    data = df.copy()
    s = stats.norm.ppf(0.75) # percent point function, known as inverse cumulative distribution function - gives quantile for the percentile
    numerator = s*(data - data.median())
    MAD = np.abs(data - data.median()).median()
    data['m_zscore'] = numerator/MAD
    outliers = data[(data['m_zscore']>degree) | (data['m_zscore'] < -degree)]
    return outliers['value'], data
```


```python
threshold = 3
outliers, transformed = modified_zscore(tx, threshold)
transformed.hist()
```




    array([[<AxesSubplot:title={'center':'value'}>,
            <AxesSubplot:title={'center':'m_zscore'}>]], dtype=object)




    
![png](TimeSeries_files/TimeSeries_394_1.png)
    



```python
plot_outliers(outliers, tx, "Outliers using Modified Z-score")
```


    
![png](TimeSeries_files/TimeSeries_395_0.png)
    



```python
data = transformed['m_zscore'].values
plot_zscore(data, d=2.5)
```


    
![png](TimeSeries_files/TimeSeries_396_0.png)
    


**QQ - Plot (Quantile-Quantile plot)** - Designed to test normality and sometimes help outliers.


```python
import scipy
import scipy.stats as stats
```


```python
res = scipy.stats.probplot(tx.values.reshape(-1), plot=plt)
```


    
![png](TimeSeries_files/TimeSeries_399_0.png)
    


The solid line represents a reference line for what normally distributed data would look like. <br> If the data you are comparing is normally distributed, all the points will lie on that straight line. <br>
 We can see that the distribution is almost normal (not perfect), and we see issues toward the distribution's tails. Majority of the outliers are at the bottom tail end (less than -2 standard deviation).

### H. Exploratory Data Analysis and Diagnosis

For time series data exploratory data analysis includes some of the time series specific characteristics, such as stationarity, effects of trends and seasonality, autocorrelation etc.

Focus will be on the following:

1. Plotting time series data using Pandas
2. Plotting time series data using hvplot
3. Decomposing time series data
4. Detecting time series stationarity
5. Applying power transformation
6. Testing for autocorrelation in time series

#### H.1 Plotting time series data using Pandas


```python
from statsmodels.datasets import co2, get_rdataset
plt.rcParams["figure.figsize"] = [12, 5] 
```


```python
chapter = 'ch9/'
filename = 'closing_price.csv'
path = Path(_base_location+chapter+filename)
```


```python
closing_price = pd.read_csv(path,
                            index_col='Date',
                            parse_dates=True)
```


```python
co2_df = co2.load_pandas().data
co2_df = co2_df.ffill()
air_passenger = get_rdataset("AirPassengers")
airp_df = air_passenger.data
airp_df.index = pd.date_range('1949', '1961', freq='M')
airp_df.drop(columns=['time'], inplace=True)
```


```python
closing_price.plot(kind="line")
```




    <AxesSubplot:xlabel='Date'>




    
![png](TimeSeries_files/TimeSeries_408_1.png)
    


To check price fluctuations in comparison to each other, we need to normalize the data, by dividing the stock price by the first-day price (first row of each stock). This will make all the stocks have same starting point.


```python
closing_price_n = closing_price.div(closing_price.iloc[0])
closing_price_n.plot()
```




    <AxesSubplot:xlabel='Date'>




    
![png](TimeSeries_files/TimeSeries_410_1.png)
    



```python
closing_price_n.plot(subplots=True);
```


    
![png](TimeSeries_files/TimeSeries_411_0.png)
    



```python
closing_price_n.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>AAPL</th>
      <th>MSFT</th>
      <th>IBM</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2019-11-01</th>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>2019-11-04</th>
      <td>1.006567</td>
      <td>1.005775</td>
      <td>1.015790</td>
    </tr>
    <tr>
      <th>2019-11-05</th>
      <td>1.005121</td>
      <td>1.005149</td>
      <td>1.017413</td>
    </tr>
    <tr>
      <th>2019-11-06</th>
      <td>1.005551</td>
      <td>1.002366</td>
      <td>1.023980</td>
    </tr>
    <tr>
      <th>2019-11-07</th>
      <td>1.017156</td>
      <td>1.003757</td>
      <td>1.027937</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Further customization of plots

start_date = '2019'
end_date = '2021'
plot = closing_price.plot(
            title=f'Closing Prices from {start_date} - {end_date}',
            ylabel= 'Closing Price')
# plt.savefig('images/fig_9.1.png', bbox_inches='tight')
```


    
![png](TimeSeries_files/TimeSeries_413_0.png)
    


There are many plotting styles that you can use within pandas simply by providing a value to the kind argument. For example, you can specify the following:
			
* line - line charts commonly used to display time series
* bar or barh (horizontal) - bar plots
* hist - histogram plots
* box - boxplots
* kde or density - kernel density estimation plots
* area - area plots
* pie - pie plots
* scatter - scatter plots 
* hexbin - hexagonal bin plots

#### H.2 Plotting time series data with interactive visualization using hvPlot

#### H.3 Decomposing time series data

There are 3 major component of any time series analysis process:
1. Trend
2. Seasonality
3. Residual

These components help make informed decisions during the modeling process.

**Trend** - Gives a sense of long-term direction of time series and can be upward, downward, or horizontal. <br>
**Seasonality** - These are repeated patterns over time. For Ex. a time series of sales data might show an increase in sales around Christmas. <br>
**Residual** - The residual is simply the remaining or unexplained portion once we extract trend and seasonality. <br>

The decomposition of time series data is the process of extracting these three components and representing them as their models. The modeling of the decomposed components can be either additive or multiplicative.

**Additive Model** when the original time series can be reconstructed by adding all three components:

$\huge{y_t} = T_t + S_t + R_t$

An additive model is reasonable when the seasonal variations do not change over time.

**Multiplicative Model** - If the time series can be constructed by multiplying all three components, you have a multiplicative model.

$\huge{y_t} = T_t * S_t * R_t$

A multiplicative model is suitable when the seasonal variation fluctuates over time.

**Seasonality** and **Trend** can be considered as **predictable components** (these are consistent, repeating patterns that can be captured and modeled).

Whereas, unpredictable components, those that shows irregularity, often called noise, can be referred as **Residual** in context of decomposition.

Different Decomposition Techniques:

1. Seasonal Decompose
2. Seasonal-Trend Decomposition with LOESS (STL)
3. hp_filter 

These methods are available in statsmodels library.


```python
from statsmodels.tsa.seasonal import seasonal_decompose, STL

plt.rcParams["figure.figsize"] = (10, 3)
```


```python
co2_df.plot()
```




    <AxesSubplot:>




    
![png](TimeSeries_files/TimeSeries_423_1.png)
    


The **co2_df** data shows a long-term linear trend, with a repeated seasonal pattern at a constant rate. (Seasonal Variation). This indicates that the CO2 dataset is an additive model.


```python
airp_df['value'].plot()
```




    <AxesSubplot:>




    
![png](TimeSeries_files/TimeSeries_425_1.png)
    


The **airp_df** data shows long-term linear upward trend and seasonality. However, the seasonality fluctuation seems to be increasing as well, indicating a multiplicative model.


```python
plt.rcParams["figure.figsize"] = (10,5)
```


```python
air_decomposed = seasonal_decompose(airp_df, model='multiplicative')
air_decomposed.plot(); plt.show()
```


    
![png](TimeSeries_files/TimeSeries_428_0.png)
    


Breaking down the air passenger data decomposition:
1. The trend component shows an upward direction. The trend indicates whether there is positive (increasing or upward), negative (decreasing or downward) or constant (no-trend or horizontal) long term movement.
2. The seasonal component shows the seasonality effect and the repeating pattern of highs and lows.
3. The residue component shows the random variations in the data after applying the model.


```python
co2_decomposed = seasonal_decompose(co2_df, model = 'additive')
```


```python
co2_decomposed
```




    <statsmodels.tsa.seasonal.DecomposeResult at 0x7faaa8be3ca0>




```python

co2_decomposed.plot(); plt.show()
```


    
![png](TimeSeries_files/TimeSeries_432_0.png)
    



```python
(air_decomposed.trend *
air_decomposed.seasonal *
air_decomposed.resid).plot(); #We can reconstruct the timeseries by multiplying the component, since the data was of multiplicative model.
```


    
![png](TimeSeries_files/TimeSeries_433_0.png)
    


There is another decomposition option within statsmodel is STL, the STL option (class) requires additional parameters than the seasonal_decompose function. The two other parameters you will use are seasonal and robust. <br> The first one is **seasonal parameter**, is for the seasonal smoother and can only take odd integer values greater than or equal to 7. Similarly, the STL function has a trend smoother (the trend parameter).

The second parameter is **robust**, which takes a Boolean value (True or False). Setting robust=True helps remove the impact of outliers on seasonal and trend components when calculated.


```python
co2_stl = STL(co2_df,
              seasonal=13,
              robust=True).fit()
co2_stl.plot(); plt.show()
```


    
![png](TimeSeries_files/TimeSeries_435_0.png)
    


The STL class uses the LOESS seasonal smoother, which stands for Locally Estimated Scatterplot Smoothing. STL is more robust than seasonal_decompose for measuring non-linear relationships. <br> On the other hand, STL assumes additive composition, so you do not need to indicate a model, unlike with seasonal_decompose.

The **Hodrick-Prescott filter** is a smoothing filter that can be used to separate short-term fluctuations (cyclic variations) from long-term trends. This is implemented as **hp_filter** in the statsmodels library.

STL and seasonal_decompose returned three components (trend, seasonal, and residual). On the other hand, hp_filter returns two components: a cyclical component and a trend component.


```python
from statsmodels.tsa.filters.hp_filter import hpfilter
plt.rcParams["figure.figsize"] = (20, 3)
co2_cyclic, co2_trend = hpfilter(co2_df)

fig, ax = plt.subplots(1,2, figsize=(16, 4))
co2_cyclic.plot(ax=ax[0], title='CO2 Cyclic Component')
co2_trend.plot(ax=ax[1], title='CO2 Trend Component')
```




    <AxesSubplot:title={'center':'CO2 Trend Component'}>




    
![png](TimeSeries_files/TimeSeries_439_1.png)
    


#### H.4 Detecting time series stationarity

A **stationary** time series implies that specific statistical properties do not vary over time and remain steady, making the processes easier to model and predict. <br> On the other hand, a **non-stationary** process is more complex to model due to the dynamic nature and variations over time.

A stationary time series is defined as a time series with a **constant mean**, a **constant variance**, and a **consistent covariance** (or **autocorrelation**) between identical distanced periods (lags). <br> Having the mean and variance as constants simplifies modeling since you are not solving for them as functions of time.

Generally, a time series with trend or seasonality can be considered non-stationary. Usually, spotting trends or seasonality visually in a plot can help you determine whether the time series is stationary or not. It is not always easy to identify stationarity or lack if it, visually. <br>

We do have some statistical tests, to help identify stationary or non-stationary time series numerically.

**Statistical Tests to identify stationarity**

1. Augmented Dicker-Fuller **(ADF)** Test
2. Kwiatkowski-Phillips-Schnidt-Shin **(KPSS)** Test

Both these, test for unit roots in a univariate time series process. Generally presence of unit roots indicates non-stationarity. <br>

ADF and KPSS test are based on Linear Regression and are a type of statistical hypothesis test. <br>
For Ex. null hypothesis for ADF states that there is a unit root in the time series, and thus it is non-stationary. <br>
For KPSS, the null hypothesis is opposite of ADF, which assumes the time series is stationary. We will need to interpret the test results to determine whether you can reject or fail the null hypothesis.

Generally, you can rely on the p-values returned to decide whether you reject or fail to reject the null hypothesis.


```python
from statsmodels.tsa.stattools import adfuller, kpss
```


```python
def print_results(output, test='adf'):
    '''
    The function takes output from adfuller and kpss functions and returns a dictionary that add labels to the output
    '''
    pval = output[1]
    test_score = output[0]
    lags = output[2]
    
    decision = 'Non-Stationary'
    if test == 'adf':
        critical = output[4]
        if pval < 0.05:
            decision = 'Stationary'
    elif test=='kpss':
        critical = output[3]
        if pval >= 0.05:
            decision = 'Stationary'
            
    output_dict = {
    'Test Statistic': test_score,
    'p-value': pval,
    'Numbers of lags': lags,
    'decision': decision
    }
    for key, value in critical.items():
        output_dict["Critical Value (%s)" % key] = value
       
    return pd.Series(output_dict, name=test)
```


```python
adf_output = adfuller(co2_df)
kpss_output = kpss(co2_df)
```


```python
pd.concat([
    print_results(adf_output, 'adf'),
    print_results(kpss_output, 'kpss')
], axis=1)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>adf</th>
      <th>kpss</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Test Statistic</th>
      <td>0.046051</td>
      <td>8.183188</td>
    </tr>
    <tr>
      <th>p-value</th>
      <td>0.962179</td>
      <td>0.01</td>
    </tr>
    <tr>
      <th>Numbers of lags</th>
      <td>27</td>
      <td>27</td>
    </tr>
    <tr>
      <th>decision</th>
      <td>Non-Stationary</td>
      <td>Non-Stationary</td>
    </tr>
    <tr>
      <th>Critical Value (1%)</th>
      <td>-3.433252</td>
      <td>0.739</td>
    </tr>
    <tr>
      <th>Critical Value (5%)</th>
      <td>-2.862822</td>
      <td>0.463</td>
    </tr>
    <tr>
      <th>Critical Value (10%)</th>
      <td>-2.567452</td>
      <td>0.347</td>
    </tr>
    <tr>
      <th>Critical Value (2.5%)</th>
      <td>NaN</td>
      <td>0.574</td>
    </tr>
  </tbody>
</table>
</div>



For *ADF*, the p-value is at 0.96, which is greater than 0.05, so you cannot reject the null hypothesis, and therefore, the time series is non-stationary.<br> For *KPSS*, the p-value is at 0.01, which is less than 0.05, so you reject the null hypothesis, and therefore, the time series is non-stationary.

1. The **Test Statistic** value is 0.046 for ADF and 8.18 for KPSS, which are above the 1% critical value threshold. This indicates that the time series is non-stationary. It confirms that you cannot reject the null hypothesis. The critical values for ADF come from a Dickey-Fuller table. 
2. The **p-value** result is associated with the test statistic. Generally, you can reject the null hypothesis if the p-value is less than 0.05 (5%). Again, when using ADF, KPSS, or other stationarity tests, make sure to understand the null hypothesis to accurately interpret the results.
3. **Number of lags** represents the number of lags used in the autoregressive process in the test (ADF and KPSS). In both tests, 27 lags were used. Since our CO2 data is weekly, a lag represents 1 week back. So, 27 lags represent 27 weeks in our data.
4. The number of observations used is the number of data points, excluding the number of lags.
5. The maximized info criteria are based on the **autolag** parameter. The default is **autolag="aic"** for the **Akaike information criterion**. Other acceptable autolag parameter values are bic for the **Bayesian information criterion** and t-stat.

Essentially stationarity can be achieved by removing trend (detrending) and seasonality effects.

**6 Main techniques to make time series stationary**, such as transformations and differencing.

1. First-Order Differencing
2. Second-Order Differencing
3. Subtracting Moving Average
4. Log Transformation
5. Decomposition
6. Hodrick-Prescott Fiter

For each transformation technique, we will run the stationarity tests and compare the results between the different techniques.

To do so, we will create two functions:
1. check_stationarity
2. plot_comparison


```python
def check_stationarity(df):

    '''
    takes a DataFrame, performs both KPSS and ADF tests, and returns the outcome.
    '''

    kps = kpss(df)
    adf = adfuller(df)

    kpss_pval, adf_pval = kps[1], adf[1]
    kpssh, adfh = 'Stationary', 'Non-Stationary'

    if adf_pval < 0.05:
        #Reject the null hypothesis
        adfh = 'Stationary'
    if kpss_pval < 0.05:
        kpssh = 'Non-Stationary'
    return(kpssh, adfh)

```


```python
def plot_comparison(methods, plot_type='line'):

    '''
    Takes a list of methods and compares their plots. The function takes plot_type, so you can explore a line chart and a histogram.
    The function calls check_stationarity function to capture the results for the subplot titles.
    '''

    n = len(methods)//2
    fig, ax = plt.subplots(n, 2, sharex=True, figsize=(20, 10))
    for i, method in enumerate(methods):
        method.dropna(inplace=True)
        name = [n for n in globals() if globals()[n] is method]
        v, r = i//2, i%2
        kpss_s, adf_s = check_stationarity(method)
        method.plot(kind=plot_type,
                    ax=ax[v,r],
                    legend=False,
                    title=f'{name[0]} --> KPSS: {kpss_s}, ADF {adf_s}')
        ax[v,r].title.set_size(20)
        method.rolling(52).mean().plot(ax=ax[v,r], legend=False)
```

Implementing methods to transform time series stationary or extracting stationary component.

**1. First Order Differencing**: This is also known as de-trending, calculated by subtracting an observation at time $t$ from the previous observation at time $t-1$, $(y_t - y_{t-1})$. <br>
This can be done using **.diff()** function which default period=1. <br>
Differenced data will contain one less data point (row) than the original data, hence the use of the .dropna() method.


```python
first_order_diff = co2_df.diff().dropna()
```

**2. Second Order Differencing**: This is useful if seasonality exists or if the first order differencing was insufficient. This is essentially differencing twice - differencing to remove seasonality followed by differencing to remove trend.


```python
differencing_twice = co2_df.diff(52).diff().dropna()
```

**3. Substracting moving average**: rolling window from the time series using **DataFrame.rolling(window=52).mean()** since it is weekly data:


```python
rolling = co2_df.rolling(window=52).mean()
substract_rolling_mean = co2_df - rolling
```

**4. Log Transformation**: Using **np.log()** is a common technique to stabilize the variance in a time series and sometimes enough to make the time series stationary. Simply, all it does is replace each observation with its log value 


```python
log_transform = np.log(co2_df)
```

**5. Decomposition**: Using decomposition to remove the trend component, such as **seasonal_decompose**. If the decomposition component is additive then the default parameter in seasonal_decompose is additive.


```python
decom = seasonal_decompose(co2_df)
sd_detrend = decom.observed - decom.trend
```

**6. Using Hodrick-Prescott** filter to remove the trend component


```python
cyclic, trend = hpfilter(co2_df)
```


```python
methods = [first_order_diff, differencing_twice, substract_rolling_mean, log_transform, sd_detrend, cyclic]
```


```python
plot_comparison(methods) #Plotting the different methods to make the CO2 time series stationary
```


    
![png](TimeSeries_files/TimeSeries_469_0.png)
    


Over-differenting time series as some studies have shown that models are less accurate. <br> 
For example, first_order_diff already made the time series stationary, and thus there was no need to difference it any further. In other words, differencing_twice was not needed. <br> Additionally, notice how **log_transform** is still non-stationary.

**Detrending** stabilizes the mean over time of the time series and sometimes can be all that is needed to make it stationary. When you decide to detrend your data, you are essentially removing an element of distraction so you can focus on hidden patterns that are not as obvious.<br> Hence, you can build a model to capture these patterns and not be overshadowed by the long-term trend (upward or downward movement).

In the presence of seasonal patterns you will need to remove the seasonal effect as well, which can be done through seasonal differencing. <br><br> This is done in addition to the first-order differencing for detrending; hence it can be called second-order differencing, twice-differencing, or differencing twice as you use differencing to remove seasonality effect first and again to remove the trend.<br> This assumes the seasonal differencing was insufficient to make the time series stationary. <br> Your goal is to use the minimal amount of differencing needed and avoid over-differencing. You will rarely need to go beyond differencing twice.

ADF and KPSS are based on Linear Regression and use OLS (Ordinary Least Squares) regression, which is used to compute the model's coefficients.

* To view OLS results for ADF, we need to set **store** parameter to **True**


```python
adf_result = adfuller(first_order_diff, store=True)
```


```python
adf_result[-1].resols.summary()

'''
This results as tuple, last element is a regression summary.
adf_result[0] : t-statistics
adf_result[1] : p-value
adf_result[2] : critical value
adf_result[3] : Includes Result Store object
'''

```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>            <td>y</td>        <th>  R-squared:         </th> <td>   0.586</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.580</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   112.4</td>
</tr>
<tr>
  <th>Date:</th>             <td>Sun, 04 Sep 2022</td> <th>  Prob (F-statistic):</th>  <td>  0.00</td> 
</tr>
<tr>
  <th>Time:</th>                 <td>13:09:52</td>     <th>  Log-Likelihood:    </th> <td> -1300.1</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>  2255</td>      <th>  AIC:               </th> <td>   2658.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>  2226</td>      <th>  BIC:               </th> <td>   2824.</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>    28</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
    <td></td>       <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>x1</th>    <td>   -1.2089</td> <td>    0.078</td> <td>  -15.517</td> <td> 0.000</td> <td>   -1.362</td> <td>   -1.056</td>
</tr>
<tr>
  <th>x2</th>    <td>    0.0694</td> <td>    0.074</td> <td>    0.934</td> <td> 0.351</td> <td>   -0.076</td> <td>    0.215</td>
</tr>
<tr>
  <th>x3</th>    <td>    0.0549</td> <td>    0.071</td> <td>    0.771</td> <td> 0.441</td> <td>   -0.085</td> <td>    0.194</td>
</tr>
<tr>
  <th>x4</th>    <td>    0.2661</td> <td>    0.068</td> <td>    3.894</td> <td> 0.000</td> <td>    0.132</td> <td>    0.400</td>
</tr>
<tr>
  <th>x5</th>    <td>    0.4363</td> <td>    0.066</td> <td>    6.603</td> <td> 0.000</td> <td>    0.307</td> <td>    0.566</td>
</tr>
<tr>
  <th>x6</th>    <td>    0.6273</td> <td>    0.064</td> <td>    9.780</td> <td> 0.000</td> <td>    0.502</td> <td>    0.753</td>
</tr>
<tr>
  <th>x7</th>    <td>    0.7159</td> <td>    0.063</td> <td>   11.377</td> <td> 0.000</td> <td>    0.592</td> <td>    0.839</td>
</tr>
<tr>
  <th>x8</th>    <td>    0.8211</td> <td>    0.062</td> <td>   13.246</td> <td> 0.000</td> <td>    0.699</td> <td>    0.943</td>
</tr>
<tr>
  <th>x9</th>    <td>    0.8455</td> <td>    0.061</td> <td>   13.779</td> <td> 0.000</td> <td>    0.725</td> <td>    0.966</td>
</tr>
<tr>
  <th>x10</th>   <td>    0.8269</td> <td>    0.061</td> <td>   13.641</td> <td> 0.000</td> <td>    0.708</td> <td>    0.946</td>
</tr>
<tr>
  <th>x11</th>   <td>    0.7763</td> <td>    0.060</td> <td>   13.011</td> <td> 0.000</td> <td>    0.659</td> <td>    0.893</td>
</tr>
<tr>
  <th>x12</th>   <td>    0.6904</td> <td>    0.058</td> <td>   11.815</td> <td> 0.000</td> <td>    0.576</td> <td>    0.805</td>
</tr>
<tr>
  <th>x13</th>   <td>    0.5949</td> <td>    0.057</td> <td>   10.486</td> <td> 0.000</td> <td>    0.484</td> <td>    0.706</td>
</tr>
<tr>
  <th>x14</th>   <td>    0.4798</td> <td>    0.055</td> <td>    8.736</td> <td> 0.000</td> <td>    0.372</td> <td>    0.588</td>
</tr>
<tr>
  <th>x15</th>   <td>    0.3971</td> <td>    0.053</td> <td>    7.466</td> <td> 0.000</td> <td>    0.293</td> <td>    0.501</td>
</tr>
<tr>
  <th>x16</th>   <td>    0.3136</td> <td>    0.052</td> <td>    6.074</td> <td> 0.000</td> <td>    0.212</td> <td>    0.415</td>
</tr>
<tr>
  <th>x17</th>   <td>    0.2572</td> <td>    0.050</td> <td>    5.096</td> <td> 0.000</td> <td>    0.158</td> <td>    0.356</td>
</tr>
<tr>
  <th>x18</th>   <td>    0.2521</td> <td>    0.050</td> <td>    5.082</td> <td> 0.000</td> <td>    0.155</td> <td>    0.349</td>
</tr>
<tr>
  <th>x19</th>   <td>    0.2443</td> <td>    0.049</td> <td>    4.982</td> <td> 0.000</td> <td>    0.148</td> <td>    0.341</td>
</tr>
<tr>
  <th>x20</th>   <td>    0.2534</td> <td>    0.049</td> <td>    5.211</td> <td> 0.000</td> <td>    0.158</td> <td>    0.349</td>
</tr>
<tr>
  <th>x21</th>   <td>    0.2867</td> <td>    0.048</td> <td>    5.940</td> <td> 0.000</td> <td>    0.192</td> <td>    0.381</td>
</tr>
<tr>
  <th>x22</th>   <td>    0.3286</td> <td>    0.048</td> <td>    6.864</td> <td> 0.000</td> <td>    0.235</td> <td>    0.422</td>
</tr>
<tr>
  <th>x23</th>   <td>    0.3501</td> <td>    0.047</td> <td>    7.426</td> <td> 0.000</td> <td>    0.258</td> <td>    0.443</td>
</tr>
<tr>
  <th>x24</th>   <td>    0.3452</td> <td>    0.046</td> <td>    7.504</td> <td> 0.000</td> <td>    0.255</td> <td>    0.435</td>
</tr>
<tr>
  <th>x25</th>   <td>    0.3440</td> <td>    0.044</td> <td>    7.904</td> <td> 0.000</td> <td>    0.259</td> <td>    0.429</td>
</tr>
<tr>
  <th>x26</th>   <td>    0.2724</td> <td>    0.039</td> <td>    6.924</td> <td> 0.000</td> <td>    0.195</td> <td>    0.350</td>
</tr>
<tr>
  <th>x27</th>   <td>    0.1844</td> <td>    0.032</td> <td>    5.846</td> <td> 0.000</td> <td>    0.123</td> <td>    0.246</td>
</tr>
<tr>
  <th>x28</th>   <td>    0.0940</td> <td>    0.021</td> <td>    4.493</td> <td> 0.000</td> <td>    0.053</td> <td>    0.135</td>
</tr>
<tr>
  <th>const</th> <td>    0.0299</td> <td>    0.009</td> <td>    3.201</td> <td> 0.001</td> <td>    0.012</td> <td>    0.048</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>57.058</td> <th>  Durbin-Watson:     </th> <td>   2.017</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td> 131.961</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.023</td> <th>  Prob(JB):          </th> <td>2.21e-29</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 4.184</td> <th>  Cond. No.          </th> <td>    27.8</td>
</tr>
</table><br/><br/>Notes:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.



#### H.5 Applying Power Transformations

We will extend what you learned from **Detecting time series stationarity**, and test for two additional assumptions: normality and homoskedasticity.

Testing for **homoskedasticity** (also spelled homoscedasticity) and **normality**. 

**Homoskedasticity** means that the variance is stable over time. More specifically, it is the variance of the residuals. When the variance is not constant, changing over time, we call it heteroskedasticity (also spelled heteroscedasticity).

**Test for Normality** essentially mean, whether the specific observation come from a normal distribution. Sometimes we may need to check normality of residuals as well. This can be part of model diagnostic stage.


```python
import numpy as np
from statsmodels.graphics.tsaplots import plot_pacf, plot_acf
import statsmodels.api as sm

plt.rcParams["figure.figsize"] = (20, 8)
```

Sometimes, you can determine normality and homoskedasticity from plots, for example, a histogram or a Q-Q plot. 

Apart from plots, we will be looking into the **White test** and the **Breusch-Pagan Lagrange** statistical test for homoskedactisity.<br>
And, **Shapiro-Wilk, D'Agostino-Pearson**, and **Kolmogorov-Smirnov** statistical tests for Normality; Overall **Shapiro-Wilk** tends to perform best for Normality.

**Testing Normality**

We can use different libraries to do the Kolmogorov-Smirnov test also known as **ktest**, implemented as *ktest* in SciPy and *ktest_normal* in statsmodels.


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import shapiro, boxcox
from statsmodels.stats.api import (het_breuschpagan, 
                                   het_goldfeldquandt, 
                                   het_white, jarque_bera,
                                   omni_normtest, normal_ad)
from statsmodels.stats.diagnostic import kstest_normal, normal_ad
from statsmodels.graphics.tsaplots import plot_acf, plot_pacf
from statsmodels.formula.api import ols
from statsmodels.tools.tools import add_constant
```


```python
from scipy.stats import shapiro, kstest, normaltest
from statsmodels.stats.diagnostic import kstest_normal
```

The normality diagnostic is a statistical test based on a null hypothesis that you need to determine whether you can accept or reject.<br> Conveniently, the following tests that you will implement have the same null hypothesis. The null hypothesis states that the data is normally distributed. We would reject the null hypothesis if the p-value is less than 0.05, making the time series not normally distributed.


```python
def is_normal(test, p_level = 0.05):
    stat, pval = test

    return 'Normal' if pval > 0.05 else 'Not Normal'
```


```python
normal_args = (np.mean(co2_df), np.std(co2_df))
```


```python
normal_args = (np.mean(co2_df),np.std(co2_df))

print(is_normal(shapiro(co2_df)))
print(is_normal(normaltest(co2_df)))
print(is_normal(normal_ad(co2_df)))
print(is_normal(kstest_normal(co2_df)))
print(is_normal(kstest(co2_df, 
                   cdf='norm', 
                   args=normal_args)))
```

    Not Normal
    Not Normal
    Not Normal
    Not Normal
    Not Normal



```python
co2_df.diff().hist()
```




    array([[<AxesSubplot:title={'center':'co2'}>]], dtype=object)




    
![png](TimeSeries_files/TimeSeries_489_1.png)
    


Sometimes, you may need to test normality as part of model evaluation and diagnostics. For example, you would evaluate the residuals (defined as the difference between actual and predicted values) if they follow a normal distribution. 

A simple autoregressive (AR(1)) model demonstrates how to use a normality test against the residuals of a model


```python
from statsmodels.tsa.api import AutoReg
model = AutoReg(co2_df.dropna(), lags=1).fit()
```

Running shapiro test against the residuals. To access residual we would use the **.resid** property as in model.resid.


```python
print(is_normal(shapiro(model.resid)))
```

    Not Normal



```python
plt.hist(model.resid);
```


    
![png](TimeSeries_files/TimeSeries_494_0.png)
    



```python
from statsmodels.graphics.gofplots import qqplot
qqplot(model.resid, line='q');
```


    
![png](TimeSeries_files/TimeSeries_495_0.png)
    



```python
plot_acf(model.resid);plt.show()
```


    
![png](TimeSeries_files/TimeSeries_496_0.png)
    


**Testing Homoskedactisity**

Testing for the stability of the variance against the model's residuals. The homoskedasticity test is performed on the model's residuals.


```python
from statsmodels.stats.api import (het_breuschpagan, het_goldfeldquandt)
```

The null hypothesis states that the data is homoskedastic for the two tests. For example, you would reject the null hypothesis if the p-value is less than 0.05, making the time series heteroskedastic.


```python
def het_test(model, test=het_breuschpagan):
    lm, lm_pvalue, fvalue, f_pvalue = (het_breuschpagan(model.resid,
                                       sm.add_constant(model.fittedvalues) 
                                        )) 

    return "Heteroskedastic" if f_pvalue <0.05 else "Homoskedastic"
```

We will use the **het_breuschpagan** function, which takes resid, the model's residual, and **exog_het**, where you provide the original data (explanatory variables) related to the heteroskedasticity in the residual


```python
het_test(model, test=het_breuschpagan)
```




    'Homoskedastic'



This result indicates that the residual is homoskedastic, with a constant variance (stable).

Similar test is White's Lagrange multiplier test. In statsmodels, you will use the **het_white** function, which has the same two parameters that you used with het_breuschpagan.


```python
het_test(model, test=het_white)
```




    'Homoskedastic'



Both tests indicate that the residuals of the autoregressive model have constant variance (homoskedastic). Both tests estimate the auxiliary regression against the squared residuals and all the explanatory variables.

Both normality and homoskedasticity are some of the tests you may need to conduct on the residuals as you diagnose your model.

**Box-Cox Transformation**

**Box-Cox Transformation** - It transforms a non-normally distributed dataset into a normally distributed one. At the same time, it stabilizes the variance, making the data homoskedastic.

$\huge T(x) = \left\{ \begin{array}{cl}
\frac{x^{\lambda}-1}{\lambda} & : \ {\lambda} \neq 0 \\
\ln{(x)} & : \ {\lambda} = 0
\end{array} \right.$

The Box-Cox transformation relies on just one parameter, ${(\lambda)}$, and covers both logarithm and power transformations.

If ${(\lambda)}$ is $0$, then you get a natural log transformation; otherwise, it's a power transformation.

The approach is to try different values of ${(\lambda)}$ and then test for normality and homoskedasticity.For example, the SciPy library has the boxcox function, and you can specify different  values using the **lmbda** parameter.

If the **lmbda** parameter is set to None, the function will find the optimal lambda () value for you.


```python
from scipy.stats import boxcox
```


```python
xt, lmbda = boxcox(airp_df['value'])
xts = pd.Series(xt, index=airp_df.index)
```


```python
ig, ax = plt.subplots(1, 2, figsize=(16,5))
airp_df.hist(ax=ax[0])
ax[0].set_title('Original Time Series')
xts.hist(ax=ax[1])
ax[1].set_title('Box-Cox Transformed')

```




    Text(0.5, 1.0, 'Box-Cox Transformed')




    
![png](TimeSeries_files/TimeSeries_513_1.png)
    


The second histogram shows that the data was transformed, and the overall distribution changed.


```python
fig, ax = plt.subplots(1, 2, figsize=(16,5))
airp_df.plot(ax=ax[0])
ax[0].set_title('Original Time Series')
xts.plot(ax=ax[1])
ax[1].set_title('Box-Cox Transformed')
```




    Text(0.5, 1.0, 'Box-Cox Transformed')




    
![png](TimeSeries_files/TimeSeries_515_1.png)
    



```python
model_airp = AutoReg(airp_df, lags=1, trend='n').fit()
model_bx = AutoReg(xts, lags=1, trend='n').fit()
```


```python
fig, ax = plt.subplots(1, 2, figsize=(16,5))
model_airp.resid.plot(ax=ax[0])
ax[0].set_title('Residuals Plot - Regular Time Series')
model_bx.resid.plot(ax=ax[1])
ax[1].set_title('Residual Plot - Box-Cox Transformed')
```




    Text(0.5, 1.0, 'Residual Plot - Box-Cox Transformed')




    
![png](TimeSeries_files/TimeSeries_517_1.png)
    


Box-Cox allows us to make the data both normal and homoskedastic and is part of a family of power transforms that includes log transform and square root transform.<br> Box-Cox is a powerful transform because it supports both root and log transforms, and others are made possible by changing the lambda values.

AutoReg model comes with two useful methods: 
* diagnostic_summary()
* plot_diagnostics()


```python
print(model_bx.diagnostic_summary())
```

           Test of No Serial Correlation       
    ===========================================
           Lag  Ljung-Box LB P-value         DF
    -------------------------------------------
             1      8.800      0.003          1
             2     12.729      0.002          2
             3     28.040      0.000          3
             4     29.202      0.000          4
             5     29.205      0.000          5
             6     31.021      0.000          6
             7     47.992      0.000          7
             8     50.468      0.000          8
             9     52.247      0.000          9
            10     59.828      0.000         10
            11    172.816      0.000         11
            12    181.177      0.000         12
    
    
                 Test of Normality              
    ============================================
    Jarque-Bera    P-value   Skewness   Kurtosis
    --------------------------------------------
          5.987      0.050     -0.128      2.031
    
    
        Test of Conditional Homoskedasticity   
    ===========================================
           Lag    ARCH-LM    P-value         DF
    -------------------------------------------
             1      0.116      0.733          1
             2      1.243      0.537          2
             3      4.112      0.250          3
             4      4.058      0.398          4
             5      6.836      0.233          5
             6      7.203      0.302          6
             7     14.755      0.039          7
             8     15.157      0.056          8
             9     15.993      0.067          9
            10     17.963      0.056         10
            11     19.322      0.056         11
            12     64.563      0.000         12
            13     63.975      0.000         13
    -------------------------------------------



```python
model_bx.plot_diagnostics(); plt.show()
```


    
![png](TimeSeries_files/TimeSeries_520_0.png)
    


The **.plot_diagnostics()** function will show four plots so you can examine the model's residuals. Mainly, the plots will show whether the residuals are normally distributed from the Q-Q plot and histogram.

#### H.6 Testing for Auto-Correlation in Time Series

**Autocorrelation** is like statistical correlation (think Pearson correlation from high school), which measures the strength of a linear relationship between two variables, except that we measure the linear relationship between time series values separated by a lag. In other words, we are comparing a variable with its lagged version of itself.

To check for autocorrelations we will perform **Ljung-Box test**, up to a specified lag and whether they are significantly far off from 0.<br> The null hypothesis for the Ljung-Box test states that the previous lags are not correlated with the current period. In other words, you are testing for the absence of autocorrelation.

Test using acorr_ljungbox from statsmodels, you need to provide a lag value. The test will run for all lags up to the specified lag (maximum lag).

When testing for autocorrelation on the residuals, the expectation is that there should be no autocorrelation between the residuals. This ensures that the model has captured all the necessary information. <br> The presence of autocorrelation in the residuals can indicate that the model missed an opportunity to capture critical information and will need to be evaluated.


```python
from statsmodels.stats.diagnostic import acorr_ljungbox
co2_diff = np.log(co2_df).diff().dropna()
```


```python
acorr_ljungbox(co2_diff, lags=10, return_df=True) #null hypothesis rejected as p<0.05
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>lb_stat</th>
      <th>lb_pvalue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>16.193898</td>
      <td>5.717801e-05</td>
    </tr>
    <tr>
      <th>2</th>
      <td>78.485798</td>
      <td>9.057856e-18</td>
    </tr>
    <tr>
      <th>3</th>
      <td>287.050787</td>
      <td>6.311581e-62</td>
    </tr>
    <tr>
      <th>4</th>
      <td>383.291716</td>
      <td>1.132455e-81</td>
    </tr>
    <tr>
      <th>5</th>
      <td>493.588582</td>
      <td>1.932434e-104</td>
    </tr>
    <tr>
      <th>6</th>
      <td>536.683057</td>
      <td>1.047943e-112</td>
    </tr>
    <tr>
      <th>7</th>
      <td>598.257880</td>
      <td>5.776363e-125</td>
    </tr>
    <tr>
      <th>8</th>
      <td>620.615981</td>
      <td>8.637442e-129</td>
    </tr>
    <tr>
      <th>9</th>
      <td>626.775866</td>
      <td>3.739862e-129</td>
    </tr>
    <tr>
      <th>10</th>
      <td>631.630413</td>
      <td>2.925751e-129</td>
    </tr>
  </tbody>
</table>
</div>




```python
acorr_ljungbox(model_bx.resid, return_df=True, lags=10) #null hypothesis rejected since p_value < 0.05
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>lb_stat</th>
      <th>lb_pvalue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>6.891379</td>
      <td>8.661245e-03</td>
    </tr>
    <tr>
      <th>2</th>
      <td>8.799865</td>
      <td>1.227817e-02</td>
    </tr>
    <tr>
      <th>3</th>
      <td>12.728606</td>
      <td>5.261829e-03</td>
    </tr>
    <tr>
      <th>4</th>
      <td>28.039695</td>
      <td>1.224399e-05</td>
    </tr>
    <tr>
      <th>5</th>
      <td>29.201881</td>
      <td>2.116595e-05</td>
    </tr>
    <tr>
      <th>6</th>
      <td>29.204901</td>
      <td>5.563811e-05</td>
    </tr>
    <tr>
      <th>7</th>
      <td>31.021143</td>
      <td>6.161812e-05</td>
    </tr>
    <tr>
      <th>8</th>
      <td>47.991784</td>
      <td>9.915323e-08</td>
    </tr>
    <tr>
      <th>9</th>
      <td>50.468158</td>
      <td>8.794752e-08</td>
    </tr>
    <tr>
      <th>10</th>
      <td>52.247452</td>
      <td>1.026842e-07</td>
    </tr>
  </tbody>
</table>
</div>



### I. Building Univariate Time Series Models Using Statistical Methods

With time series data, different methods and models can be used, depending on whether the time series is univariate or multivariate, seasonal or non-seasonal, stationary or non-stationary, and linear or nonlinear.

Some popular statistical modeling methods include **exponential smoothing**, non-seasonal **autoregressive integrated moving average (ARIMA), Seasonal ARIMA (SARIMA), Vector Autoregressive (VAR)**, and other variants of these models.

Essential Techniques for Univariate Time Series Modeling

* Plotting ACF and PACF
* Forecasting univariate time series data with exponential smoothing
* Forecasting univariate time series data with non-seasonal ARIMA
* Forecasting univariate time series data with seasonal ARIMA


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import warnings

from statsmodels.tsa.api import (kpss, adfuller, 
                                 seasonal_decompose, STL)                                 
from statsmodels.tools.eval_measures import rmspe, rmse
from statsmodels.graphics.tsaplots import plot_acf, plot_pacf

from sklearn.metrics import mean_absolute_percentage_error as mape

from itertools import product
import statsmodels as sm
from pathlib import Path


warnings.filterwarnings('ignore')
plt.rcParams["figure.figsize"] = [12, 5]  
```


```python
chapter = '/ch10/'
filename1 = 'life_expectancy_birth.csv'
filename2 = 'milk_production.csv'
```


```python
path = Path(_base_location+chapter)
```


```python
path
```




    PosixPath('/Users/rahuladlakha/Documents/Github/Code_books/TimeSeriesAnalysis_Python/Datasets/Time-Series-Analysis-with-Python-Cookbook./datasets/ch10')




```python
life = read_dataset(path, filename1, 'year')
milk = read_dataset(path, filename2, 'month')
```


```python
fig, ax = plt.subplots(2, 1, figsize=(16,12))
life.plot(title = 'Annual Life Expectancy', legend=False, ax=ax[0])
milk.plot(title = 'Monthly Milk Production', legend=False, ax=ax[1])
```




    <AxesSubplot:title={'center':'Monthly Milk Production'}, xlabel='month'>




    
![png](TimeSeries_files/TimeSeries_538_1.png)
    


Milk production data, seasonal magnitudes and variations over time seem to be steady, indicating an additive nature.

For Univariate Modeling, we need to **split** the data into **test** and **train** datasets. Then, we need to train the models on the training datasets and use the test dataset to evaluate the model and compare the predictions.

Forecast that's created for the data that will bse used in training is called an **in-sample forecast**.<br>
Forecast for unseen data such as test set is called an **out-of-sample forecast**.

When evaluating different models, then we use out-of-sample or test sets.


```python
def split_data(data, test_split):
    l = len(data)
    t_idx = round(l*(1-test_split))
    train, test = data[ : t_idx], data[t_idx : ]
    print(f'train: {len(train)}, test: {len(test)}')
    return train, test
```


```python
test_split = 0.15
milk_train, milk_test = split_data(milk, test_split)
life_train, life_test = split_data(life, test_split)
```

    train: 143, test: 25
    train: 51, test: 9



```python
def check_stationarity(df):
    results = adfuller(df)[1:3]
    s = 'Non-Stationary'
    if results[0] <0.05:
        s = 'Stationary'
    print(f"'{s}\t p-value:{results[0]} \t lags:{results[1]}")
```

We will be using different models and different variation of models for univariate time series prediction. <br>
To compare these different models and set of best and optimal parameters for the models, creating a function **get_best_model**. <br>
The model will return winning model based on scoring criterion of your choice. We can use different criterion such as **RMSPE (Root Mean Squared Percentage Error), MSE (Mean Square Error), BIC (Bayesian Information Criteria)** and **AIC Akaike's Information Criteria** which will be the default parameter to evaluate metrics for each model.


```python
def best_model(score, c='AIC'):
    initial_score = score[0][c]
    best_model = 0
    for k, v in score.items():
        if v[c] < initial_score:
            initial_score = v[c]
            best_model = k
    
    print(f'Best Model: {best_model} with lowest {c} score: {initial_score}')
    return score[best_model]['model']
```

Once we have compared different models, it would be best to plot the forecast comparing to the actuals. This will be accomplished by **plot_forecast** function.


```python
def plot_forecast(model, start, train, test):
    forecast = pd.DataFrame(model.forecast(test.shape[0]),
                            index=test.index)
    ax = train.loc[start:].plot(style='--')
    test.plot(ax=ax)
    forecast.plot(ax=ax, style='-.')
    ax.legend(['orig_train', 'orig_test', 'forecast'])
    plt.show()
```

For Hyperparameter tuning we will use grid search. In grid search, you specify a combination of parameter values to train multiple models on each set and then evaluate the winning model using the **get_best_model** function.

To use a combination of parameters, let's create a **combinator utility function** that takes a list of parameter values and returns a Cartesian Product of these choices.

Example: Parameter list contains 3 possible values for three different parameters, in such a case, function will return a list containing 3x3 of 9 possible combinations.


```python
def combinator(items):
    combo = [i for i in product(*items)]
    return combo
```

#### I.1 Plotting ACF and PACF

To build statistical model such as AR, MA, ARMA, ARIMA or SARIMA, we need to determine the type of time series model that is most suitable for your data and the values for some of the required parameters, called orders. Also known as lag orders for AR (Auto Regressors) or MA (Moving Average) components.

**ARMA** - Autoregressive Moving Average model - $\Large{ARMA(p, q)}$ <br>
- p : the autoregressive order or AR(p)
- q : the moving average order or MA(q)

Hence ARMA model is combination of AR(p) and MA(q) model.

The core idea behind these models is built on the assumptions that the current value of a particular variable, $\Large{x}$, can be estimated from the past values of itself.

In Auto-Regressive Model - Order $\Large{p}$ or $\Large{AR(p)}$, we assume that the current value, $\Large{x_t}$, at time $\Large{t}$, can be estimated from its past values 
$\Large{(x_{t-1},x_{t-2}, ..., x_{t-p})}$ <br>
If $\Large{p=2}$, this mean we use two previous records $\Large{(x_{t-1},x_{t-2})}$ to predict $\Large{x_t}$. - Depending upon the granularity of your time series data, **p=2** can be 2 hours, 2 days, 2 weeks, 2 months..

**ARMA and ARIMA**

The difference between an **ARMA** and **ARIMA**, is in the stationary assumption.

An ARMA model assumes a stationary process, while an ARIMA model does not since it handles differencing.<br>
The **d** parameter in **ARIMA(p,d,q)** is for the differencing order.

An **ARIMA** model is a more **generalized model** since it can satisfy an ARMA model by making the **differencing factor d=0**. Hence, **ARIMA(1,0,1)** is **ARMA(1,1)**.

**PARAMETERS Vs. HYPERPARAMETERS**

When training an **ARIMA model**, the **outcome** will produce a **set of parameters** called **coefficients** – for example, a coefficient value for AR Lag 1 or sigma – that are estimated by the algorithm during the model training process and are used for making predictions. They are referred to as the **model's parameters**. 

On the other hand, the **(p, d, q) parameters** are the **ARIMA(p, q, d)** are orders for AR, differencing, and MA, respectively. These are called **hyperparameters**. They are set manually and influence the model parameters that are produced (for example, the coefficients). These **hyperparameters - can be tuned** using grid search, for example, to find the best set of values that produce the best model.

Identification of Lag Values for AR and MA Models is done through **ACF (Autocorrelation Function)** and **PACF (Partial Autocorrelation function)**

PACF and ACF plots are referred to as correlograms since the plots represent the correlation statistics.

PACF plot to estimate the AR order and the ACF plot to estimate the MA order.<br> Both the ACF and PACF plots show values that range from $-1$ to $1$ on the vertical axis (y-axis), while the horizontal axis (x-axis) indicates the size of the lag. A significant lag is any lag that goes outside the shaded confidence interval.

The correlation (for both ACF and PACF) at lag zero is always one (since it represents autocorrelation of the first observation on itself).

The statsmodels library provides two functions: acf_plot and pacf_plot. Let's explore.

Life Expectancy Data, is not stationary due to presence of long-term trend. In such cases we need to difference (detrend) the time series to make it stationary before applying the ACF and PACF plots.


```python
life.plot()
life_diff = life.diff().dropna()
life_diff.plot()
```




    <AxesSubplot:xlabel='year'>




    
![png](TimeSeries_files/TimeSeries_558_1.png)
    



    
![png](TimeSeries_files/TimeSeries_558_2.png)
    



```python
check_stationarity(life_diff)
```

    'Stationary	 p-value:1.1737760312328758e-13 	 lags:1



```python
# life_diff = life.diff().dropna()
fig, ax = plt.subplots(2,1, figsize=(12,8))
plot_acf(life_diff, zero=False, ax=ax[0])
plot_pacf(life_diff, zero=False, ax=ax[1])
plt.show()
```


    
![png](TimeSeries_files/TimeSeries_560_0.png)
    


**ACF** Plot - Lag 1 has significant spike, (significance is represented when a lag (vertical line) goes above or below the shaded area. The shaded area is the confidence interval, set to 95% by default). Only the first lag is significant, which is below the confidence interval. All the remaining lags are not significant.

This indicates a moving average of order one or MA(1).

**PACF** Plot - It shows gradual decay with oscillation. 

Generally, if PACF shows a gradual decay, it indicates a moving average model.

If you are using an ARMA or ARIMA model, it would be represented as ARMA(0, 1) once the data has been differenced to make it stationary, or ARIMA(0, 1, 1), indicating a first-order differencing with d=1. In both ARMA and ARIMA, the AR order is p=0, and the MA order is q=1.

ACF and PACF can be used with more complex datasets containing strong trends and seasonality.

Example, for Milk Production datasets, it has seasonal effect and a positive upward trend indicating a non-stationary time series. It is more suitable with SARIMA model. <br>In a model, you have two components; a non-seasonal and a seasonal component. In addition to AR and MA processes for the non-seasonal components p and q respectively; we will have AR and MA orders for the seasonal component, which are represented by upper case P and Q, respectively. **SARIMA(p,d,q)(P,D,Q,S)**.



```python
milk_diff = milk.diff().dropna()
```


```python
check_stationarity(milk_diff)
```

    'Stationary	 p-value:0.030068004001784394 	 lags:14



