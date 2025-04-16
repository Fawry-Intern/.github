# Fawry's Microservices System

## Overview

This repository contains a collection of microservices that make up the organization's system. It includes services for managing users, orders, shipping, products, stores, coupons, and notifications, along with an Angular frontend. 


### Services Included
- **API Gateway**
- **User API**
- **Order API**
- **Shipping API**
- **Product API**
- **Store API**
- **Coupon API**
- **Notification API**
- **Angular Frontend (Front-End)**  
- **Bank API**
- **Bank Frontend**


### **REQUIREMENTS**  

- **Java >= 21** 
- **PostgreSQL 15** (or compatible version)  

To run the required version of the database server in a container instead of installing it manually, use the following command:  

```sh
docker run --name postgres-15 -p 5432:5432 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -d postgres:15
```

To stop and remove the container:  

```sh
docker rm -f postgres-15
```

## Deployment Instructions

### Step 1: Run [infrastructure](https://github.com/Fawry-Intern/infrastructure/blob/main/docker-compose.yml)

```sh
Run docker-compose up -d to start all services.
```

### Step 2: Run All service
Place all service directories in the same folder.

```sh
mvn spring-boot:run
```

### Step 5: Running the Angular Frontend
To run the Angular frontend locally:
1. Navigate to the `Front-End` directory:
   ```bash
   cd e-commerce-frontend
   ```

2. Install dependencies using npm:
   ```bash
   npm install
   ```

3. Start the Angular development server:
   ```bash
   ng serve
   ```

The Angular frontend will be accessible at `http://localhost:4200/` by default.

## Microservice Details  

### 1. [Gateway API](https://github.com/Fawry-Intern/gateway-api)  
- **Description**: Routes requests to other services, handles authentication, and provides a centralized entry point.  

<details>
<summary>For More Details</summary>

## Architecture Diagram
```mermaid
classDiagram
    %% API Gateway and connections
    class GatewayAPI {
        +Routes requests to appropriate microservice
        +Implements API Gateway pattern
        +Handles Authentication & Authorization
        +Manages Request Routing
        +Provides Load Balancing
        +Performs Caching
        +Supports API Versioning
        +Transforms Requests/Responses
        +Enforces Rate Limiting
    }
    
    %% Client Applications
    class ClientApplications {
        +Web application
        +Mobile application
        +Third-party integrations
    }
    
    %% Microservices
    class UserAPI {
        +User management endpoints
    }
    
    class BankAPI {
        +Banking and transaction endpoints
    }
    
    class OrderAPI {
        +Order management endpoints
    }
    
    class ProductAPI {
        +Product management endpoints
    }
    
    class StoreAPI {
        +Store and inventory endpoints
    }
    
    class CouponAPI {
        +Coupon management endpoints
    }
    
    class NotificationAPI {
        +Notification endpoints
    }
    
    %% Relationships
    ClientApplications --|> GatewayAPI: sends requests
    GatewayAPI --|> UserAPI: routes user requests
    GatewayAPI --|> BankAPI: routes banking requests
    GatewayAPI --|> OrderAPI: routes order requests
    GatewayAPI --|> ProductAPI: routes product requests
    GatewayAPI --|> StoreAPI: routes store requests
    GatewayAPI --|> CouponAPI: routes coupon requests
    GatewayAPI --|> NotificationAPI: routes notification requests
```
</details>

### 2. [User API](https://github.com/Fawry-Intern/user-api)  
- **Description**: Manages user operations (e.g., registration, login, profile).  

<details>
<summary>More details</summary>
UML Diagram

