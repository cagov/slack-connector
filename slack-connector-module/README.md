# Slack Connector

A module for simplifying posting to Slack.

## Features

- Built in retry for common connection issues using [fetch-retry](https://www.npmjs.com/package/fetch-retry).
- Tracks threads to allow for easy replies.
- Support for [chat.postMessage](https://api.slack.com/methods/chat.postMessage) and [reactions.add](https://api.slack.com/methods/reactions.add) method arguments.

## Set up

All use requires an API token and a channel id

```js
const slack_connector = require("@cagov/slack-connector");

const apiToken = `[api token here]`;
const channel = `[channel code here]`;

let slack = new slack_connector(apiToken, channel);
```

## Common use

Use this for adding replies to a slack thread.

```js
await slack.Chat("This is the start of a thread");
await slack.Reply("This is the first reply");
await slack.ReactionAdd("thumbsup"); //Thumbs up on first reply
await slack.Reply("This is the second reply");
```

## Error logging

You can catch errors in code and send them to a Slack error log

```js
try {
  //Do something that breaks
} catch (e) {
  slack.Error(e, { customdata: "goes here" });
  throw e;
}
```

## Multiple threads

You can reply to multiple threads by instantiating new objects

```js
let slackThreadOne = new slack_connector(apiToken, channel);
await slackThreadOne.Chat("This is the start of Thread 1");
let slackThreadTwo = new slack_connector(apiToken, channel);
await slackThreadTwo.Chat("This is the start of Thread 2");
await slackThreadTwo.Reply("This is a reply to Thread 2");
await slackThreadOne.Reply("This is a reply to Thread 1");
```

## Keeping a thread for re-use

You can clone a thread to be able to add reacts to it later

```js
let slack = new slack_connector(apiToken, channel);
await slack.Chat("Top Level Thread");
let toplevel = slack.Clone();
await slack.Reply("This is the first reply");
await toplevel.ReactionAdd("thumbsup"); //Thumbs up on top level thread
```

## Custom API options

You can use any options supported by the API by adding them to the `options` argument. See the full list [here](https://api.slack.com/methods/chat.postMessage).

```js
await slack.Chat("This is the start of a thread", {
  username: "My Custom Thread User",
  icon_emoji: ":chart_with_upwards_trend:",
  unfurl_media: false
});
```

You can set the default options when you define the thread.

```js
let slack = new slack_connector(apiToken, channel, {
  username: "My Custom Thread User",
  icon_emoji: ":chart_with_upwards_trend:",
  unfurl_media: false
});
await slack.Chat("Chat with the default options");
await slack.Reply("Chat with the same default options");
```
