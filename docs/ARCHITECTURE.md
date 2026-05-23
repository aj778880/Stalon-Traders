# Stalon Traders - System Architecture

## Overview

Stalon Traders is a modern, scalable trading platform built with a microservices-ready architecture. The system is divided into frontend, backend, and data layers.

## Architecture Diagram

```
┌─────────────────────────────────────────────────────┐
│                   Client Layer                      │
│  ┌──────────────┐  ┌──────────────┐               │
│  │  Web Browser │  │  Mobile App  │               │
│  └──────┬───────┘  └──────┬───────┘               │
└─────────┼──────────────────┼─────────────────────────┘
          │                  │
          └──────────┬───────┘
                     │ HTTPS
┌────────────────────┼──────────────────────────────┐
│              API Gateway Layer                     │
│  ┌──────────────────────────────────────────┐    │
│  │  Load Balancer / Reverse Proxy           │    │
│  │  (nginx, HAProxy)                        │    │
│  └──────────────────────────────────────────┘    │
└────────────────────┼──────────────────────────────┘
                     │
         ┌───────────┼───────────┐
         │           │           │
┌────────▼──┐  ┌──────▼───┐  ┌──▼───────┐
│ Auth      │  │ Trading  │  │ Portfolio│
│ Service   │  │ Service  │  │ Service  │
└────┬──────┘  └──────┬───┘  └──┬───────┘
     │                │         │
└─────┬────────────────┼─────────┘
      │ Database       │
      │ Queries        │
┌─────▼────────────────▼────────────────┐
│      Database Layer                   │
│  ┌──────────────────────────────┐    │
│  │  PostgreSQL                  │    │
│  │  - Users                     │    │
│  │  - Trades                    │    │
│  │  - Portfolio                 │    │
│  │  - Market Data               │    │
│  └──────────────────────────────┘    │
│  ┌──────────────────────────────┐    │
│  │  Redis (Cache)               │    │
│  │  - Sessions                  │    │
│  │  - Market Prices             │    │
│  │  - Frequently Accessed Data  │    │
│  └──────────────────────────────┘    │
└────────────────────────────────────────┘
```

## Frontend Architecture

### Technology Stack
- **Framework**: React 18
- **State Management**: Redux Toolkit
- **UI Library**: Material-UI (MUI)
- **Charting**: Chart.js
- **Build Tool**: Vite
- **Package Manager**: npm

### Directory Structure

```
frontend/
├── src/
│   ├── components/        # Reusable UI components
│   │   ├── Navbar.jsx
│   │   ├── Card.jsx
│   │   └── ...
│   ├── pages/             # Page components
│   │   ├── Dashboard.jsx
│   │   ├── Portfolio.jsx
│   │   ├── Markets.jsx
│   │   └── ...
│   ├── store/             # Redux store
│   │   ├── index.js
│   │   └── slices/
│   │       ├── authSlice.js
│   │       ├── portfolioSlice.js
│   │       ├── marketsSlice.js
│   │       └── tradesSlice.js
│   ├── services/          # API calls
│   │   ├── api.js
│   │   ├── authService.js
│   │   ├── tradingService.js
│   │   └── ...
│   ├── hooks/             # Custom React hooks
│   ├── utils/             # Utility functions
│   ├── i18n/              # Internationalization
│   ├── App.jsx
│   └── main.jsx
├── public/                # Static assets
├── package.json
└── vite.config.js
```

### Data Flow

```
User Input
    │
    ▼
React Component
    │
    ▼
Redux Action
    │
    ▼
Redux Reducer
    │
    ▼
State Update
    │
    ▼
Component Re-render
    │
    ▼
Display to User
```

## Backend Architecture

### Technology Stack
- **Runtime**: Node.js
- **Framework**: Express.js
- **Database**: PostgreSQL
- **ORM**: Sequelize / TypeORM
- **Authentication**: JWT + Passport.js
- **Real-time**: Socket.IO
- **Caching**: Redis
- **API Docs**: Swagger/OpenAPI

### Directory Structure

```
backend/
├── src/
│   ├── routes/            # API route handlers
│   │   ├── auth.js
│   │   ├── trades.js
│   │   ├── portfolio.js
│   │   └── markets.js
│   ├── controllers/       # Business logic
│   │   ├── authController.js
│   │   ├── tradingController.js
│   │   ├── portfolioController.js
│   │   └── ...
│   ├── models/            # Database models
│   │   ├── User.js
│   │   ├── Trade.js
│   │   ├── Portfolio.js
│   │   └── ...
│   ├── middleware/        # Custom middleware
│   │   ├── auth.js
│   │   ├── errorHandler.js
│   │   ├── validation.js
│   │   └── ...
│   ├── services/          # Business services
│   │   ├── authService.js
│   │   ├── tradingService.js
│   │   ├── portfolioService.js
│   │   └── ...
│   ├── utils/             # Utility functions
│   ├── config/            # Configuration
│   ├── database/          # Database config
│   └── server.js          # Entry point
├── migrations/            # Database migrations
├── tests/                 # Test files
├── package.json
└── .env.example
```