```mermaid
classDiagram
    %% User API and related entities
    class UserAPI {
        +POST /users/register : UserDTO
        +POST /users/login : AuthResponse
        +GET /users/:id : UserDTO
        +PUT /users/:id/update : UserDTO
        +DELETE /users/:id : void
        +PUT /users/:id/changePassword : void
    }
    
    class UserDTO {
        + Long id
        + String username
        + String firstName
        + String lastName
        + String phoneNumber
        + String address
        + String status
        + String email
        + String password
        + UserRole role
        + Instant createdAt
    }

    %% User API connections to other APIs
    class GatewayAPI {
        +Routes requests to User API
    }
    
    class BankAPI {
        +Creates accounts for users
    }
    
    class OrderAPI {
        +Retrieves user info for orders
    }
    
    class NotificationAPI {
        +Sends user notifications
    }
    
    %% Relationships
    GatewayAPI --|> UserAPI: routes requests
    UserAPI --|> BankAPI: requests account creation
    UserAPI --|> NotificationAPI: triggers notifications
    OrderAPI --|> UserAPI: retrieves user data
    
    UserAPI -- UserDTO: manages
```

ER Diagram

```mermaid
erDiagram
    USERS {
        BIGSERIAL user_id PK
        VARCHAR(100) username "UNIQUE"
        VARCHAR(255) email "UNIQUE"
        VARCHAR(255) password
        user_role role
        BOOLEAN is_active 
        TIMESTAMP created_at 
        TIMESTAMP updated_at 
    }
    
    USERS ||--o{ ORDERS : "places"
    USERS ||--o{ COUPONS : "redeems"
    USERS ||--o{ NOTIFICATIONS : "receives"
```

</details>

### 3. [Shipping API](https://github.com/Fawry-Intern/shipping-api)  
- **Description**: Manages shipping details, tracking, and logistics.  

<details>
<summary>More details</summary>

ER Diagram
```mermaid
erDiagram
    order_shipments {
        BIGSERIAL shipment_id PK
        BIGINT order_id FK
        BIGINT governorate_id FK
        BIGINT city_id FK
        VARCHAR customer_address
        BIGINT delivery_person_id FK
        VARCHAR tracking_token 
        VARCHAR confirmation_code 
        SHIPPING_STATUS status
        TIMESTAMP delivered_at
        TIMESTAMP created_at 
        TIMESTAMP updated_at 
    }

    delivery_persons {
        BIGSERIAL person_id PK
        VARCHAR name 
        VARCHAR email UNIQUE 
        VARCHAR phone_number UNIQUE 
        BOOLEAN is_active DEFAULT TRUE
        TIMESTAMP created_at DEFAULT now()
        TIMESTAMP updated_at DEFAULT now()
    }
    
    delivery_person_work_areas {
        BIGSERIAL work_area_id PK
        BIGINT delivery_person_id FK
        BIGINT governorate_id FK
        BIGINT city_id FK
        JSONB work_days
        TIMESTAMP created_at 
        TIMESTAMP updated_at 
    }

    delivery_persons ||--o{ order_shipments : delivers
    delivery_persons ||--o{ delivery_person_work_areas : works_in

```

UML Diagram

