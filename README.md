# Modern Shop --- E-Commerce Web Application

A learning-oriented e-commerce web application built with **Python,
Flask, SQLite, HTML, and CSS**. It demonstrates the core flow of an
online shop: browsing products, creating an account, signing in, adding
products to a cart, placing an order, and viewing store information
through an admin area.

> **Project status:** Educational prototype. It is not presented as a
> production-ready commerce platform. Payment processing, deployment
> hardening, and several operational safeguards are outside the current
> implementation.

## Table of Contents

-   [Project Overview](#project-overview)
-   [Objectives](#objectives)
-   [Key Features](#key-features)
-   [Technology Stack](#technology-stack)
-   [System Architecture](#system-architecture)
-   [Application Flow](#application-flow)
-   [Database Design](#database-design)
-   [Project Structure](#project-structure)
-   [Local Setup](#local-setup)
-   [Routes at a Glance](#routes-at-a-glance)
-   [Security and Privacy Notes](#security-and-privacy-notes)
-   [Current Scope and Future
    Improvements](#current-scope-and-future-improvements)

## Project Overview

Modern Shop is a small web application that illustrates how a
browser-based storefront can communicate with a Python backend and a
relational database. Flask handles incoming HTTP requests and page
rendering. SQLite stores user, product, and order records. HTML
templates provide the user interface, while CSS styles the pages.

The project is intended to demonstrate the relationship between frontend
pages, backend routes, session-based user state, and persistent data
storage.

## Objectives

1.  Build a simple product catalogue that can display and search
    products.
2.  demonstrate a basic account registration and login workflow.
3.  Maintain a session-based shopping cart.
4.  Record orders and update product stock when an order is submitted.
5.  Separate customer-facing pages from admin-only product and order
    views.
6.  Practice connecting a Flask application to a relational database.

## Key Features

### Customer-facing features

-   Browse the product catalogue.
-   Search products by name or description.
-   Register an account and sign in.
-   Add products to a session-based cart.
-   Submit an order with a delivery address.
-   Sign out and clear the current session.

### Admin features

-   View the product catalogue in the admin area.
-   Add products with a name, description, price, and stock quantity.
-   View order records and associated customer/product details.

Admin pages check the role stored in the current session before
displaying protected views. This is a basic demonstration of role-based
access; it should be strengthened before any real-world deployment.

## Technology Stack

  -----------------------------------------------------------------------
  Layer                   Technology              Responsibility
  ----------------------- ----------------------- -----------------------
  User interface          HTML, CSS               Forms, catalogue, cart,
                                                  and admin pages

  Backend                 Python, Flask           URL routing, request
                                                  handling, sessions, and
                                                  page rendering

  Database                SQLite                  Stores users, products,
                                                  and orders

  Password handling       Werkzeug security       Hashes passwords and
                          utilities               verifies submitted
                                                  passwords

  Data access             Python `sqlite3`        Executes parameterized
                                                  SQL queries
  -----------------------------------------------------------------------

## System Architecture

``` mermaid
flowchart TD
    A[Customer or Admin] --> B[Web Browser]
    B --> C[HTML and CSS Pages]
    C --> D[Flask Application]
    D --> E[Route and Session Logic]
    E --> F[SQLite Database]
    F --> E
    E --> D
    D --> B
```

### How the components work together

1.  A user opens a page in the browser.
2.  The browser sends an HTTP request to the Flask application.
3.  Flask selects the route associated with the requested URL.
4.  The route validates the relevant input and checks session/role
    information where applicable.
5.  The application reads from or writes to SQLite when data is needed.
6.  Flask renders an HTML template or redirects the browser to another
    route.

## Application Flow

### Customer journey

``` mermaid
flowchart TD
    A([Start]) --> B[Open Product Catalogue]
    B --> C{Search for a Product?}
    C -- Yes --> D[Filter by Name or Description]
    C -- No --> E[Browse Products]
    D --> F[Add Product to Cart]
    E --> F
    F --> G[Open Cart]
    G --> H{Signed In?}
    H -- No --> I[Register or Sign In]
    I --> J[Return to Checkout]
    H -- Yes --> J
    J --> K[Submit Delivery Address]
    K --> L[Create Order Records]
    L --> M[Update Product Stock]
    M --> N[Clear Cart]
    N --> O([Finish])
```

### Admin journey

``` mermaid
flowchart TD
    A([Admin Opens Admin Page]) --> B{Admin Role in Session?}
    B -- No --> C[Redirect to Login]
    B -- Yes --> D[Open Admin Area]
    D --> E[View Products or Orders]
    E --> F{Add a Product?}
    F -- Yes --> G[Submit Product Details]
    G --> H[Save Product to SQLite]
    H --> I[Refresh Product List]
    F -- No --> J([Finish])
    I --> J
```

## Database Design

The application uses three main tables.

  -----------------------------------------------------------------------
  Table                   Purpose                 Main fields
  ----------------------- ----------------------- -----------------------
  `users`                 Account and role        `id`, `name`, `email`,
                          information             `password`, `role`

  `products`              Catalogue and inventory `id`, `name`,
                          data                    `description`, `price`,
                                                  `stock`

  `orders`                Order records           `id`, `user_id`,
                                                  `product_id`,
                                                  `address`, `status`
  -----------------------------------------------------------------------

### Relationship overview

``` mermaid
erDiagram
    USERS ||--o{ ORDERS : places
    PRODUCTS ||--o{ ORDERS : appears_in

    USERS {
        int id PK
        string name
        string email
        string password
        string role
    }

    PRODUCTS {
        int id PK
        string name
        string description
        float price
        int stock
    }

    ORDERS {
        int id PK
        int user_id FK
        int product_id FK
        string address
        string status
    }
```

The `orders` table associates an order record with a user and a product.
In the current implementation, a cart containing multiple items is
recorded as multiple order rows rather than one order header with
separate order-item rows.

## Project Structure

Organize the files in the following layout so Flask can locate its
templates:

``` text
modern-shop/
├── app.py
├── init_db.py
├── database.db              # Local database; do not publish real user/order data
├── requirements.txt
├── templates/
│   ├── index.html
│   ├── signup.html
│   ├── login.html
│   ├── cart.html
│   ├── add_product.html
│   ├── admin_products.html
│   └── admin_orders.html
└── static/
    └── style.css            # If the templates link to this stylesheet
```

**Important:** Flask expects templates inside a folder named
`templates/`. If your home page file is currently named `index(1).html`,
rename it to `index.html` and place it in `templates/`. Keep only the
template files actually used by the application. If the pages contain
their own inline styles, `style.css` may not currently be used.

## Local Setup

### 1. Install Python

Use a supported Python 3 version. Verify the installation:

``` bash
python --version
```

### 2. Create and activate a virtual environment

``` bash
python -m venv .venv
```

Windows PowerShell:

``` powershell
.venv\Scripts\Activate.ps1
```

macOS/Linux:

``` bash
source .venv/bin/activate
```

### 3. Install dependencies

Create a `requirements.txt` file containing the dependencies used by
this project:

``` text
Flask
Werkzeug
```

Then install them:

``` bash
pip install -r requirements.txt
```

SQLite is included with Python, so a separate SQLite package is not
normally required.

### 4. Prepare the application configuration and database

Before running the project, replace development-only settings in the
Python files with local configuration. Use a strong, private Flask
secret key and create an admin account with a password known only to
you. Do not commit credentials or a database containing personal
information.

The application creates its database tables when started. The separate
database initialization script also inserts sample data; review and
secure its account-creation settings before running it.

### 5. Start the application

``` bash
python app.py
```

Open the local address printed by Flask in your terminal (commonly
`http://127.0.0.1:5000/`).

> Run this only as a local development application. Do not expose
> Flask's debug server to the public internet.

## Routes at a Glance

  Route                   Purpose
  ----------------------- ---------------------------------------------
  `/`                     Display and search the product catalogue
  `/signup`               Register a customer account
  `/login`                Authenticate a user
  `/logout`               Clear the current session
  `/cart/add/<id>`        Add a product to the session cart
  `/cart`                 Display cart contents
  `/order`                Submit an order; requires a signed-in user
  `/admin/products`       View products in the admin area
  `/admin/products/add`   Add a product; requires the admin role
  `/admin/orders`         View order records; requires the admin role

## Security and Privacy Notes

This repository is intended to be a **public-safe project overview**,
not a place to publish private operational data.

Before making the repository public:

-   Replace hard-coded application secrets with environment-based
    configuration.
-   Remove or replace any development/default admin credentials; never
    publish working credentials.
-   Do not commit `database.db` if it contains real accounts, password
    hashes, delivery addresses, or order history. Prefer a clean,
    reproducible sample database or a script that creates sample
    records.
-   Turn off Flask debug mode outside local development.
-   Add appropriate authorization checks to every sensitive action, not
    only admin page views.
-   Add validation for product quantities, stock availability, and
    checkout input.
-   Consider CSRF protection, secure session-cookie settings, rate
    limiting, and stronger order/stock transaction handling before
    deployment.
-   Review the complete repository and its Git history for secrets
    before publishing. Removing a secret from the latest file does not
    remove it from previous commits.

## Current Scope and Future Improvements

The current project demonstrates the basic storefront and order flow.
Potential next steps include:

-   Add product update/delete operations with validation.
-   Add cart quantity controls and remove-from-cart functionality.
-   Validate stock before checkout and prevent negative inventory.
-   Model an order header and order items separately.
-   Add order-status updates and customer order history.
-   Add automated tests for authentication, product search, cart
    behavior, and checkout.
-   Add configuration management, logging, error handling, and
    deployment documentation.
-   Add screenshots of the user interface using sanitized demo data.

## Academic and Portfolio Use

Use this README to explain the purpose, design, and flow of the project.
In your resume or portfolio, describe the features you personally
implemented and can confidently explain in an interview. Do not include
private credentials, real customer data, or confidential institutional
information.

------------------------------------------------------------------------

**Note:** This repository documents a learning project. It does not
currently include payment gateway integration or claim production-grade
security.
