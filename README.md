Project 2
================
Ruben Sowah
10-09-2022



```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE, message = F, warning = F, cache = T)
```



```{r, echo = F, eval = F}
rmarkdown::render("project2 rmd.Rmd",
                  output_format = "github_document",
                  output_file = "README.md",
                  output_options = list(
                    html_preview = FALSE, toc = TRUE, toc_depth = 2, toc_float = TRUE)
)
```

```{r, echo=FALSE}
#knitr::opts_chunk$set(fig.path = "README.md")
```
# Goal 
  Our goal is to create a vignette, which can be thought of as a long-form guide or a small illustrative sketch. Vignettes are explanations of concepts packages, etc with code and output inter-weaved. 
  
  In this project, we will create a vignette to contact the Financial data API using functions to query, parse, and return well-structured data for exploratory data analysis.

# Requirements
The following packages were used in the creation of this vignette:  

  * tidyverse: a great package for data science and data manipulation
  * httr: used to get access to the API
  * jsonlite: useful for converting JSON files into R objects
  * ggplot2: used to create sophisticated visual plots and graphics  
  
```{r, message=F, warning=F}
require(tidyverse)
require(httr)
require(jsonlite)
require(ggplot2)
```

```{r,eval=F, echo = F}
dat = GET("https://api.polygon.io/v2/aggs/ticker/AAPL/range/1/day/2020-06-01/2020-06-17?apiKey=GMfXYkoPNPB30oC66QYexyFofBFhe1_t")
str(mydata,max.level=1)
parsed <- fromJSON(rawToChar(dat$content))
str(parsed, max.level = 1)
as.data.frame(parsed)
fromJSON(readLines('https://api.polygon.io/v2/aggs/ticker/AAPL/range/1/day/2020-06-01/2020-06-17?apiKey=GMfXYkoPNPB30oC66QYexyFofBFhe1_t'))
```

# Functions to Interact With the API  
  * **stock_agg**  
  
I wrote a function to get aggregate bars for a stock over a given date range in custom time window sizes. Here the multiplier is set to 1 and the time span is daytime. The user will be prompted to enter  the stock of his/her choice as well as the date range of the aggregate window.  

```{r}
stock_agg <- function(stocksTicker,multiplier=1,timespan ='day',from,to,...){
  stock_call <- paste0("https://api.polygon.io/v2/aggs/ticker/",stocksTicker,"/range/",multiplier,'/',timespan,'/',from,'/',to,'?apiKey=GMfXYkoPNPB30oC66QYexyFofBFhe1_t')
# Convert the data into a JSON form and print it as a tibble
stock_parsed <- fromJSON(readLines(stock_call))
stock_result <- as_tibble(stock_parsed$results)
return(stock_result)
}
# Checking out the function
 stock_agg(stocksTicker = 'AAPL',from = '2021-10-11',to ='2022-10-09')
```

  * **forex_agg**
  
 This function allows the user to get aggregate bars for a forex pair over a given date range in custom time window sizes. The user is prompted to enter a forex of his/her choice as well as the start and end date of the aggregate  window.
 
```{r}
forex_agg <- function(forexTicker,multiplier=1,timespan ='day',from,to,...){
  forex_call <- paste0("https://api.polygon.io/v2/aggs/ticker/",forexTicker,"/range/",multiplier,'/',timespan,'/',from,'/',to,'?apiKey=GMfXYkoPNPB30oC66QYexyFofBFhe1_t')
  
  
# Convert the data into JSON format and print it as a tibble
forex_parsed <- fromJSON(readLines(forex_call))
forex_result <- as_tibble(forex_parsed$results)
return(forex_result)
}
# Checking out the fucntion
forex_agg(forexTicker = 'C:EURUSD',from = '2021-11-10',to ='2022-10-09')
```


  * **stock_grouped**  
  
With this function, one can get the daily open, high, low, and close (OHLC) for the entire stocks/equities markets. The user is required to enter a start date of the aggregate window.  

```{r}
stock_grouped <- function(date,adjusted='true',include_otc='true'){
  call <- paste0('https://api.polygon.io/v2/aggs/grouped/locale/us/market/stocks/',date,'?adjusted=true','&apiKey=GMfXYkoPNPB30oC66QYexyFofBFhe1_t')
 
# Convert the data into JSON format and print it as a tibble
  parsed <- fromJSON(readLines(call))
  result <- as_tibble(parsed$results)
  return(result)
}
# Checking out the function
stock_grouped(date = '2021-10-01',adjusted = 'false',include_otc = 'false')
 
```


  * **crypto_grouped**  
  