```mermaid
classDiagram
%% Shipping API and related entities
class ShippingAPI {
    +POST /order-shipments : OrderShipment
    +GET /order-shipments : List<OrderShipment>
    +GET /order-shipments/:shipment_id : OrderShipment
    +GET /order-shipments/track/:order_id/:tracking_token : OrderShipment
    +PATCH /order-shipments/:shipment_id/status : ShipmentStatus
    +GET /order-shipments/:shipment_id/status : ShipmentStatus
    +POST /order-shipments/:shipment_id/cancel : void
    +POST /order-shipments/:shipment_id/assign : DeliveryPerson
    +POST /order-shipments/:shipment_id/confirm-delivery : void
    +GET /order-shipments/:shipment_id/delivery-person : DeliveryPerson
    +GET /delivery-persons : List<DeliveryPerson>
    +GET /delivery-persons/available : List<DeliveryPerson>
    +GET /delivery-persons/:person_id/shipments : List<OrderShipment>
}

%% Order Shipment Entity
class OrderShipment {
    + Long shipmentId
    + Long orderId
    + Long governorateId
    + Long cityId
    + String customerAddress
    + DeliveryPerson deliveryPerson
    + String trackingToken
    + String confirmationCode
    + ShipmentStatus status
    + LocalDate expectedDeliveryDate
    + LocalDateTime deliveredAt
    + LocalDateTime createdAt
    + LocalDateTime updatedAt
}

%% Shipment Status Enum
class ShipmentStatus {
    <<enumeration>>
    ORDER_RECEIVED
    PROCESSING_ORDER
    ORDER_SHIPPED
    ORDER_DELIVERED
    ORDER_CANCELLED
}

%% Delivery Person Entity
class DeliveryPerson {
    + Long personId
    + String name
    + String email
    + String phoneNumber
    + Boolean isActive
    + LocalDateTime createdAt
    + LocalDateTime updatedAt
}

%% Delivery Person Work Area
class DeliveryPersonWorkArea {
    + Long workAreaId
    + DeliveryPerson deliveryPerson
    + Long governorateId
    + Long cityId
    + Set<DayOfWeek> workDays
    + LocalDateTime createdAt
    + LocalDateTime updatedAt
}

%% Day of Week Enum
class DayOfWeek {
    <<enumeration>>
    MONDAY
    TUESDAY
    WEDNESDAY
    THURSDAY
    FRIDAY
    SATURDAY
    SUNDAY
}

%% Gateway API
class GatewayAPI {
    +Routes requests to ShippingAPI
}

%% Notification API
class NotificationAPI {
   +sendNotification
}

%% Relationships
GatewayAPI --|> ShippingAPI : routes requests
ShippingAPI --> NotificationAPI : triggers notifications
DeliveryPerson "1" --> "0..*" OrderShipment : delivers
DeliveryPerson "1" --> "0..*" DeliveryPersonWorkArea : works_in
DeliveryPersonWorkArea --> "1..7" DayOfWeek : work_days
OrderShipment --> ShipmentStatus : has_status
ShippingAPI --> OrderShipment : manages
ShippingAPI --> DeliveryPerson : interacts with

``` 

</details>

### 4. [Product API](https://github.com/Fawry-Intern/product-api)  
- **Description**: Provides CRUD operations for products and inventory.  

<details>
<summary>More details</summary>
UML Diagram

```mermaid
classDiagram
    %% Product API and related entities
    class ProductAPI {
        +POST /products : ProductDTO
        +GET /products/:id : ProductDTO
        +GET /products : List~ProductDTO~
        +GET /products/search : List~ProductDTO~
        +PUT /products/:id : ProductDTO
        +DELETE /products/:id : void
        +GET /products/ids : List~Long~
    }

    class ProductDTO {
        + Long id
        + String name
        + BigDecimal price
        + String description
        + String imageUrl
        + Instant createdAt
        + Instant updatedAt
    }

    %% Product API connections to other services
    class GatewayAPI {
        +Routes requests to Product API
    }

    class StoreAPI {
        +Fetches product details for inventory
    }

    class OrderAPI {
        +Retrieves product details for orders
    }

    class NotificationAPI {
        +Sends product update notifications
    }

    %% Store API connections to other APIs
    class GatewayAPI {
        +Routes requests to Store API
    }

    %% Relationships
    GatewayAPI --|> ProductAPI: routes requests
    StoreAPI --|> ProductAPI: fetches product details
    OrderAPI --|> ProductAPI: retrieves product info
    ProductAPI --|> NotificationAPI: triggers notifications
```

ER Diagram 

```mermaid
erDiagram
    PRODUCTS {
        BIGSERIAL product_id PK
        VARCHAR product_name
        NUMERIC product_price
        TEXT description
        TEXT img_url
        TIMESTAMP created_at
        TIMESTAMP updated_at
    }
```
</details>


### 5. [Store API](https://github.com/Fawry-Intern/store-api)  
- **Description**: Manages store data and related operations.  

<details>
<summary>More details</summary>
UML Diagram

