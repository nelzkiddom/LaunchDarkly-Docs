---
title: "Haskell SDK Reference"
excerpt: ""
---
<Callout intent="info">
<CalloutTitle>This SDK is in beta</CalloutTitle>
   <CalloutDescription>The SDK is currently in beta. We do not recommend using it for production deployments.</CalloutDescription>
</Callout>

## Overview
This topic explains the basics of our Haskell server-side SDK and how it works. 

If you want to learn more, visit our [Haskell SDK GitHub repository](https://github.com/launchdarkly/haskell-server-sdk/). You can even clone and run a [sample application](https://github.com/launchdarkly/hello-haskell-server/) using this SDK.
## Getting started
Follow the steps below to get started using the LaunchDarkly SDK in your Haskell application.

The root module of the SDK `LaunchDarkly.Server` re-exports the entire project. That's all that you need to start.
[block:code]
{
  "codes": [
    {
      "code": "import LaunchDarkly.Server",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]
After you import and install the SDK, create a single shared instance of `Client`. 

Specify your SDK key here to authorize your application to connect to LaunchDarkly for a particular environment. 
[block:code]
{
  "codes": [
    {
      "code": "client :: IO Client\nclient = makeClient $ makeConfig \"YOUR_SDK_KEY\"",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]

<Callout intent="alert">
<CalloutTitle>Client must be a singleton</CalloutTitle>
   <CalloutDescription>Make `Client` a singleton. 
The client instance maintains an internal state that allows us to serve feature flags without making any remote requests.</CalloutDescription>
</Callout>

Using `client`, you can check which variation a particular user should receive for a given feature flag.
[block:code]
{
  "codes": [
    {
      "code": "boolVariation client \"YOUR_FLAG_KEY\" (makeUser \"abc\") False",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]

## Customizing your client
You can also pass other custom parameters to the client with the configuration object:
[block:code]
{
  "codes": [
    {
      "code": "{-# LANGUAGE OverloadedStrings #-}
import LaunchDarkly.Server.Config
import Data.Function ((&))
config :: Config\nconfig = (makeConfig \"YOUR_SDK_KEY\")\n    & configSetEventsCapacity 1000\n    & configSetFlushIntervalSeconds 30",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]
In the example above, we've customized the event queue capacity and flush interval parameters.
## Users
Feature flag targeting and rollouts are all determined by the user you pass to your variation calls.
[block:code]
{
  "codes": [
    {
      "code": "{-# LANGUAGE OverloadedStrings #-}
import LaunchDarkly.Server.User
import Data.Function ((&))
user :: User\nuser = (makeUser \"aa0ceb\")\n    & setFirstName \"Ernestina\"\n    & setLastName \"Evans\"\n    & setEmail \"ernestina@example.com\"\n",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]
The most important attribute in the example above is the user key. The user key is **mandatory**. 

In the example above, we used the hash `"aa0ceb"`. 

The user key should be unique to each user. You can use a primary key, an email address, or a hash, as long as the same user always has the same key. We recommend using a hash if possible.

All of the other attributes, like `firstName`, `email`, and the `custom` attributes, are **optional**. The attributes you specify appear on the LaunchDarkly dashboard automatically. You can start making user segments and targeting users with these attributes right away.

In addition to built-in attributes like names and email addresses, you can pass any of your own user data as `custom` attributes, like the `groups` attribute in the example above. Custom attributes are one of LaunchDarkly's most powerful features. They let you target users according to any data that you want to send, including organizations, groups, and account plans. Anything you pass to us becomes available instantly on our dashboard.
## Private user attributes
Optionally, you can configure the Haskell SDK to treat some or all user attributes as private user attributes. You can use private user attributes for targeting purposes without sending data about the user associated with them back to LaunchDarkly. 

To learn more about private user attributes, read [Setting user attributes](./private-user-attributes).

When you create the `Config` object, use `configSetAllAttributesPrivate` to set all user attributes as private. When you do this, all user attributes (except the key) are removed before the user is sent to LaunchDarkly.
[block:code]
{
  "codes": [
    {
      "code": "config' = configSetAllAttributesPrivate True config",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]
When you create the `Config` object, you can list specific private attributes with `configSetPrivateAttributeNames`. If any user has a custom or built-in attribute named in this list, it is removed before the user is sent to LaunchDarkly.
[block:code]
{
  "codes": [
    {
      "code": "import Data.Set (fromList)
config' = configSetPrivateAttributeNames (fromList [\"email\"]) config",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]
You can also define private attribute names on a per-user basis. 

For example:
[block:code]
{
  "codes": [
    {
      "code": "import Data.Set (fromList)
user' = userSetPrivateAttributeNames (fromList [\"email\"]) user",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]

## Variation
The `variation` family of functions determine whether a flag is enabled or not for a specific user.
[block:code]
{
  "codes": [
    {
      "code": "",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]
The functions take a `Client`, `User`, feature flag key, and a default value.

The default value only returns if an error occurs. For example, if the feature flag key doesn't exist or the user doesn't have a key specified, the default value is returned.
## VariationDetail
The `variationDetail` functions are similar to the `variation` functions, but they also return an explanation of the evaluation that is programmatically inspectable. 

To learn more about the "reason" data, read [Evaluation reasons](https://docs.launchdarkly.com/docs/evaluation-reasons).
[block:code]
{
  "codes": [
    {
      "code": "details :: IO (EvaluationDetail Bool)\ndetails = boolVariationDetail client user \"YOUR_FEATURE_KEY\" False",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]
For specific information on the `EvaluationDetail` structure, inspect the `LaunchDarkly.Server.Details` module.
## All flags
The `allFlags` function captures the state of all feature flag keys as evaluated for a specific user. This includes their values, as well as other metadata.

This method is useful when you're passing feature flags to your frontend. Specifically, you can use it to provide bootstrap flag settings for our JavaScript SDK. 

To learn more, read the [JavaScript SDK Reference](./js-sdk-reference).
[block:code]
{
  "codes": [
    {
      "code": "state = allFlags client user",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]

## Anonymous users
You can also distinguish logged-in users from anonymous users in the SDK.
[block:code]
{
  "codes": [
    {
      "code": "user` = userSetAnonymous True user",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]
You still must generate a unique key for anonymous users. Session IDs or UUIDs work best for this.

Anonymous users work just like regular users, except that they won't appear on your Users page in LaunchDarkly. You also can't search for anonymous users on your Features page, and you can't search or autocomplete by anonymous user keys. This is actually a good thing-- it keeps anonymous users from polluting your Users page!
## Flush
The LaunchDarkly SDK keeps an internal event buffer for analytics events. These events are flushed periodically in a background thread. In some situations, you may want to manually call flush to process events immediately. 

For example, if you're testing the SDK in a simulator, you may want to flush example events.
[block:code]
{
  "codes": [
    {
      "code": "flushEvents client",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]
This function will not block, but instead initiate a flush operation in the background. Note that the flush interval is configurable-- if you need to change the interval, you can do so via the configuration.
## Track
The `track` function allows you to record actions your users take on your site. This lets you record events that take place on your server. In LaunchDarkly, you can tie these events to goals in experiments. 

For example:
[block:code]
{
  "codes": [
    {
      "code": "track client user \"your-goal-key\" Nothing Nothing",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]
You can also attach a JSON object containing arbitrary data to your event, or a custom metric value.
## Identify
The `identify` function creates or updates users on LaunchDarkly, making them available for targeting and autocomplete on the dashboard. 

In most cases, you won't need to call `identify`. The `variation` call will automatically create users on the dashboard for you. `identify` is useful if you want to pre-populate your dashboard before launching any features. 
[block:code]
{
  "codes": [
    {
      "code": "identify client user",
      "language": "text",
      "name": "Haskell"
    }
  ]
}
[/block]

## Offline mode
In some situations, you might want to stop making remote calls to LaunchDarkly and fall back to default values for your feature flags. You can do this by setting offline mode in the config object with `configSetOffline`.

For example, if your software is both cloud-hosted and distributed to customers to run on premise, it might make sense to fall back to defaults when running on premise. 
[block:code]
{
  "codes": [
    {
      "code": "config' = configSetOffline True config",
      "language": "text"
    }
  ]
}
[/block]

## Shutting down
`close` safely shuts down the client instance and releases all resources associated with the client. 

In most long-running applications, you should not have to call close, but you can use it to ensure all events have been flushed synchronously before closing your application. 

You should not evaluate flags after `close` has been initiated. 
[block:code]
{
  "codes": [
    {
      "code": "close client",
      "language": "text"
    }
  ]
}
[/block]