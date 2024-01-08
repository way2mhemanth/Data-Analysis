# Lets import some libraries
import pandas as pd
import matplotlib.pyplot as plt

# lets load the dataset- change the path to your file location.
s=pd.read_csv('/workspaces/Data-Analysis/ADANI_ENTERPRISES.CSV')

# set the date index as index of the dataframe.
s.set_index('Date',inplace=True)

# Give the start and end date for historical stock data. 
startdate='04-01-2021'
enddate='02-01-2023'

# Slice the Stock according the date.
Stock=s[startdate:enddate]

'''
In buying or selling stock, we use long and short as a way to communicate the status of stock.
Long - We are buying and holding the stock.
Short - We are selling the stock.

Moving average algorithm will process the signal and output 2 signals in our case
10 day moving average: This is fast signal. As it incorporates change in recent past i.e 10 days. This will show short term trend
50 day moving average: This is slow signal. As it incorporates change in past  50 days. This will show long-term trend
'''
# Create a moving average for the data. Here we are finding the moving average for 10 days and 50 days. 
# We are focusing only on Close value of the stock data for simplicity purpose.
Stock['MA10']=Stock['Close'].rolling(10).mean()
Stock['MA50']=Stock['Close'].rolling(50).mean()

# Drop Nan values in the stock data.
Stock=Stock.dropna()

# We will buy stock when fast signal value is higher than slow signal. This indicates the stock is going to rise in near future.
# To do this, We create a new coloumn in Stock. The name is Shares and it has either 1 or 0. 1 means long position and 0 means short position.
# This is a list comprehension technique
Stock["Shares"]=[1 if Stock.loc[i,'MA10']>Stock.loc[i,'MA50'] else 0 for i in Stock.index]

# Now lets calculate the profit we made using this algorithm.
# Find the profit for each day based on today and tomarrow's value and then cumulate the gross profit.
# After that find the profit percent over the time.
Stock['Close1']=Stock['Close'].shift(-1)

Stock['Profit']=[Stock.loc[i,'Close1']-Stock.loc[i,'Close'] if Stock.loc[i,'Shares']==1 else 0 for i in Stock.index]

Stock["cum_profit"]=Stock["Profit"].cumsum()

Stock["Profit_cent"]=Stock["cum_profit"]/Stock["Close"]*100

# now lets show all of this in graph.
Stock["Profit"].plot(y="daily",color="red")

Stock["cum_profit"].plot(y="cum",color="Green")

plt.show()

! [daily profit and cummulative profit] (https://github.com/way2mhemanth/Stock_market_analysis/blob/main/ma_10_50.png)

Stock["Close"].plot()

Stock["MA10"].plot(color="red")

Stock["MA50"].plot(color="green")

plt.show()

