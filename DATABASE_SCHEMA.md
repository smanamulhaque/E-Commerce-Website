# E-Commerce Database Schema Documentation

**Database Name:** `ecommerce_1`  
**Database Type:** MySQL (MariaDB 10.4.28)  
**Generated:** October 31, 2023  
**PHP Version:** 8.2.4

---

## Table of Contents
1. [Entity Relationship Diagram](#entity-relationship-diagram)
2. [Tables Structure](#tables-structure)
3. [Relationships](#relationships)
4. [Indexes](#indexes)
5. [Data Types Reference](#data-types-reference)

---

## Entity Relationship Diagram

```
┌─────────────┐          ┌──────────────┐
│  admin_table│          │  user_table  │
├─────────────┤          ├──────────────┤
│ admin_id(PK)│          │ user_id(PK)  │
│ admin_name  │          │ username     │
│ admin_email │          │ user_email   │
│ admin_image │          │ user_password│
│ admin_pwd   │          │ user_image   │
└─────────────┘          │ user_ip      │
                         │ user_address │
                         │ user_mobile  │
                         └──────────────┘
                              │ FK
                    ┌─────────┴─────────┐
                    │                   │
          ┌─────────▼──────┐   ┌────────▼──────────┐
          │  user_orders   │   │  orders_pending   │
          ├────────────────┤   ├───────────────────┤
          │ order_id(PK)   │   │ order_id(PK)      │
          │ user_id(FK)    │   │ user_id(FK)       │
          │ amount_due     │   │ invoice_number    │
          │ invoice_number │   │ product_id(FK)    │
          │ total_products │   │ quantity          │
          │ order_date     │   │ order_status      │
          │ order_status   │   └───────────────────┘
          └────────────────┘
                    │ FK
                    │
          ┌─────────▼──────────┐
          │ user_payments      │
          ├────────────────────┤
          │ payment_id(PK)     │
          │ order_id(FK)       │
          │ invoice_number     │
          │ amount             │
          │ payment_method     │
          │ payment_date       │
          └────────────────────┘

┌──────────────────────┐
│     products         │
├──────────────────────┤
│ product_id(PK)       │
│ product_title        │
│ product_description  │
│ product_keywords     │
│ category_id(FK)      │
│ brand_id(FK)         │
│ product_image_one    │
│ product_image_two    │
│ product_image_three  │
│ product_price        │
│ date                 │
│ status               │
└──────────────────────┘
   │ FK         │ FK
   │            │
┌──▼────────┐   │    ┌─────────────┐
│ categories│   │    │   brands    │
├───────────┤   │    ├─────────────┤
│ cat_id(PK)│   │    │ brand_id(PK)│
│ cat_title │   │    │ brand_title │
└───────────┘   │    └─────────────┘
                │
        ┌───────▼──────────┐
        │  card_details    │
        ├──────────────────┤
        │ product_id(FK)   │
        │ ip_address       │
        │ quantity         │
        └──────────────────┘
```

---

## Tables Structure

### 1. `admin_table`

**Purpose:** Store administrator account information and authentication data.

**Columns:**

| Column | Data Type | Constraints | Description |
|--------|-----------|-------------|-------------|
| admin_id | INT(11) | PRIMARY KEY | Unique administrator identifier |
| admin_name | VARCHAR(100) | NOT NULL | Administrator's full name |
| admin_email | VARCHAR(200) | NOT NULL | Administrator's email address |
| admin_image | VARCHAR(255) | NOT NULL | Filename of admin profile image |
| admin_password | VARCHAR(255) | NOT NULL | Bcrypt hashed password |

**Engine:** InnoDB  
**Charset:** utf8mb4  
**Collation:** utf8mb4_general_ci

**Sample Data:**
```
admin_id | admin_name | admin_email     | admin_image       | admin_password
---------|------------|-----------------|-------------------|---------------------------------
1        | abdo       | abdo@gmail.com  | logo after 3d_2.png | $2y$10$M/A/r5...
```

---

### 2. `user_table`

**Purpose:** Store customer account information and profiles.

**Columns:**

| Column | Data Type | Constraints | Description |
|--------|-----------|-------------|-------------|
| user_id | INT(11) | PRIMARY KEY | Unique user identifier |
| username | VARCHAR(100) | NOT NULL | User's login username |
| user_email | VARCHAR(100) | NOT NULL | User's email address |
| user_password | VARCHAR(255) | NOT NULL | Bcrypt hashed password |
| user_image | VARCHAR(255) | NOT NULL | Filename of user profile image |
| user_ip | VARCHAR(100) | NOT NULL | User's IP address for session tracking |
| user_address | VARCHAR(255) | NOT NULL | User's delivery address |
| user_mobile | VARCHAR(20) | NOT NULL | User's mobile phone number |

**Engine:** InnoDB  
**Charset:** utf8mb4  
**Collation:** utf8mb4_general_ci

**Sample Data:**
```
user_id | username | user_email      | user_ip | user_address | user_mobile
--------|----------|-----------------|---------|--------------|------------
1       | abdo     | abdo@gmail.com  | ::1     | Cairo        | 123456789
```

---

### 3. `products`

**Purpose:** Store all product information and details.

**Columns:**

| Column | Data Type | Constraints | Description |
|--------|-----------|-------------|-------------|
| product_id | INT(11) | PRIMARY KEY, AUTO_INCREMENT | Unique product identifier |
| product_title | VARCHAR(120) | NOT NULL | Product name/title |
| product_description | VARCHAR(255) | NOT NULL | Product description |
| product_keywords | VARCHAR(255) | NOT NULL | SEO keywords for search |
| category_id | INT(11) | NOT NULL | Foreign key to categories table |
| brand_id | INT(11) | NOT NULL | Foreign key to brands table |
| product_image_one | VARCHAR(255) | NOT NULL | Primary product image filename |
| product_image_two | VARCHAR(255) | NOT NULL | Secondary product image filename |
| product_image_three | VARCHAR(255) | NOT NULL | Tertiary product image filename |
| product_price | FLOAT | NOT NULL | Product price in USD |
| date | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Product creation/update timestamp |
| status | VARCHAR(100) | NOT NULL | Product status ('true' or 'false') |

**Engine:** InnoDB  
**Charset:** utf8mb4  
**Collation:** utf8mb4_general_ci

**Sample Data:**
```
product_id | product_title           | product_price | category_id | brand_id | status
-----------|-------------------------|---------------|-------------|----------|-------
1          | HAVIT HV-G92 Gamepad   | 120           | 6           | 9        | true
2          | ASUS FHD Gaming Laptop | 700           | 6           | 2        | true
3          | CANON EOS DSLR Camera  | 380           | 6           | 1        | true
4          | Breed Dry Dog Food     | 100           | 3           | 9        | true
```

---

### 4. `categories`

**Purpose:** Store product category classifications.

**Columns:**

| Column | Data Type | Constraints | Description |
|--------|-----------|-------------|-------------|
| category_id | INT(11) | PRIMARY KEY, AUTO_INCREMENT | Unique category identifier |
| category_title | VARCHAR(100) | NOT NULL | Category name |

**Engine:** InnoDB  
**Charset:** utf8mb4  
**Collation:** utf8mb4_general_ci

**Sample Data:**
```
category_id | category_title
------------|----------------
1           | Mobiles
2           | Books
3           | Food
4           | Clothes
5           | HeadPhones
6           | Electronics
7           | Accessories
```

---

### 5. `brands`

**Purpose:** Store product brand information.

**Columns:**

| Column | Data Type | Constraints | Description |
|--------|-----------|-------------|-------------|
| brand_id | INT(11) | PRIMARY KEY, AUTO_INCREMENT | Unique brand identifier |
| brand_title | VARCHAR(100) | NOT NULL | Brand name |

**Engine:** InnoDB  
**Charset:** utf8mb4  
**Collation:** utf8mb4_general_ci

**Sample Data:**
```
brand_id | brand_title
---------|------------
1        | Canon
2        | Lenovo
3        | Nike
4        | Dell
5        | Polo
6        | Hp
7        | Apple
8        | Oppo
9        | Other
10       | Samsung
13       | Nokia
```

---

### 6. `card_details`

**Purpose:** Store shopping cart items for customers.

**Columns:**

| Column | Data Type | Constraints | Description |
|--------|-----------|-------------|-------------|
| product_id | INT(11) | PRIMARY KEY | Unique product identifier in cart |
| ip_address | VARCHAR(255) | NOT NULL | Customer's IP address (cart session ID) |
| quantity | INT(100) | NOT NULL | Quantity of product in cart |

**Engine:** InnoDB  
**Charset:** utf8mb4  
**Collation:** utf8mb4_general_ci

**Notes:** 
- Uses IP address as session identifier for cart persistence
- Cart items are not tied to user_id, allowing anonymous shopping

---

### 7. `orders_pending`

**Purpose:** Store orders that are pending payment or processing.

**Columns:**

| Column | Data Type | Constraints | Description |
|--------|-----------|-------------|-------------|
| order_id | INT(11) | PRIMARY KEY, AUTO_INCREMENT | Unique order identifier |
| user_id | INT(11) | NOT NULL | Foreign key to user_table |
| invoice_number | INT(255) | NOT NULL | Unique invoice identifier |
| product_id | INT(11) | NOT NULL | Foreign key to products table |
| quantity | INT(255) | NOT NULL | Quantity ordered |
| order_status | VARCHAR(255) | NOT NULL | Order status (typically 'pending') |

**Engine:** InnoDB  
**Charset:** utf8mb4  
**Collation:** utf8mb4_general_ci

**Sample Data:**
```
order_id | user_id | invoice_number | product_id | quantity | order_status
---------|---------|---|-------------|----------|------------|----------
1        | 1       | 312346784    | 1          | 3        | pending
2        | 1       | 312346784    | 2          | 1        | pending
3        | 1       | 312346784    | 4          | 1        | pending
4        | 1       | 1918753782   | 3          | 2        | pending
5        | 1       | 351837813    | 1          | 2        | pending
```

---

### 8. `user_orders`

**Purpose:** Store confirmed/completed orders with payment status.

**Columns:**

| Column | Data Type | Constraints | Description |
|--------|-----------|-------------|-------------|
| order_id | INT(11) | PRIMARY KEY, AUTO_INCREMENT | Unique order identifier |
| user_id | INT(11) | NOT NULL | Foreign key to user_table |
| amount_due | INT(255) | NOT NULL | Total order amount |
| invoice_number | INT(255) | NOT NULL | Unique invoice identifier |
| total_products | INT(255) | NOT NULL | Total number of products in order |
| order_date | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Order placement timestamp |
| order_status | VARCHAR(255) | NOT NULL | Payment status ('paid' or 'pending') |

**Engine:** InnoDB  
**Charset:** utf8mb4  
**Collation:** utf8mb4_general_ci

**Sample Data:**
```
order_id | user_id | amount_due | invoice_number | total_products | order_date              | order_status
---------|---------|------------|---|------------|---------|----------
1        | 1       | 1160       | 312346784     | 3              | 2023-10-22 15:31:20     | paid
2        | 1       | 760        | 1918753782    | 1              | 2023-10-24 00:25:10     | pending
3        | 1       | 240        | 351837813     | 1              | 2023-10-24 18:41:02     | pending
```

---

### 9. `user_payments`

**Purpose:** Store payment transaction records.

**Columns:**

| Column | Data Type | Constraints | Description |
|--------|-----------|-------------|-------------|
| payment_id | INT(11) | PRIMARY KEY, AUTO_INCREMENT | Unique payment identifier |
| order_id | INT(11) | NOT NULL | Foreign key to user_orders table |
| invoice_number | INT(11) | NOT NULL | Unique invoice identifier |
| amount | INT(11) | NOT NULL | Payment amount |
| payment_method | VARCHAR(255) | NOT NULL | Payment method used (e.g., 'paypal') |
| payment_date | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Payment timestamp |

**Engine:** InnoDB  
**Charset:** utf8mb4  
**Collation:** utf8mb4_general_ci

**Sample Data:**
```
payment_id | order_id | invoice_number | amount | payment_method | payment_date
-----------|----------|---|--------|---|---
1          | 1        | 312346784      | 1160   | paypal         | 2023-10-24 00:23:26
```

---

## Relationships

### Foreign Key Relationships

```
products.category_id → categories.category_id
products.brand_id → brands.brand_id
card_details.product_id → products.product_id
orders_pending.user_id → user_table.user_id
orders_pending.product_id → products.product_id
user_orders.user_id → user_table.user_id
user_payments.order_id → user_orders.order_id
```

### Relationship Types

| Relationship | Type | Description |
|---|---|---|
| Categories to Products | 1:M | One category has many products |
| Brands to Products | 1:M | One brand has many products |
| Products to Cart | 1:M | One product can be in multiple carts |
| Users to Orders | 1:M | One user can have many orders |
| Orders to Payments | 1:1 | One order has one payment transaction |
| Products to Orders | 1:M | One product can be in many orders |

---

## Indexes

| Table | Column | Type | Purpose |
|---|---|---|---|
| admin_table | admin_id | PRIMARY | Quick admin lookup |
| user_table | user_id | PRIMARY | Quick user lookup |
| products | product_id | PRIMARY | Quick product lookup |
| categories | category_id | PRIMARY | Quick category lookup |
| brands | brand_id | PRIMARY | Quick brand lookup |
| card_details | product_id | PRIMARY | Quick cart item lookup |
| orders_pending | order_id | PRIMARY | Quick pending order lookup |
| user_orders | order_id | PRIMARY | Quick order lookup |
| user_payments | payment_id | PRIMARY | Quick payment lookup |

---

## Data Types Reference

| Data Type | Size | Usage |
|---|---|---|
| INT(11) | 4 bytes | Identifiers, counts, quantities |
| VARCHAR(100-255) | Variable | Text fields, names, emails |
| FLOAT | 4 bytes | Prices and decimal numbers |
| TIMESTAMP | 4 bytes | Date/time tracking |

---

## Best Practices Notes

### Security
- All passwords are hashed using bcrypt ($2y$10$...)
- Use prepared statements in PHP to prevent SQL injection
- Validate all user inputs before database operations

### Performance
- Primary keys indexed for fast lookups
- Consider adding indexes on frequently queried columns:
  - `orders_pending.user_id`
  - `user_orders.user_id`
  - `products.category_id`
  - `products.brand_id`

### Data Integrity
- Foreign key relationships ensure referential integrity
- Product deletion should cascade to orders and cart
- Use transactions for multi-table operations

### Future Considerations
- Add `created_at` and `updated_at` timestamps to all tables
- Add soft deletes for data retention
- Consider archiving old order data for performance
- Add user roles and permissions table for admin hierarchy

---

**Last Updated:** 04-12-2026  
**Database Version:** MySQL 10.4.28
