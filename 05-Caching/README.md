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

## Retrieving items from the Cache
The key we cache is always a **Request object**, never a string.

```
self.addEventListener('fetch', function(event){
    event.respondWith(
        caches.match(event.request)
            .then(function(response){
                if (response) {
                    return response;
                } else {
                    return fetch(event.request);
                }
            })
    );
});
```

We are intercepting the request and we're not issuing a new one.

Even "/" request must be cached.

## Implementing Dynamic Caching

Instead of add, we use **put** that requires you to provide a request and response key-value pair. So put **doesn't send any request**.

### Key Points

We must return the response so that in our promise chain then is returned to the original requester, which is coming from our HTML page or JS files.

You can only consume (which means use) a response once. So we should call response clone.

```
self.addEventListener('fetch', function (event) {
   event.respondWith(
      caches.match(event.request)
         .then(function (response) {
            if (response) {
               return response;
            } else {
               return fetch(event.request)
                  .then(function (res) {
                     return caches.open('dynamic')
                        .then(function (cache) {
                           cache.put(event.request.url, res.clone());
                           return res;
                        })
                  })
            }
         })
   );
});
```
