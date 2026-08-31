# 🎬 Movie Ticket Booking Management System

### Built on Pega Platform | NIP-MovieTicket-AdduriAswani

> A full-stack case management application built on the **Pega Platform** to automate the end-to-end movie ticket booking process — from booking request and seat availability to ticket generation and confirmation notification.

---

## 📌 Project Overview

The **Movie Ticket Booking Management System** is a **Pega-based case management application** developed as part of a **Next-In-Practice (NIP)** project.

The application automates the complete movie ticket booking lifecycle using Pega's low-code capabilities, including:

* 🔄 Case lifecycle workflows
* 🗃️ Data objects and data references
* ⚙️ Business rules and data transforms
* 💰 Automated booking cost calculation
* 🎟️ Seat allocation and ticket generation
* ⏱️ SLA management and escalation
* 📧 Automated confirmation notifications
* 👥 Work queues and conditional routing

---

## 🚀 Case Lifecycle

The application follows a **5-stage booking workflow**:

```text
Booking Request
       ↓
Availability Check
       ↓
Approval
       ↓
Booking Execution
       ↓
Notification
```

### 1️⃣ Booking Request

The customer initiates a movie ticket booking request by providing:

* Movie Name
* Show Date
* Show Time
* Number of Tickets

### 2️⃣ Availability Check

The system automatically verifies whether sufficient seats are available.

It captures:

* Seat Availability Status
* Available Seats Count

Bookings proceed only when sufficient seats are available.

### 3️⃣ Approval

The booking request is routed to the **Users** queue for confirmation.

The booking can be:

* ✅ Confirmed
* ⏳ Pending
* ❌ Cancelled

The workflow branches based on the customer's selection.

### 4️⃣ Booking Execution

Once the booking is confirmed, the system:

* Assigns seat numbers
* Generates a unique Ticket ID
* Updates booking information
* Sets booking confirmation status

Example Ticket ID:

```text
TKT-2024-001847
```

### 5️⃣ Notification

After successful booking completion, a confirmation email is automatically triggered.

The email contains:

* 🎬 Movie Name
* 📅 Show Date & Time
* 💺 Seat Numbers
* 🎟️ Ticket ID
* 💰 Total Cost

---

# 🏗️ Key Features

## ✅ US-001 — Submit Booking Request

* Creates a Movie Ticket Request case
* Captures movie, show date, show time, and ticket quantity
* Integrates with **Movie** and **Show** data objects

---

## ✅ US-002 — Check Show Availability

* Automated availability verification
* Captures available seat count
* Determines whether booking can proceed
* Prevents booking when seats are unavailable

---

## ✅ US-003 — Calculate Booking Cost

The booking cost is automatically calculated using a **Declare Expression**:

```text
.BookingCost = .TicketPrice * .NumberOfTickets
```

The ticket price is retrieved from the Show/Movie data using the:

```text
LookupShowDetails
```

Data Transform.

---

## ✅ US-004 — Confirm Booking Request

The approval stage uses a **Collect Confirmation** assignment routed to the:

```text
Users
```

work queue.

Booking status can be:

```text
Confirmed
Pending
Cancelled
```

The workflow branches according to the confirmation decision.

---

## ✅ US-005 — Maintain Movie and Show Data

The application uses reusable **Movie** and **Show** data objects.

### 🎬 Movie Data Object

Stores:

* Movie Title
* Genre
* Language
* Duration
* Certification
* Ticket Price
* Release Date
* Synopsis
* Show Availability
* Active Status

### 🎭 Show Data Object

Stores:

* Show Label
* Show Date
* Show Time
* Show Type
* Total Seats
* Available Seats
* Ticket Price
* Associated Movie
* Booking SLA Deadline

The **Movie ↔ Show** relationship is implemented using a **Data Reference**.

---

## ✅ US-006 — Review Booking Details

Before final submission, the confirmation screen displays:

| Booking Information |
| ------------------- |
| Movie Name          |
| Show Date & Time    |
| Number of Tickets   |
| Total Cost          |

This allows the customer to review the booking before proceeding.

---

## ✅ US-007 — Process Ticket Booking

The Booking Execution stage captures:

* 💺 Seat Numbers
* 🎟️ Ticket ID
* ✅ Booking Confirmation Status

A unique Ticket ID is generated for every successful booking.

---

## ✅ US-008 — Notify Booking Confirmation

The application uses the following correspondence rule:

```text
SendConfirmation_0
```

A confirmation email is triggered when the case reaches resolution.

The notification includes:

```text
Movie Name
Show Date & Time
Seat Numbers
Ticket ID
Total Cost
```

---

## ✅ US-009 — Define Booking SLA

An SLA is configured for the **Movie Ticket Request** case type.

| SLA Parameter |                Configuration |
| ------------- | ---------------------------: |
| 🎯 Goal       |                        1 Day |
| ⏰ Deadline    |                       2 Days |
| 🚨 Escalation | Automatic urgency escalation |

Urgency automatically increases when the SLA is breached.

---

✅ US-010 — Route Booking to Queue
The Booking Execution stage uses conditional routing based on *Show Type*.
| Show Type   | Work Queue          |
| ----------- | ------------------- |
| 🎬 Standard | `StandardShowQueue` |
| ⭐ Premium   | `PremiumShowQueue`  |

🗃️ Data Objects
🎬 Movie
| Property      | Type                  |
| ------------- | --------------------- |
| Movie Title   | Text                  |
| Genre         | Picklist              |
| Language      | Picklist              |
| Duration      | Integer               |
| Certification | Picklist              |
| Ticket Price  | Currency              |
| Release Date  | Date                  |
| Synopsis      | Text                  |
| Show List     | Data Reference → Show |
| Is Active     | Boolean               |

