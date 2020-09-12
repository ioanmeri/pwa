# Web Push Notifications

**For Developer**: The best way to drive user engagement and re-engage users with your application.

**For User**: You get informed about things which matter to you, at least if it's done in a good way.

For Android, even work if your phone is in your pocket.

## Why Push Notifications?

A background process of the Chrome browser will keep on running even if the application is closed and we'll use our service worker to receive incoming push notification.

* Show up even if App (and Browser!) is closed! - for Android and Chrome.
* Drive User Engagement
* Mobile-App like Experience

## How Does it Work?

First of all: **User Enables Notifications**

Push notifications contain 2 parts:
* **push**: Server pushes information to our application
* **notification**: What we show to the user in the end

In the web app, we can check for **existing push subscriptions**.

The **subscription** will be managed by the browser for you, is a **browser-device combination**

A new subscription is created in the JS with the service worker.

### Browser Vendor Push Server

Each browser vendor has its own push server.

When we set up a new subscription in JS, JS will automatically reach out to that push server and fetch an endpoint for us.

This endpoint is simply a URL, to which we can later send new push messages so that the browser vendor service will forward them to our web app.

Can pass additional authentication to the subscription so we - only - can send push notification to that URL.

### Our Backend Server
Same or different Servers, as a full stack or just api server.

Your service worker can now send this created subscription, to your back-end server to store it there.

It will be later our back end server which later wants to push messages to the front-end app.


Our Backend Server > Browser Vendor Push Server (API endpoint) > Delivers it to Service Worker (push event) > Display Notification 