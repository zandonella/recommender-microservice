# Recommender Microservice

This microservice generates **personalized cat recommendations** for users in
the **CatCall** platform. It pulls data from the Preferences, Favorites, and Cat
Database microservices, filters out liked cats, and scores the remaining results
based on how well they match a user's preferences.

## Notes

- This service is part of the CatCall platform and depends on three other
  microservices:
  - **Preferences Service** – for user filter settings
  - **Favorites Service** – to exclude previously liked cats
  - **Cat Database Service** – to retrieve cat listings
- MongoDB is **not used** in this service.
- Recommendations can be returned in **strict mode** (exact matches only) or
  **scored mode** (ranked by match strength).

## Base Configuration

- **Base URL:** `http://localhost:<PORT>/api/recommend`
- **Default Port:** `3000`
- **Port in CatCall:** Determined by `PORT_<SERVICE>` in the root `.env`
- **Change the Port:** Set `PORT=<your_port>` in a `.env` file (see
  `.env.example`)
- **Content Type:** `application/json`
- **Response Format:** `JSON`

---

## Endpoints

> All endpoints expect and return JSON. If a server, user validation, or
> database error occurs, a `500 Internal Server Error` or `400/404` response
> will be returned with an appropriate message.

| Method | Route            | Description                                     |
| ------ | ---------------- | ----------------------------------------------- |
| GET    | `/api/recommend` | Return a list of cat recommendations for a user |

---

### `GET /api/recommend`

**Return a list of cat recommendations for a specific user.**

- Requires a `userID` query parameter.
- Combines preferences and favorites to filter and sort available cats.
- If preferences include `strict: true`, only exact matches are returned.
- Otherwise, cats are **scored** based on how closely they match the user’s
  preferences.

**Query Parameters:**

- `userID` – The user’s unique identifier (usually an email)

**Example:**

```http
GET /api/recommend?userID=user@example.com
```

**Success Response:**

```json
{
  "recommendedCats": [
    {
      "_id": "cat-123",
      "name": "Mittens",
      "age": 3,
      "color": "Gray",
      "catScore": 4
    },
    {
      "_id": "cat-456",
      "name": "Whiskers",
      "age": 5,
      "color": "White",
      "catScore": 3
    }
  ]
}
```

- If in strict mode, `catScore` is omitted and only exact matches are returned.

**Error Responses:**

```json
{ "error": "Missing userID" }
```

```json
{ "error": "Error getting recommendations" }
```

---

## Environment Setup To Run Locally

1. Copy the example environment file:

```bash
cp .env.example .env
```

2. Modify the values in `.env` as needed:

**Example `.env` contents:**

```env
PORT=3000

PREFERENCES_SERVICE_URL=http://localhost:3001
FAVORITES_SERVICE_URL=http://localhost:3002
CAT_DATABASE_URL=http://localhost:3003
```

---

## Running Locally (Without Docker)

Make sure [Node.js](https://nodejs.org/) is installed and all dependent services
are running.

```bash
npm install
npm start
```

Expected output:

```
Server is running on port 3000.
```

---

## Running with Docker

You can also run this microservice in isolation using Docker:

### 1. Build the image

```bash
docker build -t recommender-microservice .
```

### 2. Run the container

```bash
docker run -p 3000:3000 --env-file .env recommender-microservice
```

> ⚠️ Ensure your `.env` file includes valid local service URLs for
> `PREFERENCES_SERVICE_URL`, `FAVORITES_SERVICE_URL`, and `CAT_DATABASE_URL`.

---