🎭 Show
| Property             | Type                   |
| -------------------- | ---------------------- |
| Show Label           | Text                   |
| Show Date            | Date                   |
| Show Time            | Time                   |
| Show Type            | Picklist               |
| Total Seats          | Integer                |
| Available Seats      | Integer                |
| Ticket Price         | Currency               |
| Movie                | Data Reference → Movie |
| Booking SLA Deadline | Date & Time            |
Show Type Values
Standard
Premium
IMAX
🔧 Pega Rules Configured

| Rule                   | Rule Type          | Purpose                               |
| ---------------------- | ------------------ | ------------------------------------- |
| `.BookingCost`         | Declare Expression | Automatically calculates booking cost |
| `LookupShowDetails`    | Data Transform     | Retrieves Ticket Price and Show Type  |
| `CalculateBookingCost` | Data Transform     | Sets Booking Cost                     |
| `VerifyAvailability`   | Data Transform     | Verifies seat availability            |
| `AssignSeats`          | Data Transform     | Allocates seats                       |
| `GenerateTicketID`     | Data Transform     | Generates unique Ticket ID            |
| `UpdateBookingSystem`  | Data Transform     | Updates booking information           |
| `SendConfirmation_0`   | Correspondence     | Sends confirmation email              |
| `BookingRequest_Flow`  | Flow               | Controls booking workflow             |
👥 Personas & Work Queues

| Work Queue          | Stage             | Routing Condition         |
| ------------------- | ----------------- | ------------------------- |
| `Users`             | Approval          | All booking confirmations |
| `StandardShowQueue` | Booking Execution | Show Type = Standard      |
| `PremiumShowQueue`  | Booking Execution | Show Type = Premium       |

🛠️ Technology Stack

| Technology            | Details                                |
| --------------------- | -------------------------------------- |
| **Platform**          | Pega Platform                          |
| *Development Tools*   | App Studio + Dev Studio                |
| *Application*         | `NIP-MovieTicket-AdduriAswani`         |
| *Version*             | `01.01.01`                             |
| *Branch*              | `BP-1`                                 |
| *Case Class*          | `MoV-MovieTic-Work-MovieTicketRequest` |

Pega Capabilities Used
Case Types
Data Objects
Data References
Data Transforms
Declare Expressions
Decision Shapes
Work Queues
SLA
Correspondence
Email Notifications
Case Lifecycle
📂 Project Structure
NIP-MovieTicket-AdduriAswani
│
├── Case Types
│   └── Movie Ticket Request
│       ├── Booking Request
│       ├── Availability Check
│       ├── Approval
│       ├── Booking Execution
│       └── Notification
│
├── Data Types
│   ├── Movie
│   └── Show
│
├── Rules
│   ├── Declare Expressions
│   ├── Data Transforms
│   ├── Correspondence
│   └── Flows
│
└── SLA
    ├── Goal: 1 Day
    └── Deadline: 2 Days
🧩 Challenges & Solutions
Challenge: Booking Cost Showing `$0.00`
During development, the Booking Cost initially displayed:
$0.00
🔍 Root Cause
The issue was caused by a chain of three configuration problems.
1. Declare Expression
The Declare Expression was initially hardcoded:
.BookingCost = 0
✅ Fix
It was changed to:
.BookingCost = .TicketPrice * .NumberOfTickets
2. Incorrect Data Transform Target
The `CalculateBookingCost` Data Transform was updating:
.TotalCost
instead of:
.BookingCost
✅ Fix
The target field was corrected to:
.BookingCost
3. Missing Lookup Mapping

The `LookupShowDetails` Data Transform did not contain the required mappings.
 ✅ Fix
Mappings were added for:
TicketPrice
ShowType
This ensured that the correct Show information was available before calculating the booking cost.
🔄 End-to-End Booking Flow
Customer
   │
   ▼
┌───────────────────────┐
│   Booking Request     │
│ Movie + Date + Time   │
│ Number of Tickets     │
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│ Availability Check    │
│ Verify Available Seats│
└───────────┬───────────┘
            │
       Seats Available?
        /          \
      No            Yes
      │              │
      ▼              ▼
   Stop Case      Approval
                     │
                     ▼
              Customer Confirms?
                /          \
              No            Yes
              │              │
              ▼              ▼
           Cancel       Booking Execution
                            │
                            ▼
                     Assign Seats
                            │
                            ▼
                     Generate Ticket ID
                            │
                            ▼
                    Update Booking Data
                            │
                            ▼
                        Notification
                            │
                            ▼
                    Confirmation Email
🎯 Project Outcomes
The project demonstrates practical implementation of:
* Case management using Pega
* Low-code application development
* Workflow automation
* Business rule configuration
* Data modeling and relationships
* Conditional routing
* Automated calculations
* SLA management
* Work queue management
* Automated email notifications
* End-to-end case lifecycle management
👨‍💻 Author
*Adduri Aswani*
🎓 Built as part of the *Pega Next-In-Practice (NIP) Project*
*August 2026*
⭐ Project Highlights
🎬 Movie & Show Management
🎟️ Automated Ticket Booking
💺 Seat Availability & Allocation
💰 Dynamic Booking Cost Calculation
🔄 End-to-End Case Workflow
👥 Queue-Based Routing
⏱️ SLA & Escalation Management
📧 Automated Email Confirmation
🗃️ Reusable Pega Data Objects
⚙️ Business Rules & Data Transforms

*Built with Pega Platform to demonstrate real-world workflow automation and case management.*
