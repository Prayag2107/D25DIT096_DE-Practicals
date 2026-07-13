# Enterprise E-commerce Database Schema

## Introduction
This document outlines a mock enterprise database schema designed to support full data engineering lifecycle requirements, including ETL pipelines, real-time analytics, and business intelligence. This mock schema serves as a foundational data structure to illustrate data flow from transactional generation to analytical serving layers.

---

## Database Overview
The e-commerce platform consists of multiple critical business domains to manage end-to-end operations effectively. These domains include:
* Customer Management
* Product Catalog
* Category Management
* Inventory
* Orders
* Payments
* Shipping
* Reviews
* Shopping Cart
* Clickstream Analytics

---

## Entity Relationship Summary

| Entity | Primary Key | Purpose |
| :--- | :--- | :--- |
| Customers | `customer_id` | Stores user profiles and demographic data. |
| Products | `product_id` | Catalog of all items available for sale. |
| Categories | `category_id` | Classification of products into groups. |
| Suppliers | `supplier_id` | Information about vendors supplying products. |
| Inventory | `inventory_id` | Real-time stock levels across warehouses. |
| Orders | `order_id` | Finalized checkout transactions by customers. |
| Order_Items | `order_item_id` | Individual line items for each order. |
| Payments | `payment_id` | Financial transaction records for orders. |
| Shipments | `shipment_id` | Delivery tracking and logistics status. |
| Shopping_Cart | `cart_id` | Active shopping sessions for users. |
| Cart_Items | `cart_item_id` | Products currently added to a user's cart. |
| Reviews | `review_id` | Customer feedback and ratings on products. |
| Clickstream_Events | `event_id` | Real-time tracking of user interactions on the platform. |

---

## Database Tables

### Customers
**Purpose:** Maintain comprehensive customer profile information for order processing and analytics.

| Column | Data Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `customer_id` | INT | PRIMARY KEY | Unique identifier for the customer. |
| `first_name` | VARCHAR(50) | NOT NULL | Customer's first name. |
| `last_name` | VARCHAR(50) | NOT NULL | Customer's last name. |
| `email` | VARCHAR(100) | UNIQUE, NOT NULL | Customer's email address. |
| `phone` | VARCHAR(20) | UNIQUE | Customer's contact number. |
| `gender` | VARCHAR(10) | NULL | Customer's gender. |
| `date_of_birth` | DATE | NULL | Customer's date of birth. |
| `city` | VARCHAR(50) | NULL | City of residence. |
| `state` | VARCHAR(50) | NULL | State of residence. |
| `country` | VARCHAR(50) | NULL | Country of residence. |
| `registration_date` | DATETIME | NOT NULL | Timestamp of account creation. |
| `account_status` | VARCHAR(20) | NOT NULL | Status (e.g., Active, Suspended). |

---

### Categories
**Purpose:** Organize products logically into taxonomic categories for better browsing.

| Column | Data Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `category_id` | INT | PRIMARY KEY | Unique identifier for the category. |
| `category_name` | VARCHAR(100) | NOT NULL | Display name of the category. |
| `description` | VARCHAR(255) | NULL | Detailed description of the category. |

---

### Suppliers
**Purpose:** Track vendor details for supply chain and procurement management.

| Column | Data Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `supplier_id` | INT | PRIMARY KEY | Unique identifier for the supplier. |
| `supplier_name` | VARCHAR(100) | NOT NULL | Official name of the supplying vendor. |
| `contact_person` | VARCHAR(100) | NULL | Primary contact person. |
| `phone` | VARCHAR(20) | NULL | Contact phone number. |
| `email` | VARCHAR(100) | NULL | Contact email address. |
| `warehouse_location` | VARCHAR(150) | NULL | Physical location of the supplier's warehouse. |

---

### Products
**Purpose:** Serve as the central catalog storing all product details and pricing.

| Column | Data Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `product_id` | INT | PRIMARY KEY | Unique identifier for the product. |
| `category_id` | INT | FOREIGN KEY | Reference to Categories table. |
| `supplier_id` | INT | FOREIGN KEY | Reference to Suppliers table. |
| `product_name` | VARCHAR(150) | NOT NULL | Name of the product. |
| `description` | VARCHAR(500) | NULL | Detailed product description. |
| `brand` | VARCHAR(50) | NULL | Brand of the product. |
| `price` | DECIMAL(10,2) | NOT NULL | Base selling price. |
| `discount` | DECIMAL(5,2) | DEFAULT 0.00 | Percentage discount on the product. |
| `stock_quantity` | INT | NOT NULL | Total available stock. |
| `created_at` | DATETIME | NOT NULL | When the product was added. |