I wrote a function that allows the user to get the daily open, high, low, and close (OHLC) for the entire cryptocurrency markets, by just entering the beginning date of the aggregate window.

```{r}
crypto_grouped <- function(date,adjusted='true'){
  call <- paste0('https://api.polygon.io/v2/aggs/grouped/locale/us/market/crypto/',date,'?adjusted=true','&apiKey=GMfXYkoPNPB30oC66QYexyFofBFhe1_t')
 
# Convert the data into JSON format and print it as a tibble
  parsed <- fromJSON(readLines(call))
  result <- as_tibble(parsed$results)
  return(result)
}
# Checking out the function
crypto_grouped(date = '2021-10-01',adjusted = 'true')
```

  * **stock_Daily_OC**  
  
This function allows the user to get the open, close and after hours prices of a stock symbol on a certain date. The name of the stock and the date of the open/close is all that is required.  

```{r}
stock_Daily_OC <- function(stocksTicker, date,adjusted = 'true'){
  
  call <- paste0('https://api.polygon.io/v1/open-close/',stocksTicker,'/',date,'?adjusted=true','&apiKey=GMfXYkoPNPB30oC66QYexyFofBFhe1_t')
  
# Read the data in JSON format  
  fromJSON(readLines(call))
}
# Checking out the function
stock_Daily_OC(stocksTicker = 'AAPL', date = '2020-10-14')
```


  * **crypto_Daily_OC**  
  
With this function, the user can get the open, close prices of a cryptocurrency symbol on a certain day.  

```{r}
crypto_Daily_OC <- function(from, to, date,...){
  
  call <- paste0('https://api.polygon.io/v1/open-close/crypto/',from,'/',to,'/',date,'?adjusted=true','&apiKey=GMfXYkoPNPB30oC66QYexyFofBFhe1_t')
   
 # Read the data into JSON format
  fromJSON(readLines(call))
}
# Checking out the function
crypto_Daily_OC(from = 'BTC',to = 'USD', date = '2020-10-14',adjusted = F)
```


# Data Exploration  
  This part  of the vignette constitutes in getting two data from our function calls above, combine them in one, and perform basic exploratory data analysis.  

  * **Pulling and Combining data**  
  
 We will get the apple stock aggregate data for a range date of one year and combine it with the grouped daily data of the entire stock market. This combination is feasible since both data have mostly common variables.  
 
```{r}
data_agg <- stock_agg(stocksTicker = 'AAPL',from = '2021-10-11',to ='2022-10-09')
data_grouped <- stock_grouped(date = '2021-10-11',adjusted,include_otc )
# Combine the datas using a full join, and remove all missing values
mydata <- full_join(data_grouped,data_agg) %>%
          na.omit()
# Assign new names to the variables.
colnames(mydata) <- c('Symbol', 'tradingVolume','weightedVolPrice','openPrice','closePrice','highestPrice','lowestPrice','timeStamp','transactions')
print(mydata)
```

  * **Creating new variables that are functions of other variables**  
  
  I will create three variables. The first variable is *avgPrice* which is the average price of the lowest and highest prices of the symbol.
  The second variable created is *transType* which is the type of transactions. A low number of transactions is one below 70000, a moderate type of transactions is between 70000 and 160000, and a high number of transactions is one above 160000.  
  The third variable is the price level. An average stock price less than 100 is classified as 0, whereas an average stock price higher than 100 is classified as 1.
  
```{r}
mydata <- mydata %>%
          mutate(avgPrice = (highestPrice + lowestPrice)/2,
                 transType = ifelse(transactions < 70000,'low',
                                ifelse(transactions >=70000 & transactions <= 160000, 'moderate','high')),
                 priceLevel = ifelse(avgPrice < 100, '0','1'))
# Coerce the transType and priceLevel variables into  factors
mydata[11] <- as.factor(mydata$transType)
mydata[12] <- factor(mydata$priceLevel, levels = c(0,1), labels = c('cheap','expensive'))
print(mydata, width = 100)
```
  
  * **Contingency tables**
  
Some contingency tables will be created for the categorical variables of our data, *transType* and *priceLevel*

