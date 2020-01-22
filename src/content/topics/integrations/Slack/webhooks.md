---
title: "Webhook integration"
excerpt: ""
---
## Overview
This topic explains how to use the Slack webhook integration to set up [Slack Incoming Webhooks](https://api.slack.com/incoming-webhooks) to receive any activities in LaunchDarkly. When something is changed (e.g. when a feature flag is updated, when a new team member is invited to LaunchDarkly, and more) we'll send an incoming webhook to Slack.
<Callout intent="info">
  <CalloutTitle>New Slack app is now available</CalloutTitle>
   <CalloutDescription>LaunchDarkly has developed a [new Slack app](./slack-app) which is more powerful and easier to use than the Slack webhooks integration.
The content below describes the older webhook-based integration.

## Creating a Slack incoming hook
First, create an incoming hook in Slack. To do this, head to the [Incoming Webhooks](https://my.slack.com/services/new/incoming-webhook/) page in Slack, select a channel, and click Add Incoming WebHooks integration.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/a914ddc-slack_webhooks.png",
        "slack_webhooks.png",
        1004,
        400,
        "#39a37c"
      ],
      "caption": ""
    }
  ]
}
[/block]
Copy the Webhook URL, and in LaunchDarkly, navigate to the  [Integrations page](https://app.launchdarkly.com/integrations), and click Add Integration in the chat integrations section. 
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/13b1d08-slack_dropdown.png",
        "slack_dropdown.png",
        1186,
        358,
        "#f7f7f7"
      ],
      "caption": ""
    }
  ]
}
[/block]

## Adding a policy filter
By default, room notifications set up without a policy filter will receive a notification for any change in LaunchDarkly (across any project, environment, goal, feature flag, etc.). You can optionally specify a policy (using the same syntax as our [custom roles](./custom-roles) feature to filter the events sent to your room.

For example, if you only want to receive an event when a change is made to one of your production feature flags, you can add the following policy to your room notification: 
[block:code]
{
  "codes": [
    {
      "code": "[{\n  \"effect\": \"allow\",\n  \"resources\": [\n    \"proj/*:env/production:flag/*\"\n  ],\n  \"actions\": [\n    \"*\"\n  ]\n}]",
      "language": "json",
      "name": "Policy"
    }
  ]
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/df57e20-slack_policy.png",
        "slack_policy.png",
        1183,
        577,
        "#959595"
      ],
      "caption": ""
    }
  ]
}
[/block]

## Disabling a Slack incoming webhook
You can temporarily disable a Slack incoming webhook by hitting the On/Off switch on the [Integrations](https://app.launchdarkly.com/integrations) page.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/ab98943-slack_off.png",
        "slack_off.png",
        1184,
        403,
        "#f8f8f8"
      ],
      "caption": "Configuring a Slack incoming webhook in LaunchDarkly"
    }
  ]
}
[/block]