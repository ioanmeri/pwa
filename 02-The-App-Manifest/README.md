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

## Properties for Safari

Include on head
```
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-status-bar-style" content="black">
  <meta name="apple-mobile-web-app-title" content="PWAGram">
  <link rel="apple-touch-icon" href="/src/images/icons/apple-icon-57x57.png" sizes="57x57">
  <link rel="apple-touch-icon" href="/src/images/icons/apple-icon-60x60.png" sizes="60x60">
  <link rel="apple-touch-icon" href="/src/images/icons/apple-icon-72x72.png" sizes="72x72">
  <link rel="apple-touch-icon" href="/src/images/icons/apple-icon-76x76.png" sizes="76x76">
  <link rel="apple-touch-icon" href="/src/images/icons/apple-icon-114x114.png" sizes="114x114">
  <link rel="apple-touch-icon" href="/src/images/icons/apple-icon-120x120.png" sizes="120x120">
  <link rel="apple-touch-icon" href="/src/images/icons/apple-icon-144x144.png" sizes="144x144">
  <link rel="apple-touch-icon" href="/src/images/icons/apple-icon-152x152.png" sizes="152x152">
  <link rel="apple-touch-icon" href="/src/images/icons/apple-icon-180x180.png" sizes="180x180">
```

## Properties for Internet Explorer
```
  <meta name="msapplication-TileImage" content="/src/images/icons/app-icon-144x144.png">
  <meta name="msapplication-TileColor" content="#fff">
  <meta name="theme-color" content="#3f51b5">
```