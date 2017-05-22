I recently needed to obtain some stock price data for student projects, and found that the R package [`quantmod`](https://cran.r-project.org/web/packages/quantmod/index.html) does this really nicely and easily.  The following R code downloads daily opening, closing, high, low, and trading volumes for various stocks (a mixture of randomly and non-randomly chosen ones, but you can change these if you know the stock symbol).  The default source for the data is yahoo finance, but that can be changed within the `getSymbols` function.

```
library(quantmod)
# download financial data
getSymbols("ASX")  # advanced semiconductor engineering
getSymbols("AAPL") # apple
getSymbols("AMZN") # amazon
getSymbols("AZN")  # astrazeneca
getSymbols("BP")   # british petroleum
getSymbols("C")    # citigroup
getSymbols("CDE")  # couer mining
getSymbols("DAL")  # delta airlines
getSymbols("DPZ")  # domino's pizza
getSymbols("EQY")  # equity one
getSymbols("F")    # ford
getSymbols("GIL")  # gidlan activewear
getSymbols("GOOGL")# google
getSymbols("HOT")  # starwood hotesl and resorts worldwide
getSymbols("JPM")  # JP Morgan
getSymbols("K")    # kellogs
getSymbols("KO")   # coca-cola
getSymbols("M")    # Macy's Inc
getSymbols("MSFT") # microsoft
getSymbols("NOK")  # nokia
getSymbols("PG")   # procter-gamble
getSymbols("RBS")  # royal bank of scotland
getSymbols("SAM")  # boston beer company
getSymbols("SPGI") # S&P Global inc
getSymbols("T")    # AT&T
getSymbols("TEF")  # Telefonica
getSymbols("UA")   # under armour
getSymbols("V")    # visa inc
getSymbols("WMT")  # walmart
getSymbols("WHR")  # whirlpool inc
getSymbols("XIN")  # xinyuan real estate co ltd

all_stocks <- list(ASX, AAPL, AMZN, AZN, BP, C, CDE, DAL, DPZ, EQY, F, GIL, GOOGL, HOT, JPM, K, KO, M, MSFT, NOK, PG, RBS, SAM, SPGI, T, TEF, UA, V, WMT, WHR, XIN)

all_stocks <- lapply(all_stocks, Cl)
```

This final line creates a list all_stocks that contains only the closing prices (and dates) for each stock.  Finally, to ensure the stock data all have the same start and end dates, we can truncate each time series at a common starting point:

```
xtc <- function(XTS.obj, start_date = "2010-01-04"){
   dts <- index(XTS.obj)
   strt <- which(dts == start_date)
   if(length(strt) == 0){
     out <- 0
     } else {
     out <- XTS.obj[-c(1:(strt - 1))]
   }
 out
}
stocks_trunc <- lapply(all_stocks, xtc)
stock_data   <- as.matrix(Reduce("cbind", stocks_trunc))
```

When a stock doesn’t go back as far your start date (make sure this is a valid trading day), this is indicated by `xtc` returning 0 rather than a vector of prices.  And that’s it….  `stock_data` is a matrix containing dates, and closing prices with each of the requested stocks, ready to go for analysis!