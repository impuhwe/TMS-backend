# Task Management System Backend

REST API for managing tasks in the Task Management System. The API stores task data in MongoDB and exposes CRUD endpoints for the frontend application.

## Technologies Used

- **Node.js** - JavaScript runtime
- **Express 5** - HTTP server and REST API framework
- **MongoDB** - Document database
- **Mongoose** - MongoDB object modeling and schema validation
- **dotenv** - Loads configuration from environment variables
- **CORS** - Allows requests from the frontend application
- **Nodemon** - Restarts the server automatically during development

## Prerequisites

Install the following before running the backend:

- Node.js and npm
- A MongoDB instance, either:
  - MongoDB Community Server running locally, or
  - A MongoDB Atlas cluster

## Installation

From the `backend-project` directory, install the dependencies:

```bash
npm install
```

Create a `.env` file in the `backend-project` directory:

```env
MONGO_URI=mongodb://127.0.0.1:27017/tms
PORT=5000
```

For MongoDB Atlas, replace `MONGO_URI` with the connection string supplied by Atlas:

```env
MONGO_URI=mongodb+srv://<username>:<password>@<cluster-url>/tms?retryWrites=true&w=majority
PORT=5000
```

Keep the `.env` file private and do not commit database credentials.

## Database Setup

### Local MongoDB

1. Install MongoDB Community Server.
2. Start the MongoDB service.
3. Set `MONGO_URI` to `mongodb://127.0.0.1:27017/tms`.
4. Start the backend. MongoDB and the `tms` database will be used automatically.

No manual database or collection creation is required. Mongoose creates the database and task collection when the first task is saved.

### MongoDB Atlas

1. Create a cluster in MongoDB Atlas.
2. Create a database user.
3. Add the development machine's IP address to the Atlas network access list.
4. Copy the connection string into `MONGO_URI` in `.env`.
5. Start the backend.

## Running the Project

Start the server in production-style mode:

```bash
npm start
```

Start the server in development mode with automatic restarts:

```bash
npm run dev
```

By default, the API runs at `http://localhost:5000`. The root endpoint can be used as a basic health check:

```text
GET http://localhost:5000/
```

Expected response:

```json
{
  "message": "API running"
}
```

## API Endpoints

All task endpoints use the `/tasks` base path.

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/tasks` | Get all tasks, newest first |
| `GET` | `/tasks?status=Pending` | Filter tasks by status |
| `GET` | `/tasks/:id` | Get one task by MongoDB ObjectId |
| `POST` | `/tasks` | Create a task |
| `PUT` | `/tasks/:id` | Update a task |
| `DELETE` | `/tasks/:id` | Delete a task |

### Create Task Example

```json
{
  "title": "Prepare project documentation",
  "description": "Document the installation and API workflow.",
  "status": "Pending",
  "priority": "High"
}
```

### Task Fields

- `title` - Required, trimmed string
- `description` - Optional string; defaults to an empty string
- `status` - `Pending` or `Completed`; defaults to `Pending`
- `priority` - `Low`, `Medium`, or `High`; defaults to `Medium`
- `createdAt` and `updatedAt` - Automatically managed by Mongoose

## Technical Decisions and Additional Features

- **MVC-style organization:** Routes define endpoints, controllers handle request logic, and the Mongoose model defines the task schema.
- **Environment-based configuration:** The MongoDB connection string and port are read from environment variables instead of being hard-coded.
- **Validation:** Required fields and enum values are enforced by the controller and Mongoose schema. Invalid MongoDB IDs and status filters return a `400` response.
- **Consistent error responses:** Missing tasks return `404`; validation and malformed input return `400`; unexpected server errors return `500`.
- **Automatic timestamps:** Mongoose timestamps provide `createdAt` and `updatedAt` values for every task.
- **CORS support:** CORS middleware allows the separate frontend application to call the API during local development.
- **JSON request handling:** Express parses incoming JSON request bodies with `express.json()`.
- **Newest-first results:** Task queries are sorted by `createdAt` in descending order.

## Project Structure

```text
backend-project/
├── config/
│   └── db.js
├── controllers/
│   └── taskController.js
├── models/
│   └── Task.js
├── routes/
│   └── taskRoutes.js
├── .env
├── package.json
├── query
└── server.js
```

## Notes

- The backend must be connected to MongoDB before it starts listening for requests.
- The `query` file currently contains the text `MongoDB`; database initialization is handled by Mongoose rather than by a separate SQL script.
- The backend package does not currently define automated tests. The `npm test` script is still the default placeholder.
