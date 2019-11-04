Introduction

Finneas-Stockholm is a simple UI built from the AngularJS framework and served up through Node Express. The app makes calls to a free API provided by Quandl, retrieving financial data for the user to view. Currently, the app displays stock price data by date, but in the future, it will also display relevant economic data from other datasets on Quandl, such as interest rates on US treasuries and implied volatility term structures on the S&P 500. In other words, Quandl is a great resource and everyone should use it. 

<a class = "margin-left:30px" href = "http://www.quandl.com">USE QUANDL!</a>

The app simulates a portfolio manager where you can add stocks to your portfolio. Currently only price information is displayed, but I am working on allowing the user to set the number of shares and purchase date, which will then translate into a net profit/loss binding in the UI. What follows is a full schedule of features I have imagined for this app:

UPCOMING FEATURES:
1. Ability to set number of shares and purchase date.<br>
	<span class = "margin-left:10px">a. Automatic calculation of profit/loss according to current date and purchase date.<span><br>
	<span class = "margin-left:10px">b. Calculation of breakeven point</span><br>
2. Routing to Portfolio, Economic Data, Black-Scholes Analysis, Grafana Data tabs.<br>
	<span class = "margin-left:10px">a. Portfolio Tab</span><br>
		<span class = "margin-left:10px">I. Date<br>
		II. Purchase Date<br>
		III. Number of Days Held (date - purchase_date)<br>
		IV. Price<br>
		V. Purchase Price<br>
		VI. Shares<br>
		VII. Liquidity (=current_price*shares)
		VIII. Trade Price (=purchase_price*shares)
		IX. Profit/Loss (=shares*(current_price-purchase_price)
	b. Economic Data Tab
		I. US Treasury 3 Month, Continuous
			* Quotes Are Semi-Annual
		II. US Treasury 1 Year, Continuous 
			* Quotes Are Semi-Annual
		III. US Treasury 10 Year, Continuous
			* Quotes Are Semi-Annual
		IV. VIX Spot
		V. VIX 1 Month Future
		VI. VIX 2 Month Future
		VII. VIX 3 Month Future
	c. Black-Scholes Analysis Tab
		I. Input
			A. Observed: Spot, Interest, Dividend, Volatility (VIX)
			B. User: Strike, Expiration, Call/Put
		II. Output
			A. Black-Scholes Price Call/Put
			B. Greeks
			C. Breakeven Underlying Price
	d. Grafana Tab
		Notes: Will connect
3. OAuth2 Authentication/Authorization Flow And Features
	a. Storage of Users in Database
	b. Storage of User's Portfolios in Database
	c. Retrieval of Portfolio Upon Login

THOUGHTS

1. Architecture

Currently: 
Front-end AngularJS app accesses Quandl. Quandl API essentially acts as a back-end for front-end app. Technically, all calls are pointed back to Node Server which also serves up AngularJS app, hitting endpoints designated for redirects. These endpoints in turn send out requests to Quandl and return the response to the user. In other words, the user never directly accesses Quandl; Node essentially acts as a proxy (specifically, a request is received at a designated Node Express endpoint, a new request is formed via the npm package 'request' and then its response received, and that response is returned as the response to the original request.

This flow is set up to prevent the Quandl API key from being exposed to the user. The API key is stored on the Node Server and appended to the redirect in order to authenticate without the user's knowledge. 

Future:
Create proper backend that interfaces with database (MySQL?). Populate database with responses from Quandl. Set trigger for automatic update every morning. Have frontend talk to database through backend. Have user authenticate with backend before access to resources is granted (i.e., OAuth2, fool.)

Essentially, figure out how to implement this flow:

	Flow 1: USER- > FRONT END SERVER -> BACKEND SERVER -> DATABASE -> BACKEND SERVER -> FRONTEND SERVER-> USER
		Flow 1A: FRONT END SERVER -> BACKEND SERVER
			I: OAuth2 flow? Facebook/Google Authentication?
	Flow 2: TRIGGER MECHANISM (SERVER?) -> QUANDL SERVER-> TRIGGER MECHANISM (SERVER?) -> DATABASE
		Flow 2A Notes: How to trigger automatic http calls. Threads? Logstash?

2. Grafana Service

Once the backend is differentiated and the database is separated out from the frontend, Grafana will be able to interface with the database and provide visualizations of the equity and security data provided by Quandl. These visualizations can be embedded within iframes in the AngularJS HTML component templates. The Grafana tab will need to receive the user's portfolio and figure out a way of providing input to Grafana's api to retrieve the correct dataset from the database.