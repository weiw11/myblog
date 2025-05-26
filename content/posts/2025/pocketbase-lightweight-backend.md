---
title: "PocketBase: Lightweight Backend for Modern Prototyping"
tags: ["pocketbase", "firebase", "supabase", "api", "sql", "auth", "security"]
date: 2025-05-25T16:04:13-04:00
draft: false
---

PocketBase is an open-source backend written in Go, designed to be lightweight, fast, and developer-friendly. It's ideal for developers building prototypes, local-first applications, or lightweight production apps without managing a complex backend stack.

![pocketbase overview](/images/pocketbase_overview.png)

## What is PocketBase?

PocketBase is a single-binary backend that includes:

- Embedded SQLite database
- RESTful and WebSocket APIs
- File storage system
- User authentication (email/password, OAuth2)
- Web-based admin UI
- Built-in data validation and access control

It runs entirely from a **single executable**, requiring no external services or runtime dependencies.

## Key Features

### Single Binary Deployment

Running PocketBase is as simple as downloading the binary and executing:

```bash
./pocketbase serve
```

This launches both the API server and a web-based admin panel at `http://127.0.0.1:8090/_/`.

### Realtime with WebSockets

PocketBase supports realtime updates over WebSocket connections, allowing frontend applications to react instantly to data changes.

### Authentication

- Email/password auth
- OAuth2 support
- Admin-only access controls and record-level rules

### File Storage

Files can be uploaded via API and stored either locally or in a S3 compatible storage.

### Extensive

One of the main feature of PocketBase is that it can be used as a framework which enables you to write your own custom app business logic in [Go](https://pocketbase.io/docs/go-overview) or [JavaScript](https://pocketbase.io/docs/js-overview) and still have a portable backend at the end.


This is done by creating a `*.pb.js` file(s) inside the `pb_hooks` directory.

```js
// Creating a custom api endpoint
routerAdd("GET", "/hello/{name}", (e) => {
  let name = e.request.pathValue("name")
  return e.json(200, { "message": "Hello " + name })
})

// Custom hook on actions, such as record successful update like below
onRecordAfterUpdateSuccess((e) => {
    console.log("user updated...", e.record.get("email"))
    e.next()
}, "users")
```

## Use Cases

PocketBase is suitable for:

- Rapid prototyping
- Offline-first mobile or desktop apps
- Internal tools or admin dashboards
- Small-to-medium production applications

It’s not intended for large-scale, horizontally scalable systems.

## Sample API Usage

### Create a Collection Record

```bash
curl -X POST http://127.0.0.1:8090/api/collections/posts/records \
  -H "Content-Type: application/json" \
  -d '{"title": "Hello PocketBase", "content": "Quick and local backend"}'
```

### Subscribe to Realtime Changes (JavaScript)

```js
const client = new PocketBase('http://127.0.0.1:8090');

client.collection('posts').subscribe('*', (event) => {
  console.log('Change detected:', event.action, event.record);
});
```

## Limitations

While PocketBase is powerful for its size, it's important to be aware of its constraints:

- Based on SQLite, which limits concurrent write throughput
- Not built for distributed or high-availability deployments
- Still under active development; some features may be evolving

## Conclusion

PocketBase offers a practical and efficient solution for developers needing a minimal backend without the overhead of a traditional server stack.

Its single-binary design, realtime capabilities, and straightforward API make it a strong choice for many modern applications, especially where simplicity and speed of development are priorities.
