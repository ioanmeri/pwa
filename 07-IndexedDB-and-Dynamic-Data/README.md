# IndexedDB AND Dynamic Data

The cache API is awesome for caching assets like script files, CSS or images. But what about dynamic content fetched from a server?


## Caching Dynamic Content with IndexedDB


### Dynamic Caching vs Caching Dynamic Content

Not the same.

Dynamic Caching: You dynamically add items to your cache.

IndexedDB: is a in-browser database, a key-value database. For **storing JSON data, XML data**, so basically the structured or unstructered data you're getting back from your back-end, not files, not assets which define how your application works or looks but the content of your application.


### Why use it?
* for data that change frequently, that is what's meant with Dynamic content, typically is in JSON format or XML.
* we can store the formatted response, or part of the response.

The **Approach is Comparable** (Fetch Data and Store for Retrieval in the Future)

**The Data Nature and Format is Different!**

## Introducing IndexedDB

A transactional Key-Value Database in the Browser

**Transactional**: If one Action within a Transaction fails, none of the Actions of that Transaction are applied, to keep db integrity.

If you write 10 entries in the db and one of these writes fails, none of the entries are added.

You want to ensure that the db doesn't enter some invalid state thereafter because you don't know which data was written.

* Store significant amounts of unstructured data, including Files / Blobs.
* Can be accessed asynchronously

Being able to asynchronously access IndexedDB is the reason why it is so great
* you can use it from normal js code which is loaded through your pages
* as well as service workers.
(that as a side not is also the difference to local storage.)

Local Storage, Session storage are accessed synchronously and therefore can't be used in service workers.


Database --> Object Store --> Object

## idb

This package wraps IndexedDb and allows you to use promises


Service workers have a special syntax which allows us to import other packages with **importScripts**

in sw.js


```
importScripts('/src/js/idb.js');

var STATIC_FILES = [
  ...,
  'src/js/idb.js'
]
```


### Variable which can give us access to the db in a promise-like way


in ```sw.js```

* Create a new object store which is like a table, if it doesn't exist yet
```
var dbPromise = idb.open('posts-store', 1, function(db){
  if(!db.objectStoreNames.contains('posts')){
    db.createObjectStore('posts', {keyPath: 'id'});
  }
});
```

 
* I want to store the transformed cloned response
```
self.addEventListener('fetch', function(event){

  var url = 'api-url.com/posts';
  if(event.request.url.indexOf(url) > -1){
    event.respondWith(fetch(event.request)
      .then(function(res){

        var clonedRes = res.clone();
        clonedRes.json()
          .then(function(data){
            for(var key in data){
              dbPromise
                .then(function(db){
                  var tx = db.transaction('posts', 'readwrite');
                  var store = tx.objectStore('posts');
                  store.put(data[key]); // object
                  return tx.complete;
                });
            }
          });
        return res;

      })
    );
  }else if (isArray(event.request.url, STATIC_FILES)) {
    event.respondWith(
      caches.match(event.request)
    )
  }else {
    //...
  }


})

```

* Or with helper function in ```utility.js```
```
function writeData(st, data){
  return dbPromise
  .then(function(db){
    var tx = db.transaction(st, 'readwrite');
    var store = tx.objectStore(st);
    store.put(data); // object
    return tx.complete;
  });
}
```

Now in Browser Storage we have a db
IndexedDb -> posts-store -> posts

with entries
//
# Key             Value
0  'first-post'   Object 


## Read Data
Fetch data from our indexedDB.

```
function readAllData(st){
  return dbPromise
    .then(function(db){
      var tx = db.transaction(st, 'readonly');
      var store = tx.objectStore(st);
      return store.getAll(); 
    })
}
```
if that fails, it returns null, undefined


in sw.js
```
if('indexedDB' in window){
  readAllData('posts')
    .then(function(data){
      if(!networkDataReceived){
        console.log('From cache', data);
        updateUI(data);
      }
    })
}

```

## Delete indexedDB key values
if we delete a value from real db that's not fully reflected in our indexedb

We could clear it and then write all new entires anyway

in ```utility.js```
```
function clearAllData(st){
  return dbPromise
    .then(function(db){
      var tx = db.transaction(st, 'readwrite');
      var store = tx.objectStore(st);
      store.clear();
      return tx.complete;
    });
}
```

in ```sw.js```

```
event.respondWith(fetch(event.request)
  .then(function(res){
    var clonedRes = res.clone();
    clearAllData('posts')
      .then(function(){
        //..clonedRes.json()
        //..writeData('posts', data)
      })
  })

);

```

## Delete single item from db

in ```utility.js```

```
function deleteItemFromData(st, id){
  return dbPromise
    .then(function(db){
      var tx = db.transaction(st, 'readwrite');
      var store = tx.objectStore(st);
      store.delete(id);
      return tx.complete;
    })
    .then(function(){
      console.log('Item deleted');
    })
} 
```