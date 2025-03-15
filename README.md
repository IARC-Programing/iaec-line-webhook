# EManufac Line Webhook

For these codes and idea is original from Faizan Ahmad [faizanah](https://github.com/faizanah). Thank you very much for the idea and the great article [How to Implement a Secure Webhook in Node.js](https://medium.com/@faizan.ahmad.info/how-to-implement-a-secure-webhook-in-node-js-7c00e1314f3f)

This Code is going to create custom webhook for use [Line Messaging API](https://developers.line.biz/en/docs/messaging-api/overview/)
To make it integrate with the E-Manufac System.

This code mainly as a backend system however we also provide the frontend side too.

## Code Structure

- **frontend**: Contains the code of React 19.0 on Vite 6.2 as a client to generate webhook link
- **config**: Contains the configuration file for example the log managmenet system initialization
- **functions**: Contains functions to handling the events for example, function for login from lime
- **logs**: Contains the logs (ignored)
- **eventHandling.js**: Contains code for handling the event by choosing the appropiate function to use
- **server.js**: Main Logic of the software. It handle webhook, url and signature generator and it also serving the user interface

# Original Readme

# Implementing Secure Webhooks using node.js

<p align="center">
  <img src="./banner.png" alt="Implementing Secure Webhooks using node.js" width="1000" height="400" />
</p>

This is a Node.js application to create a secure webhook server using Express. The server verifies incoming webhook requests by validating their signatures and timestamps. It provides a middleware function to verify the signature and timestamp of incoming webhook requests and an endpoint to receive webhook payloads.

## ❯ Table of Contents

- [❯ Up and Running](#-up-and-running)
  - [Step 1: Clone this repository](#pre-requisites)
  - [Step 2: Install dependencies](#)
  - [Step 3: Set up environment variables](#)
  - [Step 4: Serve your app](#)
- [❯ Webhook Endpoint](#-webhook-endpoint)
- [❯ Testing](#-testing)
- [❯ Conclusion](#-conclusion)

## ❯ Up and Running

Before starting, make sure you have at least those components on your workstation:

- Install [Node.js and NPM](https://nodejs.org/en/download/). (20.14.x recommended)
  - on OSX use [homebrew](http://brew.sh) `brew install node`
  - on Windows use [chocolatey](https://chocolatey.org/) `choco install nodejs`

#### Step 1: Clone this repository

```sh
git clone https://github.com/faizanah/secure-webhook.git # or clone your own fork
```

#### Step 2: Install dependencies.

```sh
cd secure-webhook
npm install
```

#### Step 3: Set up environment variables

- `PORT`: The port on which the server will run. If not specified, it defaults to port 3000.
- `WEBHOOK_TOKEN`: Your webhook shared token key used for verifing incoming source.
- `SECRET`: Your shared secret key used for generating and verifying signatures.
- `MAX_TIME_DIFFERENCE`: The maximum allowed time difference (in seconds) between the current time and the timestamp provided in the webhook request headers. If not specified, it defaults to 60 seconds.

Export environment variables through terninals

```
export WEBHOOK_TOKEN="your_shared_webhook_token"
export SECRET="your_shared_secret"
export MAX_TIME_DIFFERENCE=60
export PORT=3000
```

Or rename the `/env.example` to `.env` update the values file for your environment

```
WEBHOOK_TOKEN=your_shared_webhook_token
SECRET=your_shared_secret
PORT=3000
MAX_TIME_DIFFERENCE=60
```

#### Step 4: Serve your app

Go to the project dir and start your app with this npm script.

```bash
# Launch the development server
$ node server.js
```

Secure webhook server running on `http://localhost:3000/`.

## ❯ Webhook Endpoint

#### POST `/webhook`

This endpoint is used to receive webhook payloads. It expects the following headers:

- `x-webhook-token`: The webhook token indicates the request source.

- `x-signature`: The signature generated by hashing the payload and timestamp with the shared secret key.
- `x-timestamp`: The timestamp indicating when the payload was created.

The payload must be a JSON object containing at least the following fields:

- `event`: The event type of the payload.
- `data`: The data associated with the event.

Example payload:

```json
{
  "event": "user.created",
  "data": {
    "id": "1",
    "full_name": "Faizan Ahmad",
    "email": "faizan.ahmad.info@gmail.com"
  }
}
```

## ❯ Testing

For testing purposes, you can use the following bash script:

```sh
$ bash test.sh
```

Below is the content for the test.sh file, which should be available in the same directory. This script executes the curl request:

```bash
#!/bin/bash

# Define the payload
payload='{"event":"user.created","data":{"id":"1","full_name":"Faizan","email":"faizan.ahmad.info@gmail.com","contact":"+923338184261"}}'

# Generate the current Unix timestamp
timestamp=$(date +%s)

# Define the webhook shared token
webhook_token='your_shared_webhook_token'

# Define the shared secret
shared_secret='your_shared_secret'



# Generate the HMAC SHA-256 signature in hexadecimal format
signature=$(echo -n "$payload$timestamp" | openssl dgst -sha256 -hmac "$shared_secret" | sed 's/^.* //')

# Print the generated signature
echo "Payload: $payload"
echo "Webhook Token: $webhook_token"
echo "Shared Secret: $shared_secret"
echo "Generated timestamp: $timestamp"
echo "Generated signature: $signature"

# Send the payload with the signature and timestamp to the webhook
curl -X POST -H "Content-Type: application/json" -H "x-signature: $signature" -H "x-webhook-token: $webhook_token" -H "x-timestamp: $timestamp" -d "$payload" "http://localhost:3000/webhook"
```

This script will generate the necessary payload, timestamp, and HMAC signature, then send the payload with the required headers to the specified webhook URL.

## ❯ Conclusion

Implementing secure webhooks is essential to protect your application from various security threats. By verifying the source, validating the payload, and handling events securely, you can ensure that your webhooks are robust and reliable.

With these steps, you now have a basic implementation of secure webhooks using Node.js. Customise the validation and event handling to suit your application's requirements, and you'll be well on your way to secure and efficient webhook integration.