```mermaid
classDiagram
    %% Store API and related entities
    class StoreAPI {
        +POST /stores : StoreDTO
        +GET /stores/:id : StoreDTO
        +GET /stores : List<StoreDTO>
        +PUT /stores/:id : StoreDTO
        +DELETE /stores/:id : void
        +GET /stores/:id/stock : List~StockDTO~
        +POST /stores/:id/stock : StockDTO
        +PUT /stores/:id/stock/:productId : StockDTO
        +GET /stores/:id/consumptions : List~ProductConsumptionDTO~
        +POST /stores/:id/consumptions : ProductConsumptionDTO
    }
    
    class Stores {
        + Long id
        + String name
        + String address
    }

    class Stock {
        + Long id
        + Long productId
        + Long storeId
        + Integer availableQuantity
        + Instant lastUpdated
    }
    
    class ProductConsumption {
        + Long id
        + Long productId
        + Long storeId
        + BigDecimal price
        + Long quantity
        + Instant date
    }
    
    %% Store API connections to other APIs
    class GatewayAPI {
        +Routes requests to Store API
    }
    
    class ProductAPI {
        +GET /api/products/ids : List~ProductResponse~
    }

    class ProductResponse {
        + Long id
        + String name
        + BigDecimal price
        + String description
        + String imageUrl
        + Instant createdAt
        + Instant updatedAt
    }
    
    class OrderAPI {
        +Checks product availability
        +Reserves products
    }
    
    class NotificationAPI {
        +Sends inventory alerts
    }
    
    %% Relationships
    GatewayAPI --|> StoreAPI: routes requests
    StoreAPI --|> ProductResponse: retrieves product details
    ProductResponse --|> ProductAPI: fetches from
    OrderAPI --|> StoreAPI: checks availability
    StoreAPI --|> NotificationAPI: triggers inventory alerts
    
    StoreAPI -- Stores: manages
    Stores "1" -- "*" Stock: contains
    Stores "1" -- "*" ProductConsumption: records
```

ER Diagram

```mermaid
erDiagram
    STORES {
        BIGSERIAL store_id PK
        VARCHAR store_name "UNIQUE"
        VARCHAR store_address
    }
    
    STOCK {
        BIGSERIAL stock_id PK
        BIGINT product_id
        BIGINT store_id FK
        INT stock_available_quantity
        TIMESTAMP stock_last_updated
    }
    
    PRODUCT_CONSUMPTIONS {
        BIGSERIAL consumption_id PK
        BIGINT product_id
        BIGINT store_id FK
        NUMERIC product_price
        BIGINT consumption_quantity
        TIMESTAMP consumption_date
    }
    
    STORES ||--o{ STOCK : "has"
    STORES ||--o{ PRODUCT_CONSUMPTIONS : "has"
    STOCK }o--|| PRODUCT_CONSUMPTIONS : "tracks products"
```

</details>


### 6. [Coupon API](https://github.com/Fawry-Intern/coupon-api)  
- **Description**: Creates and manages discount coupons.  

<details>
<summary>More details</summary>

UML Diagram

```mermaid
classDiagram
    %% Coupon API and related entities
    class CouponAPI {
        +POST /coupons/create : CouponDTO
        +GET /coupons/:id : CouponDTO
        +GET /coupons/code/:code : CouponDTO
        +GET /coupons : List~CouponDTO~
        +PUT /coupons/:id/update : CouponDTO
        +PUT /coupons/:id/disable : void
        +POST /coupons/:id/consume : void
        +GET /coupons/user/:userId : List~CouponDTO~
        +GET /coupons/active : List~CouponDTO~
        +GET /coupons/expired : List~CouponDTO~
    }

    class Coupons {
        + Long id
        + String code
        + String discountType
        + BigDecimal discountValue
        + BigDecimal minPurchase
        + Integer usageLimit
        + Integer timesUsed
        + Instant expiryDate
        + Boolean isActive
    }

    %% Coupon API connections to other APIs
    class GatewayAPI {
        +Routes requests to Coupon API
    }

    class OrderAPI {
        +Requests coupon validation
        +Applies coupons to orders
    }

    class NotificationAPI {
        +Sends coupon notifications
    }

    class UserAPI {
        +Tracks user-specific coupon usage
    }

    %% Relationships
    GatewayAPI --|> CouponAPI: routes requests
    OrderAPI --|> CouponAPI: validates coupons
    CouponAPI --|> NotificationAPI: triggers notifications
    CouponAPI --|> UserAPI: tracks usage

    CouponAPI -- Coupons: manages
```


