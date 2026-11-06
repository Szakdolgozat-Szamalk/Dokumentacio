# GraphQL alapok
## 1. Mi az a GraphQL?
A **GraphQL** egy API-khoz használható lekérdezési nyelv és futtatókörnyezet. Segítségével a frontend pontosan meghatározhatja, hogy milyen adatokat szeretne lekérni a backenden keresztül.
A REST API egyik alternatívája, de a két megoldás akár ugyanabban a projektben is használható.
## 2. GraphQL és REST API összehasonlítása
### REST API
REST API esetén általában több különálló végpontot használunk az adatok lekérésére és módosítására.
Például:
| HTTP metódus | Végpont | Művelet |
|---|---|---|
| GET | `/api/events` | Események listázása |
| GET | `/api/events/1` | Egy esemény lekérése |
| POST | `/api/events` | Új esemény létrehozása |
| PUT | `/api/events/1` | Esemény módosítása |
| DELETE | `/api/events/1` | Esemény törlése |
### GraphQL
GraphQL esetén jellemzően egy közös végpontot használunk, például `/graphql`.
A kérésben adjuk meg, hogy milyen műveletet szeretnénk végrehajtani, és milyen adatokat kérünk vissza.
Példa:
```graphql
query {
  events {
    title
    date
  }
}
```
Ebben a példában csak az események címét és dátumát kérjük le, a többi mezőre nincs szükségünk.
**Legfontosabb különbség:**
- GraphQL esetén a kliens kiválaszthatja a lekérni kívánt mezőket.
- REST esetén jellemzően a végpont által meghatározott válaszstruktúrát kapjuk meg.
## 3. A GraphQL legfontosabb fogalmai
### 3.1. Schema (Séma)
A séma határozza meg, hogy milyen adattípusok, mezők és műveletek érhetők el az API-n keresztül.
Példa:
```graphql
type Event {
  id: ID!
  title: String!
  date: String!
  description: String
}
type Query {
  events: [Event!]!
  event(id: ID!): Event
}
```
A séma leírja az esemény adatszerkezetét és a lekérdezési lehetőségeket.
### 3.2. Query (Lekérdezés)
Az adatok lekérésére szolgál, hasonlóan a REST API GET kéréseihez.
Példa:
```graphql
query {
  event(id: 1) {
    title
    date
  }
}
```
Ebben a példában egy adott esemény címét és dátumát kérjük le az azonosítója alapján.
### 3.3. Mutation (Módosítás)
Az adatok módosítására használhatjuk. Ide tartozik:
- Új adatok létrehozása.
- Meglévő adatok frissítése.
- Adatok törlése.
Példa:
```graphql
mutation {
  createEvent(title: "Esemény") {
    id
    title
  }
}
```
A példa egy egyszerűsített szemléltetés. A tényleges műveletet a séma definíciójának megfelelően kell kialakítani.
### 3.4. Resolver
A resolver határozza meg, hogy hogyan kapja meg a rendszer a kért adatokat, illetve hogyan hajtja végre a műveleteket.
Például lekérdezheti az eseményeket az adatbázisból.
## 4. Regisztráció és bejelentkezés GraphQL-lel
A GraphQL önmagában nem biztosít autentikációt. A regisztrációt és a bejelentkezést külön műveletekként kell megvalósítani.
### A folyamat részei
1. Felhasználói adatok validálása.
2. Jelszavak biztonságos hash-elése.
3. Regisztráció és bejelentkezés megvalósítása.
4. Session vagy token alapú hitelesítés kialakítása.
5. Jogosultságok ellenőrzése a védett műveleteknél.
### Autentikáció és autorizáció
Fontos különbség a két fogalom között:
| Fogalom | Jelentése |
|---|---|
| Autentikáció | A felhasználó személyazonosságának ellenőrzése. |
| Autorizáció | Annak ellenőrzése, hogy a felhasználó milyen műveleteket végezhet el. |
## 5. Hasznos dokumentációk
- [GraphQL hivatalos dokumentáció](https://graphql.org/learn/)
- [Laravel Lighthouse dokumentáció](https://lighthouse-php.com/)
- [Apollo dokumentáció](https://www.apollographql.com/docs/)
A **Laravel Lighthouse** egy Laravelhez készült GraphQL-szervercsomag, ezért PHP-s backend esetén releváns lehet.
Az **Apollo** egy másik, többek között Node.js-alapú GraphQL-megoldás.
## 6. GraphQL használata Laravelben
### 6.1. Szükséges eszközök
A fejlesztéshez az alábbiak szükségesek:
| Eszköz | Feladat |
|---|---|
| PHP | A Laravel futtatásához |
| Composer | PHP-csomagok és Laravel-függőségek kezelésére |
| Laravel | Backend keretrendszer |
| Lighthouse | GraphQL-szerver Laravelhez |
| Node.js és npm | Opcionálisan a frontend-eszközökhöz |
### 6.2. Telepítések ellenőrzése
Első lépésként ellenőrizzük, hogy a PHP és a Composer telepítve van-e.
A terminálban futtassuk a következő parancsokat:
```bash
php -v
composer --version
node -v
npm -v
```
Ha valamelyik parancs nem működik, először telepíteni vagy konfigurálni kell a hozzá tartozó eszközt.
### 6.3. Laravel-projekt létrehozása
Hozzunk létre egy különálló projektet a GraphQL kipróbálásához:
```bash
composer create-project laravel/laravel graphql-gyakorlat
cd graphql-gyakorlat
```
Ez létrehoz egy új Laravel-alkalmazást a `graphql-gyakorlat` mappában.
### 6.4. Lighthouse telepítése
A projekt mappájában futtassuk:
```bash
composer require nuwave/lighthouse
```
Ez telepíti a Lighthouse csomagot, amely lehetővé teszi a GraphQL API kialakítását Laravelben.
Ezután publikáljuk az alapértelmezett GraphQL-sémát:
```bash
php artisan vendor:publish --tag=lighthouse-schema
```
A séma a GraphQL API által elérhető adattípusokat és műveleteket írja le.
### 6.5. Laravel-szerver elindítása
A projekt gyökérmappájában futtassuk:
```bash
php artisan serve
```
A Laravel fejlesztői szervere általában a következő címen érhető el:
```text
http://127.0.0.1:8000
```
A Lighthouse alapértelmezett GraphQL-végpontja:
```text
http://127.0.0.1:8000/graphql
```
### 6.6. GraphQL-kérések tesztelése
A GraphQL-kéréseket jellemzően HTTP POST kéréssel küldjük el a `/graphql` végpontra.
A teszteléshez használhatunk:
- GraphiQL felületet.
- Más API-tesztelő eszközt.
A GraphiQL felület külön beállítást igényelhet.