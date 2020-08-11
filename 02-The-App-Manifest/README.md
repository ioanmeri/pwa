# Understanding the App Manifest

## Web App Manifest

Make Your Web App **Installable**.

## Why

With a home screen icon, we increase User Interaction

* Do not type URL
* Do not manage Bookmark

## How

In root web folder (the one containing our index.html), we add a new file `manifest.json`

In **any page** we add the manifest in the head:
```
<link rel="manifest" href="/manifest.json">
```

## Manifest Properties
* name: Long name of app (e.g. on Splashscreen)
* short_name: Short name of app (e.g. below icon)
* start_url: Which page to load on startup
* scope: "." Which pages are included in "PWA Experience"
* display: "standalone" Should it look like a standalone app? (no url bar)
* background_color: "#fff" Background whilst loading & on Splashscreen
* theme_color: "#3F51B5" Theme color (e.g on top bar in task switcher)
* description: "Keep running until you're super sweaty!" Description (e.g. as favorite)
* dir: "ltr" Read direction of your app
* lang: "en-US" Main language of app
* orientation: "portrait-primary" Set (and enforce) default orientation
* "icons": [...] Configure icons (e.g. on home screen)
    - src
    - type
    - sizes: "48x48" Icon size, browser chooses best one for given device
* related_applications: [
    ```
    {
        "platform": "play",
        "url": "https://play.google.com/store/.../,
        "id": "com.example.app1"
    }
    ```
]