---
layout: post
title:  "Power BI | Push dataset rows on Hybrid dataset"
date:   2019-07-19 11:00:00
categories: powerbi dataset hybrid
---
# >_

## Scenario

When you create an **streaming dataset** in the Power BI web portal, you should define the schema of your dataset table will follow.

After defining every column on your table, you can choice to setup the dataset as an ***Hybrid*** one just checking the ***Historic data analysis*** option before saving it.

After creating the dataset, the Power BI web portal provides you an endpoint to push dataset rows (and some examples to push data on it from cUrl or powershell) that not requires to get an authentication token, because already contains an authentication key. Just call the REST method and pass the json representation of your table and... Everything is fine!

But... It is not completely true.

## Problem 1: Streaming vs Hybrid dataset

The ***streaming dataset*** type (aka you didn't check the *Historic data analysis* option), when consumed, ***doesn't allow to handle any column value outside numeric ones***.

This is very important because when you have some text coming from your data source (i.e. for grouping data, filtering or whatever), you should figure out that this text values couldn't be shown as values on your panels or reports.

At the other hand, an ***hybrid dataset*** allows you to use text fields as values, fact that allow you to use it, as previously mentioned, to apply filters in your report and panel values.

But, the hybrid dataset have a json payload incompatibility within the endpoint and request body suggested by the Power BI web portal that allows you to avoid getting an authentication token.

## Problem 2: Hybrid schema json payload

Let's imagine that you want to publish data from a console application / webjob / Azure Function / whatever written in C#. 

In example, your C# code defines `PowerbiData` entity as follows...

```csharp
public class PowerbiData 
{
    public int Number { get; set; }
    public string Key { get; set; }
}
```
... and just serializes the `PowerBiData` object as `StringContent` in json format ...

```json
{
    "Number": 123,
    "Key": "OneTwoThree"
```

... and posts it from an `HttpClient` instance.

What happens?

Damn! Error 400! And the message of this error will look like...

> Did not encounter start array token or start object token for rowset

## Solution

Personally, I didn't found any reference about this scenario on the internet, but probably because I didn't search for it properly.

But I have been investigating on how to use the complete [Power BI REST API to push dataset rows](https://docs.microsoft.com/es-es/rest/api/power-bi/pushdatasets/datasets_postrows).

The most annoying part for me of this way is to get a service principal authorized to perform *read/write* operations in Power BI and get the access token from the AAD API to use it as bearer authorization header on each post request. I totally discard it, it does not makes any sense to me for this simple scenario.

But... Just for trying, I have followed the json payload structure of dataset post rows REST method, but with the auto generated URL (with key).

```json
{
    "rows": [
        {
            "Number": 123,
            "Key": "OneTwoThree"
        }
    ]
}
```

The object to pass as `StringContent` now is build following this payload.

```csharp
// To be improved
// ...
PowerbiData entity = new PowerbiData { Number = 123, Key = "OneTwoThree" };
JObject jsonObject = new JObject(new JProperty("rows", new JArray(JsonConvert.SerializeObject(entity))));

var response = await client.PostAsync(endpointUrl, new StringContent(jsonObject.ToString(), Encoding.UTF8, JsonMediaType), cancellationToken);

```

I have tried again with the same `HttpClient.PostAsync` method and... Voilá!

Now the direct push dataset rows api endpoint works even you have configured your dataset as **Hybrid**.