# SmallBizHack Sydney
**Welcome to SmallBizHack Sydney 2018!** Here you will find useful information and sample code to complete your app. 

* [The challenge](#the-challenge)
* [APIs at your disposal](#apis-at-your-disposal)
* [Intro to the QuickBooks APIs](#intro-to-the-quickbooks-apis)
* [Creating your QuickBooks app](#creating-your-quickbooks-app)
* [Setting up OAuth 2.0 access](#setting-up-oauth-20-access)
* Hello QuickBooks!
    * [Testing the API with Postman](#testing-the-api-with-postman)
    * [Implementing the API with our sample code](#implementing-the-api-with-our-sample-code)
* Get notified when data changes
    * [Webhooks](#webhooks)
    * [Change data capture (CDC)](#change-data-capture-cdc)

    

# The challenge
**Create a solution that saves a small business time or money**

# APIs at your disposal
This hack is all about creating new innovations that help small businesses. We encourage you to use the APIs below _(HINT HINT: there are special prizes for the teams who best utilise these APIs)_, but you're free to use any other APIs and frameworks to bring your app to life. 

* For **QuickBooks API**, you're in the right place. Read on below!
* For **Telstra API**, head to [dev.telstra.com](https://dev.telstra.com/)
* For **Google cloud API**, head to [cloud.google.com/apis](https://cloud.google.com/apis/)
* For **PayPal API**, head to [developer.paypal.com](https://developer.paypal.com/)

# Intro to the QuickBooks APIs
Every small business needs to track their income, expense and profits, pay employees or lodge tax. They use an accounting application like QuickBooks to automate these painful tasks. QuickBooks is designed as an open ecosystem for small businesses, so YOU can build an app which hooks onto QuickBooks, to solve the myriad of other painpoints associated with running a business. 

Once your app is granted access to their QuickBooks account by a business owner, you can consume a rich set of financial data via the QuickBooks acccounting APIs. You can use this data and the available  APIs to to help them run their business more efficiently. 

Common tasks performed by the QuickBooks API include:
* Manage customers and vendors databases 
* Manage sales-side transactions (invoices, sales receipt) and purchase-side (bills, expenses) transactions.
* Retrieve reports: profit and loss, outstanding invoices...
* Manage product inventory

Check the [API reference](https://developer.intuit.com/docs/api/accounting) for the complete list of APIs. Do also check our app marketplace [apps.com](https://www.apps.com) for example of use cases and apps which use the QuickBooks APIs.


# Creating your QuickBooks app

1. Sign up for an account at [https://developer.intuit.com](https://developer.intuit.com)

2. Click on **my apps** and choose the **Just start coding** option. Select access to the **accounting** API and click **create app** ![alt-text](https://intuitdeveloper.github.io/assets/img/create-app.png "create your QuickBooks app") 

3. You should now be greeted by your QuickBooks app dashboard. Head over the **Keys** section and note down your `client Id` and `client secret` ![alt-text](https://intuitdeveloper.github.io/assets/img/get-your-keys.jpg "Get your app keys") 

4. Create a [Sandbox company](https://developer.intuit.com/v2/ui#/sandbox). Select `Australia` in the drop down country list and click **add** to create a AU small business account with dummy data. Take a moment to login your sandbox account and play around the QuickBooks application to understand how it works. ![alt-text](https://intuitdeveloper.github.io/assets/img/create-sandbox.jpg "Create sandbox account") 

# Setting up OAuth 2.0 access

If you are not familiar with OAuth 2.0, check [understand OAuth in 5 mins](https://aaronparecki.com/oauth-2-simplified/#roles)

There are available libraries to generate OAuth 2.0 tokens. For simplicty  we'll show you how to manually generate the token using our **OAuth playground**. You can then make an API call by just plugging the obtained OAuth 2.0 token in Postman or our sample codes.

1. Head over to the [OAuth playground](https://developer.intuit.com/v2/ui#/playground). Select `Accounting` for the scope.

2. Click on **Get authorization code**. You will be presented with an authorisation dialogue. Select your sandbox company and click **connect**. ![alt-text](https://intuitdeveloper.github.io/assets/img/oauth-playground-connect.jpg "Authorization dialogue") 

3. Back in the OAuth playground, note down the `Realm Id`. This is a unique identifier for your Sandbox QuickBooks account which you will need later on.  We sometimes refer to `it as *Company Id*

4. Now click on **Get Token** to exhange the authorization code for your `access_token` and `refresh_token`. If all goes well you should be presented with a response like below:

```json
{
  "refresh_token": "xxxxxxxx",
  "access_token": "xxxxxxxx",
  "expires_in": 3600,
  "x_refresh_token_expires_in": 8726400,
  "id_token": "xxxxxxxx",
  "token_type": "bearer"
}
```

Here's a run down of the OAuth response from QuickBooks:

* `access_token`: *token you will add in the authorization header of every API call to access data from your sandbox company. It is short lived: 1 hour. Can be refreshed before each call by calling the refresh token endpoint.*
* `refresh_token`: *Required to acquire a short lived `access token`. These last 100 days, but whenever our token endpoint returns a new one for the same company, start using it instead for subsequent calls. If the token is lost, generate a new refresh token by re-authorising your app.*
* `id_token`: *JSON web token containing information about the user and company which authorised your app. The token is base 64 encoded. You can head to https://jwt.io/ to decode it and check what is in there.*

# Hello QuickBooks!

## Testing the API with Postman

The Postman client is  a great way to navigate the QuickBooks API before you get your hands deep into into your code. You can skip and directly implement the QuickBook API using the sample code provided for this Hackathon - see section [Implementing the API with our sample code](#implementing-the-api-with-our-sample-code)

**TIP**: There is a handy code generator in POSTMAN which will generate code snippets in most available laguages. ![alt-text](https://intuitdeveloper.github.io/assets/img/postman-code-snippets.jpg "Postman code snippets") 

1. Download the Postman client [www.getpostman.com](https://www.getpostman.com/)

2. Go to our [QuickBooks postman collection page](https://developer.intuit.com/docs/00_quickbooks_online/2_build/20_explore_the_quickbooks_online_api/20_postman) and import the API collection into Postman by clicking **Run in Postman** (make sure to select the first option which uses OAuth 2.0 authorization)

3. Plugin your company Id in the postman environment variable. ![alt-text](https://intuitdeveloper.github.io/assets/img/postman-variables.jpg "Postman environment variables") 

**TIP**: Change the `UserAgent` to something unique (your team name) so your requests can be tracked by Intuit Engineers to help debug your code if necessary

4. Now paste the Access Token from the OAuth playground ![alt-text](https://intuitdeveloper.github.io/assets/img/postman-update-access-token.jpg "Postman access token") 

5. For this example, we are doing a data query using the QuickBooks `query` endpoint against the `customer` entity. Click on the **customer** folder in the Postman explorer panel, and select the **Customer-ReadAll** request.

     We want to get a list of all the customers that owe my small business client money! 
     
     **NOTE**: The query API uses SQL like queries. See details of the [query API](https://developer.intuit.com/docs/00_quickbooks_online/2_build/20_explore_the_quickbooks_online_api/50_data_queries). We will soon be launching a GraphQL version of the API which will improve performance and code efficiency_). 
     
     In the **body** tab, change the SQL query to:

    ```SELECT * FROM customer WHERE Balance > '0.0'```
 
6. Click **Send**. You should get back a list of customers who have unpaid balance. ![alt-text](https://intuitdeveloper.github.io/assets/img/postman-sample-response.jpg "Postman access token") 


## Implementing the API with our sample code

Samples are available in the following languages, use the readme within the individual language folder for additional instructions on how to run the sample and plugin the OAUth 2.0 token.

* [DotNet](https://github.com/IntuitDeveloper/HackathonSamples/tree/master/dotnet/SampleApp_hackathon)
* [Java](https://github.com/IntuitDeveloper/HackathonSamples/tree/master/java)
* [Nodejs](https://github.com/IntuitDeveloper/HackathonSamples/tree/master/nodejs)
* [PHP](https://github.com/IntuitDeveloper/HackathonSamples/blob/master/php)
* [Python](https://github.com/IntuitDeveloper/HackathonSamples/blob/master/python)


# Get notified when data changes

To track data changes there are different mechanisms:
* Change data capture (CDC): run a periodic query with date filter to download what changed since your last run.
* Webhooks: register a listener for change events 


## Webhooks

With webhooks enabled for your application, Intuit will notify your endpoint within 5 mins of a
change for all company ids you are tracking. 

To configure your local machine to receive webhooks events: 
1. Download & install 8.0 or higher version of nodejs from [here](https://nodejs.org/en/download/)
2. Start the local webserver, this server will log all incoming traffic to console. `node sink.js`
3. Make your webserver public using [ngrok](https://ngrok.com/). Install ngrok & create a tunnel between ngrok: `ngrok http 3000`
4. Go to webhooks section for your app on developer.intuit.com and update the address to ngrok
supplied dns (make sure to sure the https version) in the “Developer Webhooks” section. Also
subscribe to changes to all entities
5. Change some data.
6. Wait for 5 mins to see a log on your webserver’s console

## Change data capture (CDC)

Change data capture is the second mechanism for downloading changes to data in QuickBooks
since you last checked. You will need to track the last date you called this API. In request
params you can provide both a list of entities & last check date.








