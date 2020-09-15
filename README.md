# AWS Lambda telegram chat bot

This bot uses Markovify to generate text replies based on a given text (corpus). So if you want a bot which behaves
like your favorite writer (well, more like their mad version), give this bot their works as a corpus and you're done.

Hosting the bot on AWS Lambda is basically free: unless you are going for a really heavy load, you won't exceed the
free tier limits.

## Requirements

- [serverless framework](https://www.serverless.com/framework/docs/providers/aws/guide/installation/)
- [serverless requirements plugin](https://www.serverless.com/plugins/serverless-python-requirements)
- [AWS credentials](https://www.serverless.com/framework/docs/providers/aws/guide/credentials/) (namely, you will need
  AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY)

## Creating a new bot

Use [BotFather](https://t.me/botfather). It will provide you with a token which looks like this:
1330876708:oJsBe_EYy0NkZDdZF0010EwfdcaWJvHb5mZ. You probably should also disable privacy (using BotFather's /setprivacy
command) so your bot could see messages in groups.

## Deploying

Create a file named `corpus.txt` in your source directory and put your corpus text there.

Use these shell commands in the source directory:

```bash
export AWS_ACCESS_KEY_ID=<your-key-here>
export AWS_SECRET_ACCESS_KEY=<your-secret-key-here>
export TELEGRAM_TOKEN=<your-bot-token>
export WEBHOOK_PATH=<webhook-path>
serverless deploy
```

For a webhook path, use some secret string, for example, your bot's token with ":" removed. Webhook path will be used
to create your webhook URL and it must be secret so your script could receive messages from trusted source only. 

After a successful deploy, you will get a Lambda function created and an endpoint URL set, it looks like this:
https://gvebA3wjxi.execute-api.eu-west-1.amazonaws.com/prod/1330876708oJsBe_EYy0NkZDdZF0010EwfdcaWJvHb5mZ

Now you should [set a webhook](https://core.telegram.org/bots/webhooks) so Telegram will send all messages to your
endpoint URL. Use this shell command (I'm using the example endpoint and token, you should use yours):

```bash
curl -F "url=https://gvebA3wjxi.execute-api.eu-west-1.amazonaws.com/prod/1330876708oJsBe_EYy0NkZDdZF0010EwfdcaWJvHb5mZ" \
https://api.telegram.org/bot1330876708:oJsBe_EYy0NkZDdZF0010EwfdcaWJvHb5mZ/setWebhook
```

On success, you will get:

```bash
{"ok":true,"result":true,"description":"Webhook was set"}
```

Now try talking to your bot, it should respond with some nonsense which is the behavior we were aiming for. 
If it does not, you can probably get an idea what's going on from the AWS Lambda function monitoring 
(they have lots of logging there).