---

### Inventory
**Purpose:** Manage real-time physical stock counts across various warehouses.

| Column | Data Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `inventory_id` | INT | PRIMARY KEY | Unique identifier for the inventory record. |
| `product_id` | INT | FOREIGN KEY | Reference to Products table. |
| `warehouse` | VARCHAR(50) | NOT NULL | Name or ID of the warehouse. |
| `available_stock` | INT | NOT NULL | Stock available for sale. |
| `reserved_stock` | INT | NOT NULL | Stock reserved for pending orders. |
| `updated_at` | TIMESTAMP | NOT NULL | Last update timestamp. |

---

### Orders
**Purpose:** Store the overarching details of a customer's finalized purchase.

| Column | Data Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `order_id` | INT | PRIMARY KEY | Unique identifier for the order. |
| `customer_id` | INT | FOREIGN KEY | Reference to Customers table. |
| `order_date` | DATETIME | NOT NULL | Timestamp when the order was placed. |
| `total_amount` | DECIMAL(12,2) | NOT NULL | Total cost of the order. |
| `payment_status` | VARCHAR(20) | NOT NULL | E.g., Pending, Completed, Failed. |
| `shipping_status` | VARCHAR(20) | NOT NULL | E.g., Processing, Shipped, Delivered. |
| `delivery_date` | DATE | NULL | Expected or actual delivery date. |

---

### Order_Items
**Purpose:** Break down each order into its constituent products.

| Column | Data Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `order_item_id` | INT | PRIMARY KEY | Unique identifier for the line item. |
| `order_id` | INT | FOREIGN KEY | Reference to Orders table. |
| `product_id` | INT | FOREIGN KEY | Reference to Products table. |
| `quantity` | INT | NOT NULL | Number of units purchased. |
| `unit_price` | DECIMAL(10,2) | NOT NULL | Price per unit at the time of purchase. |
| `subtotal` | DECIMAL(12,2) | NOT NULL | `quantity` * `unit_price`. |

---

### Payments
**Purpose:** Record financial transactions and link them to their respective orders.

| Column | Data Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `payment_id` | INT | PRIMARY KEY | Unique identifier for the payment. |
| `order_id` | INT | FOREIGN KEY | Reference to Orders table. |
| `payment_method` | VARCHAR(50) | NOT NULL | E.g., Credit Card, PayPal, Stripe. |
| `payment_date` | DATETIME | NOT NULL | Timestamp of the payment. |
| `transaction_id` | VARCHAR(100) | UNIQUE | External transaction reference ID. |
| `amount` | DECIMAL(12,2) | NOT NULL | Amount paid. |
| `payment_status` | VARCHAR(20) | NOT NULL | E.g., Success, Declined, Refunded. |

---

### Shipments
**Purpose:** Track the physical delivery logistics of customer orders.

| Column | Data Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `shipment_id` | INT | PRIMARY KEY | Unique identifier for the shipment. |
| `order_id` | INT | FOREIGN KEY | Reference to Orders table. |
| `courier_name` | VARCHAR(100) | NOT NULL | Logistics provider name. |
| `tracking_number` | VARCHAR(100) | UNIQUE | Tracking number from the courier. |
| `shipped_date` | DATETIME | NULL | Timestamp when the package was shipped. |
| `delivered_date` | DATETIME | NULL | Timestamp when the package was delivered. |
| `shipment_status` | VARCHAR(50) | NOT NULL | Current status of the shipment. |

---

### Shopping_Cart
**Purpose:** Maintain the active state of a user's pre-checkout session.

| Column | Data Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `cart_id` | INT | PRIMARY KEY | Unique identifier for the cart session. |
| `customer_id` | INT | FOREIGN KEY | Reference to Customers table. |
| `created_at` | DATETIME | NOT NULL | Timestamp when the cart was created. |

---

### Cart_Items
**Purpose:** Store the individual items currently selected in a user's shopping cart.

