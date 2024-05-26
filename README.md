# Getting Started

## Reference Documentation

### Prerequisites
* Java 17 or higher
* Maven (to build the application)
* Run Apache Zookeeper (default port: 2181) and Kafka (default port: 9092) on default ports

### Installation and Running the Application
* To build the application: go to the application root directory and run the Maven command:
  ```sh
  mvn clean install
  ```
* To run the application instance: go to the target directory of the application and run:
  ```sh
  java -jar <application_jar>.jar
  ```
### Service and API Details
* All APIs are accessed via the API gateway.
* Services are managed by the Eureka server service registry and discovery (http://localhost:8181/eureka/web).
* Start the Eureka server and API gateway service before starting any of the other services.
* All APIs require an access token in the Authorization header, except for the following endpoints:
  * `/auth/register`
  * `/auth/authenticate`
  * `/eureka`
  * `/actuator`
* To obtain the authorization token, use the following API:
  ```sh
  POST http://localhost:8181/auth/authenticate
  ```
  with Request Body
  ```json
  {
    "username": "username",
    "password": "user_password"
  }
  ```
  
#### identity-service (User Principal Creation and Authentication)
The service is accessed at http://localhost:8181/auth and provides the following endpoints:
* **POST /register** - Used for registering a user, takes the request body:
  ```json
  {
    "username": "username",
    "password": "user_password"
  }
  ```
* **POST /authenticate** - Used for authenticating a user, takes the request body:
  ```json
  {
    "username": "username",
    "password": "user_password"
  }
  ```
and returns an authorization token.
* **GET /validate** - Validates the authorization token.


#### inventory-service (Product Inventory Management)
  This service is accessed at http://localhost:8181/api/inventory and provides the following endpoint:
  * **GET /add-stock** - Adds items to the inventory stock, with the request body:
    ```json
    {
      "skuCode": "",
      "quantity": 0
    }
    ```

#### order-service (Order Placement and Retrieval)
  This service is accessed at http://localhost:8181/api/order and provides the following endpoints:
  * **POST /** - Places an order request and updates the order based on inventory status, with the request body:
    ```json
    {
      "orderLineItems": [
        {"skuCode": "sku-code", "price": 0.0, "quantity": 0}
      ]
    }
    ```
  * **GET /** - Retrieves orders, with optional request parameters `pageNumber` and `pageSize` (default values are 0 and 10 respectively), and returns a page of orders placed by the user.

**Note:** The order service checks the inventory after accepting the order request and updates the order status to either `ORDER_SUCCESS` or `ORDER_REJECTED` based on inventory availability. The order service and inventory service use Kafka for asynchronous communication.


#### product-service (Product Management)
  This service is accessed at http://localhost:8181/api/product and provides the following endpoints:
  * **POST /** - Stores a product in the database, with the request body:
    ```json
    {
      "name": "",
      "description": "",
      "price": 0.0
    }
    ```
  * **GET /** - Retrieves products, with optional request parameters `pageNumber` and `pageSize` (default values are 0 and 10 respectively), and returns a page of products.
  * **GET /{productId}** - Retrieves product details by `productId`.
  * **PUT /{productId}** - Updates a product, takes a path parameter `productId` and a request body:
    ```json
    {
      "name": "",
      "description": "",
      "price": 0.0
    }
    ```
  * **DELETE /{productId}** - Deletes a product by `productId`.

    
  