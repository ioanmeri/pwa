# Background Sync

 Background synchronization allows you to synchronize your data even if the user is offline while sending it.

 ## Send data even when you're offline

 ### How it works

> Store the request and send it at a later point of time.

You can't cache the request itself, only the response. There is a technique we can use though.

We can use a service worker to register a sync task. We also need to store the data we want to send with the post /put request.

We Store Data which should be sent in IndexedDB.

When we getting connectivity again, a so-called **sync event will be executed on the service worker** and you can listen to that event in the sw.

The sw will fetch the request data and send it to the server. This **will even work if we did close the tab or even the browser**

### Basic Setup

```feed.js```

```
var form = document.querySelector('form');
var titleInput = document.querySelector('#title');
var locationInput = document.querySelector('#location');


form.addEventListener('submit', function(){
  event.preventDefault();

  if(titleInput.value.trim() === '' || locationInput.value.trim() === ''){
    alert('Please enter valid data!);
    return;
  }

  closeCreatePostModal();

  // Register a Sync Task
  if('serviceWorker' in navigator && 'SyncManager' in window){
     navigator.serviceWorker.ready
      .then(function(sw){
         var post = {
           id: new Date().toISOString(),
           title: titleInput.value,
           location : locationInput.value,
         };
         writeData('sync-posts', post)
          .then(function(){
            return sw.sync.register('sync-new-posts');
          })
          .then(function(){
             var snackBarContainer = document.querySelector('#confirmation-toast');
             var data = {message: 'Your Post was saved for syncing'};
             snackBarContainer.MaterialSnackBar.showSnackbar(data);
          })
          .catch(function(err){
            console.log(err)
          })
      });
  } else {
    sendData();
  }

});


function sendData(){
  fetch('api-endpoint', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Accept': 'application/json',
    },
    body: JSON.stringify({
      id: new Date().toISOString(),
      title: titleInput.value,
      location: locationInput.value,
    })
  })
  .then(function(res){
    console.log('Sent data', res);
    updateUI();
  })
}
```

## Syncing Data in Service Worker

```sw.js```

self.addEventListener('sync', function(event){
  console.log('[Service Worker] Background syncing', event);
  if(event.tag === 'sync-new-posts'){
    console.log('[Service Worker] Syncing new Posts');
    event.waitUntil(
      readAllData('sync-posts')
        .then(function(data){
          for(var dt of data){
            fetch('api-endpoint', {
              method: 'POST',
              headers: {
                'Content-Type': 'application/json',
                'Accept': 'application/json',
              },
              body: JSON.stringify({
                id: dt.id,
                title: dt.title,
                location: dt.location,
              })
            })
            .then(function(res){
              console.log('Sent data', res);
              if(res.ok){
                deleteItemFromData('sync-posts', dt.id);
              }
            })
            .catch(function(err){
              console.log(err)
            })
          }
        })
    );
  }
});


## Periodic Sync

Is all about getting data on a regular basis from the server. Not about sending data.

Could get updates on the background so next time the user opens our web app, it already has the fresh data, it doesn't need to fetch it at the point of time we start the app.

It's not supported by browsers yet but it will come in the future and will work like this.

Register a Periodic Sync Task with the service worker. There we also pass a schedule where we basically say, please update every hour or something like that.

This schedule will trigger the periodic sync event on the service worker to which we can listen with add event listener based on the schedule.
So this makes the service worker reach out the server and request new data.

Periodic Sync is NOT about Connectivity Issues!