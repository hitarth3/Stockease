# Stockease

Stockease is a desktop-based inventory and stock management application built with Python and PyQt6. It is designed to manage products, inventory levels, sales orders, purchase orders, customer returns and refunds, and business analytics with visual reporting in a centralized interface.

---

## Features

* **Product Management**
  * Add, view, and remove products across categorized inventory.
  * Dynamically fetch product categories directly from the database.
  * Automatically initialize inventory records with zero quantity upon adding new products.
  * Input validation and duplicate product detection to protect database integrity.

* **Inventory & Stock Management**
  * Real-time stock visibility displaying Product ID, Category, Product Name, In-Stock Quantity, and Last Order Date.
  * Increment and update stock quantities with automated conflict resolution (`ON CONFLICT DO UPDATE`).
  * Multi-row selection support for batch removal of inventory items.

* **Sales Order Management & Invoicing**
  * Interactive sales order creation with live dropdowns and price calculation.
  * Real-time inventory verification to prevent orders that exceed available stock.
  * Automatic inventory deduction upon completing a sales order.
  * Relational record-keeping storing orders in `sales_orders` and generating linked `invoices`.
  * Automated PDF invoice generation using ReportLab.

* **Purchase Order Management**
  * Vendor purchase order generation for stock replenishment.
  * Lifecycle state tracking (distinguishing between pending and executed orders).
  * Order execution workflow that automatically adds received quantities to current inventory.
  * Purchase order receipt and report generation exported directly to PDF.

* **Returns & Refunds**
  * Return processing linked directly to historical sales orders.
  * Automatic lookup of product details and sale unit price.
  * Recording of return quantities and customer return reasons into the `returns` table.

* **Dashboard & Visual Reporting**
  * Integrated interactive sales performance dashboard embedded via Matplotlib (`FigureCanvasQTAgg`).
  * Visual bar charts displaying top-selling products, quantities sold, revenue generated (₹), and transaction counts.
  * Dynamic status bar summarizing total units sold, cumulative revenue, and last update timestamp.
  * Automatic dashboard and inventory data refreshes when navigating between pages.

* **Database Migration & Seed Utilities**
  * Migration script (`migrate_db.py`) to initialize or reset relational schemas cleanly.
  * Automated seed scripts (`seed_categories.py` and `seed_products.py`) to populate realistic initial product categories and inventory for immediate testing.

* **User Interface & Authentication**
  * Dedicated login and registration window (`front_page.py`) with animated background styling and SHA-256 password hashing.
  * Collapsible sidebar desktop interface (`main.py` / `dashboard.py`) with icon and text navigation.
  * Uniform dark-themed UI styling defined in Qt Style Sheets (`style.qss`).
  * Compiled Qt resource management (`resources_rc.py`) supporting vector icons and branding assets.

---

## Technology Stack

* **Programming Language**: Python 3.9+
* **GUI Framework**: PyQt6 (v6.7.1)
* **Database**: PostgreSQL
* **Database Driver**: psycopg (v3) / psycopg2-binary (v2.9.9)
* **Data Visualization**: Matplotlib (with Qt backend `FigureCanvasQTAgg`)
* **Document Generation**: ReportLab (v4.2.5) for PDF invoice and purchase order generation
* **Additional Utilities**: `openpyxl` (v3.1.5), `requests` (v2.32.3)
* **UI Design & Styling**: Qt Designer (`sidebar.ui`), Qt Style Sheets (`style.qss`), Qt Resource System (`resources_rc.py`)

---

## Project Structure

```text
Stockease/
├── main.py                 # Main desktop application shell and navigation controller
├── front_page.py           # User authentication interface (Login & Sign Up)
├── dashboard.py            # Compiled Qt Designer interface for main window & sidebar
├── database.py             # PostgreSQL connection configuration and auth utilities
├── db_connect.py           # Alternative database connection and user query helpers
├── inventory.py            # Inventory tracking window and stock management operations
├── product.py              # Product catalog window and category associations
├── Purchase_Order.py       # Purchase order workflow, stock replenishment, and PDF generation
├── salesorder.py           # Sales order processing, stock deduction, and invoice generation
├── return_refund.py        # Customer returns and refund handling module
├── graph.py                # Embedded Matplotlib sales performance dashboard
├── migrate_db.py           # Database migration script for application schema setup
├── seed_categories.py      # Seed utility for initial product categories
├── seed_products.py        # Seed utility for initial product catalog and inventory
├── stockease_schema.sql    # Complete PostgreSQL relational schema with triggers
├── requirement.txt         # Core Python dependencies
├── style.qss               # Application-wide Qt stylesheet definitions
├── sidebar.ui              # Qt Designer XML definition for main window layout
├── resources_rc.py         # Compiled Qt resources (icons and graphics)
├── backg.png               # Authentication background graphic
├── muscle_base.png         # Application branding logo
└── icon/                   # Directory containing UI icons (.ico and .png)
```