ER Diagram

```mermaid
erDiagram
    COUPONS {
        BIGSERIAL coupon_id PK
        VARCHAR(20) coupon_code
        VARCHAR(20) discount_type
        NUMERIC discount_value
        NUMERIC min_purchase 
        INT usage_limit 
        INT times_used 
        TIMESTAMP expiry_date 
        BOOLEAN is_active 
    }
    
    COUPONS ||--o{ ORDERS : "applies to"
    COUPONS ||--o{ USERS : "used by"
```

</details>


### 7. [Order API](https://github.com/Fawry-Intern/order-api)  
- Description: Handles order creation, updates, and retrieval.  

<details>
<summary>More details</summary>

Description:

- The customer initiates a checkout.
- The Order Microservice:
1. Validates the coupon with the Coupon Service.
2. Checks inventory with the Store Microservice.
3. Processes payment with the Bank Service.
4. Initiates shipping with the Shipping Microservice.
5. Sends a notification via the Notification Microservice.
- If the payment fails, the system:
  - Reverses the payment.
  - Cancels shipping.
  - Cancels the order.
  - Sends a cancellation notification.

Limitations:
- Lacked clarity on inter-service communication (e.g., REST vs. Kafka).
- Compensation steps were not detailed enough.
- No mention of database transactions or consistency mechanisms.

### Improved Design (Compensation Flow with Saga Pattern)
After researching best practices for distributed systems, I adopted the Choreographed Saga Pattern using Kafka to ensure better decoupling and consistency across services.

