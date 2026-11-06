# PWA / PROGRESSIVE WEB APP

Az oldalunk a manifest miatt telepíthető saját ikonnal és külön
ablakkal, a service worker előre eltárolja az app vázát, ezért gyorsan
és net nélkül is elindul, az adatok viszont mindig a Laravel API-ból
jönnek, így a cache-ben soha nem marad elavult vagy más felhasználóhoz
tartozó adat.

## MI A PWA?

A PWA olyan webalkalmazás, amit telepíteni lehet, és utána úgy
viselkedik, mint egy natív app:

-   saját ikon a kezdőképernyőn / asztalon / tálcán
-   külön ablakban fut, böngésző címsor nélkül
-   gyorsan indul, és internet nélkül is betölt (legalább részben)
-   kaphat értesítéseket, gyorsmenüt, számot (badge) az ikonon

Nem külön technológia vagy keretrendszer, hanem webes szabványok
együttese. Ugyanaz a React + Laravel kód fut weboldalként és telepített
appként is.

\"Progressive\" = fokozatos fejlesztés: régi vagy gyengébb böngészőben
sima weboldalként működik, modern böngészőben extra képességeket kap.
Semmi nem törik el, ha valami nem támogatott.

### ELŐNYÖK

-   egy kódbázis minden platformra (Android, iOS, Windows, macOS)
-   nincs app store, nincs jóváhagyásra várás, nincs store-jutalék
-   a frissítés azonnal kimegy mindenkinek
-   kicsi, nem kell nagy telepítőcsomagot letölteni

### KORLÁTOK

-   iOS-en nincs automatikus telepítési ajánlat, a user kézzel telepít
    (Megosztás -\> Főképernyőhöz adás)
-   push értesítés iOS-en csak telepített appban működik
-   mély rendszerhozzáférés (háttérfolyamatok, bizonyos hardverek)
    korlátozott a natív apphoz képest
-   Safari böngészőben: ha a user 7 napnyi Safari-használat alatt
    egyszer sem nyitja meg az oldalt, a JS-ből írt tárhely
    (localStorage, IndexedDB) törlődhet. A főképernyőre telepített app
    ez alól kivétel. Nálunk ez nem gond, mert minden fontos adat a
    Laravel DB-ben van, a localStorage csak cache (pl. a téma).

## A HÁROM ALAPPILLÉR

### HTTPS

-   A service worker csak biztonságos kapcsolaton működik.
-   Kivétel: localhost (fejlesztéshez).

### WEB APP MANIFEST

(manifest.webmanifest) JSON fájl, ami leírja az appot a böngészőnek és
az operációs rendszernek. Telepítéskor ebből lesz az ikon, a név, a
splash screen.

Fontos mezők:

-   id az app egyedi azonosítója (pl. \"/\")
-   name teljes név (telepítéskor, splash screenen)
-   short_name rövid név az ikon alá (kb. 12 karakter)
-   description rövid leírás
-   start_url melyik oldal nyíljon meg induláskor
-   scope melyik útvonalak tartoznak az apphoz
-   display standalone = külön ablak, címsor nélkül
-   theme_color címsor / állapotsor színe
-   background_color splash screen háttérszíne
-   icons 192x192, 512x512 + egy 512-es \"maskable\"
-   screenshots gazdagabb telepítő ablak (wide + narrow)
-   shortcuts gyorsmenü az ikonon (hosszú nyomás / jobb klikk)
-   categories, lang, dir
-   launch_handler a már nyitott ablakot használja-e újra

Maskable ikon: Android a saját formájára vágja (kör, lekerekített
négyzet). Ha nincs maskable ikon, fehér keretbe teszi a sima ikont. A
lényeg (logó) az ikon közepén, a \"biztonsági zónában\" legyen, hogy
vágás után is látsszon.

### SERVICE WORKER

(sw.js) JS fájl, ami a böngészőben, az oldaltól külön szálon fut, és
\"proxyként\" az app és a hálózat közé áll: minden kérést elkaphat, és
eldönti, hogy hálózatról vagy cache-ből szolgálja ki.

Életciklus: register -\> install -\> (waiting) -\> activate -\> fetch

-   register: az app regisztrálja (main.tsx-ben vagy a plugin)
-   install: letölti és eltárolja az app shellt (precache)
-   waiting: ha már fut egy régi verzió, az új vár, amíg minden régi fül
    bezárul, vagy a user a \"Frissítés\" gombra nyom
-   activate: átveszi az irányítást, törli a régi cache-eket
-   fetch: kérések kiszolgálása a stratégia szerint

App shell = az app \"váza\": index.html, JS, CSS, ikonok, fontok.
Telepítéskor előre letöltjük (precache), így az app azonnal indul, és
net nélkül is betölt.

Cache stratégiák:
-   Cache First először cache, ha nincs benne, hálózat (képek, fontok,
    3D modellek -- ritkán változnak)
-   Network First először hálózat, ha nincs net, cache (adatok, amiknek
    frissnek kell lenniük)
-   Stale While azonnal a cache-elt választ adja, közben Revalidate
    háttérben frissít (nem kritikus adatok)
-   Network Only soha nem cache-el (login, vásárlás, POST)
-   Cache Only csak cache (precache-elt app shell)

## MIKOR TELEPÍTHETŐ?

Chrome / Edge (Android, desktop):

-   HTTPS
-   manifest: name vagy short_name, 192-es és 512-es ikon, start_url,
    display (standalone / fullscreen / minimal-ui)
-   A service worker már nem feltétele a menüből telepítésnek (Chrome
    108 mobil / 112 desktop óta), de az automatikus telepítési
    ajánlathoz és az offline működéshez továbbra is kell.
-   Saját \"Telepítés\" gomb: beforeinstallprompt esemény (csak Chromium
    alapú böngészőkben van).