```{r}
# one way tables
table(mydata$transType)
table(mydata$priceLevel)
# two way table
table(mydata$transType, mydata$priceLevel)
```
 Based on the summary of the transactions type , there are 23 high transactions, 79 moderate transactions,and 10402 transactions that are below 70000.  
 
 Based on the priceLevel table, there are 9602 stocks with an average price less than 100 dollars and 902 stocks with an average price greater than 100 dollars.
 
 The first row and first column of the two way contingency table shows that there are 13 high transactions that have an average price lower than 100 dollars.  
 
 * **Nummerical Summaries for some quantitative variables**  
   
    The first summary is the average of the weighted volume price and the standard deviation of the trading volume on each setting of the type of transactions.  
    The second summary is the average and median of the trading volume, the variance of the open price on each setting of the average price level.  
    The third summary is the the interquartile range of the close price of the stocks, the mean and standard deviation of the avgPrice variable on each setting of the price level and the type of transactions.
 
```{r}
summary1 <- mydata %>%
            group_by(transType) %>%
            summarize(average = mean(weightedVolPrice), Voldeviation = sd(tradingVolume)); summary1
summary2 <- mydata %>%
            group_by(priceLevel) %>%
            summarize(averageVol = mean(tradingVolume), medVol = median(tradingVolume), OPricevariance = var(openPrice)); summary2
summary3 <- mydata %>%
            group_by(priceLevel,transType) %>%
            summarize(range = IQR(closePrice), average = mean(avgPrice), dev = sd(avgPrice)); summary3
```
 
 * **Visual PLots**  
 
    * **Scatter plot of the trading volume of the stocks and the number of transactions**  
  
  Scatter plots are usually used to check if there is a relationship between two variables. Below, we got the scatter plot of the volume of AAPL stocks trading and the number of AAPL transactions. We notice on the graph a correlation of approximately 74 percent. We can affirm that those two variables are significantly correlated.
    
```{r}
g <- ggplot(mydata, aes(x= tradingVolume, y= transactions))
g + geom_point(aes(fill = avgPrice), color = 'red') +
      labs(title = 'Volume vs Number of transactions')
```
 
  * **Graphical summary of different type of transactions, categorized by the level price of the stocks**
```{r}
g <- ggplot(mydata, aes(x= transType))
g + geom_bar(aes(fill = priceLevel), position = 'dodge')+
  labs(x = 'Type of transactions') +
  labs(title = 'Type of transaction by stock price level') +
  facet_wrap (~priceLevel, labeller = label_both)
```

The above plot is a bar plot, used to summarize categorical variables. We summarized the type of AAPL stock transactions ranging from low transaction, to high transaction. A transaction can be defined as the buying or selling of the stocks. Each transaction is then classified according to the average price of the stocks, whether the average price is considered cheap or expensive.

  * **Graphical summary of stocks price levels**
  
  Here is another bar plot that summarizes the AAPL stock by average price level. There are almost 10000 cheap stocks and less than 3000 expensive ones. 
```{r}
g <- ggplot(mydata, aes(x= priceLevel))
g + geom_bar(aes(fill = priceLevel ), position = 'dodge')+
  labs(x = 'Price level of the stocks') + 
  labs(title = 'Bar plot of the average stocks price levels')+
  coord_flip()
  
```

  * **Boxplot of the transaction variable for each level of th e transaction type**
  
  Boxplots are a way of summarizing the five number of a quantitative data graphically. In the below figure, we are getting the five number summary(minimum, first quartile, median, third quartile, and maximum) of the amount of AAPL transactions, and grouped by the type of transaction(low, medium, or high).  
  The plots appear to be normally distributed,and we can notice the presence of an outlier on the high transaction boxplot.
  
```{r}
g <- ggplot(mydata, aes(x = transType, y = transactions))
g + geom_boxplot()+
  geom_point(aes(col = transType), position = 'jitter')+
  labs(title = 'Boxplot for the number of transactions')
```


* **Histogram of the number of AAPL transactions**
```{r}
g <- ggplot(mydata, aes(x= transactions))
g + geom_histogram(bins = 20, fill = 'blue', color = 5)+
   labs(title = 'Histogram of the number of transactions')
  
```

The above histogram represents the summary of the number of transactions of the AAPL stock. The histogram is highly skewed to the right.  

# Wrap-Up  

To summarize everything I did in this vignette, I built functions to interact with some of the Financial API's endpoints, retrieved and combined some data, then perform some basic exploratory data analysis. 
 
