# value_investing
Program that pulls financials from Yahoo Finance and outputs a score in the value of a company and the quality of a company, ranked out of 100

import yfinance as yf

### This is how Yahoo Finance finds the company
company = input("Which company would you like me to evaluate? ").upper()
stock = yf.Ticker(company)

### Each company will be scored on 5 statistics designed to test the value of a company, i.e. is it worth more than it is trading at, with a max score being 20 and a minimum being 0
### The 5 stats are forward PE ratio, price to book ratio, forward peg ratio, ev/ebitda, and price to free cash flow

def value_investing():
    value_score = 0
    
    ### Essentially forward PE ratio is a metric that divides a company's current stock price by its projected EPS
    
    forward_pe_ratio = round(stock.info.get('forwardPE', 0), 2)
    print(f"Forward PE: {forward_pe_ratio}")
    if forward_pe_ratio < 0:
        value_score += 0
    elif forward_pe_ratio < 16:
        value_score += 20
    elif forward_pe_ratio < 18.5:
        value_score += 16
    elif forward_pe_ratio < 22:
        value_score += 12
    elif forward_pe_ratio < 24:
        value_score += 9
    elif forward_pe_ratio < 27:
        value_score += 7
    elif forward_pe_ratio < 33:
        value_score += 5
    elif forward_pe_ratio < 38:
        value_score += 3
    elif forward_pe_ratio < 50:
        value_score += 1
    else:
        value_score += 0

    price_book = stock.info.get('priceToBook')
    price_book = round(price_book, 2)
    print(f"Price to Book: {price_book}")


    ### Price to book ratio is calculated by dividing the company's stock price by all of its assets
  
    if price_book < 0:
        value_score += 0
    elif price_book < .75:
        value_score += 20
    elif price_book < .85:
        value_score += 18
    elif price_book < .925:
        value_score += 16
    elif price_book < 1:
        value_score += 14
    elif price_book < 1.2:
        value_score += 12
    elif price_book < 1.45:
        value_score += 11
    elif price_book < 1.7:
        value_score += 10
    elif price_book < 2:
        value_score += 8
    elif price_book < 3:
        value_score += 3
    else:
        value_score += 0



    ### This is VERY important!
    ### Yahoo Finance does not give an output for forward PEG ratio, and backwards PEG would be unfair to newly founded companies
    ### HOWEVER, Yahoo Finance does have PEG ratios on each company's individual profile
    ### I found the easiest way to extract PEG ratio is just to go to each profile manually and input it
    
    ### How to do this: Go to the company's Yfinance page: (https://finance.yahoo.com/quote/{put ticker here}/), scroll down to valuation measures, and near the middle is forward PEG

    ### If anyone has a better way to find it, please DM. However it is not in the standard Yfinance keys for whatever reason
    
    peg_ratio = float(input("What is the 5 year PEG ratio? "))

    if peg_ratio < 0:
        value_score += 0
    elif peg_ratio < .85:
        value_score += 20
    elif peg_ratio < .95:
        value_score += 17
    elif peg_ratio < 1.2:
        value_score += 15
    elif peg_ratio < 1.5:
        value_score += 13
    elif peg_ratio < 1.7:
        value_score += 11
    elif peg_ratio < 2:
        value_score += 9
    elif peg_ratio < 2.5:
        value_score += 5
    elif peg_ratio < 3:
        value_score += 3
    else:
        value_score += 0
    
    ev_ebitda = stock.info.get('enterpriseToEbitda')
    print(f"EV/EBITDA: {ev_ebitda}")

    ### EV/EBITDA compares a company's enterprise value by its total earnings before interest/taxes/debt/amortization


    if ev_ebitda is not None: 
        if ev_ebitda < 0:
            value_score += 0
        elif ev_ebitda < 8:
            value_score += 20
        elif ev_ebitda < 9:
            value_score += 18
        elif ev_ebitda < 10:
            value_score += 16
        elif ev_ebitda < 11.5:
            value_score += 14
        elif ev_ebitda < 13:
            value_score += 12
        elif ev_ebitda < 15:
            value_score += 10
        elif ev_ebitda < 17.5:
            value_score += 7
        elif ev_ebitda < 19:
            value_score += 4
        elif ev_ebitda < 22:
            value_score += 2
        else:
            value_score += 0
    else: 
        value_score += 0

    price = stock.info.get('currentPrice')
    shares = stock.info.get('sharesOutstanding')
    cashflow = stock.info.get('freeCashflow')

    if cashflow is not None:

        market_cap = price * shares
        p_fcf = round(market_cap / cashflow, 2)
        print(f"P/FCF: {p_fcf}")
    
    
        if p_fcf < 0:
            value_score += 0
        elif p_fcf < 16:
            value_score += 20
        elif p_fcf < 18:
            value_score += 17
        elif p_fcf < 19.5:
            value_score += 14
        elif p_fcf < 21:
            value_score += 12
        elif p_fcf < 24:
            value_score += 8
        elif p_fcf < 28:
            value_score += 4
        elif p_fcf < 32:
            value_score += 3
        elif p_fcf < 36:
            value_score += 1
        else:
            value_score += 0
    else:
        print("No P/FCF")
        value_score = round(value_score * 1.25,0)


    ### P/FCF frequently does not work, even for bigger companies, so if it doesnt work I just adjust the value score by essentially making the value score out of 80, then scaling it up   to 100
    
    
    print(f"            (value){value_score} out of 100")


    ### The highest value scores I have found are CNC with 97, PFE with 88, and outside of the healthcare sector, NEM with 70

