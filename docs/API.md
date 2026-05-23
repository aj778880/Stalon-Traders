# Backend API Documentation

## Overview

Stalon Traders Backend is a RESTful API built with Express.js, providing comprehensive trading, portfolio, and market data management capabilities.

## Base URL

```
http://localhost:3000/api
```

## Authentication

All protected endpoints require a JWT token in the Authorization header:

```
Authorization: Bearer <token>
```

## Response Format

All responses follow a standard JSON format:

### Success Response
```json
{
  "success": true,
  "data": { /* response data */ },
  "message": "Operation successful"
}
```

### Error Response
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Error description"
  }
}
```

---

## Authentication Endpoints

### Register User
```http
POST /auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "securePassword123",
  "name": "John Doe"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "email": "user@example.com",
    "name": "John Doe",
    "token": "jwt_token_here"
  }
}
```

---

### Login
```http
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "email": "user@example.com",
    "token": "jwt_token_here",
    "expiresIn": 3600
  }
}
```

---

### Refresh Token
```http
POST /auth/refresh
Content-Type: application/json

{
  "refreshToken": "refresh_token_here"
}
```

---

## User Endpoints

### Get User Profile
```http
GET /user/profile
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "email": "user@example.com",
    "name": "John Doe",
    "country": "US",
    "language": "en",
    "created_at": "2023-01-15T10:30:00Z"
  }
}
```

---

### Update User Profile
```http
PUT /user/profile
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "John Doe Updated",
  "country": "UK",
  "language": "en"
}
```

---

### Change Password
```http
POST /user/change-password
Authorization: Bearer <token>
Content-Type: application/json

{
  "currentPassword": "oldPassword123",
  "newPassword": "newPassword123"
}
```

---

## Trading Endpoints

### Get All Trades
```http
GET /trades
Authorization: Bearer <token>

Query Parameters:
  - page: 1 (default)
  - limit: 20 (default)
  - status: pending|completed|cancelled
  - symbol: BTC
```

**Response:**
```json
{
  "success": true,
  "data": {
    "trades": [
      {
        "id": "uuid",
        "symbol": "BTC",
        "type": "buy",
        "quantity": 0.5,
        "price": 45000,
        "total": 22500,
        "status": "completed",
        "created_at": "2023-01-15T10:30:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 150
    }
  }
}
```

---

### Create Trade
```http
POST /trades
Authorization: Bearer <token>
Content-Type: application/json

{
  "symbol": "BTC",
  "type": "buy",
  "quantity": 0.5,
  "price": 45000
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "symbol": "BTC",
    "type": "buy",
    "quantity": 0.5,
    "price": 45000,
    "total": 22500,
    "status": "pending",
    "created_at": "2023-01-15T10:30:00Z"
  }
}
```

---

### Get Trade Details
```http
GET /trades/:id
Authorization: Bearer <token>
```

---

### Update Trade
```http
PUT /trades/:id
Authorization: Bearer <token>
Content-Type: application/json

{
  "quantity": 1,
  "price": 45500
}
```

---

### Cancel Trade
```http
DELETE /trades/:id
Authorization: Bearer <token>
```

---

## Portfolio Endpoints

### Get Portfolio Overview
```http
GET /portfolio
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "total_value": 100000,
    "cash_available": 25000,
    "invested": 75000,
    "performance": {
      "day_change": 1500,
      "day_change_percent": 1.5,
      "week_change": 3000,
      "week_change_percent": 3.0
    }
  }
}
```

---

### Get Portfolio Positions
```http
GET /portfolio/positions
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "data": {
    "positions": [
      {
        "symbol": "BTC",
        "quantity": 0.5,
        "entry_price": 45000,
        "current_price": 47000,
        "value": 23500,
        "gain_loss": 1000,
        "gain_loss_percent": 4.5,
        "purchase_date": "2023-01-15"
      }
    ]
  }
}
```

---

### Get Portfolio Performance
```http
GET /portfolio/performance
Authorization: Bearer <token>

Query Parameters:
  - period: day|week|month|year|all (default: all)
```

---

## Market Data Endpoints

### Get All Markets
```http
GET /markets
Authorization: Bearer <token>

Query Parameters:
  - page: 1 (default)
  - limit: 50 (default)
  - type: stock|crypto|commodity
  - sort_by: name|price|change_24h
```

**Response:**
```json
{
  "success": true,
  "data": {
    "markets": [
      {
        "id": "uuid",
        "symbol": "BTC",
        "name": "Bitcoin",
        "price": 47000,
        "change_24h": 2.5,
        "volume": 28500000000,
        "market_cap": 950000000000
      }
    ]
  }
}
```

---

### Get Market Details
```http
GET /markets/:symbol
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "data": {
    "symbol": "BTC",
    "name": "Bitcoin",
    "price": 47000,
    "high_24h": 48000,
    "low_24h": 46000,
    "change_24h": 2.5,
    "volume": 28500000000,
    "market_cap": 950000000000,
    "description": "Bitcoin is a cryptocurrency..."
  }
}
```

---

### Get Price History
```http
GET /markets/:symbol/history
Authorization: Bearer <token>

Query Parameters:
  - period: 1h|1d|1w|1m|3m|6m|1y|all (default: 1d)
  - format: json|csv
```

**Response:**
```json
{
  "success": true,
  "data": {
    "symbol": "BTC",
    "period": "1d",
    "data": [
      {
        "timestamp": "2023-01-15T00:00:00Z",
        "open": 46500,
        "high": 48000,
        "low": 46000,
        "close": 47000,
        "volume": 28500000000
      }
    ]
  }
}
```

---

## WebSocket Real-time Events

### Connect
```javascript
const socket = io('http://localhost:3000', {
  auth: {
    token: 'jwt_token_here'
  }
});
```

### Events

#### Price Update
```javascript
socket.on('price:update', (data) => {
  console.log(data);
  // {
  //   symbol: 'BTC',
  //   price: 47000,
  //   timestamp: '2023-01-15T10:30:00Z'
  // }
});
```

#### Trade Execution
```javascript
socket.on('trade:executed', (data) => {
  console.log(data);
  // {
  //   trade_id: 'uuid',
  //   symbol: 'BTC',
  //   status: 'completed',
  //   executed_price: 47000
  // }
});
```

#### Portfolio Update
```javascript
socket.on('portfolio:update', (data) => {
  console.log(data);
  // {
  //   total_value: 100000,
  //   day_change: 1500
  // }
});
```

---

## Error Codes

| Code | HTTP | Description |
|------|------|-------------|
| `AUTH_INVALID` | 401 | Invalid credentials |
| `AUTH_EXPIRED` | 401 | Token expired |
| `AUTH_REQUIRED` | 401 | Authentication required |
| `FORBIDDEN` | 403 | Access denied |
| `NOT_FOUND` | 404 | Resource not found |
| `VALIDATION_ERROR` | 422 | Validation failed |
| `INSUFFICIENT_FUNDS` | 400 | Not enough funds |
| `MARKET_CLOSED` | 400 | Market is closed |
| `INTERNAL_ERROR` | 500 | Server error |

---

## Rate Limiting

- **Public endpoints**: 100 requests per hour
- **Protected endpoints**: 1000 requests per hour
- **Trading endpoints**: 100 requests per hour

---

## Pagination

Most list endpoints support pagination:

```
GET /trades?page=2&limit=50
```

Response includes:
```json
{
  "pagination": {
    "page": 2,
    "limit": 50,
    "total": 250,
    "total_pages": 5
  }
}
```

---

For SDK documentation and code examples, see the respective language guides.