![Compensation Flow](https://github.com/Fawry-Intern/.github/blob/main/images/ordre_high_level_deign_v2.png)

Description:
- Order Service: Publishes an `order_created` event to Kafka and adds the order to its local database (TX 1).
- Store Service: Consumes the event, reduces inventory, and publishes a `store_updated` event (TX 2).
- Payment Bank Service: Processes the payment and publishes a `bank_updated` event (TX 3).
- Shipping Service: Ships the order and publishes a `shipping_updated` event (TX 2).
- Compensation Flow: If any step fails (e.g., payment fails):
  - Reverse inventory (Store Service).
  - Reverse payment (Payment Bank Service).
  - Cancel shipping (Shipping Service).

Why This is Better:
- Uses Kafka for event-driven communication, reducing coupling between services.
- Ensures consistency with local transactions for each service.
- Provides a clear compensation flow for handling failures.

Order UML Class Digram
![Order UML Class](https://github.com/Fawry-Intern/.github/blob/main/images/Order%20Digram.drawio.png)

API Flow (Alternative Approach)
I also explored an API-based approach for some interactions, which is useful for synchronous calls.

![API Flow](https://github.com/Fawry-Intern/.github/blob/main/images/order_digram.png)

Description:
- `order_products`: Fetch product details.
- `check_inventory`: Verify stock availability.
- `make_payment`: Process payment.
- `ship_order`: Initiate shipping.
- `send_notifications`: Notify customer and driver, with a reference notification.

Limitations:
- Lacks compensation details for failures.
- REST-based communication can be less reliable than Kafka for distributed transactions.

Why the Saga Pattern?
The Choreographed Saga Pattern with Kafka was chosen as the best practice because:
- It decouples services, allowing them to operate independently.
- Kafka ensures reliable event delivery and supports scalability.
- Local transactions per service maintain consistency without requiring a global transaction coordinator.

Future Improvements
- Add retry mechanisms for Kafka event publishing.
- Include the Notification Service in the compensation flow.
- Implement circuit breakers for REST-based interactions.


ER Diagram 

```mermaid
erDiagram
    ORDERS {
        BIGSERIAL order_id PK
        NUMERIC order_amount
        INT coupon_code
        TIMESTAMP created_at
    }
    
    ORDER_ITEMS {
        BIGSERIAL order_item_id PK
        BIGINT product_id 
        BIGINT order_id "FK"
        INT quantity 
        NUMERIC price 
    }
    
    ORDERS ||--o{ ORDER_ITEMS : "contains"
```
</details>


### 8. [Notification API](https://github.com/Fawry-Intern/notification-api)  
- **Description**: Sends notifications (e.g., email, SMS).  

### 9. [E-Commerce Frontend](https://github.com/Fawry-Intern/e-commerce-frontend)  
- **Description**: Web interface for interacting with the microservices.  

### 10. [Bank API](https://github.com/Fawry-Intern/bank-api)  
- **Description**: Handles payment processing, transactions, and bank-related operations.  
<details>
<summary> More details </summary>

UML Diagram

```mermaid
classDiagram
    %% Bank API and related entities
    class BankAPI {
        +POST /bank/accounts/create : AccountDTO
        +POST /bank/accounts/login : AuthResponse
        +GET /bank/accounts/:id : AccountDTO
        +POST /bank/accounts/:id/deposit : TransactionDTO
        +POST /bank/accounts/:id/withdraw : TransactionDTO
        +GET /bank/accounts/:id/transactions : List<TransactionDTO>
    }
    
    class AccountDTO {
        + Long id
        + String cardNumber
        + String cvv
        + BigDecimal balance
        + AccountStatus status
        + Instant createdAt
        + Long userId
    }

    class TransactionDTO {
        + Long id
        + TransactionType type
        + BigDecimal amount
        + String note
        + Instant createdAt
        + Long accountId
    }

    %% Bank API connections to other APIs
    class GatewayAPI {
        +Routes requests to Bank API
    }
    
    class UserAPI {
        +Provides user data for accounts
    }
    
    class OrderAPI {
        +Processes order payments
    }
    
    class NotificationAPI {
        +Sends transaction notifications
    }
    
    %% Relationships
    GatewayAPI --|> BankAPI: routes requests
    UserAPI --|> BankAPI: provides user data
    OrderAPI --|> BankAPI: requests payment processing
    BankAPI --|> NotificationAPI: triggers transaction alerts
    
    BankAPI -- AccountDTO: manages
    AccountDTO "1" -- "*" TransactionDTO: records
```

ER Diagram

```mermaid
erDiagram
    USERS {
        BIGSERIAL user_id PK
        VARCHAR username UNIQUE
        VARCHAR email UNIQUE
        VARCHAR password
        ENUM role
        ENUM status DEFAULT_ACTIVE
        VARCHAR phone_number
        TEXT user_address
        TIMESTAMP created_at DEFAULT_CURRENT_TIMESTAMP
    }

    ACCOUNTS {
        BIGSERIAL account_id PK
        VARCHAR card_number UNIQUE
        VARCHAR cvv
        NUMERIC balance CHECK_BALANCE
        ENUM account_status DEFAULT_ACTIVE
        TIMESTAMP created_at DEFAULT_CURRENT_TIMESTAMP
        BIGINT user_id FK
    }

    TRANSACTIONS {
        BIGSERIAL transaction_id PK
        ENUM transaction_type
        NUMERIC transaction_amount CHECK_AMOUNT
        TEXT transaction_note
        TIMESTAMP created_at DEFAULT_CURRENT_TIMESTAMP
        BIGINT account_id FK
    }

    USERS ||--o{ ACCOUNTS : "owns"
    ACCOUNTS ||--o{ TRANSACTIONS : "records"
```

</details>

### 11. [Bank UI](https://github.com/Fawry-Intern/bank-ui)  
- **Description**: User interface for managing bank transactions and payments.  

### 12. [Eureka Server](https://github.com/Fawry-Intern/eureka-server)  
- **Description**: Service discovery for microservices, allowing dynamic registration and lookup.  

### 13. [Infrastructure](https://github.com/Fawry-Intern/infrastructure)  
- **Description**: Contains infrastructure-related configurations and deployment scripts.  
