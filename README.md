# Online Movie Ticket Booking System

A database project built with **MySQL** to manage an online movie ticket booking system. It stores customer, theatre, movie, show, booking, and ticket-detail information, and demonstrates SQL concepts such as relationships, indexes, views, stored procedures, triggers, and reporting queries.

## Table of Contents

- [Project Overview](#project-overview)
- [Features](#features)
- [Database Schema](#database-schema)
- [Database Objects](#database-objects)
- [Requirements](#requirements)
- [Setup and Execution](#setup-and-execution)
- [Example Queries and Procedures](#example-queries-and-procedures)
- [Important Notes](#important-notes)
- [Future Improvements](#future-improvements)

## Project Overview

The **Online Movie Ticket Booking System** organizes movie-show information and customer bookings in a relational database. The SQL script creates the database and tables, inserts sample data, adds indexes and views, defines stored procedures and triggers, and runs example queries.

This is a database-focused project. It does not include a web or mobile user interface.

## Features

- Store customer contact details.
- Manage theatres and their locations.
- Maintain movie details, including genre, language, duration, and release date.
- Schedule shows with dates, times, ticket prices, and available-seat counts.
- Store bookings and associated booking details.
- Restrict payment modes to `UPI`, `Cash`, or `Card`.
- Use indexes to support common lookups.
- Create views for booking summaries, low-seat shows, and customer spending.
- Use stored procedures to retrieve customer bookings, create a booking record, and report revenue by payment mode.
- Use triggers to prevent booking more seats than are available and to reduce the available-seat count after booking details are inserted.
- Run sample queries for filtering, grouping, sorting, and basic revenue/ticket analysis.

## Database Schema

The database is named `online_movie_ticket_booking`.

| Table | Purpose | Main columns |
|---|---|---|
| `customers` | Customer contact information | `customer_id`, `customer_name`, `phone`, `email` |
| `theatres` | Theatre information and location | `theatre_id`, `theatre_name`, `city`, `total_screens` |
| `movies` | Movie metadata | `movie_id`, `movie_name`, `genre`, `language`, `duration_minutes`, `release_date` |
| `shows` | Movie show schedule and seat availability | `show_id`, `movie_id`, `theatre_id`, `show_date`, `show_time`, `ticket_price`, `available_seats` |
| `bookings` | Booking header and payment information | `booking_id`, `customer_id`, `booking_date`, `total_amount`, `payment_mode` |
| `booking_details` | Shows and seats included in a booking | `ticket_id`, `booking_id`, `show_id`, `seats_booked`, `subtotal` |
| `seat_log` | Table prepared for recording seat changes | `log_id`, `show_id`, `seat_change`, `change_type`, `change_date` |

### Relationships

- One customer can have multiple bookings.
- A movie can have multiple shows.
- A theatre can host multiple shows.
- A booking can have multiple booking-detail records.
- A show can be referenced by multiple booking-detail records.

## Database Objects

### Views

| View | Description |
|---|---|
| `view_booking_summary` | Shows booking ID, customer name, booking date, total amount, and payment mode. |
| `view_low_seats` | Lists shows with fewer than 100 available seats. |
| `view_customer_spend` | Shows each customer’s booking count and total amount spent. |

### Stored procedures

| Procedure | Purpose | Example |
|---|---|---|
| `sp_customer_bookings` | Retrieves bookings for a specified customer ID. | `CALL sp_customer_bookings(1);` |
| `sp_book_ticket` | Calculates the total from the selected show’s ticket price and seat count, then inserts a booking record. | `CALL sp_book_ticket(1, 1, 2, 'UPI');` |
| `sp_revenue_report` | Groups booking totals by payment mode. | `CALL sp_revenue_report();` |

### Triggers

| Trigger | Timing | Purpose |
|---|---|---|
| `trg_prevent_overbooking` | `BEFORE INSERT` on `booking_details` | Raises an error if the requested seats exceed the show’s available seats. |
| `trg_reduce_seats` | `AFTER INSERT` on `booking_details` | Subtracts the booked seats from the show’s available-seat count. |

### Indexes

The script creates indexes on:

- `shows(movie_id)`
- `shows(theatre_id)`
- `bookings(customer_id)`
- `booking_details(show_id)`
- `movies(genre)`
- `theatres(city)`

## Requirements

- MySQL Server (a version supporting the SQL features used in the script).
- MySQL Workbench or another MySQL client.
- Permission to create a database, tables, views, stored procedures, triggers, and indexes.

## Setup and Execution

1. Start MySQL Server and open MySQL Workbench or your preferred MySQL client.
2. Open the provided .txt file.
3. Execute the script from the beginning.


## Important Notes

- The sample show dates and booking dates are historical example data.
- `sp_book_ticket` inserts a row into `bookings`; in the provided script, it does not insert the corresponding row into `booking_details` or validate/decrement seats itself. The booking-detail trigger handles seat reduction when a detail row is inserted.
- The `seat_log` table is created, but the supplied triggers do not insert records into it.
- The final sample `INSERT INTO booking_details` uses an existing booking ID and show ID. Running the script repeatedly without recreating the database can lead to duplicate test entries or changed seat counts.
- Review the sample data and business rules before using this schema for a real booking application. A production implementation would need transaction handling and additional validation for payment, seat selection, and concurrent bookings.

## Future Improvements

- Add unique constraints for customer email and phone where appropriate.
- Add validation for positive seat counts and non-negative available seats.
- Update the booking procedure to create both booking and booking-detail records and to handle unavailable shows/seats.
- Use transactions and concurrency-safe seat allocation to avoid simultaneous overbooking.
- Record seat changes in `seat_log`.
- Add cancellation and refund workflows.
- Add user authentication and an application interface.