iOS / iPadOS Safari:

-   Megosztás -\> Főképernyőhöz adás (nincs automatikus ajánlat, saját
    telepítés gomb sem lehet, csak rövid útmutatót mutathatunk)
-   Az ikon az apple-touch-icon-ból jön: 180x180, NEM átlátszó
    háttérrel, mert az átlátszó részt az iOS feketével tölti ki.
-   Az újabb iOS-eken a főképernyőre tett oldal alapból web appként
    (külön ablakban) nyílik.

## HOGYAN HASZNÁLJUK AZ ÖNFEJLESZTŐ WEBAPP-BAN?

Eszköz: vite-plugin-pwa (a Google Workbox könyvtárára épül)

-   build közben legenerálja a service workert
-   a manifestet a vite.config.ts-ből generálja, és a sort automatikusan
    beszúrja (ezért ilyenkor az index.html-ből a kézi manifest sort
    töröljük)
-   precache-eli a build kimenetét (JS, CSS, HTML, ikonok, fontok)
-   React hookot ad a frissítéshez: useRegisterSW

## TESZTELÉS, HIBAKERESÉS

-   Fejlesztői módban (npm run dev) a service worker alapból NEM fut.
    Teszteléshez: npm run build, majd npm run preview.
-   Chrome DevTools -\> Application fül: Manifest -- hibák, ikonok,
    telepíthetőség Service workers -- állapot, \"Update on reload\",
    Unregister Cache storage -- mi van a cache-ben Storage -- \"Clear
    site data\" (ha beragad egy régi verzió)
-   Offline teszt: DevTools -\> Network -\> \"Offline\"
-   A Lighthouse-ból kikerült a PWA kategória: a telepíthetőséget az
    Application fülön ellenőrizzük (teljesítményt továbbra is a
    Lighthouse mér).
-   Valódi telefonon is tesztelni kell (HTTPS-sel), főleg iOS-en.

## GYAKORI HIBÁK

-   Az index.html vagy a sw.js cache-elődik a szerveren -\> a userek a
    régi verzión ragadnak
-   Relatív manifest útvonal (\"manifest.webmanifest\" perjel nélkül)
    -\> aloldalon rossz helyről kéri le
-   Nincs maskable ikon -\> Androidon fehér keretes ikon
-   Átlátszó apple-touch-icon -\> iOS-en fekete háttér
-   API válaszok / bejelentkezett adatok cache-elése -\> elavult vagy
    más userhez tartozó adat
-   A sw.js nem a gyökérben van -\> a scope csak egy almappára terjed ki
-   Fejlesztés közben beragadt régi service worker -\> DevTools:
    Unregister / Clear site data

## **FORRÁSOK**

-   Chrome telepíthetőségi feltételek változása:
    <https://developer.chrome.com/blog/update-install-criteria>
-   Safari 7 napos tárhely-korlát és a főképernyős appok:
    <https://ionic.io/blog/is-apple-trying-to-kill-pwas>
