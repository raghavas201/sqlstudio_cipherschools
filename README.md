# sqlstudio_cipherschools

## 1. Project Overview
sqlstudio_cipherschools is a browser-based SQL learning platform designed as a sandbox for students to practice SQL queries. It provides a safe environment where users can execute read-only SQL queries against pre-configured datasets to solve specific assignments. The system is designed for educational purposes, focusing on query logic without the risk of data modification.

## 2. Tech Stack

### Frontend
- React (Next.js)
- Tailwind CSS

### Backend
- Node.js
- Express.js

### Databases
- PostgreSQL (Supabase)
- MongoDB (Atlas)

### LLM
- Google Gemini (via AI Studio)

## 3. Architecture Overview
The application follows a client-server architecture with Express.js acting as the central orchestrator.

- **Frontend ↔ Backend API**: The React frontend communicates with the Node.js backend via RESTful APIs for fetching assignments, executing queries, and requesting hints.
- **Backend ↔ PostgreSQL**: The backend connects to a Supabase PostgreSQL instance to execute user-submitted SQL queries in a read-only transaction.
- **Backend ↔ MongoDB**: Assignment metadata, questions, and answers are stored in MongoDB Atlas and retrieved by the backend.
- **Backend ↔ LLM**: The backend sends assignment context and user queries to the Google Gemini API to generate conceptual hints.

## 4. Data Flow: Execute Query
1.  **User submits SQL**: The user types a query in the frontend editor and clicks "Execute".
2.  **Backend validates SQL**: The backend acts as a gatekeeper, checking for forbidden keywords (INSERT, UPDATE, DELETE, DROP) and ensuring the query is a SELECT statement.
3.  **Query executes in PostgreSQL**: If valid, the query is executed against the PostgreSQL database within a read-only transaction block.
4.  **Results returned to frontend**: The resulting rows or error messages are sent back to the frontend for display.

*(See hand-drawn data-flow diagram in `/diagrams`)*

## 5. Database Usage

### MongoDB
- **Purpose**: Stores assignment metadata.
- **Content**: Assignment titles, descriptions, difficulty levels, and the "expected" schema/data used for frontend display (sample data).
- **Constraint**: MongoDB does NOT execute user SQL queries.

### PostgreSQL
- **Purpose**: Executes user SQL queries.
- **Content**: Pre-seeded relational tables (e.g., employees, orders) that match the assignment schemas.
- **Constraint**: Enforced as read-only.

## 6. Security & Constraints
- **SELECT-only enforcement**: The SQL validator detects and blocks any data modification statements (DML) or data definition statements (DDL).
- **SQL validation**: Input is sanitized and validated on the backend before ever reaching the database driver.
- **No schema modification**: Users cannot create, drop, or alter tables.
- **No data mutation**: Users cannot insert, update, or delete rows.
- **Backend-only access**: Database credentials are stored securely on the backend server; the frontend has no direct access to the databases.

## 7. Setup Instructions
1.  **Clone repo**: Clone the project repository to your local machine.
2.  **Install dependencies**:
    - Run `npm install` in the `backend` directory.
    - Run `npm install` in the `frontend` directory.
3.  **Configure environment variables**: Create `.env` files in both directories using the provided examples.
4.  **Start backend and frontend**:
    - Terminal 1: `cd backend` then `npm run dev`
    - Terminal 2: `cd frontend` then `npm run dev`

## 8. Environment Variables
**Backend (.env)**
- `PORT`
- `MONGO_URI`
- `POSTGRES_URL`
- `LLM_API_KEY`

**Frontend (.env.local)**
- `NEXT_PUBLIC_API_BASE_URL`

## 9. Known Limitations
- **No authentication**: The system is an open sandbox; user progress is not saved across sessions.
- **No auto-grading**: The platform executes queries but does not automatically verify if the result matches the correct answer.
- **Limited SQL support**: Optimized for standard `SELECT` queries; advanced stored procedures or specific database extensions may not be supported.
- **Single-user assumptions**: Created as a simple project environment, not optimized for high-concurrency production loads.

