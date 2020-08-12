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