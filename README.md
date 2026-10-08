# Condominium Parking Rental System

A Java Swing desktop application for renting parking slots in a condominium. Residents can book and request cancellation of slots, while an administrator can view reports and approve or decline cancellation requests. Data is stored in a MySQL database through JDBC.

## Features

**Customer**
- Log in with a resident ID and password
- Browse a color-coded visual slot layout per floor/section (replaces the old dropdown)
- Filter slots by **All / PWD Only / E-Car Only / Regular Only**
- Book a slot with a form (name, contact, unit, unit ID, duration, plate number, car type, E-Car checkbox)
- Automatic price computation per **Day / Week / Month** with PWD discount applied
- Invoice dialog after a successful booking
- Send a **cancellation request** for a rented slot

**Admin**
- Everything above, plus:
- **Reports** tab: rented slots per section with customer, vehicle, duration, and amount paid
- **Cancel Requests** tab: approve (cancels the rental) or decline pending requests
- **Cancellations** tab: history log of all cancelled rentals
- Cancel any rented slot directly

**Input validation**
- Contact number limited to 11 digits
- Plate number limited to 7 characters
- E-Car-only slots reject non-E-Car vehicles
- Rent duration must be greater than zero

## Pricing

Each floor has a monthly regular price and a lower PWD discount price. Shorter durations are derived from the monthly rate:

| Unit  | Rate                  |
|-------|-----------------------|
| Month | monthly price         |
| Week  | monthly price / 4     |
| Day   | monthly price / 30    |

Total = rate × duration. PWD slots use the discount price.

| Section       | Regular (Php) | PWD (Php) |
|---------------|---------------|-----------|
| Basement 2    | 2,200 | 1,800 |
| Basement 1    | 2,500 | 2,000 |
| Ground Floor  | 3,000 | 2,400 |
| 2nd Floor     | 2,800 | 2,200 |
| 3rd Floor     | 2,800 | 2,200 |
| 4th Floor     | 2,600 | 2,100 |
| 5th Floor     | 2,600 | 2,100 |

## Slot Layout

Each floor has 20 slots, set up by `Schema.sql`:

- Slots **1-5**: PWD
- Slots **10 and 15**: E-Car only
- All other slots: regular

In the GUI, PWD slots are blue, E-Car slots are purple, and taken slots are marked as taken.

## Project Structure

```
Programs_Apps_Systems/
├── Schema.sql              # Creates the database, tables, floors, and slots
├── app/
│   ├── Frontend.java       # Main Swing GUI (booking, reports, requests, invoice) + main()
│   ├── LoginFrame.java     # Login window
│   └── Theme.java          # Dark theme colors and look-and-feel setup
└── lib/
    ├── Database.java       # JDBC connection settings
    ├── ParkingDAO.java     # All SQL queries (load, save, cancel, requests, logs)
    ├── ParkingSystem.java  # Business logic, login, transactions, reports
    ├── Floor.java          # Floor/section with pricing and its slots
    ├── Slot.java           # A parking slot and its rental details
    └── Vehicle.java        # Vehicle details (plate, type, E-Car flag)
```

## Database

Tables created by `Schema.sql`: `FLOOR`, `CUSTOMER`, `VEHICLE`, `SLOT`.

Two more tables, `CANCELLATION_LOG` and `CANCEL_REQUEST`, are created automatically by `ParkingDAO` the first time they are needed.

## Requirements

- Java JDK 11 or newer
- MySQL Server (running on `localhost:3306`)
- MySQL Connector/J (JDBC driver) added to your classpath

## Setup and Running

1. **Create the database.** Run the schema in MySQL:
   ```bash
   mysql -u root -p < Schema.sql
   ```
   Warning: the script drops and recreates the tables, so running it again resets all data.

2. **Configure the connection.** Open `lib/Database.java` and set your own MySQL username and password in `DB_USER` and `DB_PASSWORD`. Change the URL if your database is not on `localhost:3306`.

3. **Compile.** From the `Programs_Apps_Systems` folder (replace the jar path with your Connector/J jar):
   ```bash
   javac -cp ".:mysql-connector-j.jar" -d out lib/*.java app/*.java
   ```
   On Windows, use `;` instead of `:` in the classpath.

4. **Run.**
   ```bash
   java -cp "out:mysql-connector-j.jar" app.Frontend
   ```

If MySQL is not running or the credentials are wrong, the app shows a "Database connection failed" message and exits.

## Default Login Accounts

| Role     | ID           | Password       |
|----------|--------------|----------------|
| Admin    | `admin123`   | `Admin@123`    |
| Customer | `0123456789` | `Resident@123` |
| Customer | `9876543210` | `Resident@456` |

These accounts are hardcoded in `ParkingSystem.java` for demo purposes.

## Known Limitations

- Accounts are hardcoded and passwords are stored in plain text (acceptable for a class project, not for production).
- Database credentials are stored directly in `Database.java`.

## Authors

*Group 4*
- NICOLE CHRISTIANE CAVERO
- JUSTIN DANIEL GRANADA
- TAYSHAUN LI GUILAS
- ANDREA DENICE JARDIO
- EDWARD KARSON LEE REBOTON
