# E-commerce Platform Database Documentation

## Table of Contents

- [Introduction](#introduction)
- [Entities and Attributes](#entities-and-attributes)
  - [1. Product](#1-product)
  - [2. Category](#2-category)
  - [3. Customer](#3-customer)
  - [4. Address](#4-address)
  - [5. Order](#5-order)
  - [6. OrderItem](#6-orderitem)
  - [7. Payment](#7-payment)
  - [8. Shipment](#8-shipment)
  - [9. ShippingProvider](#9-shippingprovider)
  - [10. Inventory](#10-inventory)
  - [11. Warehouse](#11-warehouse)
  - [12. Feedback](#12-feedback)
- [Relationships](#relationships)
- [Cardinality and Participation Constraints](#cardinality-and-participation-constraints)

---

## Introduction

This documentation outlines the database structure for a fully-featured e-commerce platform designed to manage products, customers, orders, payments, inventory, and deliveries. The database is meticulously designed to support scalability, security, performance, and data integrity, aligning with both functional and non-functional requirements.

---

## Entities and Attributes

Each entity represents a distinct object or concept within the e-commerce system. Attributes define the properties or characteristics of these entities.

### 1. Product

**Description:** Represents items available for sale on the platform.

**Attributes:**

- `ProductID` (**PK**): Unique identifier for each product.
- `Name`: The name of the product.
- `Description`: Detailed information about the product.
- `Price`: The selling price of the product.
- `StockQuantity`: Current stock level of the product.
- `ReorderThreshold`: Stock level at which the product should be reordered.
- `CategoryID` (**FK**): References the primary category of the product.
- `SubcategoryID` (**FK**, nullable): References the subcategory, if applicable.
- `CreatedAt`: Timestamp when the product was added.
- `UpdatedAt`: Timestamp of the last update to the product details.

**Rationale:** The `ReorderThreshold` facilitates automated inventory management. `CategoryID` and `SubcategoryID` allow hierarchical categorization, enhancing product organization and searchability.

### 2. Category

**Description:** Defines classifications for products, enabling organized browsing and management.

**Attributes:**

- `CategoryID` (**PK**): Unique identifier for each category.
- `Name`: The name of the category.
- `Description`: Detailed information about the category.
- `ParentCategoryID` (**FK**, nullable): References a parent category for hierarchical structuring.
- `CreatedAt`: Timestamp when the category was created.
- `UpdatedAt`: Timestamp of the last update to the category.

**Rationale:** Self-referencing `ParentCategoryID` allows for multi-level category hierarchies, supporting complex product classifications.

### 3. Customer

**Description:** Represents users registered on the platform who can make purchases.

**Attributes:**

- `CustomerID` (**PK**): Unique identifier for each customer.
- `FirstName`: Customer's first name.
- `LastName`: Customer's last name.
- `Email` (**Unique**): Customer's email address, used for login and communication.
- `PasswordHash`: Encrypted password for authentication.
- `PhoneNumber`: Customer's contact number.
- `CreatedAt`: Timestamp when the customer registered.
- `UpdatedAt`: Timestamp of the last update to customer details.

**Rationale:** Storing `PasswordHash` ensures security by not keeping plaintext passwords. `Email` serves as a unique identifier for account management and communication.

### 4. Address

**Description:** Stores shipping and billing addresses associated with customers.

**Attributes:**

- `AddressID` (**PK**): Unique identifier for each address.
- `CustomerID` (**FK**): References the customer owning the address.
- `Street`: Street address.
- `City`: City of the address.
- `State`: State or province.
- `PostalCode`: ZIP or postal code.
- `Country`: Country of the address.
- `IsPrimary`: Boolean indicating if it's the primary address.
- `CreatedAt`: Timestamp when the address was added.
- `UpdatedAt`: Timestamp of the last update to the address.

**Rationale:** Allowing multiple addresses per customer provides flexibility for shipping and billing preferences.

### 5. Order

**Description:** Represents a customer's purchase, encompassing all items, payment, and delivery details.

**Attributes:**

- `OrderID` (**PK**): Unique identifier for each order.
- `CustomerID` (**FK**): References the customer who placed the order.
- `OrderDate`: Date and time when the order was placed.
- `Status`: Current status of the order (e.g., Pending, Confirmed, Shipped).
- `TotalAmount`: Total cost of the order.
- `ShippingAddressID` (**FK**): References the address where the order is shipped.
- `BillingAddressID` (**FK**): References the billing address for the order.
- `PaymentID` (**FK**): References the payment associated with the order.
- `ShipmentID` (**FK**, nullable): References the shipment details, if shipped.
- `CreatedAt`: Timestamp when the order was created.
- `UpdatedAt`: Timestamp of the last update to the order.

**Rationale:** Linking to both shipping and billing addresses allows for distinct delivery and payment locations. `ShipmentID` being nullable accommodates orders that haven't been shipped yet.

### 6. OrderItem

**Description:** Details each product within an order, including quantity and pricing.

**Attributes:**

- `OrderItemID` (**PK**): Unique identifier for each order item.
- `OrderID` (**FK**): References the parent order.
- `ProductID` (**FK**): References the product being ordered.
- `Quantity`: Number of units ordered for the product.
- `UnitPrice`: Price per unit at the time of order.
- `TotalPrice`: Total cost for the product (`Quantity` × `UnitPrice`).
- `CreatedAt`: Timestamp when the order item was created.
- `UpdatedAt`: Timestamp of the last update to the order item.

**Rationale:** Captures the specifics of each product within an order, enabling detailed order tracking and inventory management.

### 7. Payment

**Description:** Stores payment information related to an order.

**Attributes:**

- `PaymentID` (**PK**): Unique identifier for each payment.
- `OrderID` (**FK**): References the associated order.
- `PaymentMethod`: Method used for payment (e.g., Credit Card, PayPal).
- `PaymentStatus`: Current status of the payment (e.g., Pending, Completed).
- `PaymentDate`: Date and time when the payment was made.
- `Amount`: Amount paid.
- `TransactionID` (**Unique**): Identifier from the payment gateway.
- `CreatedAt`: Timestamp when the payment was recorded.
- `UpdatedAt`: Timestamp of the last update to the payment.

**Rationale:** `TransactionID` ensures traceability of payments through external gateways. Linking directly to `OrderID` facilitates order-payment association.

### 8. Shipment

**Description:** Contains details about the delivery of an order.

**Attributes:**

- `ShipmentID` (**PK**): Unique identifier for each shipment.
- `OrderID` (**FK**): References the associated order.
- `ShippingProviderID` (**FK**): References the shipping provider handling the shipment.
- `TrackingNumber`: Tracking number provided by the shipping provider.
- `ShipmentDate`: Date when the order was shipped.
- `EstimatedDeliveryDate`: Projected delivery date.
- `Status`: Current status of the shipment (e.g., Shipped, In Transit).
- `CreatedAt`: Timestamp when the shipment was created.
- `UpdatedAt`: Timestamp of the last update to the shipment.

**Rationale:** Facilitates tracking of orders from dispatch to delivery, enhancing customer experience through visibility.

### 9. ShippingProvider

**Description:** Represents external companies responsible for delivering shipments.

**Attributes:**

- `ShippingProviderID` (**PK**): Unique identifier for each shipping provider.
- `Name`: Name of the shipping provider.
- `ContactInfo`: Contact details (e.g., phone number, email).
- `CreatedAt`: Timestamp when the shipping provider was added.
- `UpdatedAt`: Timestamp of the last update to the shipping provider.

**Rationale:** Managing multiple shipping providers allows flexibility in delivery options and redundancy.

### 10. Inventory

**Description:** Tracks stock levels of products across various warehouses.

**Attributes:**

- `InventoryID` (**PK**): Unique identifier for each inventory record.
- `ProductID` (**FK**): References the product.
- `WarehouseID` (**FK**): References the warehouse holding the stock.
- `QuantityAvailable`: Current stock available in the warehouse.
- `ReorderLevel`: Stock level at which to trigger a reorder.
- `CreatedAt`: Timestamp when the inventory record was created.
- `UpdatedAt`: Timestamp of the last update to the inventory.

**Rationale:** Enables precise stock management and ensures products are available across multiple locations.

### 11. Warehouse

**Description:** Represents physical storage locations for products.

**Attributes:**

- `WarehouseID` (**PK**): Unique identifier for each warehouse.
- `Name`: Name of the warehouse.
- `Location`: Physical address or description of the warehouse location.
- `Capacity`: Maximum storage capacity of the warehouse.
- `CreatedAt`: Timestamp when the warehouse was added.
- `UpdatedAt`: Timestamp of the last update to the warehouse.

**Rationale:** Facilitates distribution strategies by managing multiple storage locations, optimizing delivery times and stock distribution.

### 12. Feedback

**Description:** Captures customer feedback and ratings related to orders and products.

**Attributes:**

- `FeedbackID` (**PK**): Unique identifier for each feedback entry.
- `CustomerID` (**FK**): References the customer providing feedback.
- `OrderID` (**FK**, nullable): References the order associated with the feedback, if applicable.
- `Rating`: Numerical rating (e.g., 1 to 5).
- `Comments`: Textual feedback from the customer.
- `CreatedAt`: Timestamp when the feedback was submitted.
- `UpdatedAt`: Timestamp of the last update to the feedback.

**Rationale:** Collecting feedback enhances customer satisfaction and provides valuable insights for improving products and services.

---

## Relationships

The relationships between entities define how data is interconnected within the database. Understanding these relationships is crucial for maintaining data integrity and enabling efficient data retrieval.

| **Relationship**                | **Entity 1**      | **Entity 2**      | **Cardinality** | **Entity 1 Participation** | **Entity 2 Participation** |
|---------------------------------|-------------------|-------------------|------------------|-----------------------------|-----------------------------|
| **Product ↔ Category**          | Product           | Category          | Many-to-One      | Mandatory                   | Optional                    |
| **Category ↔ Category**         | Category (Child)  | Category (Parent) | Many-to-One      | Optional                    | Optional                    |
| **Customer ↔ Address**          | Customer          | Address           | One-to-Many      | Mandatory                   | Optional                    |
| **Customer ↔ Order**            | Customer          | Order             | One-to-Many      | Optional                    | Mandatory                   |
| **Order ↔ OrderItem**           | Order             | OrderItem         | One-to-Many      | Mandatory                   | Mandatory                   |
| **Product ↔ OrderItem**         | Product           | OrderItem         | One-to-Many      | Optional                    | Mandatory                   |
| **Order ↔ Payment**             | Order             | Payment           | One-to-One       | Mandatory                   | Optional                    |
| **Order ↔ Shipment**            | Order             | Shipment          | One-to-One       | Optional                    | Optional                    |
| **Shipment ↔ ShippingProvider** | Shipment          | ShippingProvider  | Many-to-One      | Mandatory                   | Optional                    |
| **Product ↔ Inventory**         | Product           | Inventory         | One-to-Many      | Mandatory                   | Optional                    |
| **Warehouse ↔ Inventory**       | Warehouse         | Inventory         | One-to-Many      | Optional                    | Mandatory                   |
| **Customer ↔ Feedback**         | Customer          | Feedback          | One-to-Many      | Optional                    | Optional                    |
| **Order ↔ Feedback**            | Order             | Feedback          | One-to-Many      | Optional                    | Optional                    |

---

## Cardinality and Participation Constraints

Understanding the cardinality (relationship types) and participation constraints (mandatory or optional) between entities is essential for accurate database design.

| **Relationship**                | **Cardinality** | **Entity 1 Participation** | **Entity 2 Participation** |
|---------------------------------|------------------|-----------------------------|-----------------------------|
| **Product ↔ Category**          | Many-to-One      | Mandatory                   | Optional                    |
| **Category ↔ Category**         | Many-to-One      | Optional                    | Optional                    |
| **Customer ↔ Address**          | One-to-Many      | Mandatory                   | Optional                    |
| **Customer ↔ Order**            | One-to-Many      | Optional                    | Mandatory                   |
| **Order ↔ OrderItem**           | One-to-Many      | Mandatory                   | Mandatory                   |
| **Product ↔ OrderItem**         | One-to-Many      | Optional                    | Mandatory                   |
| **Order ↔ Payment**             | One-to-One       | Mandatory                   | Optional                    |
| **Order ↔ Shipment**            | One-to-One       | Optional                    | Optional                    |
| **Shipment ↔ ShippingProvider** | Many-to-One      | Mandatory                   | Optional                    |
| **Product ↔ Inventory**         | One-to-Many      | Mandatory                   | Optional                    |
| **Warehouse ↔ Inventory**       | One-to-Many      | Optional                    | Mandatory                   |
| **Customer ↔ Feedback**         | One-to-Many      | Optional                    | Optional                    |
| **Order ↔ Feedback**            | One-to-Many      | Optional                    | Optional                    |

### Cardinality Definitions:

- **One-to-One (1:1):** Each record in Entity A relates to one, and only one, record in Entity B.
- **One-to-Many (1:N):** A single record in Entity A can relate to multiple records in Entity B.
- **Many-to-One (N:1):** Multiple records in Entity A can relate to a single record in Entity B.

### Participation Definitions:

- **Mandatory:** An entity must participate in the relationship.
- **Optional:** An entity may or may not participate in the relationship.

### Detailed Relationship Constraints:

#### Product ↔ Category

- **Cardinality:** Many Products belong to One Category.
- **Participation:**
  - **Product:** Must belong to a Category (**Mandatory**).
  - **Category:** May have zero or more Products (**Optional**).

#### Category ↔ Category (Self-Referencing)

- **Cardinality:** Many Subcategories belong to One Parent Category.
- **Participation:**
  - **Subcategory:** May or may not have a Parent Category (**Optional**).
  - **Parent Category:** May have zero or more Subcategories (**Optional**).

#### Customer ↔ Address

- **Cardinality:** One Customer can have Many Addresses.
- **Participation:**
  - **Customer:** Must exist to have Addresses (**Mandatory**).
  - **Address:** May or may not be present (**Optional**).

#### Customer ↔ Order

- **Cardinality:** One Customer can place Many Orders.
- **Participation:**
  - **Customer:** May exist without placing Orders (**Optional**).
  - **Order:** Must be associated with a Customer (**Mandatory**).

#### Order ↔ OrderItem

- **Cardinality:** One Order can contain Many OrderItems.
- **Participation:**
  - **Order:** Must have at least one OrderItem (**Mandatory**).
  - **OrderItem:** Must belong to an Order (**Mandatory**).

#### Product ↔ OrderItem

- **Cardinality:** One Product can appear in Many OrderItems.
- **Participation:**
  - **Product:** May exist without being part of any OrderItem (**Optional**).
  - **OrderItem:** Must reference a Product (**Mandatory**).

#### Order ↔ Payment

- **Cardinality:** One Order has One Payment.
- **Participation:**
  - **Order:** Must have a Payment (**Mandatory**).
  - **Payment:** May or may not be initially associated with an Order (**Optional**).

#### Order ↔ Shipment

- **Cardinality:** One Order can have One Shipment.
- **Participation:**
  - **Order:** May have a Shipment once shipped (**Optional**).
  - **Shipment:** Must be associated with an Order (**Optional**).

#### Shipment ↔ ShippingProvider

- **Cardinality:** Many Shipments are handled by One ShippingProvider.
- **Participation:**
  - **Shipment:** Must be handled by a ShippingProvider (**Mandatory**).
  - **ShippingProvider:** May exist without any Shipments (**Optional**).

#### Product ↔ Inventory

- **Cardinality:** One Product can have Many Inventory records.
- **Participation:**
  - **Product:** Must have Inventory records (**Mandatory**).
  - **Inventory:** May or may not exist for a Product (**Optional**).

#### Warehouse ↔ Inventory

- **Cardinality:** One Warehouse can hold Many Inventory records.
- **Participation:**
  - **Warehouse:** May exist without holding Inventory (**Optional**).
  - **Inventory:** Must belong to a Warehouse (**Mandatory**).

#### Customer ↔ Feedback

- **Cardinality:** One Customer can provide Many Feedback entries.
- **Participation:**
  - **Customer:** May exist without providing Feedback (**Optional**).
  - **Feedback:** Must be associated with a Customer (**Optional**).

#### Order ↔ Feedback

- **Cardinality:** One Order can have Many Feedback entries.
- **Participation:**
  - **Order:** May exist without any Feedback (**Optional**).
  - **Feedback:** May or may not be associated with an Order (**Optional**).

---