def quality_investing():
    quality_score = 0
    roa = stock.info.get('returnOnAssets')

    ### ROA is a statistic that measures a company's profitability by checking how profitable it is on its assets
    
    
    if roa is not None:
        roa = 100 * roa
    else:
        roa = "N/A"

    print(f"ROA: {roa}")


    if roa == "N/A":
        quality_score += 0

    elif roa > 20:
        quality_score += 20
    elif roa > 17:
        quality_score += 18
    elif roa > 14:
        quality_score += 16
    elif roa > 12: 
        quality_score += 13
    elif roa > 9:
        quality_score += 10
    elif roa > 7:
        quality_score += 8
    elif roa > 5:
        quality_score += 5
    elif roa > 3:
        quality_score += 3
    elif roa > 1:
        quality_score += 2
    elif roa > 0:
        quality_score += 1
    else:
        quality_score += 0
    


    income_statement = stock.income_stmt

    revenue_series = income_statement.loc['Total Revenue']

    years = list(revenue_series.keys())
    latest_revenue = revenue_series[years[0]]
    four_years_ago_revenue = revenue_series[years[3]]

    ### This is the only one that falls apart if a company IPO'd in the last 4 years. The reason that it is 4 is that it inexplicably fails for the year 2020. 

    ### CAGR calculates the average annual rate an investment grows over a period of longer than a year
    
    if four_years_ago_revenue > 0:
        cagr = round(((latest_revenue / four_years_ago_revenue) ** (1 / 4)) - 1, 2)
    else:
        cagr = "N/A"

    print(f"CAGR: {cagr}")
    
    if cagr == "N/A":
        quality_score += 0
    elif cagr >= 0.30:
        quality_score += 20
    elif cagr >= 0.25:
        quality_score += 18
    elif cagr >= 0.20:
        quality_score += 16
    elif cagr >= 0.17:
        quality_score += 14
    elif cagr >= 0.14:
        quality_score += 12
    elif cagr >= 0.12:
        quality_score += 10
    elif cagr >= 0.10:
        quality_score += 8
    elif cagr >= 0.08:
        quality_score += 6
    elif cagr >= 0.06:
        quality_score += 4
    elif cagr >= 0.04:
        quality_score += 2
    else:
        quality_score += 0
    

    total_debt = stock.info.get('totalDebt')
    ebitda = stock.info.get('ebitda')

    ### Debt/EBITDA is a stat that compares a company's debt and total earnings, essentially asking how long it would take a company to pay off its debt if it used all of its EBITDA
    ### This is the only one where a company can score over 20 for having zero debt

    if total_debt is None or ebitda is None or ebitda == 0:
        print("Debt/EBITDA: N/A")
    else:
        debt_to_ebitda = round(total_debt / ebitda, 2)
        print(f"Debt/EBITDA: {debt_to_ebitda}")
    
        if debt_to_ebitda == 0:
            quality_score += 25
        elif debt_to_ebitda < 0:
            quality_score += 0
        elif debt_to_ebitda < 0.5:
            quality_score += 20
        elif debt_to_ebitda < 1:
            quality_score += 18
        elif debt_to_ebitda < 1.5:
            quality_score += 16
        elif debt_to_ebitda < 2:
            quality_score += 13
        elif debt_to_ebitda < 2.5:
            quality_score += 10
        elif debt_to_ebitda < 3:
            quality_score += 7
        elif debt_to_ebitda < 4:
            quality_score += 4
        elif debt_to_ebitda < 5:
            quality_score += 2
        else:
            quality_score += 0

    roe = stock.info.get('returnOnAssets')
    if roe is not None: 
        roe = round(roe, 2)
    else:
        roe = "N/A"

    ### ROE is return on equity, dividing a company's net income by shareholder equity

    print(f"ROE: {roe}")

    if roe == "N/A":
        roe_score = 0
    elif roe >= 0.30:
        roe_score = 20
    elif roe >= 0.25:
        roe_score = 18
    elif roe >= 0.20:
        roe_score = 16
    elif roe >= 0.17:
        roe_score = 14
    elif roe >= 0.14:
        roe_score = 12
    elif roe >= 0.12:
        roe_score = 10
    elif roe >= 0.10:
        roe_score = 8
    elif roe >= 0.08:
        roe_score = 6
    elif roe >= 0.06:
        roe_score = 4
    elif roe >= 0.04:
        roe_score = 2
    else:
        roe_score = 0

    quality_score += roe_score


    ### FCF Margin is a stat that measures how well a company turns its revenue into free cash flow

    cashflow = stock.info.get('freeCashflow')
    revenue = stock.info.get('totalRevenue')

    if cashflow is not None and revenue is not None:
        fcf_margin = round(cashflow / revenue, 2)
        print(f"FCF Margin: {fcf_margin}")

        if fcf_margin > .25:
            quality_score += 20
        elif fcf_margin > .22:
            quality_score += 18
        elif fcf_margin > .2:
            quality_score += 15
        elif fcf_margin > .18:
            quality_score += 12
        elif fcf_margin > .15:
            quality_score += 10
        elif fcf_margin > .12:
            quality_score += 8
        elif fcf_margin > .1:
            quality_score += 6
        elif fcf_margin > .7:
            quality_score += 4
        elif fcf_margin > .5:
            quality_score += 2
        else:
            quality_score += 0
    

    else:
        quality_score * 1.25
        print("FCF Margin: N/A")

    ### The highest quality score I have found is NVIDIA at a perfect 100, Arista Networks is the 2nd highest I've found at 87. 

    print(f"           (quality){quality_score} out of 100")
    return quality_score

value_investing()
quality_investing()
