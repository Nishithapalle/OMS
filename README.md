Order Management System (OMS) – MuleSoft Project

Overview
This project implements an Order Management System using MuleSoft with API-led connectivity.

The system allows users to:
- Place orders
- Check stock availability
- Handle partial and failed orders

It follows a 3-layer API architecture:
- Experience API
- Process API
- System API

---

Architecture

The project is designed using MuleSoft API-led connectivity.

High-Level Architecture

Client (Postman)
        ↓
Experience API
        ↓
Process API
        ↓
System API
        ↓
Database

---

Experience API
- Entry point for client requests
- Handles request/response structure
- Sends requests to Process API
- Returns final response to user
- Client ID Enforcement applied

---

Process API
- Core business logic layer
- Validates input data
- Handles order processing
- Determines:
  - Success
  - Partial success
  - Failure
- Calls System API for DB operations

---

System API
- Responsible for database interaction
- Performs:
  - Stock lookup
  - Order creation
  - Stock updates

---

Database Design

The following tables are created:

- orders → Stores order details  
- order_items → Stores items per order  
- stock → Maintains SKU inventory  

---

Flow Implementation

1. Client sends request to Experience API
2. Experience API forwards request to Process API
3. Process API:
   - Validates request
   - Applies business logic
   - Calls System API
4. System API:
   - Fetches stock details
   - Updates stock
   - Inserts order and order items
5. Response flows back to client

---

Features Implemented

- ✔ RAML-first API design  
- ✔ 3-layer API architecture  
- ✔ Database integration  
- ✔ Business logic implementation  
- ✔ Standardized error handling  
- ✔ Property file configuration  
- ✔ Deployment to CloudHub  
- ✔ Client ID Enforcement policy  
- ✔ End-to-end testing using Postman  

---

Security

Client ID Enforcement policy is applied to all APIs.

Each request requires:
- `client_id`
- `client_secret`

---

Sample Request

```json
{
  "items": [
    {
      "sku": "MOB-IPH15-128-BLK",
      "quantity": 2
    }
  ]
}

---

Error Codes Used

The following error codes are implemented in the application:

| Error Code | Type                     | Description |
|-----------|--------------------------|-------------|
| 400       | Bad Request              | Invalid input (wrong SKU, negative quantity, empty fields) |
| 401       | Unauthorized             | Missing or invalid client_id / client_secret |
| 403       | Forbidden                | Access denied |
| 404       | Not Found                | SKU not found in stock |
| 405       | Method Not Allowed       | Unsupported HTTP method |
| 406       | Not Acceptable           | Invalid request format |
| 415       | Unsupported Media Type   | Incorrect content type |
| 500       | Internal Server Error    | Unexpected system failure |
| 501       | Not Implemented          | API method not implemented |

---

Assumptions

- SKU provided in the request must exist in the stock table  
- Quantity requested must be greater than zero  
- Each order contains at least one item  
- Stock availability is checked at the time of order placement  
- Orders are processed sequentially (no concurrent updates)  
- Client authentication is handled using Client ID Enforcement policy  
- Valid requests follow the defined RAML structure 

---
Sample responses

- Stock Availability

{
    "stock": [
        {
            "sku": "MOB-IPH15-128-BLK",
            "availableQuantity": 0,
            "isAvailable": true
        },
        {
            "sku": "LAP-DELL-5480",
            "availableQuantity": 0,
            "isAvailable": false
        },
        {
            "sku": "EAR-BT-SONY-WH",
            "availableQuantity": 36,
            "isAvailable": true
        }
    ]
}

- Partial success

{
    "phoneNumber": "1234567890",
    "requestStatus": "PARTIAL_SUCCESS",
    "itemResults": [
        {
            "sku": "MOB-IPH15-128-BLK",
            "quantity": 1,
            "status": "Not Available"
        },
        {
            "sku": "EAR-BT-SONY-WH",
            "quantity": 1,
            "status": "Order Placed"
        }
    ]
}

- Error Response

{
    "errorCode": "400",
    "message": "/items/0/quantity -1 is not greater or equal to 1",
    "correlationId": "107d2a15-0432-4fca-99e7-47f1f30ac8ef",
    "timestamp": "2026-05-06T07:26:18.705773076Z"
}