---

## Installation

### 1. Clone the Repository

```bash
git clone <repository-url>
cd Stockease
```

### 2. Create and Activate a Virtual Environment

On macOS/Linux:

```bash
python3 -m venv venv
source venv/bin/activate
```

On Windows:

```bash
python -m venv venv
venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirement.txt
```

*(Note: Matplotlib is required for the visual reporting module. If not already present in your environment, install it via `pip install matplotlib`.)*

---

## Database Setup

Stockease requires a running PostgreSQL instance.

### 1. Create the Database

Log in to PostgreSQL and create the database:

```sql
CREATE DATABASE stockease_db;
```

### 2. Configure Database Credentials

Open `database.py` and update `DB_CONFIG` with your local PostgreSQL connection parameters:

```python
DB_CONFIG = {
    "dbname": "stockease_db",
    "user": "<your_db_user>",
    "password": "<your_db_password>",
    "host": "localhost",
    "port": "5432"
}
```

> **Note**: Never commit sensitive database passwords or credentials to version control.

### 3. Run Schema Migrations

Initialize the required database tables using the migration utility:

```bash
python migrate_db.py
```

*Alternatively, the database schema and automated stock update triggers can be loaded directly via `psql`:*

```bash
psql -U <your_db_user> -d stockease_db -f stockease_schema.sql
```

### 4. Seed Initial Data (Optional)

Populate sample product categories and initial inventory:

```bash
python seed_categories.py
python seed_products.py
```

---

## Running the Application

### Direct Dashboard Entry Point

To launch the main desktop application directly with all navigation modules:

```bash
python main.py
```

### Authentication Entry Point

To launch the application via the user login and registration screen:

```bash
python front_page.py
```

---

## Main Modules

* **`main.py`**: Serves as the primary desktop application entry point. Initializes `MainWindow`, loads `style.qss`, registers all sub-windows inside a `QStackedWidget`, and manages sidebar navigation and view refreshes.
* **`front_page.py`**: Provides the animated login and user registration interface. Handles password hashing and authenticates users before routing to the main window.
* **`dashboard.py`**: Auto-generated Python UI class (`Ui_MainWindow`) compiled from `sidebar.ui` via PyQt's UIC tool. Defines layout geometry, sidebar buttons, and widget hierarchy.
* **`product.py`**: Manages the product catalog (`ProductWindow`). Supports adding products linked to categories, removing products, and automatically initializing stock records.
* **`inventory.py`**: Implements the inventory monitoring and modification interface (`InventoryWindow`). Displays quantities on hand, handles manual stock updates, and supports batch record deletion.
* **`salesorder.py`**: Coordinates customer sales order entry (`SalesOrderWindow`). Validates stock availability, deducts inventory upon sale, logs orders and invoices, and compiles downloadable PDF invoices using ReportLab.
* **`Purchase_Order.py`**: Handles incoming purchase orders (`PurchaseWindow`). Allows managers to log pending vendor orders, mark orders as executed to increment inventory stock, and export PDF receipts.
* **`return_refund.py`**: Provides the return workflow interface (`ReturnRefundWindow`). Links returns to specific sales orders, captures return reasons, and logs returns in the database.
* **`graph.py`**: Implements the visual analytics module (`SalesGraphWindow`). Embeds a Matplotlib bar chart inside PyQt6 to display units sold, revenue, and order metrics by product.
* **`database.py` / `db_connect.py`**: Manages PostgreSQL database connection sessions via `psycopg` and provides user verification and password hashing helpers.
* **`migrate_db.py`**: Drops outdated structures and creates the relational table schema needed for application operations.
* **`seed_categories.py` / `seed_products.py`**: Standalone utilities to populate categories and sample product items with starting stock levels.

---

## Development Notes

This repository contains the complete desktop application source code, UI layouts, database scripts, and assets developed as a software engineering project. All features documented in this README reflect the implementation currently present in the codebase.