| Column | Data Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `cart_item_id` | INT | PRIMARY KEY | Unique identifier for the cart item. |
| `cart_id` | INT | FOREIGN KEY | Reference to Shopping_Cart table. |
| `product_id` | INT | FOREIGN KEY | Reference to Products table. |
| `quantity` | INT | NOT NULL | Number of units in the cart. |

---

### Reviews
**Purpose:** Capture user-generated feedback and ratings for products.

| Column | Data Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `review_id` | INT | PRIMARY KEY | Unique identifier for the review. |
| `customer_id` | INT | FOREIGN KEY | Reference to Customers table. |
| `product_id` | INT | FOREIGN KEY | Reference to Products table. |
| `rating` | INT | NOT NULL | Rating score (e.g., 1 to 5). |
| `review_text` | VARCHAR(1000) | NULL | Written review content. |
| `review_date` | DATETIME | NOT NULL | Timestamp of the review submission. |

---

### Clickstream_Events
**Purpose:** Log detailed user interaction telemetry for behavioral analytics.

| Column | Data Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `event_id` | VARCHAR(50) | PRIMARY KEY | Unique string identifier for the event. |
| `customer_id` | INT | FOREIGN KEY | Associated customer (if logged in). |
| `session_id` | VARCHAR(100) | NOT NULL | Web session identifier. |
| `event_name` | VARCHAR(50) | NOT NULL | E.g., 'page_view', 'add_to_cart'. |
| `page_url` | VARCHAR(255) | NOT NULL | URL of the page where the event occurred. |
| `device` | VARCHAR(50) | NULL | Device type (Mobile, Desktop). |
| `browser` | VARCHAR(50) | NULL | Browser used (Chrome, Safari). |
| `operating_system` | VARCHAR(50) | NULL | OS (Windows, iOS, Android). |
| `location` | VARCHAR(100) | NULL | Geolocation based on IP. |
| `timestamp` | TIMESTAMP | NOT NULL | Exact time of the event. |
| `metadata` | JSON | NULL | Extra event properties in JSON format. |

---

# Primary Keys

| Table | Primary Key |
| :--- | :--- |
| Customers | `customer_id` |
| Categories | `category_id` |
| Suppliers | `supplier_id` |
| Products | `product_id` |
| Inventory | `inventory_id` |
| Orders | `order_id` |
| Order_Items | `order_item_id` |
| Payments | `payment_id` |
| Shipments | `shipment_id` |
| Shopping_Cart | `cart_id` |
| Cart_Items | `cart_item_id` |
| Reviews | `review_id` |
| Clickstream_Events | `event_id` |

---

# Foreign Key Relationships

| Parent Table | Child Table | Relationship |
| :--- | :--- | :--- |
| Customers | Orders | 1:N |
| Products | Order_Items | 1:N |
| Orders | Payments | 1:1 |
| Orders | Shipments | 1:1 |
| Categories | Products | 1:N |
| Suppliers | Products | 1:N |
| Customers | Reviews | 1:N |
| Products | Reviews | 1:N |
| Customers | Shopping_Cart | 1:1 |
| Shopping_Cart | Cart_Items | 1:N |
| Products | Cart_Items | 1:N |
| Customers | Clickstream_Events | 1:N |

---

# Data Sources

| Domain | Source Tables |
| :--- | :--- |
| **Transaction Processing** | `Orders`, `Order_Items`, `Payments`, `Shopping_Cart`, `Cart_Items` |
| **Inventory System** | `Inventory`, `Suppliers`, `Products` |
| **Customer Analytics** | `Customers`, `Orders`, `Reviews` |
| **Recommendation Engine** | `Clickstream_Events`, `Order_Items`, `Reviews`, `Products` |
| **Business Intelligence Dashboard** | `Orders`, `Order_Items`, `Payments`, `Inventory` |
| **Marketing Analytics** | `Clickstream_Events`, `Customers`, `Categories` |

---

# Sample SQL CREATE TABLE Statements

