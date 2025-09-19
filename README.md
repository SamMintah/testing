# API Testing Guide - New Implementations

This guide covers all the new endpoints and features implemented for product location/condition filtering and seller inventory tracking.

## 🔐 Authentication

All protected endpoints require JWT token in the Authorization header:
```
Authorization: Bearer <your_jwt_token>
```

## 📦 Product Endpoints with Location & Condition Filtering

### 1. Get Products with Filters
**Endpoint:** `GET /products`
**Access:** Public

**New Query Parameters:**
- `location` - Filter by product location (e.g., "Accra", "Kumasi")
- `condition` - Filter by condition: `FRESH`, `DRIED`, `PROCESSED`, `ORGANIC`

**Example Requests:**
```bash
# Get all products
GET /products

# Get fresh products
GET /products?condition=FRESH

# Get products from Accra
GET /products?location=Accra

# Get dried products from Tamale
GET /products?condition=DRIED&location=Tamale

# Get products with pagination and filters
GET /products?page=1&limit=10&condition=ORGANIC&location=Kumasi

# Combine with existing filters
GET /products?condition=FRESH&minPrice=10&maxPrice=50&search=tomato
```

### 2. Create Product with Location & Condition
**Endpoint:** `POST /products`
**Access:** Authenticated users
**Content-Type:** `multipart/form-data`

**New Fields in Request Body:**
- `location` (optional) - Product location
- `condition` (optional) - Product condition (default: "FRESH")

**Example Request:**
```bash
POST /products
Content-Type: multipart/form-data

{
  "name": "Fresh Tomatoes",
  "description": "Locally grown fresh tomatoes",
  "primaryCategoryId": 2,
  "price": 15.50,
  "stockQuantity": 100,
  "location": "Kumasi, Ashanti",
  "condition": "FRESH",
  "currency": "GHS"
}
```

## 🏪 Seller Inventory Tracking Endpoints

## 📝 Product Requests (Unified Agent & Seller Submissions)

### 1. Create Product Request
**Endpoint:** `POST /product-requests`
**Access:** Authenticated agents and sellers
**Content-Type:** `multipart/form-data`

**Request Body:**
```json
{
  "name": "Fresh Organic Tomatoes",
  "description": "Locally grown organic tomatoes",
  "category": "Vegetables",
  "price": 25.00,
  "currency": "GHS",
  "stockQuantity": 100,
  "location": "Kumasi, Ashanti",
  "condition": "FRESH"
}
```

**Example Requests:**
```bash
# Agent creating a product request
POST /product-requests
Authorization: Bearer <agent_jwt_token>
Content-Type: multipart/form-data

{
  "name": "Premium Rice",
  "category": "Grains",
  "price": 45.00,
  "stockQuantity": 200,
  "location": "Tamale, Northern",
  "condition": "DRIED",
  "farmerId": 123
}

# Seller creating a product request
POST /product-requests
Authorization: Bearer <seller_jwt_token>
Content-Type: multipart/form-data

{
  "name": "Organic Honey",
  "category": "Processed Foods",
  "price": 35.00,
  "stockQuantity": 50,
  "location": "Ho, Volta",
  "condition": "ORGANIC"
}
```

### 2. Get My Product Requests
**Endpoint:** `GET /product-requests/my-requests`
**Access:** Authenticated agents and sellers

**Query Parameters:**
- `page` (optional) - Page number
- `limit` (optional) - Items per page
- `status` (optional) - Filter by status: `PENDING`, `APPROVED`, `REJECTED`, `IN_REVIEW`, `COMPLETED`
- `category` (optional) - Filter by category
- `location` (optional) - Filter by location
- `condition` (optional) - Filter by condition

**Example Requests:**
```bash
# Get all my requests
GET /product-requests/my-requests

# Get pending requests only
GET /product-requests/my-requests?status=PENDING

# Get requests with filters
GET /product-requests/my-requests?condition=ORGANIC&location=Kumasi&page=1&limit=10
```

### 3. Get All Product Requests (Admin)
**Endpoint:** `GET /product-requests`
**Access:** Admin only

**Query Parameters:**
- `page`, `limit` - Pagination
- `status` - Filter by status
- `category`, `location`, `condition` - Filter by product attributes
- `requestType` - Filter by requester type: `AGENT` or `SELLER`
- `agentId`, `sellerId`, `farmerId` - Filter by specific requester
- `startDate`, `endDate` - Date range filtering

**Example Requests:**
```bash
# Get all product requests
GET /product-requests

# Get pending requests from agents
GET /product-requests?status=PENDING&requestType=AGENT

# Get organic products from sellers
GET /product-requests?condition=ORGANIC&requestType=SELLER

# Get requests from specific location
GET /product-requests?location=Accra&page=1&limit=20
```

