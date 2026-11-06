# GraphQL Basics
## 1. What is GraphQL?
**GraphQL** is a query language and runtime for APIs. It allows the frontend to specify exactly which data it wants to retrieve from the backend.
It is an alternative to REST APIs, but both approaches can also be used within the same project.
## 2. GraphQL vs. REST API
### REST API
With REST APIs, we generally use multiple separate endpoints to retrieve and modify data.
For example:
| HTTP Method | Endpoint | Operation |
|---|---|---|
| GET | `/api/events` | Retrieve a list of events |
| GET | `/api/events/1` | Retrieve a single event |
| POST | `/api/events` | Create a new event |
| PUT | `/api/events/1` | Update an event |
| DELETE | `/api/events/1` | Delete an event |
### GraphQL
With GraphQL, we typically use a single endpoint, such as `/graphql`.
The request specifies which operation we want to perform and which data we want to receive in return.
Example:
```graphql
query {
  events {
    title
    date
  }
}
```
In this example, we only request the event titles and dates. We do not need the other fields.
**The main difference:**
- With GraphQL, the client can select the fields it wants to retrieve.
- With REST, the client typically receives a response structure predefined by the endpoint.
## 3. The Most Important GraphQL Concepts
### 3.1. Schema
The schema defines which data types, fields, and operations are available through the API.
Example:
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
The schema describes the event data structure and the available query operations.
### 3.2. Query
Queries are used to retrieve data, similarly to GET requests in REST APIs.
Example:
```graphql
query {
  event(id: 1) {
    title
    date
  }
}
```
In this example, we retrieve the title and date of a specific event based on its ID.
### 3.3. Mutation
Mutations are used to modify data. This includes:
- Creating new data.
- Updating existing data.
- Deleting data.
Example:
```graphql
mutation {
  createEvent(title: "Event") {
    id
    title
  }
}
```
This example is a simplified illustration. The actual operation must be implemented according to the schema definition.
### 3.4. Resolver
A resolver defines how the system retrieves the requested data and how it executes operations.
For example, it can retrieve events from the database.
## 4. Registration and Login with GraphQL
GraphQL does not provide authentication by itself. Registration and login must be implemented as separate operations.
### The main steps
1. Validate user data.
2. Securely hash passwords.
3. Implement registration and login.
4. Set up session-based or token-based authentication.
5. Check permissions for protected operations.
### Authentication vs. Authorization
There is an important difference between these two concepts:
| Concept | Meaning |
|---|---|
| Authentication | Verifying the identity of a user. |
| Authorization | Checking which operations a user is allowed to perform. |
## 5. Useful Documentation
- [Official GraphQL Documentation](https://graphql.org/learn/)
- [Laravel Lighthouse Documentation](https://lighthouse-php.com/)
- [Apollo Documentation](https://www.apollographql.com/docs/)
**Laravel Lighthouse** is a GraphQL server package built for Laravel, making it a relevant option for PHP-based backends.
**Apollo** is another GraphQL solution, used with technologies such as Node.js.
## 6. Using GraphQL with Laravel
### 6.1. Required Tools
The following tools are required for development:
| Tool | Purpose |
|---|---|
| PHP | Required to run Laravel |
| Composer | Managing PHP packages and Laravel dependencies |
| Laravel | Backend framework |
| Lighthouse | GraphQL server for Laravel |
| Node.js and npm | Optional tools for frontend development |
### 6.2. Checking the Installation
First, make sure PHP and Composer are installed.
Run the following commands in the terminal:
```bash
php -v
composer --version
node -v
npm -v
```
If any of these commands do not work, the corresponding tool must be installed or configured first.
### 6.3. Creating a Laravel Project
Create a separate project to experiment with GraphQL:
```bash
composer create-project laravel/laravel graphql-gyakorlat
cd graphql-gyakorlat
```
This creates a new Laravel application inside the `graphql-gyakorlat` directory.
### 6.4. Installing Lighthouse
Run the following command inside the project directory:
```bash
composer require nuwave/lighthouse
```
This installs the Lighthouse package, which allows you to build a GraphQL API in Laravel.
Next, publish the default GraphQL schema:
```bash
php artisan vendor:publish --tag=lighthouse-schema
```
The schema describes the data types and operations available through the GraphQL API.
### 6.5. Starting the Laravel Server
Run the following command from the project's root directory:
```bash
php artisan serve
```
The Laravel development server is usually available at:
```text
http://127.0.0.1:8000
```
The default Lighthouse GraphQL endpoint is:
```text
http://127.0.0.1:8000/graphql
```
### 6.6. Testing GraphQL Requests
GraphQL requests are typically sent to the `/graphql` endpoint using HTTP POST requests.
For testing, you can use:
- The GraphiQL interface.
- Other API testing tools.
The GraphiQL interface may require additional configuration.