### Request/Response Flow

```
HTTP Request
    │
    ▼
Express Middleware Chain
    ├── cors
    ├── bodyParser
    ├── authentication
    └── validation
    │
    ▼
Route Handler
    │
    ▼
Controller
    │
    ▼
Service Layer
    │
    ▼
Database Query / Cache
    │
    ▼
Response Processing
    │
    ▼
HTTP Response
```

## Database Design

### Core Tables

#### Users
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY,
    email VARCHAR UNIQUE NOT NULL,
    password_hash VARCHAR NOT NULL,
    name VARCHAR NOT NULL,
    country VARCHAR,
    language VARCHAR,
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);
```

#### Trades
```sql
CREATE TABLE trades (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    symbol VARCHAR NOT NULL,
    type VARCHAR (buy/sell),
    quantity DECIMAL,
    price DECIMAL,
    total DECIMAL,
    status VARCHAR,
    created_at TIMESTAMP
);
```

#### Portfolio
```sql
CREATE TABLE portfolios (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    total_value DECIMAL,
    cash_available DECIMAL,
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);
```

#### Market Data
```sql
CREATE TABLE market_data (
    id UUID PRIMARY KEY,
    symbol VARCHAR NOT NULL,
    price DECIMAL,
    volume BIGINT,
    change_24h DECIMAL,
    timestamp TIMESTAMP
);
```

## API Architecture

### RESTful Endpoints
```
POST   /api/auth/register       - Register user
POST   /api/auth/login          - User login
GET    /api/user/profile        - Get user profile
PUT    /api/user/profile        - Update profile

GET    /api/trades              - Get all trades
POST   /api/trades              - Create trade
GET    /api/trades/:id          - Get trade details
DELETE /api/trades/:id          - Cancel trade

GET    /api/portfolio           - Get portfolio overview
GET    /api/portfolio/positions - Get positions
GET    /api/portfolio/performance - Get performance

GET    /api/markets             - Get all markets
GET    /api/markets/:symbol     - Get market details
GET    /api/markets/:symbol/history - Price history
```

### Real-time WebSocket Events
```
price:update        - Market price changes
trade:executed      - Trade execution
portfolio:update    - Portfolio changes
notification:new    - New notifications
```

## Security Architecture

### Authentication & Authorization
1. **JWT Tokens** - Stateless authentication
2. **Refresh Tokens** - Token renewal
3. **RBAC** - Role-based access control
4. **2FA** - Two-factor authentication support

### Data Protection
- Password hashing (bcrypt)
- SQL injection prevention
- CORS configuration
- Rate limiting
- Input validation
- HTTPS/TLS encryption

## Caching Strategy

### Redis Cache Layers
1. **Session Cache** - User sessions
2. **Market Data Cache** - Real-time prices
3. **User Cache** - User profiles
4. **Query Cache** - Frequently accessed queries

### Cache Invalidation
- TTL-based expiration
- Event-based invalidation
- Manual cache clearing

## Deployment Architecture

### Docker Containers
```
┌─────────────────┐
│  Frontend Build │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  nginx (static) │
└────────┬────────┘
         │
         ▼
┌─────────────────────────────┐
│  Backend Container          │
│  ┌──────────────────────┐   │
│  │  Express API Server  │   │
│  └──────────────────────┘   │
└────────┬────────────────────┘
         │
         ▼
┌──────────────┐  ┌──────────────┐
│ PostgreSQL   │  │ Redis        │
└──────────────┘  └──────────────┘
```

## Performance Optimization

### Frontend
- Code splitting
- Lazy loading
- Image optimization
- Caching strategies
- Bundle size optimization

### Backend
- Database indexing
- Query optimization
- Connection pooling
- Caching layers
- Compression (gzip)

### Infrastructure
- Load balancing
- CDN for static assets
- Auto-scaling capabilities
- Monitoring & alerts

## Monitoring & Logging

### Logging
- Application logs
- Request/response logs
- Error tracking
- Performance logs

### Monitoring
- Uptime monitoring
- Performance metrics
- Error rate tracking
- Real-time alerts
- Health checks

## Scalability

### Horizontal Scaling
- Stateless backend services
- Load balancer distribution
- Database read replicas
- Cache distribution

### Vertical Scaling
- Optimized queries
- Resource management
- Memory usage optimization
- CPU efficiency

---

For more details, see the individual service documentation.
