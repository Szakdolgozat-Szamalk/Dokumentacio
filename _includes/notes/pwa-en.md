# PWA / PROGRESSIVE WEB APP

Because of the manifest, our site can be installed with its own icon and a separate window, and the service worker pre-caches the app shell so it launches quickly and works offline. However, the data always comes from the Laravel API, meaning outdated data or data belonging to another user never remains in the cache.

## WHAT IS A PWA?

A PWA is a web application that can be installed and afterwards behaves like a native app:

* custom icon on the home screen / desktop / taskbar
* runs in a separate window, without browser address bar
* launches quickly and loads even without the internet (at least partially)
* can receive notifications, quick actions, and badges on the icon

It is not a separate technology or framework, but a combination of web standards. The exact same React + Laravel code runs both as a website and as an installed app.

"Progressive" = progressive enhancement: it works as a plain website in old or weaker browsers, and gains extra capabilities in modern browsers. Nothing breaks if something is not supported.

### ADVANTAGES

* one codebase for every platform (Android, iOS, Windows, macOS)
* no app store, no waiting for approval, no store commission
* updates go out to everyone instantly
* small size, no need to download a large installation package

### LIMITATIONS

* on iOS, there is no automatic installation prompt; the user must install manually (Share -> Add to Home Screen)
* push notifications on iOS only work inside an installed app
* deep system access (background processes, certain hardware) is limited compared to a native app
* in Safari: if the user does not open the site even once during 7 days of Safari usage, storage written via JS (localStorage, IndexedDB) may be deleted. An app installed on the home screen is an exception to this. This is not an issue for us because all important data is in the Laravel DB, and localStorage is only used for caching (e.g., theme).

## THE THREE PILLARS

### HTTPS

* The service worker only works over a secure connection.
* Exception: localhost (for development).

### WEB APP MANIFEST

(manifest.webmanifest) A JSON file that describes the app to the browser and the operating system. During installation, this provides the icon, name, and splash screen.

Important fields:

* id unique identifier of the app (e.g., "/")
* name full name (used during installation, splash screen)
* short_name short name displayed under the icon (approx. 12 characters)
* description short description
* start_url which page should open upon launch
* scope which paths belong to the app
* display standalone = separate window, without address bar
* theme_color color of the address bar / status bar
* background_color splash screen background color
* icons 192x192, 512x512 + one 512 "maskable" icon
* screenshots richer installation window (wide + narrow)
* shortcuts quick menu on the icon (long press / right click)
* categories, lang, dir
* launch_handler whether to reuse an already open window

Maskable icon: Android crops it into its own shape (circle, rounded square). If there is no maskable icon, it places the plain icon inside a white border. The main content (logo) should be in the center of the icon, within the "safe zone", so it remains visible after cropping.

### SERVICE WORKER

(sw.js) A JS file that runs in the browser on a thread separate from the page, acting as a "proxy" between the app and the network: it can intercept every request and decide whether to serve it from the network or the cache.

Lifecycle: register -> install -> (waiting) -> activate -> fetch

* register: the app registers it (in main.tsx or via plugin)
* install: downloads and caches the app shell (precache)
* waiting: if an old version is already running, the new one waits until all old tabs are closed or the user clicks the "Update" button
* activate: takes over control, deletes old caches
* fetch: serving requests according to the strategy

App shell = the "framework" of the app: index.html, JS, CSS, icons, fonts. We download it in advance during installation (precache), so the app launches instantly and loads offline.

Cache strategies:

* Cache First checks cache first, falls back to network if not found (images, fonts, 3D models -- change rarely)
* Network First checks network first, falls back to cache if offline (data that needs to be fresh)
* Stale While Revalidate returns cached response immediately while updating in the background (non-critical data)
* Network Only never caches (login, purchase, POST)
* Cache Only cache only (pre-cached app shell)

## WHEN CAN IT BE INSTALLED?

Chrome / Edge (Android, desktop):

* HTTPS
* manifest: name or short_name, 192 and 512 icons, start_url, display (standalone / fullscreen / minimal-ui)
* The service worker is no longer a requirement for installation from the menu (since Chrome 108 mobile / 112 desktop), but it is still required for the automatic install prompt and offline functionality.
* Custom "Install" button: beforeinstallprompt event (only available in Chromium-based browsers).

iOS / iPadOS Safari:

* Share -> Add to Home Screen (no automatic prompt, nor can there be a custom install button; we can only show a short guide)
* The icon comes from apple-touch-icon: 180x180, WITHOUT a transparent background, because iOS fills the transparent area with black.
* On newer iOS versions, a site added to the home screen opens as a web app by default (in a separate window).

## HOW TO USE IT IN THE SELF-BUILDING WEBAPP?

Tool: vite-plugin-pwa (built on Google's Workbox library)

* generates the service worker during build
* generates the manifest from vite.config.ts and automatically inserts the tag (therefore, we delete the manual manifest line from index.html in this case)
* pre-caches the build output (JS, CSS, HTML, icons, fonts)
* provides a React hook for updates: useRegisterSW

## TESTING, DEBUGGING

* In development mode (npm run dev), the service worker does NOT run by default. To test: npm run build, then npm run preview.
* Chrome DevTools -> Application tab: Manifest -- errors, icons, installability; Service workers -- status, "Update on reload", Unregister; Cache storage -- what is in the cache; Storage -- "Clear site data" (if an old version gets stuck)
* Offline test: DevTools -> Network -> "Offline"
* The PWA category was removed from Lighthouse: check installability on the Application tab (Lighthouse still measures performance).
* Must also be tested on a real phone (with HTTPS), especially on iOS.

## COMMON ERRORS

* index.html or sw.js is cached on the server -> users get stuck on the old version
* Relative manifest path ("manifest.webmanifest" without leading slash) -> requested from the wrong place on subpages
* No maskable icon -> white-bordered icon on Android
* Transparent apple-touch-icon -> black background on iOS
* Caching API responses / logged-in data -> outdated data or data belonging to another user
* sw.js is not in the root directory -> scope is limited to a single subfolder
* Old service worker stuck during development -> DevTools: Unregister / Clear site data

## **SOURCES**

* Changes to Chrome installability criteria:
[https://developer.chrome.com/blog/update-install-criteria](https://developer.chrome.com/blog/update-install-criteria)
* Safari 7-day storage limit and home screen apps:
[https://ionic.io/blog/is-apple-trying-to-kill-pwas](https://ionic.io/blog/is-apple-trying-to-kill-pwas)