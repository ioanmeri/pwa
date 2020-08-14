# Caching with Service Workers

Providing Offline Support


## Why Support Offline Access?
* Poor Connection
* No Connection
* Lie-Fie!

With service workers, you will learn that you can pre-cache or in general, that you can cache certain files, assets your web app might need and therefore can still display this page and even allow navigation whilst your app is offline.

## The Cache API

You store key-value pairs

Key: Request, Value: Response

The Cache API can be accessed from both service workers and ordinary JavaScript.

Cache Data can be retrieved instead of sending Network Request.

In the end, by using the **fetch event** in the service worker, which of course will be the key to intercepting any requests and **the Cache API**, you have a complete **network proxy** living in your sw.

## Identifying (Pre-) Cacheable Items

At least cache the **app shell** (header, footer, side nav...) already when the service worker is installing.

## Static Caching at Installation

Cache static files like: index.html, app.js, app.css, image.png

First we open (or create if does not exist) a sub-cache with the Cache API and then we store the static assess we want.

with `cache.add` the Service Worker will 
* send the request to the server
* get that asset 
* and store it

 (all in one step).

```
self.addEventListener('install', function(event){
    console.log('[Service Worker] Installing Service Worker ...', event);
    event.waitUntil(
        caches.open('static')
            .then(function(cache){
                console.log('[Service Worker] Precaching App Shell');
                cache.add('/src/js/app.js')
            })
    );
});
```