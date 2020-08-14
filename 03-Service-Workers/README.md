# Service Workers

Doing Work Behind the Scenes

## What are Service Workers?

JS runs on a Single Thread

Service Worker

* They have their own Single Thread and are **decoupled** from HTML pages
* Manages **ALL Pages** of a given scope (e.g. all pages of a domain, not attached to a single page like normal js)
* Lives on **even after pages have been closed** (it's a **background process**)
* Listen to specific events (they react to events)

Background process attached to entire applications that react to certain events.


## "Listenable" Events (in Service Worker)

### Fetch
Browser or Page-related JavaScript initiates a Fetch (Http Request)

Think of a SW serving as a **network proxy**. Every HTTP request sent via fetch goes through the SW.

### Push Notifications
SW receives Web Push Notification (from Server).

Is all about getting the user back to the application after you close it.

### Notification Interaction
User interacts with displayed Notification

### Background Sync
You store a certain action and re-execute it when internet connection is re-established.

SW receives Background Sync Event (e.g. Internet Connection was restored)

### Service Worker Lifecycle
Service Worker Phase changes

## Service Worker Lifecycle

index.html -> loads -> app.js --> registers -> sw.js

sw.js --> Installation (+ emits install Event) --> Activation --> (activate Event) ---> Idle --> Terminated

SW now controls all pages of Scope.

Service Workers will only get active if there is not old service worker instance running.

Browsers install SW only if the SW code is an updated version or the first time.

When SW is terminated (sleeping) but can wake up as soon as events are coming in.

## Registering a Service Worker

* Add the `sw.js` file in public folder to be able to use it everywhere in the application (scope).
* Register sw in `app.js` (so it is included in all pages)

```
if ('serviceWorker' in navigator) {
    navigator.serviceWorker
        .register('/sw.js')
        .then(function(){
            console.log('Service worker registered!');
        });
}
```

### Register a Fetch Event
A fetch event will be emitted when the HTML page load assets like scripts or css or an image (or if we manually send a fetch request).

```
self.addEventListener('fetch', function(event){
    console.log('[Service Worker] Fetching something ...', event);
    event.respondWith(fetch(event.request));
});
```

## Deferring the App Install Banner

Show App install at a later time and point than Google suggests (two times user visits the app and 5 minutes have passed)

Show the Install Banner when user clicks on plus button in app


`app.js`
```
window.addEventListener('beforeinstallprompt', function(event){
    console.timeLog('beforeinstallprompt fired');
    event.preventDefault();
    deferredPrompt = event;
    return false;
});
```

`feed.js`
```
function openCreatePostModal() {
  createPostArea.style.display = 'block';
  if(deferredPrompt){
    deferredPrompt.prompt();

    deferredPrompt.userChoice.then(function(choiceResult){
      console.log(choiceResult.outcome);

      if (choiceResult.outcome === 'dismissed') {
        console.log('User cancelled installation');
      } else {
        console.log('User added to home screen');
      }
    });

    deferredPrompt = null;
  }
}
```