# Email Finder Slack Bot

A Slack bot that looks up work email addresses from LinkedIn profile URLs using Clay. Mention the bot in any Slack channel with a LinkedIn URL and it will reply in the thread with the email it finds.

## How it works

1. **Mention the bot** in a Slack channel with a LinkedIn profile URL:
   ```
   @email_finder https://linkedin.com/in/johndoe
   ```
2. **The bot sends the URL to Clay** via a webhook, along with the channel and thread context.
3. **Clay looks up the email** using its data enrichment and calls back the bot's HTTP endpoint (`/clay-callback`) with the result.
4. **The bot replies in the thread** with the email if found, or a message saying no email was found.

## Setup

### Prerequisites
- Node.js
- A [Slack app](https://api.slack.com/apps) with Socket Mode enabled
- A [Clay](https://clay.com) table with a webhook source and HTTP callback configured

### Installation

```bash
npm install
```

### Environment variables

Create a `.env` file in the project root:

```
SLACK_BOT_TOKEN=xoxb-...
SLACK_SIGNING_SECRET=...
SLACK_APP_TOKEN=xapp-...
CLAY_WEBHOOK_URL=https://api.clay.com/v3/sources/webhook/...
PORT=3333
```

### Slack app requirements

The Slack app manifest should include:

- **Bot scopes:** `app_mentions:read`, `chat:write`, `channels:history`
- **Event subscriptions:** `app_mention`
- **Socket Mode:** enabled

### Running

```bash
node script.js
```

### Exposing the callback endpoint

Clay needs a public URL to send results back to. Use [localtunnel](https://theboroer.github.io/localtunnel-www/) to expose the local server:

```bash
npm install -g localtunnel
lt --port 3333
```

Set the Clay HTTP callback URL to:
```
https://<your-tunnel-url>.loca.lt/clay-callback
```

## Clay callback payload

Clay should send a POST request to `/clay-callback` with this body:

```json
{
  "channel": "<slack-channel-id>",
  "thread_ts": "<slack-thread-timestamp>",
  "user": "<slack-user-id>",
  "name": "<person name>",
  "email": "<work email or empty>"
}
```
