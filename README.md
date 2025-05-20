# Online Bookstore Database Management System

## Project Overview
A comprehensive relational database designed to support an online bookstore operation. This database manages all aspects of a modern bookstore including inventory management, customer accounts, order processing, sales tracking, and book information.

## Features

- **Book Management**: Store detailed information about books, including title, author, publisher, ISBN, price, and categorization
- **Inventory Tracking**: Monitor stock levels, track restocking needs, and manage inventory
- **Customer Management**: Store customer information and purchase history
- **Order Processing**: Handle order creation, payment processing, and order status tracking
- **Review System**: Allow customers to leave ratings and reviews for books
- **Publisher & Author Tracking**: Maintain information about publishers and authors
- **Multi-address Support**: Store multiple shipping addresses per customer

## Entity Relationship Diagram (ERD)

```
┌──────────────┐       ┌───────────┐       ┌───────────┐
│   Category   │       │ Publisher │       │  Author   │
├──────────────┤       ├───────────┤       ├───────────┤
│ category_id  │       │publisher_id│       │ author_id │
│ name         │       │ name      │       │ first_name│
│ description  │       │ address   │       │ last_name │
└──────┬───────┘       │ phone     │       │ biography │
       │               │ email     │       │ birth_date│
       │               │ website   │       └─────┬─────┘
       │               └─────┬─────┘             │
       │                     │                   │
       │                     │      ┌────────────┴─┐
       │                     │      │  book_author │
       │                     │      ├──────────────┤
       │                     │      │ book_id      │
┌──────▼─────────────────────▼──┐   │ author_id    │
│             Book              │◄──┴──────────────┘
├──────────────────────────────┬┤
│ book_id                      ││
│ isbn                         ││
│ title                        ││      ┌───────────┐
│ category_id                  ││      │ Inventory │
│ publisher_id                 ││      ├───────────┤
│ publication_date             ├┼─────►│inventory_id│
│ language                     ││      │ book_id   │
│ page_count                   ││      │ quantity  │
│ price                        ││      │ reorder   │
│ description                  ││      └───────────┘
│ cover_image_url              ││
└┬───────────────────────────┬─┘│
 │                           │  │      ┌─────────┐
 │                           │  │      │ Review  │
 │  ┌────────────────────┐   │  │      ├─────────┤
 │  │    Order_Details   │   │  │      │review_id│
 │  ├────────────────────┤   │  │      │ book_id │
 └─►│ order_detail_id    │   │  └─────►│customer_id
    │ order_id           │   │         │ rating  │
    │ book_id            │   │         │ comment │
    │ quantity           │   │         └────┬────┘
    │ price_per_unit     │   │              │
    │ discount           │   │              │
    └┬───────────────────┘   │              │
     │                       │              │
     │  ┌─────────────┐      │              │
     │  │ Book_Order  │      │     ┌────────▼───────┐
     │  ├─────────────┤      │     │    Customer    │
     └─►│ order_id    │      │     ├────────────────┤
        │ customer_id │◄─────┴────►│ customer_id    │
        │ order_date  │            │ first_name     │
        │ address_id  │◄───┐       │ last_name      │
        │ total_amount│    │       │ email          │
        │ status      │    │       │ password_hash  │
        │ ship_method │    │       │ phone          │
        │ tracking    │    │       └─────────┬──────┘
        └─────┬───────┘    │                 │
              │            │                 │
              │            │                 │
    ┌─────────▼──────┐     │      ┌──────────▼────────┐
    │    Payment     │     │      │ Shipping_Address  │
    ├────────────────┤     │      ├───────────────────┤
    │ payment_id     │     │      │ address_id        │
    │ order_id       │     │      │ customer_id       │
    │ payment_date   │     │      │ address_line1     │
    │ amount         │     └─────►│ address_line2     │
    │ payment_method │            │ city              │
    │ transaction_id │            │ state             │
    │ status         │            │ postal_code       │
    └────────────────┘            │ country           │
                                  │ is_default        │
                                  └───────────────────┘
```

## Database Setup Instructions

### Prerequisites
- MySQL Server 5.7 or higher
- MySQL Client or MySQL Workbench

### Installation Steps

1. **Create the database**:
   ```sql
   CREATE DATABASE bookstore;
   USE bookstore;
   ```

2. **Import the database schema**:
   ```bash
   # Using MySQL command line client
   mysql -u username -p bookstore < bookstore_database.sql
   
   # Or in MySQL client after connecting
   source path/to/bookstore_database.sql
   ```

3. **Verify installation**:
   ```sql
   SHOW TABLES;
   ```
   You should see all 12 tables listed.

### Optional: Import Sample Data

If you want to populate the database with sample data:
```bash
mysql -u username -p bookstore < sample_data.sql
```

## Database Usage Examples

### Find all books by a specific author:
```sql
SELECT b.title, b.isbn, b.price
FROM book b
JOIN book_author ba ON b.book_id = ba.book_id
JOIN author a ON ba.author_id = a.author_id
WHERE a.last_name = 'Rowling' AND a.first_name = 'J.K.';
```

### Get inventory status for all books:
```sql
SELECT b.title, i.quantity_in_stock, 
       CASE WHEN i.quantity_in_stock <= i.reorder_level 
            THEN 'Reorder needed' 
            ELSE 'In stock' 
       END AS stock_status
FROM book b
JOIN inventory i ON b.book_id = i.book_id
ORDER BY i.quantity_in_stock;
```

### Find best-selling books:
```sql
SELECT b.title, SUM(od.quantity) as total_sold
FROM book b
JOIN order_details od ON b.book_id = od.book_id
JOIN book_order bo ON od.order_id = bo.order_id
WHERE bo.status != 'Cancelled'
GROUP BY b.book_id
ORDER BY total_sold DESC
LIMIT 10;
```

## License
This project is available under the MIT License.

## Contact
For questions or support, please contact: your.email@example.com
