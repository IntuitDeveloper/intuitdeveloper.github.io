# SmallBizHack Sydney
**Welcome to SmallBizHack Sydney 2018!** Here you will find useful information and sample code to complete your app. 

* [The challenge](#the-challenge)
* [APIs at your disposal](#apis-at-your-disposal)
* [Intro to the QuickBooks APIs](#intro-to-the-quickbooks-apis)
* [Setting up OAuth 2.0 access](#setting-up-oauth-20-access)
* [Hello QuickBooks!](#hello-quickbooks!)
    *  [Testing the API with Postman](#testing-the-api-with-postman)
    *  [Implementing the API with our sample code](#implementing-the-api-with-our-sample-code]
    

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

# Setting up OAuth 2.0 access

There are available libraries to generate OAuth 2.0 tokens. For simplicty sake here we'll show you how to manually generate the token using our OAuth playground. You can then get to your first succesful API call in minutes by plugging the obtained OAuth 2.0 token in Postman or our sample codes.

1. Sign up for an account at [https://developer.intuit.com](​https://developer.intuit.com)

2. Click on **my apps** and choose the **Just start coding** option. Select access to the **accounting** API and click **create app**

![alt-text](https://github.com/jplemoussu/smallbizhack-sydney/blob/master/Images/create-app.png "create your QuickBooks app") 

3. You should now be greeted by your QuickBooks app dashboard. Head over the **Keys** section and note down your `client Id` and `client secret`

4. Create a [Sandbox company](https://developer.intuit.com/v2/ui#/sandbox). Select `Australia` in the drop down country list and click **add** to create a AU small business account with dummy data. Take a moment to login your sandbox account and play around the QuickBooks app to understand how it works.

5. Time to grant your app access to the Sandbox company and generate your access token using the [OAuth playground](https://developer.intuit.com/v2/ui#/playground). Select `Accounting` for the scope.

6. Click on **Get authorization code**. You will be presented with an authorisation dialogue. Select your sandbox company and click **connect**. 

7. Back in the OAuth playground, note down the `Realm Id`. This is a unique identifier for your Sandbox QuickBooks account which you will need later on.  We sometimes refer to it as "Company Id"

8. Now click on **Get Token** to exhange the authorization code for your access and refresh tokens. If all goes well you should be presented with a response like below:

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

* `access_token`: token you will add in the authorization header of every API call to access data from your sandbox company. It is short lived: 1 hour. Can be refreshed before each call by calling the refresh token endpoint.
* `refresh_token`: Required to acquire a short lived `access token`. These last 100 days, but whenever our token endpoint returns a new one for the same company, start using it instead for subsequent calls. If the token is lost, generate a new refresh token by re-authorising your app.
* `id_token`: JSON web token containing information about the user and company which authorised your app. The token is base 64 encoded. You can head to https://jwt.io/ to decode it and check what is in there.

# Hello QuickBooks!

## Testing the API with Postman

 The Postman client is  a great way to navigate the QuickBooks API before you get your hands deep into into your code. You can skip and directly implement the QuickBook API using the sample code provided for this Hackathon - see section [using our sample code](#sample-code) 

**TIP**: There is a handy code generator in POSTMAN which will generate code snippets in most available laguages. ![alt-text](https://github.com/IntuitDeveloper/intuitdeveloper.github.io/blob/master/smallbizhack-sydney/Images/postman-code-snippets.jpg "Postman code snippets") 

1. Download the Postman client [www.getpostman.com](https://www.getpostman.com/)

2. Import the QuickBooks API collection into Postman by clicking the **Run in Postman** button on [this page](https://developer.intuit.com/docs/00_quickbooks_online/2_build/20_explore_the_quickbooks_online_api/20_postman) (make sure to select the first option which uses OAuth 2.0 authorization)

3. Plugin your company Id in the postman environment variable.

![alt-text](https://github.com/IntuitDeveloper/intuitdeveloper.github.io/blob/master/smallbizhack-sydney/Images/postman-variables.jpg "Postman environment variables") 

**TIP **: Change the `UserAgent` to something unique (your team name) so your requests can be tracked by Intuit Engineers to help debug your code if necessary

4. Now paste the Access Token from the OAuth playground 

![alt-text](https://github.com/IntuitDeveloper/intuitdeveloper.github.io/blob/master/smallbizhack-sydney/Images/postman-update-access-token.jpg "Postman access token") 


5. For this example, we are doing a data query using the QuickBooks `query` endpoint against the `customer` entity. Click on the **customer** folder in the Postman explorer panel, and select the **Customer-ReadAll** request.

    We want to get a list of all the customers that owe my small business client money! The query API uses SQL like queries (_note: we will soon be launching a GraphQL version of the API which will improve performance and code efficiency_). In the **body** tab, change the SQL query to:

    ```SELECT * FROM customer WHERE Balance > '0.0'```
 
    More details on the [query API](https://developer.intuit.com/docs/00_quickbooks_online/2_build/20_explore_the_quickbooks_online_api/50_data_queries)

6. Click **Send**. You should get back a list of customers who have unpaid balance.

TODO insert screenshot

## Implementing the APIs using sample code

Samples are available in the following languages, use the readme within the individual language folder for additional instructions on how to run the sample.
* [DotNet](dotnet)
* [Java](java)
* [Nodejs](nodejs)
* [PHP](php)
* [Python](python)