### 4. Update Product Request Status (Admin)
**Endpoint:** `PATCH /product-requests/:id/status`
**Access:** Admin only

**Request Body:**
```json
{
  "status": "APPROVED",
  "completionNotes": "Product approved and listed on marketplace"
}
```

**Example Requests:**
```bash
# Approve a request
PATCH /product-requests/123/status
{
  "status": "APPROVED",
  "completionNotes": "Excellent product quality"
}

# Reject a request
PATCH /product-requests/123/status
{
  "status": "REJECTED",
  "rejectionReason": "Insufficient product information"
}
```

## 🏪 Seller Inventory Tracking Endpoints

### 1. Get My Inventory
**Endpoint:** `GET /sellers/me/inventory`
**Access:** Authenticated sellers only

**Query Parameters:**
- `page` (optional) - Page number (default: 1)
- `limit` (optional) - Items per page (default: 20)
- `location` (optional) - Filter by location
- `condition` (optional) - Filter by condition
- `lowStock` (optional) - Show only low stock items (true/false)

**Example Requests:**
```bash
# Get all inventory
GET /sellers/me/inventory

# Get inventory with pagination
GET /sellers/me/inventory?page=1&limit=10

# Get low stock items only
GET /sellers/me/inventory?lowStock=true

# Get fresh products in my inventory
GET /sellers/me/inventory?condition=FRESH

# Get products from specific location
GET /sellers/me/inventory?location=Accra

# Combined filters
GET /sellers/me/inventory?condition=ORGANIC&lowStock=true&page=1&limit=5
```

**Response Example:**
```json
{
  "data": {
    "items": [
      {
        "id": 1,
        "name": "Premium Jasmine Rice",
        "description": "High-quality fragrant jasmine rice",
        "category": "Grains",
        "price": 45.00,
        "stockQuantity": 500,
        "location": "Accra, Greater Accra",
        "condition": "DRIED",
        "stockStatus": "IN_STOCK",
        "imagesUrls": ["https://..."],
        "isActive": true,
        "createdAt": "2024-01-15T10:00:00Z",
        "updatedAt": "2024-01-15T10:00:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 50,
      "totalPages": 3
    }
  }
}
```### 2
. Get My Sales History
**Endpoint:** `GET /sellers/me/sales`
**Access:** Authenticated sellers only

**Query Parameters:**
- `page` (optional) - Page number (default: 1)
- `limit` (optional) - Items per page (default: 20)
- `startDate` (optional) - Start date filter (ISO format: 2024-01-01)
- `endDate` (optional) - End date filter (ISO format: 2024-01-31)
- `status` (optional) - Order status filter

**Example Requests:**
```bash
# Get all sales
GET /sellers/me/sales

# Get sales with pagination
GET /sellers/me/sales?page=1&limit=10

# Get sales for January 2024
GET /sellers/me/sales?startDate=2024-01-01&endDate=2024-01-31

# Get delivered orders only
GET /sellers/me/sales?status=DELIVERED

# Get recent sales (last 7 days)
GET /sellers/me/sales?startDate=2024-01-15&endDate=2024-01-22

# Combined filters
GET /sellers/me/sales?status=DELIVERED&startDate=2024-01-01&page=1&limit=5
```

**Response Example:**
```json
{
  "data": {
    "sales": [
      {
        "id": 123,
        "orderNumber": "ORD-2024-001",
        "status": "DELIVERED",
        "totalAmount": 150.00,
        "currency": "GHS",
        "customer": {
          "name": "John Doe",
          "email": "john@example.com"
        },
        "items": [
          {
            "productId": 1,
            "productName": "Fresh Tomatoes",
            "quantity": 5,
            "unitPrice": 15.00,
            "totalPrice": 75.00,
            "productImage": "https://..."
          }
        ],
        "createdAt": "2024-01-15T10:00:00Z",
        "updatedAt": "2024-01-15T12:00:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 25,
      "totalPages": 2
    }
  }
}
```

### 3. Get My Analytics
**Endpoint:** `GET /sellers/me/analytics`
**Access:** Authenticated sellers only

**Query Parameters:**
- `period` (optional) - Analysis period: `week`, `month`, `quarter`, `year` (default: month)

**Example Requests:**
```bash
# Get monthly analytics (default)
GET /sellers/me/analytics

# Get weekly analytics
GET /sellers/me/analytics?period=week

# Get quarterly analytics
GET /sellers/me/analytics?period=quarter

# Get yearly analytics
GET /sellers/me/analytics?period=year
```

