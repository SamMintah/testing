# Product Management API Documentation

This document provides a detailed overview of the API endpoints for managing products, categories, inventory, and related resources.

**Base URL**: `/`

**Authentication**: Most endpoints require a JWT Bearer token in the `Authorization` header. Endpoints marked as `Public` do not require authentication. Role-based access control is specified for each endpoint where applicable.

---

## Table of Contents
1. [Categories API](#categories-api)
2. [Products API](#products-api)
3. [Public Products API](#public-products-api)
4. [Product Requests API](#product-requests-api)
5. [Reviews API](#reviews-api)
6. [Inventory API](#inventory-api)
7. [Warehouses API](#warehouses-api)

---

## Categories API

Base Path: `/categories`

### Create Category
- **Endpoint**: `POST /categories`
- **Permission**: `ADMIN`, `SUPER_ADMIN`
- **Description**: Creates a new product category.
- **Request Body**: `CreateCategoryDto`
  ```json
  {
    "name": "string (required)",
    "slug": "string (optional, auto-generated from name if not provided)",
    "description": "string (optional)",
    "imageUrl": "string (optional, URL)",
    "parentId": "number (optional, ID of parent category)",
    "sortOrder": "number (optional, default: 0)"
  }
  ```

### Get All Categories
- **Endpoint**: `GET /categories`
- **Permission**: `Public`
- **Description**: Retrieves a paginated list of categories.
- **Query Parameters**: `CategoryQueryParams`
  - `page`: `number` (optional, default: 1)
  - `limit`: `number` (optional, default: 20)
  - `parentId`: `number` (optional, filter by parent category ID)
  - `isActive`: `boolean` (optional, filter by active status)
  - `search`: `string` (optional, search by name)
  - `sortBy`: `string` (optional, enum: 'name', 'sortOrder', 'createdAt', 'updatedAt', default: 'sortOrder')
  - `sortDir`: `string` (optional, enum: 'asc', 'desc', default: 'asc')
  - `includeChildren`: `boolean` (optional, default: false)
  - `includeProducts`: `boolean` (optional, default: false)

### Get Category Hierarchy
- **Endpoint**: `GET /categories/hierarchy`
- **Permission**: `Public`
- **Description**: Retrieves the full category tree structure.

### Get Categories with Product Counts
- **Endpoint**: `GET /categories/with-product-counts`
- **Permission**: `Public`
- **Description**: Retrieves all categories along with the number of products in each.

### Get Category by ID
- **Endpoint**: `GET /categories/:id`
- **Permission**: `Public`
- **Description**: Retrieves a single category by its ID.
- **Path Parameters**:
  - `id`: `number` (required) - The ID of the category.

### Update Category
- **Endpoint**: `PUT /categories/:id`
- **Permission**: `ADMIN`, `SUPER_ADMIN`
- **Description**: Updates an existing category.
- **Path Parameters**:
  - `id`: `number` (required) - The ID of the category to update.
- **Request Body**: `UpdateCategoryDto`
  ```json
  {
    "name": "string (optional)",
    "slug": "string (optional)",
    "description": "string (optional)",
    "imageUrl": "string (optional, URL)",
    "parentId": "number (optional)",
    "sortOrder": "number (optional)",
    "isActive": "boolean (optional)"
  }
  ```

### Delete Category
- **Endpoint**: `DELETE /categories/:id`
- **Permission**: `ADMIN`, `SUPER_ADMIN`
- **Description**: Deletes a category (soft delete).
- **Path Parameters**:
  - `id`: `number` (required) - The ID of the category to delete.

---

## Products API

Base Path: `/products`

### Create Product
- **Endpoint**: `POST /products`
- **Permission**: `ADMIN`
- **Description**: Creates a new product.
- **Request Body**: `multipart/form-data`
  - `name`: `string (required)`
  - `description`: `string (optional)`
  - `primaryCategoryId`: `number (required)`
  - `categoryIds`: `number[] (optional)`
  - `price`: `number (optional)`
  - `basePrice`: `number (optional)`
  - `currency`: `string (optional, default: 'GHS')`
  - `stockQuantity`: `number (optional)`
  - `location`: `string (optional)`
  - `condition`: `string (optional, enum: 'FRESH', 'DRIED', 'PROCESSED', 'ORGANIC', default: 'FRESH')`
  - `isActive`: `boolean (optional, default: true)`
  - `discountPercentage`: `number (optional)`
  - `discountStartDate`: `Date (optional, ISO format)`
  - `discountEndDate`: `Date (optional, ISO format)`
  - `images`: `File[] (optional, up to 5 files)`

### Get All Products
- **Endpoint**: `GET /products`
- **Permission**: `Public`
- **Description**: Retrieves a paginated list of products.
- **Query Parameters**: `ProductQueryParams`
  - `page`: `number` (optional, default: 1)
  - `limit`: `number` (optional, default: 20)
  - `category`: `string` (optional, category slug)
  - `isActive`: `boolean` (optional)
  - `minPrice`: `number` (optional)
  - `maxPrice`: `number` (optional)
  - `location`: `string` (optional)
  - `condition`: `string` (optional, enum: 'FRESH', 'DRIED', 'PROCESSED', 'ORGANIC')
  - `search`: `string` (optional)
  - `sortBy`: `string` (optional, enum: 'name', 'price', 'createdAt', 'stockQuantity')
  - `sortDir`: `string` (optional, enum: 'asc', 'desc')

### Get Discounted Products
- **Endpoint**: `GET /products/discounted`
- **Permission**: `Public`
- **Description**: Retrieves products that are currently discounted.
- **Query Parameters**:
  - `page`: `number` (optional, default: 1)
  - `limit`: `number` (optional, default: 20)
  - `minDiscount`: `number` (optional)

### Get Weekly Specials
- **Endpoint**: `GET /products/weekly-specials`
- **Permission**: `Public`
- **Description**: Retrieves products marked as weekly specials.
- **Query Parameters**:
  - `page`: `number` (optional, default: 1)
  - `limit`: `number` (optional, default: 20)

### Get Featured Products
- **Endpoint**: `GET /products/featured`
- **Permission**: `Public`
- **Description**: Retrieves products marked as featured.
- **Query Parameters**:
  - `page`: `number` (optional, default: 1)
  - `limit`: `number` (optional, default: 20)

### Get Product by ID
- **Endpoint**: `GET /products/:id`
- **Permission**: Authenticated User
- **Description**: Retrieves a single product by its ID, including review information.
- **Path Parameters**:
  - `id`: `number` (required) - The ID of the product.

### Update Product
- **Endpoint**: `PATCH /products/:id`
- **Permission**: `ADMIN`
- **Description**: Updates an existing product.
- **Path Parameters**:
  - `id`: `number` (required) - The ID of the product to update.
- **Request Body**: `multipart/form-data` with fields from `UpdateProductDto`
  - `name`: `string (optional)`
  - `description`: `string (optional)`
  - ... (and other fields from `CreateProductDto`)
  - `images`: `File[] (optional, up to 5 files)`

### Delete Product
- **Endpoint**: `DELETE /products/:id`
- **Permission**: `ADMIN`
- **Description**: Deletes a product (soft delete).
- **Path Parameters**:
  - `id`: `number` (required) - The ID of the product to delete.

### Add Product Image
- **Endpoint**: `POST /products/:id/images`
- **Permission**: `ADMIN`
- **Description**: Adds an image URL to a product.
- **Path Parameters**:
  - `id`: `number` (required)
- **Request Body**: `ProductImageDto`
  ```json
  {
    "imageUrl": "string (required, URL)"
  }
  ```

### Remove Product Image
- **Endpoint**: `DELETE /products/:id/images`
- **Permission**: `ADMIN`
- **Description**: Removes an image URL from a product.
- **Path Parameters**:
  - `id`: `number` (required)
- **Request Body**: `ProductImageDto`
  ```json
  {
    "imageUrl": "string (required, URL)"
  }
  ```

### Get Products with Reviews
- **Endpoint**: `GET /products/with-reviews`
- **Permission**: Authenticated User
- **Description**: Retrieves products along with their review statistics.
- **Query Parameters**: `ProductQueryParams` (same as `GET /products`)

### Manage Product Categories
- **Endpoint**: `POST /products/:id/categories`
- **Permission**: `ADMIN`, `SUPER_ADMIN`
- **Description**: Adds a product to one or more categories.
- **Request Body**: `{ "categoryIds": [1, 2] }`

- **Endpoint**: `DELETE /products/:id/categories`
- **Permission**: `ADMIN`, `SUPER_ADMIN`
- **Description**: Removes a product from one or more categories.
- **Request Body**: `{ "categoryIds": [1, 2] }`

- **Endpoint**: `PUT /products/:id/categories`
- **Permission**: `ADMIN`, `SUPER_ADMIN`
- **Description**: Sets the categories for a product, replacing existing ones.
- **Request Body**: `{ "categoryIds": [1, 2] }`

---

## Public Products API

Base Path: `/public/products`

This controller provides public access to product information without authentication.

### Get All Public Products
- **Endpoint**: `GET /public/products`
- **Permission**: `Public`
- **Description**: Retrieves a paginated list of active products.
- **Query Parameters**:
  - `page`, `limit`, `category`, `search`, `minPrice`, `maxPrice`, `sortBy`, `sortDir`

### Get Public Product by ID
- **Endpoint**: `GET /public/products/:id`
- **Permission**: `Public`
- **Description**: Retrieves a single product by its ID.
- **Path Parameters**:
  - `id`: `number` (required)

*(Other endpoints like `/weekly-specials`, `/featured`, `/discounted` are also available under `/public/products` and behave the same as their counterparts in the main `/products` controller.)*

---

## Product Requests API

Base Path: `/product-requests`

### Create Product Request
- **Endpoint**: `POST /product-requests`
- **Permission**: `AGENT`, `SELLER`
- **Description**: Allows agents and sellers to submit a request for a new product.
- **Request Body**: `multipart/form-data` with fields from `CreateProductRequestDto`
  - `name`: `string (required)`
  - `description`: `string (optional)`
  - `category`: `string (required)`
  - `price`: `number (required)`
  - `stockQuantity`: `number (required)`
  - `location`: `string (optional)`
  - `condition`: `string (optional)`
  - `farmerId`: `number (optional, for Agents)`
  - `images`: `File[] (optional, up to 5)`

### Get My Product Requests
- **Endpoint**: `GET /product-requests/my-requests`
- **Permission**: `AGENT`, `SELLER`
- **Description**: Retrieves product requests submitted by the current user.
- **Query Parameters**:
  - `page`, `limit`, `status`, `category`, `location`, `condition`

### Get All Product Requests (Admin)
- **Endpoint**: `GET /product-requests`
- **Permission**: `ADMIN`, `SUPER_ADMIN`
- **Description**: Retrieves all product requests with filtering options.
- **Query Parameters**: `ProductRequestQueryParams`
  - `page`, `limit`, `status`, `category`, `location`, `condition`, `requestType`, `agentId`, `sellerId`, `farmerId`, `startDate`, `endDate`

### Get Product Request by ID
- **Endpoint**: `GET /product-requests/:id`
- **Permission**: `ADMIN`, `SUPER_ADMIN`, `AGENT` (owner), `SELLER` (owner)
- **Description**: Retrieves a single product request by ID.
- **Path Parameters**:
  - `id`: `number` (required)

### Update Product Request
- **Endpoint**: `PATCH /product-requests/:id`
- **Permission**: `AGENT` (owner), `SELLER` (owner)
- **Description**: Updates a pending product request.
- **Path Parameters**:
  - `id`: `number` (required)
- **Request Body**: `multipart/form-data` with fields from `UpdateProductRequestDto`
  - `name`, `description`, `category`, `price`, etc.
  - `images`: `File[] (optional, up to 5)`

### Delete Product Request
- **Endpoint**: `DELETE /product-requests/:id`
- **Permission**: `AGENT` (owner), `SELLER` (owner)
- **Description**: Deletes a pending product request.
- **Path Parameters**:
  - `id`: `number` (required)

### Update Product Request Status (Admin)
- **Endpoint**: `PATCH /product-requests/:id/status`
- **Permission**: `ADMIN`, `SUPER_ADMIN`
- **Description**: Approves or rejects a product request.
- **Path Parameters**:
  - `id`: `number` (required)
- **Request Body**: `UpdateProductRequestStatusDto`
  ```json
  {
    "status": "string (required, enum: 'APPROVED', 'REJECTED')",
    "rejectionReason": "string (optional, required if status is 'REJECTED')",
    "completionNotes": "string (optional)"
  }
  ```

### Manage Product Request Images
- **Endpoint**: `POST /product-requests/:id/images`
- **Permission**: `AGENT` (owner), `SELLER` (owner)
- **Description**: Adds images to a product request.
- **Files**: `images` (up to 5)

- **Endpoint**: `DELETE /product-requests/:id/images`
- **Permission**: `AGENT` (owner), `SELLER` (owner)
- **Description**: Removes an image from a product request.
- **Request Body**: `{ "imageUrl": "string" }`

---

## Reviews API

Base Path: `/reviews`

### Create Review
- **Endpoint**: `POST /reviews`
- **Permission**: Authenticated User (Customer)
- **Description**: Creates a review for a product.
- **Request Body**: `CreateReviewDto`
  ```json
  {
    "productId": "number (required)",
    "rating": "number (required, 1-5)",
    "comment": "string (optional)",
    "orderId": "number (optional, for verification)"
  }
  ```

### Get All Reviews
- **Endpoint**: `GET /reviews`
- **Permission**: Authenticated User
- **Description**: Retrieves a list of reviews with filtering.
- **Query Parameters**: `ReviewQueryParams`
  - `productId`, `customerId`, `rating`, `isVerified`, `page`, `limit`, `sortBy`, `sortDir`

### Get Review by ID
- **Endpoint**: `GET /reviews/:id`
- **Permission**: Authenticated User
- **Description**: Retrieves a single review by ID.
- **Path Parameters**:
  - `id`: `number` (required)

### Update Review
- **Endpoint**: `PUT /reviews/:id`
- **Permission**: Authenticated User (Owner)
- **Description**: Updates a review written by the current user.
- **Path Parameters**:
  - `id`: `number` (required)
- **Request Body**: `UpdateReviewDto`
  ```json
  {
    "rating": "number (optional, 1-5)",
    "comment": "string (optional)"
  }
  ```

### Delete Review
- **Endpoint**: `DELETE /reviews/:id`
- **Permission**: Authenticated User (Owner)
- **Description**: Deletes a review written by the current user.
- **Path Parameters**:
  - `id`: `number` (required)

### Get Reviews for a Product
- **Endpoint**: `GET /reviews/product/:productId`
- **Permission**: Authenticated User
- **Description**: Retrieves all reviews for a specific product.
- **Path Parameters**:
  - `productId`: `number` (required)
- **Query Parameters**:
  - `page`, `limit`, `sortBy`, `sortDir`

### Get Review Statistics for a Product
- **Endpoint**: `GET /reviews/product/:productId/stats`
- **Permission**: Authenticated User
- **Description**: Retrieves review statistics (average rating, count) for a product.
- **Path Parameters**:
  - `productId`: `number` (required)

### Get My Reviews
- **Endpoint**: `GET /reviews/customer/my-reviews`
- **Permission**: Authenticated User (Customer)
- **Description**: Retrieves all reviews written by the current user.
- **Query Parameters**:
  - `page`, `limit`

---

## Inventory API

Base Path: `/inventory`

### Create Inventory Record
- **Endpoint**: `POST /inventory`
- **Permission**: `ADMIN`, `MANAGER`
- **Description**: Creates an inventory record for a product in a warehouse.
- **Request Body**: `CreateInventoryDto`
  ```json
  {
    "productId": "number (required)",
    "warehouseId": "number (required)",
    "quantity": "number (required)",
    "batchNumber": "string (optional)",
    "expiryDate": "Date (optional, ISO format)"
  }
  ```

### Get Inventory Record by ID
- **Endpoint**: `GET /inventory/:id`
- **Permission**: `ADMIN`, `MANAGER`, `AGENT`
- **Description**: Retrieves a single inventory record.
- **Path Parameters**:
  - `id`: `number` (required)

### Get Inventory by Product ID
- **Endpoint**: `GET /inventory/product/:productId`
- **Permission**: `ADMIN`, `MANAGER`, `AGENT`
- **Description**: Retrieves all inventory records for a specific product across all warehouses.
- **Path Parameters**:
  - `productId`: `number` (required)

### Update Inventory Record
- **Endpoint**: `PUT /inventory/:id`
- **Permission**: `ADMIN`, `MANAGER`
- **Description**: Updates an inventory record.
- **Path Parameters**:
  - `id`: `number` (required)
- **Request Body**: `UpdateInventoryDto`
  ```json
  {
    "quantity": "number (optional)",
    "batchNumber": "string (optional)",
    "expiryDate": "Date (optional, ISO format)"
  }
  ```

### Transfer Inventory
- **Endpoint**: `POST /inventory/:id/transfer`
- **Permission**: `ADMIN`, `MANAGER`
- **Description**: Transfers a quantity of a product from one warehouse to another.
- **Path Parameters**:
  - `id`: `number` (required) - The ID of the source inventory record.
- **Request Body**: `TransferInventoryDto`
  ```json
  {
    "sourceWarehouseId": "number (required)",
    "destinationWarehouseId": "number (required)",
    "quantity": "number (required)",
    "reason": "string (optional)"
  }
  ```

### Adjust Inventory
- **Endpoint**: `POST /inventory/:id/adjust`
- **Permission**: `ADMIN`, `MANAGER`
- **Description**: Adjusts the quantity of an inventory record (e.g., for stock takes, damages).
- **Path Parameters**:
  - `id`: `number` (required)
- **Request Body**: `AdjustInventoryDto`
  ```json
  {
    "adjustmentType": "string (required, enum: 'INCREASE', 'DECREASE')",
    "quantity": "number (required)",
    "reason": "string (required)"
  }
  ```

### Get Inventory Movements
- **Endpoint**: `GET /inventory/movements`
- **Permission**: `ADMIN`, `MANAGER`
- **Description**: Retrieves a log of all inventory movements (adjustments, transfers).
- **Query Parameters**: `InventoryQueryParams`
  - `page`, `limit`, `productId`, `warehouseId`, `startDate`, `endDate`

### Get Inventory Summary
- **Endpoint**: `GET /inventory/summary`
- **Permission**: `ADMIN`, `MANAGER`
- **Description**: Retrieves a summary of inventory data.
- **Query Parameters**: `InventorySummaryParams`
  - `category`: `string` (optional)
  - `groupBy`: `string` (optional, enum: 'warehouse', 'product', 'category', 'none')

### Get Low Stock Items
- **Endpoint**: `GET /inventory/analytics/low-stock`
- **Permission**: `ADMIN`, `MANAGER`
- **Description**: Retrieves items that are below a certain stock threshold.
- **Query Parameters**: `LowStockParams`
  - `threshold`: `number` (optional, default: 20)
  - `warehouseId`: `number` (optional)

### Get Expiring Items
- **Endpoint**: `GET /inventory/analytics/expiring`
- **Permission**: `ADMIN`, `MANAGER`
- **Description**: Retrieves items that are expiring soon.
- **Query Parameters**: `ExpiringItemsParams`
  - `days`: `number` (optional, default: 30)
  - `warehouseId`: `number` (optional)

---

## Warehouses API

Base Path: `/warehouses`

### Create Warehouse
- **Endpoint**: `POST /warehouses`
- **Permission**: `ADMIN`, `MANAGER`
- **Description**: Creates a new warehouse.
- **Request Body**: `CreateWarehouseDto`
  ```json
  {
    "name": "string (required)",
    "address": "string (required)",
    "region": "string (required)",
    "district": "string (optional)",
    "managerId": "number (optional)"
  }
  ```

### Get All Warehouses
- **Endpoint**: `GET /warehouses`
- **Permission**: `Public`
- **Description**: Retrieves a list of all warehouses.
- **Query Parameters**: `WarehouseQueryParams`
  - `page`, `limit`, `region`, `search`

### Get Warehouse by ID
- **Endpoint**: `GET /warehouses/:id`
- **Permission**: `ADMIN`, `MANAGER`, `AGENT`
- **Description**: Retrieves a single warehouse by ID.
- **Path Parameters**:
  - `id`: `number` (required)

### Update Warehouse
- **Endpoint**: `PUT /warehouses/:id`
- **Permission**: `ADMIN`, `MANAGER`
- **Description**: Updates an existing warehouse.
- **Path Parameters**:
  - `id`: `number` (required)
- **Request Body**: `UpdateWarehouseDto`
  ```json
  {
    "name": "string (optional)",
    "address": "string (optional)",
    "region": "string (optional)",
    "district": "string (optional)",
    "managerId": "number (optional)"
  }
  ```

### Delete Warehouse
- **Endpoint**: `DELETE /warehouses/:id`
- **Permission**: `ADMIN`
- **Description**: Deletes a warehouse. Fails if the warehouse has inventory.
- **Path Parameters**:
  - `id`: `number` (required)

### Get Warehouse Inventory
- **Endpoint**: `GET /warehouses/:id/inventory`
- **Permission**: `ADMIN`, `MANAGER`, `AGENT`
- **Description**: Retrieves all inventory for a specific warehouse.
- **Path Parameters**:
  - `id`: `number` (required)

### Get Warehouse Statistics
- **Endpoint**: `GET /warehouses/analytics/stats`
- **Permission**: `ADMIN`, `MANAGER`
- **Description**: Retrieves statistics about warehouses (e.g., total inventory value per warehouse).
