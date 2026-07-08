# TourPlanner — Travel Booking & Management System

A full-stack web application for planning and booking tours across Bangladesh. Users can browse destinations, reserve hotel rooms, book transport, and manage their trips — while hotel managers and travel agents handle their own inventories through dedicated dashboards.

Built as a course project for **CSE370 – Database Systems** at BRAC University.

---

## Features

### Traveller (User)
- Register and log in with role-based access
- Browse tour destinations with descriptions
- Select a destination → choose hotel → select room → book transport
- Choose arrival and departure dates, optional dinner reservation
- View and manage personal reservations
- Leave ratings and feedback for visited destinations
- Download booking invoice

### Hotel Manager
- Add hotels linked to destinations
- Add rooms with room number, type (single/double/suite), and cost
- Toggle room availability on/off
- Update hotel details

### Travel Agent
- Add transport options (bus, car, microbus, etc.) for destinations
- Set transport cost and availability
- Manage transport listings

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Python 3, Flask |
| Database | SQLite via Flask-SQLAlchemy |
| ORM | SQLAlchemy (models with relationships and foreign keys) |
| Auth | Flask-Login (session-based, role-based access) |
| Frontend | HTML, CSS, Jinja2 templating |
| Server | Flask development server |

---

## Database Schema

Seven tables with normalized relationships:

```
users
├── user_id (PK)
├── first_name, last_name
├── phone_number, email (unique)
├── username (unique), password
└── role_id  (1=user, 2=hotel manager, 3=travel agent)

tour_destinations
├── destination_id (PK)
├── destination_name
└── destination_description

hotels
├── hotel_id (PK)
├── hotel_name, hotel_location
├── manager_id → users.user_id (FK)
└── dest_id → tour_destinations.destination_id (FK)

rooms
├── room_id (PK)
├── room_no, room_type, cost
├── availability (Boolean)
└── hotel_id → hotels.hotel_id (FK)

transports
├── transport_id (PK)
├── transport_type, cost
├── availability (Boolean)
├── agent_id → users.user_id (FK)
└── dest_id → tour_destinations.destination_id (FK)

reservations
├── reservation_id + user_id (composite PK)
├── reservation_date, arrival_date, departure_date
├── dinner_reservation (Boolean)
├── room_id → rooms.room_id (FK)
└── transport_id → transports.transport_id (FK)

reviews
├── user_id + dest_id (composite PK)
├── rating (Integer)
└── feedback (Text)
```

### Entity Relationships
- A **User** (manager) manages one or more **Hotels**
- A **User** (agent) owns one or more **Transports**
- A **Hotel** belongs to a **TourDestination** and has many **Rooms**
- A **Transport** belongs to a **TourDestination**
- A **Reservation** links a **User** to a **Room** and a **Transport**
- A **Review** links a **User** to a **TourDestination** (composite PK — one review per user per destination)

---

## How to Run Locally

**Prerequisites:** Python 3.8+

**Step 1 — Clone the repository:**
```bash
git clone https://github.com/tanvirul-islam-rifat/Tour-Planner-CSE370-Project.git
cd Tour-Planner-CSE370-Project
```

**Step 2 — Create and activate a virtual environment:**
```bash
python3 -m venv venv
source venv/bin/activate        # Mac/Linux
venv\Scripts\activate           # Windows
```

**Step 3 — Install dependencies:**
```bash
pip install -r requirements.txt
```

**Step 4 — Run the application:**
```bash
python3 app.py
```

**Step 5 — Open in browser:**
```
http://127.0.0.1:5000
```

The SQLite database (`instance/tourplanner.db`) is included in the repository — no database setup required. The app is ready to use immediately after running.

---

## Project Structure

```
Tour-Planner-CSE370-Project/
├── app.py                      # Application entry point
├── requirements.txt            # Python dependencies
├── instance/
│   └── tourplanner.db          # SQLite database (pre-populated)
├── app/
│   ├── __init__.py             # Flask app factory, SQLAlchemy init
│   ├── models.py               # Database models (7 tables)
│   ├── routes.py               # All route handlers and business logic
│   └── templates/
│       ├── index.html          # Landing page
│       ├── home.html           # User home / destination browser
│       ├── login.html          # Login page
│       ├── signup.html         # Registration page
│       ├── see_destination.html
│       ├── select_hotel.html
│       ├── select_room.html
│       ├── select_date.html
│       ├── select_transport.html
│       ├── confirm_booking.html
│       ├── booking_success.html
│       ├── invoice_template.html
│       ├── reservation.html
│       ├── user_reservations.html
│       ├── profile.html
│       ├── add_review.html
│       ├── manage_hotels.html
│       ├── add_rooms.html
│       ├── change_hotel_details.html
│       └── manage_transports.html
└── README.md
```

---

## Technical Architecture

- **Language:** Python 3.x
- **Framework:** Flask (micro web framework)
- **Paradigm:** MVC-style architecture — models (`models.py`), views (Jinja2 HTML templates), controllers (`routes.py`)
- **Database:** SQLite, accessed via Flask-SQLAlchemy ORM
- **Authentication:** Flask-Login with role-based access control (`role_id` field distinguishes travellers, hotel managers, and travel agents)
- **Interface:** Web browser (HTML/CSS frontend served by Flask)

## Core Engineering Practices Demonstrated

- **Relational Database Design:** Seven normalized tables with foreign key constraints, one-to-many relationships (Hotel → Rooms, Destination → Hotels), and composite primary keys (Reservation, Review) — designed to eliminate data redundancy
- **ORM-based Data Modeling:** All database interactions handled through SQLAlchemy model classes with defined `relationship()` and `backref` mappings — no raw SQL strings in application code
- **Role-Based Access Control:** A single `users` table with a `role_id` field gates access to three completely separate dashboards (traveller, hotel manager, travel agent) using Flask-Login session management
- **Multi-step Booking Flow:** Reservation creation spans five sequential pages (destination → hotel → room → transport → date → confirm) with state carried through the session, demonstrating multi-step form handling in a stateless HTTP environment
- **Composite Primary Keys:** The `reservations` and `reviews` tables use composite PKs (`user_id + reservation_id`, `user_id + dest_id`) to enforce business rules at the database level — a user cannot review the same destination twice
- **Invoice Generation:** A dedicated invoice template renders a printable booking summary from reservation data, demonstrating dynamic document generation from a relational query

## Author

**Md. Tanvirul Islam Rifat**
- **GitHub:** [@tanvirul-islam-rifat](https://github.com/tanvirul-islam-rifat)
- **LinkedIn:** [Tanvirul Islam Rifat](https://www.linkedin.com/in/tanvirul-islam-rifat)
