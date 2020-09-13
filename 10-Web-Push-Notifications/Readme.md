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

## Displaying Notifications

No "push" Event needs to be triggered to display Notifications!

### Notification API

We'll use the notification API to create and show notifications, set title, body & more.

Via Service Workers (Necessary when working with "push" Event!) or Via Page JS

### Get Permissions

2 buttons with ```enable-notifications``` class for desktop & mobile.

Check if browser supports notifications
```
if ('Notification' in window) {
  
}
```

if you ask for notification permissions, you implicitly ask for push permissions.

Notifications **depend on the device** you're displaying it on, not on the browser, because they are shown by the device.

Notification is a **system feature** displayed by the OS. Hence the user may interact with it when our page isn't even open.

## Push part of Push Notifications

### Protecting Vendor Server Url endpoint

2 ways

We do it by passing configuration to subscribe with a JS object.

We set our **own back-end server as the only valid source**, sending new push messages.

### **VAPID** with WebPush

We Have 2 keys. A public and a private one.

Install webpush and use it to generate these vapid keys, to then use to secure the push messages.

#### Cloud Functions
```
npm install web-push
```

You only run this once during development:
```
npm run web-push generate-vapid-keys
```

This gives us a public and a private key