```sql
-- Customers Table
CREATE TABLE Customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(20) UNIQUE,
    gender VARCHAR(10),
    date_of_birth DATE,
    city VARCHAR(50),
    state VARCHAR(50),
    country VARCHAR(50),
    registration_date DATETIME NOT NULL,
    account_status VARCHAR(20) NOT NULL
);

-- Products Table
CREATE TABLE Products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    category_id INT,
    supplier_id INT,
    product_name VARCHAR(150) NOT NULL,
    description VARCHAR(500),
    brand VARCHAR(50),
    price DECIMAL(10,2) NOT NULL,
    discount DECIMAL(5,2) DEFAULT 0.00,
    stock_quantity INT NOT NULL,
    created_at DATETIME NOT NULL,
    FOREIGN KEY (category_id) REFERENCES Categories(category_id),
    FOREIGN KEY (supplier_id) REFERENCES Suppliers(supplier_id)
);

-- Orders Table
CREATE TABLE Orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT,
    order_date DATETIME NOT NULL,
    total_amount DECIMAL(12,2) NOT NULL,
    payment_status VARCHAR(20) NOT NULL,
    shipping_status VARCHAR(20) NOT NULL,
    delivery_date DATE,
    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id)
);

-- Order_Items Table
CREATE TABLE Order_Items (
    order_item_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT,
    product_id INT,
    quantity INT NOT NULL,
    unit_price DECIMAL(10,2) NOT NULL,
    subtotal DECIMAL(12,2) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES Orders(order_id),
    FOREIGN KEY (product_id) REFERENCES Products(product_id)
);

-- Payments Table
CREATE TABLE Payments (
    payment_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT,
    payment_method VARCHAR(50) NOT NULL,
    payment_date DATETIME NOT NULL,
    transaction_id VARCHAR(100) UNIQUE,
    amount DECIMAL(12,2) NOT NULL,
    payment_status VARCHAR(20) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES Orders(order_id)
);
```

---

# Sample Records

### Customers

| customer_id | first_name | last_name | email | phone | city | country |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Alice | Smith | alice.s@example.com | 555-0101 | New York | USA |
| 2 | Bob | Jones | bjones@example.com | 555-0102 | Chicago | USA |
| 3 | Carol | White | c.white@example.com | 555-0103 | London | UK |

### Products

| product_id | product_name | brand | price | discount | stock_quantity |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 101 | Wireless Headphones | AudioPro | 199.99 | 10.00 | 500 |
| 102 | 4K Smart TV | VisionMax | 799.00 | 50.00 | 120 |
| 103 | Bluetooth Speaker | SoundWave | 49.99 | 0.00 | 850 |

### Orders

| order_id | customer_id | order_date | total_amount | payment_status | shipping_status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 5001 | 1 | 2023-10-25 10:00:00 | 189.99 | Completed | Shipped |
| 5002 | 2 | 2023-10-26 14:30:00 | 749.00 | Completed | Processing |
| 5003 | 3 | 2023-10-26 16:45:00 | 49.99 | Pending | Pending |

### Payments

| payment_id | order_id | payment_method | transaction_id | amount | payment_status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 9001 | 5001 | Credit Card | TXN-CC-8472 | 189.99 | Success |
| 9002 | 5002 | PayPal | TXN-PP-1193 | 749.00 | Success |
| 9003 | 5003 | Stripe | TXN-ST-0042 | 49.99 | Pending |

---

# Data Engineering Lifecycle Mapping

| Lifecycle Stage | Tables/Data Used |
| :--- | :--- |
| **Data Generation** | Web/Mobile apps generating `Clickstream_Events`, `Orders`, `Payments`, `Cart_Items`. |
| **Data Ingestion** | Extracting transactional data (`Orders`, `Products`) and streaming logs (`Clickstream_Events`). |
| **Data Storage** | Raw logs into Data Lake, Relational data into Operational Database (OLTP). |
| **Data Transformation** | ETL/ELT combining `Customers`, `Orders`, and `Clickstream_Events` to create analytical datasets. |
| **Data Serving** | Aggregated views provided to BI Tools to analyze `Payments`, `Inventory` levels, and Sales. |

---

# Conclusion
This mock enterprise e-commerce schema serves as a robust foundational model for building advanced data engineering platforms. By encompassing critical domains—such as customer interactions, inventory tracking, financial transactions, and granular clickstream analytics—it effectively simulates real-world enterprise data challenges. 

The schema's strict adherence to relational mapping, primary and foreign key constraints, and diverse data types ensures data integrity. Moving forward, this structure perfectly supports scalable cloud data platforms, complex ETL pipelines, real-time streaming analytics, comprehensive business intelligence reporting, and predictive machine learning models like recommendation engines.
