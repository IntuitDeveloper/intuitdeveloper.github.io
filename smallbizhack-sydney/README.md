# SmallBizHack Sydney
Welcome to SmallBizHack Sydney 2018! Here you will find useful information and sample code to complete your app. 

* [The challenge](#the-challenge)
* [APIs at your disposal](#apis-at-your-disposal)
* [Implementing the QuickBooks API](#implementing-the-quickbooks-api)

## THE CHALLENGE
Create a solution that saves a small business time or money


## APIs at your disposal
This hack is all about creating new innovations that help small businesses. We encourage you to use the APIs below _(HINT HINT: there are special prizes for the teams who best utilise these APIs)_, but you're free to use any other APIs and frameworks to bring your app to life. 

* For **QuickBooks API**, you're in the right place. Read on below!
* For **Telstra API**, head [here](https://dev.telstra.com/apis)
* For **Google cloud API**, head [here](https://dialogflow.com/docs/getting-started/basics)
* For **PayPal API**, head [here](https://developer.paypal.com/)

# Implementing the QuickBooks API

## Intro to the QuickBooks APIs
Every small business needs to track their income, expense and profits, pay employees or lodge tax. They use an accounting application like QuickBooks to automate these painful tasks. QuickBooks is designed as an open ecosystem for small businesses, so YOU can build an app which hooks onto QuickBooks, to solve the myriad of other painpoints associated with running a business. 

Once your app is granted access to their QuickBooks account by a business owner, you can consume a rich set of financial data via the QuickBooks acccounting APIs. You can use this data and the available  APIs to to help them run their business more efficiently. 

Common tasks performed by the QuickBooks API include:
* Manage customers and vendors databases 
* Manage sales-side transactions (invoices, sales receipt) and purchase-side (bills, expenses) transactions.
* Retrieve reports: profit and loss, outstanding invoices...
* Manage product inventory

Check the [API reference](https://developer.intuit.com/docs/api/accounting) for the complete list of APIs. Do also check our app marketplace [apps.com](https://www.apps.com) for example of use cases and apps which use our APIs.


## Setting up OAuth access

There are available libraries to generate OAuth 2.0 tokens. For simplicty sake here we'll show you how to manually generate the token using our OAuth playground, and get to your first succesful API call in minutes.

1. Sign up for an account on [​developer.intuit.com](​https://developer.intuit.com)

2. Click on **my apps** and choose the **Just start coding**> option. Select access to the **accounting** API and click **create app**

![alt-text](https://github.com/jplemoussu/smallbizhack-sydney/blob/master/Images/create-app.png "create your QuickBooks app") 

3. You should now be greeted by your QuickBooks app dashboard. Head over the **Keys** section and note down your `client Id` and `client secret`

4. Create a [Sandbox company](https://developer.intuit.com/v2/ui#/sandbox) to run APIs against. Select `Australia` in the drop down and click `add` to create a AU small business account with dummy data. Take a moment to login your sandbox account and play around the QuickBooks app to understand how it works.

5. Time to grant your app access to the Sandbox company and generate your access token using the [OAuth playground](https://developer.intuit.com/v2/ui#/playground). Select `Accounting` and `OpenID` for the scopes.

6. Click on `Get authorization code`. You will be presented with an authorisation dialogue. Select your sandbox company and click `connect`. 

7. Back in the OAuth playground, click on `Get Token` to exhange the authorization code for your access and refresh tokens. If all goes well you should be presented with a response like below:

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

## Your first API call with POSTMAN

1. To construct your first API call and test access to your sandbox account, we will be using the API client POSTMAN. 

NOTE: You can also skip this part and directly use the available QuickBooks SDKs or your prefered API client library if you prefer. The setup steps will depend on what you use. You will need to plug in your `client Id`, `client secret` and set the sandbox endpoint `https://sandbox-quickbooks.api.intuit.com`. The POSTMAN collection we've created is just a great way to navigate the API and test the capabilities of the API before you get your hands into the code.

TIP 1: If you use POSTMAN, there is a handy code generator which will generate code snippets in most available laguages.

![alt-text](https://github.com/intuitdeveloper.github.io/smallbizhack-sydney/blob/master/Images/postman-code-snippets.jpg "Postman code snippets") 


TIP 2: Change the user agent to something unique so your requests can be tracked by Intuit Engineers to help debug your code if necessary



2. For this example, I'll be using the `customer` entity and generate a data query using the QuickBooks `query` endpoint. I want to get a list of all the customers that owe my small business client money! The query API uses SQL like queries (_note: we will soon be launching a GraphQL version of the API which will improve performance and code efficiency_)

    ex: ```SELECT * FROM customer WHERE Balance > '0.0'```
 
More details on the query API here

The constructed request looks like

```
POST https://sandbox-quickbooks.api.intuit.com/v3/company/193514693720239/query

Request Headers:
    accept:"application/json"
    content-type:"application/text"
    authorization:"Bearer <YourAccessToken>"

Request Body:
    "SELECT * FROM customer WHERE Balance > '0.0'"
```

Which gives the following JSON response

```json
{
    "QueryResponse": {
        "Customer": [
            {
                "Taxable": false,
                "BillAddr": {
                    "Id": "2",
                    "Line1": "25 Stone Drive",
                    "City": "Sydney",
                    "CountrySubDivisionCode": "NSW",
                    "PostalCode": "2001",
                    "Lat": "-33.737492",
                    "Long": "151.195909"
                },
                "Job": false,
                "BillWithParent": false,
                "Balance": 1650,
                "BalanceWithJobs": 1650,
                "CurrencyRef": {
                    "value": "AUD",
                    "name": "Australian Dollar"
                },
                "PreferredDeliveryMethod": "None",
                "domain": "QBO",
                "sparse": false,
                "Id": "1",
                "SyncToken": "0",
                "MetaData": {
                    "CreateTime": "2020-07-25T22:02:17-07:00",
                    "LastUpdatedTime": "2020-08-23T14:36:42-07:00"
                },
                "GivenName": "Adwin",
                "FamilyName": "Ko",
                "FullyQualifiedName": "Adwin Ko",
                "CompanyName": "Ko International Ltd",
                "DisplayName": "Adwin Ko",
                "PrintOnCheckName": "Adwin Ko",
                "Active": true
            },
            {
                "Taxable": false,
                "BillAddr": {
                    "Id": "4",
                    "Line1": "9 Temple St",
                    "City": "Melbourne,",
                    "CountrySubDivisionCode": "VIC",
                    "PostalCode": "3001",
                    "Lat": "-37.869967",
                    "Long": "145.093636"
                },
                "Job": false,
                "BillWithParent": false,
                "Balance": 7150,
                "BalanceWithJobs": 7150,
                "CurrencyRef": {
                    "value": "AUD",
                    "name": "Australian Dollar"
                },
                "PreferredDeliveryMethod": "None",
                "domain": "QBO",
                "sparse": false,
                "Id": "3",
                "SyncToken": "0",
                "MetaData": {
                    "CreateTime": "2020-07-25T22:02:21-07:00",
                    "LastUpdatedTime": "2020-08-23T22:03:14-07:00"
                },
                "GivenName": "Benjamin",
                "FamilyName": "Yeung",
                "FullyQualifiedName": "Benjamin Yeung",
                "CompanyName": "Yeung's Architects",
                "DisplayName": "Benjamin Yeung",
                "PrintOnCheckName": "Benjamin Yeung",
                "Active": true
            }
        ],
        "startPosition": 1,
        "maxResults": 5
    },
    "time": "2018-05-15T09:37:45.387-07:00"
}
```

If you made it here, well done. Take a small break before exploring the other endpoints!











