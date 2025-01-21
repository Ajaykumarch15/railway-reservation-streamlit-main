# Railway System Management Application with Streamlit and SQLite

## Overview

This application is a comprehensive **Railway System Management** tool developed using **Streamlit** and **SQLite**. It provides functionality for train administrators to manage trains, book tickets, cancel reservations, and view seat details. It also allows users to search for trains based on train numbers or destinations.

---

## Features

### 1. **Database Initialization**
   - The database is initialized with three main tables:
     - **Users**: Stores user credentials.
     - **Employees**: Stores employee credentials and designations.
     - **Trains**: Stores train details, including train number, name, departure date, starting destination, and ending destination.
   - Dynamic seat tables are created for each train with 50 seats, categorized into **Window**, **Aisle**, and **Middle**.

### 2. **Train Management**
   - **Add Train**: Add new trains with details such as train number, name, departure date, starting destination, and ending destination. Automatically creates a seat table for the train.
   - **View Trains**: Display all trains stored in the database.
   - **Search Train**:
     - Search by train number.
     - Search by starting and ending destinations.
   - **Delete Train**: Delete train details and associated seat data.

### 3. **Seat Management**
   - **Book Ticket**:
     - Select train number and seat type.
     - Provide passenger details (name, age, gender).
     - Automatically assigns the next available seat.
   - **Cancel Ticket**: Cancel a ticket by providing the train number and seat number.
   - **View Seats**: View seat availability and passenger details for a selected train.

### 4. **Dynamic Seat Allocation**
   - Seats are categorized as:
     - **Window**: Seats ending in 0, 4, 5, or 9.
     - **Aisle**: Seats ending in 2, 3, 6, or 7.
     - **Middle**: Remaining seats.
   - The system dynamically tracks available seats and assigns them during booking.

---

## Code Highlights

### **Database Setup**
The `create_DB_if_Not_available()` function initializes the database tables if they do not already exist:
```python
def create_DB_if_Not_available():
    c.execute('''CREATE TABLE IF NOT EXISTS users (username TEXT, password TEXT)''')
    c.execute('''CREATE TABLE IF NOT EXISTS employees (employee_id TEXT, password TEXT, designation TEXT)''')
    c.execute('''CREATE TABLE IF NOT EXISTS trains (train_number TEXT, train_name TEXT, departure_date TEXT, starting_destination TEXT, ending_destination TEXT)''')
```

### **Train Management Functions**
- **Add Train**:
  ```python
  def add_train(train_number, train_name, departure_date, starting_destination, ending_destination):
      c.execute("INSERT INTO trains VALUES (?, ?, ?, ?, ?)",
                (train_number, train_name, departure_date, starting_destination, ending_destination))
      conn.commit()
      create_seat_table(train_number)
  ```

- **Search Train by Destination**:
  ```python
  def search_trains_by_destinations(starting_destination, ending_destination):
      train_query = c.execute("SELECT * FROM trains WHERE starting_destination = ? AND ending_destination = ?",
                              (starting_destination, ending_destination))
      return train_query.fetchall()
  ```

### **Seat Management**
- **Book Ticket**:
  ```python
  def book_ticket(train_number, passenger_name, passenger_age, passenger_gender, seat_type):
      seat_number = allocate_next_available_seat(train_number, seat_type)
      if seat_number:
          c.execute(f"UPDATE seats_{train_number} SET booked=1, passenger_name=?, passenger_age=?, passenger_gender=? WHERE seat_number=?",
                    (passenger_name, passenger_age, passenger_gender, seat_number[0]))
          conn.commit()
  ```

- **Cancel Ticket**:
  ```python
  def cancel_tickets(train_number, seat_number):
      c.execute(f"UPDATE seats_{train_number} SET booked=0, passenger_name='', passenger_age='', passenger_gender='' WHERE seat_number=?",
                (seat_number,))
      conn.commit()
  ```

---

## User Interface
The Streamlit sidebar provides navigation for different functionalities:
```python
functions = st.sidebar.selectbox("Select Train Functions", [
    "Add Train", "View Trains", "Search Train", "Delete Train", "Book Ticket", "Cancel Ticket", "View Seats"])
```

Each function provides a user-friendly interface with forms, tables, and dynamic feedback (success/error messages).

---

## Installation and Setup
1. Install the required Python packages:
   ```bash
   pip install streamlit pandas
   ```
2. Run the application:
   ```bash
   streamlit run app.py
   ```
3. Ensure the `railway_system.db` file is in the same directory as the script.

---

## Future Enhancements
- Add user authentication for different roles (Admin, Employee, Passenger).
- Integrate additional features like train schedules, real-time seat availability updates, and ticket printing.
- Enhance UI with more detailed views and filters.

---

This application provides a foundational system for managing train bookings and can be extended for real-world use cases.