**Response Example:**
```json
{
  "data": {
    "period": "month",
    "summary": {
      "totalProducts": 25,
      "lowStockProducts": 3,
      "totalOrders": 45,
      "totalRevenue": 2500.00
    },
    "topProducts": [
      {
        "id": 1,
        "name": "Fresh Tomatoes",
        "imagesUrls": ["https://..."],
        "totalSold": 150,
        "totalRevenue": 750.00
      }
    ],
    "recentOrders": [
      {
        "id": 123,
        "orderNumber": "ORD-2024-001",
        "status": "DELIVERED",
        "totalAmount": 150.00,
        "customerName": "John Doe",
        "createdAt": "2024-01-15T10:00:00Z"
      }
    ]
  }
}
```

## 🧪 Testing Scenarios

### Scenario 1: Product Filtering by Location and Condition
```bash
# Test 1: Get all fresh products
curl -X GET "http://localhost:3000/products?condition=FRESH"

# Test 2: Get products from Accra
curl -X GET "http://localhost:3000/products?location=Accra"

# Test 3: Get dried products from Tamale with pagination
curl -X GET "http://localhost:3000/products?condition=DRIED&location=Tamale&page=1&limit=5"

# Test 4: Combine with price filtering
curl -X GET "http://localhost:3000/products?condition=ORGANIC&minPrice=20&maxPrice=100"

# Test 5: Empty filter parameters (should return all products)
curl -X GET "http://localhost:3000/products?location=&condition=&page=1&limit=10"

# Test 6: Mix of empty and valid parameters
curl -X GET "http://localhost:3000/products?location=&condition=FRESH&page=1&limit=10"
```

### Scenario 2: Seller Inventory Management
```bash
# Test 1: Login as seller first
curl -X POST "http://localhost:3000/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email": "seller@example.com", "password": "password123"}'

# Test 2: Get inventory (use token from login)
curl -X GET "http://localhost:3000/sellers/me/inventory" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"

# Test 3: Get low stock items
curl -X GET "http://localhost:3000/sellers/me/inventory?lowStock=true" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"

# Test 4: Get sales history
curl -X GET "http://localhost:3000/sellers/me/sales?startDate=2024-01-01" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"

# Test 5: Get analytics
curl -X GET "http://localhost:3000/sellers/me/analytics?period=month" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

### Scenario 3: Create Product with New Fields
```bash
# Test: Create product with location and condition
curl -X POST "http://localhost:3000/products" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Organic Carrots",
    "description": "Fresh organic carrots from local farm",
    "primaryCategoryId": 2,
    "price": 25.00,
    "stockQuantity": 50,
    "location": "Ho, Volta",
    "condition": "ORGANIC",
    "currency": "GHS"
  }'
```

## 📊 Expected Data from Seeded Database

After running the seed, you should have products with these conditions and locations:

**Locations:**
- Accra, Greater Accra
- Kumasi, Ashanti  
- Tamale, Northern
- Cape Coast, Central
- Ho, Volta
- Sekondi-Takoradi, Western
- Bolgatanga, Upper East
- Wa, Upper West
- Koforidua, Eastern
- Elmina, Central
- Sunyani, Brong Ahafo
- Techiman, Brong Ahafo

**Conditions:**
- FRESH (vegetables, fruits)
- DRIED (rice, fish, legumes)
- PROCESSED (flour, oils, smoked items)
- ORGANIC (honey, some vegetables)

## 🔍 Testing Tips

1. **Authentication**: Always test with proper JWT tokens
2. **Pagination**: Test with different page sizes and page numbers
3. **Filtering**: Test individual filters and combinations
4. **Edge Cases**: Test with invalid parameters, empty results
5. **Data Validation**: Test with invalid condition values or malformed dates

## 🚨 Common Issues to Test

1. **Invalid condition values** - Should return validation error
2. **Invalid date formats** - Should return proper error message
3. **Unauthorized access** - Should return 401 for protected endpoints
4. **Non-existent seller** - Should return 404 for seller-specific endpoints
5. **Empty results** - Should return empty array with proper pagination info
6. **Empty filter parameters** - Should be ignored and return all results
7. **Whitespace-only parameters** - Should be treated as empty and ignored

## 📝 Notes

- All monetary values are in GHS (Ghana Cedis)
- Dates should be in ISO format (YYYY-MM-DD or full ISO string)
- Stock status is automatically calculated: IN_STOCK (≥10), LOW_STOCK (<10), OUT_OF_STOCK (0)
- Location filtering uses case-insensitive partial matching
- Condition filtering requires exact match (case-sensitive)
- **Empty filter parameters** (e.g., `?location=&condition=`) are automatically ignored
- **Whitespace-only parameters** are treated as empty and ignored
- Only meaningful filter values are applied to the query
