# Organization Microservices System

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

### 2. [User API](https://github.com/Fawry-Intern/user-api)  
- **Description**: Manages user operations (e.g., registration, login, profile).  

### 3. [Order API](https://github.com/Fawry-Intern/order-api)  
- **Description**: Handles order creation, updates, and retrieval.  

### 4. [Shipping API](https://github.com/Fawry-Intern/shipping-api)  
- **Description**: Manages shipping details, tracking, and logistics.  

### 5. [Product API](https://github.com/Fawry-Intern/product-api)  
- **Description**: Provides CRUD operations for products and inventory.  

### 6. [Store API](https://github.com/Fawry-Intern/store-api)  
- **Description**: Manages store data and related operations.  

### 7. [Coupon API](https://github.com/Fawry-Intern/coupon-api)  
- **Description**: Creates and manages discount coupons.  

### 8. [Notification API](https://github.com/Fawry-Intern/notification-api)  
- **Description**: Sends notifications (e.g., email, SMS).  

### 9. [E-Commerce Frontend](https://github.com/Fawry-Intern/e-commerce-frontend)  
- **Description**: Web interface for interacting with the microservices.  

### 10. [Bank API](https://github.com/Fawry-Intern/bank-api)  
- **Description**: Handles payment processing, transactions, and bank-related operations.  

### 11. [Bank UI](https://github.com/Fawry-Intern/bank-ui)  
- **Description**: User interface for managing bank transactions and payments.  

### 12. [Eureka Server](https://github.com/Fawry-Intern/eureka-server)  
- **Description**: Service discovery for microservices, allowing dynamic registration and lookup.  

### 13. [Infrastructure](https://github.com/Fawry-Intern/infrastructure)  
- **Description**: Contains infrastructure-related configurations and deployment scripts.  
