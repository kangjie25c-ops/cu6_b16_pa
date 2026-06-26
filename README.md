# CU6 B15 - Practical Assessment (Event Management API)

## Getting Started - Follow These Steps In Order

### Step 1: Extract the Files

Unzip the downloaded `.zip` file to a folder on your computer.

### Step 2: Open in VS Code

Open the `cu6_b15_pa-qv` folder in Visual Studio Code.

### Step 3: Install Dependencies

Open the terminal in VS Code and run:

```bash
npm install
```

This installs Express, Mongoose, CORS, and Nodemon.

### Step 4: Set Up MongoDB Connection

Before starting the server, make sure your MongoDB is running (either locally or via MongoDB Atlas).

Open `server.js` and add your MongoDB connection where indicated:

```js
mongoose.connect("mongodb://localhost:27017/cu6_b15_pa")
```

Or use your Atlas connection string if you are using a cloud database.

### Step 5: Start the Server

```bash
npm start
```

You should see:

```
Server started on port 5123
```

If you also see a successful MongoDB connection message, you are ready to continue.

### Step 6: Test the Base Route

Open your browser or Postman and go to:

```
http://localhost:5123/
```

You should see: **"Good luck with the exam!"**

---

## What to Work On (Recommended Order)

The project is an **Event Management API** with three entities: **Organizer**, **Event**, and **Registration**. Work through them in this order since Events depend on Organizers, and Registrations depend on Events.

---

### Phase 1: Organizer (Start Here - No Dependencies)

Work on these files in order:

| Step | File | What to Do |
|------|------|------------|
| 1 | `models/organizer.js` | Create the Mongoose schema with fields: `name` (String, required), `bio` (String), `contact` (String) |
| 2 | `controllers/organizer.js` | Create CRUD functions: `getOrganizers`, `getOrganizer`, `AddNewOrganizer`, `updateOrganizer`, `deleteOrganizer` |
| 3 | `routes/organizer.js` | Set up routes: `GET /organizers`, `GET /organizers/:id`, `POST /organizers`, `PUT /organizers/:id`, `DELETE /organizers/:id` |
| 4 | `server.js` | Register the organizer routes |

**Test with Postman:**
- `POST http://localhost:5123/organizers` with JSON body `{ "name": "John", "bio": "Event planner", "contact": "john@email.com" }`
- `GET http://localhost:5123/organizers` - should return the organizer you just created

---

### Phase 2: Event (Depends on Organizer)

| Step | File | What to Do |
|------|------|------------|
| 1 | `models/event.js` | Create the schema with fields: `title` (String, required), `organizer` (ObjectId, ref: 'Organizer', required), `date` (Date, required), `location` (String, required), `category` (String), `description` (String) |
| 2 | `controllers/event.js` | Create CRUD functions: `getEvents`, `getEvent`, `AddNewEvent`, `updateEvent`, `deleteEvent`. Use `.populate('organizer')` on GET requests |
| 3 | `routes/event.js` | Set up routes: `GET /events`, `GET /events/:id`, `POST /events`, `PUT /events/:id`, `DELETE /events/:id`. The GET all route should support sorting, pagination, and filtering |
| 4 | `server.js` | Register the event routes |

**Test with Postman:**
- `POST http://localhost:5123/events` with JSON body using an organizer `_id` from Phase 1
- `GET http://localhost:5123/events` - should return events with organizer details populated

---

### Phase 3: Registration (Depends on Event)

| Step | File | What to Do |
|------|------|------------|
| 1 | `models/registration.js` | Create the schema with fields: `event` (ObjectId, ref: 'Event', required), `name` (String, required), `email` (String, required), `phone` (String, required), `registrationDate` (Date, default: Date.now) |
| 2 | `controllers/registration.js` | Create functions: `getRegistrationsByEvent` (with `.populate('event')`), `createRegistration` (prevent duplicate email+event combinations) |
| 3 | `routes/registration.js` | Set up routes: `GET /registrations?event=xxx`, `POST /registrations` |
| 4 | `server.js` | Register the registration routes |

**Test with Postman:**
- `POST http://localhost:5123/registrations` with an event `_id` from Phase 2
- `GET http://localhost:5123/registrations?event=<eventId>` - should return registrations with event details
- Try posting the same email + event again - it should be rejected (no duplicates)

---

### Don't Forget in `server.js`

You also need to:
- Set up CORS middleware (`app.use(cors())`)
- Connect to MongoDB
- Import and use all three route files

---

## Understanding the Folder Structure

Think of this project like a restaurant:

### `models/` - The Menu (What the data looks like)

This folder describes the **shape of your data**. Each file is like a form template.
For example, the organizer model says: "Every organizer must have a name, and can optionally have a bio and contact info."

It does NOT do anything with the data — it just describes what it should look like.

### `controllers/` - The Kitchen (Where the work happens)

This folder contains the **actual logic**. When someone asks "give me all events" or "create a new organizer", the controller is the code that talks to the database, gets or saves the data, and sends back a response.

Think of it as the chef — it receives the order and prepares the result.

### `routes/` - The Waiter (Connects URLs to the kitchen)

This folder decides **which controller function to call** based on the URL and HTTP method. For example:
- Someone visits `GET /organizers` → the route sends it to the `getOrganizers` controller function
- Someone sends `POST /events` → the route sends it to the `AddNewEvent` controller function

Think of it as the waiter — it takes the customer's order (the URL) and passes it to the kitchen (the controller).

### How they work together

```
A request comes in (e.g. GET /events)
        |
        v
   routes/event.js        --> "Oh, they want all events. Let me call getEvents."
        |
        v
   controllers/event.js   --> "Let me ask the database for all events and send them back."
        |
        v
   models/event.js        --> "Here's what an event looks like in the database."
```

### Full folder layout

```
cu6_b15_pa-qv/
  server.js              <- The starting point. Connects everything together.
  package.json           <- Lists what packages the project needs
  models/
    organizer.js         <- Describes what an organizer looks like
    event.js             <- Describes what an event looks like
    registration.js      <- Describes what a registration looks like
  controllers/
    organizer.js         <- Logic for creating, reading, updating, deleting organizers
    event.js             <- Logic for creating, reading, updating, deleting events
    registration.js      <- Logic for creating and reading registrations
  routes/
    organizer.js         <- URLs for organizer actions
    event.js             <- URLs for event actions
    registration.js      <- URLs for registration actions
```

## Quick Reference

- **Server runs on:** `http://localhost:(your local port)`
- **Start command:** `npm start` (uses nodemon for auto-restart)
- **Database:** MongoDB (local)
