# 🌐 Microservices API Gateway

> Enterprise-grade API Gateway for distributed microservices architecture

[![Next.js](https://img.shields.io/badge/Next.js-000000?style=for-the-badge&logo=next.js&logoColor=white)]()
[![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=node.js&logoColor=white)]()
[![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=for-the-badge&logo=typescript&logoColor=white)]()
[![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)]()
[![AWS SQS](https://img.shields.io/badge/AWS_SQS-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)]()
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)]()

## 📋 Overview

Robust API Gateway implementation following microservices patterns with AWS SQS message queuing, rate limiting, circuit breakers, and centralized authentication. Built for high availability and scalability.

## ✨ Key Features

- 🚪 **API Gateway Pattern**: Single entry point for all microservices
- 🔐 **Centralized Auth**: JWT tokens with refresh mechanism
- ⚡ **Rate Limiting**: Token bucket algorithm per user/IP
- 🔄 **Circuit Breaker**: Automatic fallback on service failures
- 📨 **AWS SQS Integration**: Async message processing
- 🎯 **Service Discovery**: Dynamic service registry
- 📊 **Load Balancing**: Round-robin and weighted strategies
- 🛡️ **Request Validation**: JSON Schema validation
- 📝 **API Versioning**: Support for v1, v2, etc.
- 🔍 **Distributed Tracing**: Request correlation IDs
- 📈 **Metrics & Monitoring**: Prometheus-ready endpoints
- 🚨 **Error Handling**: Standardized error responses

## 🏗️ Architecture

### Hexagonal Architecture (Ports & Adapters)

```
gateway/
├── src/
│   ├── domain/
│   │   ├── entities/          # Route, Service, User
│   │   ├── value-objects/     # Endpoint, HttpMethod
│   │   └── repositories/      # Interface definitions
│   ├── application/
│   │   ├── use-cases/         # RouteRequest, AuthenticateUser
│   │   ├── ports/
│   │   │   ├── input/         # API interfaces
│   │   │   └── output/        # Service interfaces
│   │   └── services/
│   │       ├── rate-limiter/  # Token bucket implementation
│   │       ├── circuit-breaker/ # Failure protection
│   │       └── load-balancer/ # Distribution logic
│   └── infrastructure/
│       ├── http/              # Express/Fastify adapters
│       ├── database/          # MySQL with Prisma
│       ├── queue/             # AWS SQS adapter
│       ├── cache/             # Redis for rate limiting
│       └── monitoring/        # Prometheus metrics
│
microservices/
├── user-service/              # Port 4001
├── product-service/           # Port 4002
├── order-service/             # Port 4003
└── notification-service/      # Port 4004
```

### Request Flow

```
Client Request
      ↓
┌─────────────────┐
│   API Gateway   │
│   (Port 4000)   │
└────────┬────────┘
         │
    ┌────┴────┐
    │  Auth   │ → Validate JWT
    └────┬────┘
         │
    ┌────┴────────┐
    │Rate Limiter │ → Check limits
    └────┬────────┘
         │
    ┌────┴─────────┐
    │Load Balancer │ → Select instance
    └────┬─────────┘
         │
    ┌────┴──────────┐
    │Circuit Breaker│ → Health check
    └────┬──────────┘
         │
    ┌────┴─────┐
    │Microservice│
    └──────────┘
```

## 🚀 Tech Stack

### Frontend (Admin Dashboard)
- Next.js 14 (App Router)
- TypeScript
- TailwindCSS
- Recharts (metrics visualization)
- React Query

### Gateway Service
- Node.js + Express/Fastify
- TypeScript
- MySQL (service registry, configs)
- Redis (rate limiting, caching)
- AWS SQS (async messaging)
- Prometheus client

### Microservices
- Node.js microservices
- MySQL databases
- Message-driven communication

## 🐳 Quick Start

```bash
# Clone repository
git clone https://github.com/andremlopesbr/microservices-api-gateway.git
cd microservices-api-gateway

# Environment setup
cp .env.example .env

# Start with Docker Compose
docker-compose up -d

# Access
# Gateway: http://localhost:4000
# Dashboard: http://localhost:3000
# User Service: http://localhost:4001
# Product Service: http://localhost:4002
```

## 📦 Services Architecture

| Service | Port | Description |
|---------|------|-------------|
| Gateway | 4000 | Main API Gateway |
| Dashboard | 3000 | Admin interface |
| User Service | 4001 | Authentication & users |
| Product Service | 4002 | Product catalog |
| Order Service | 4003 | Order processing |
| Notification Service | 4004 | Email/SMS notifications |
| MySQL | 3306 | Relational database |
| Redis | 6379 | Cache & rate limiting |
| AWS SQS | - | Message queue |

## 🔌 API Gateway Endpoints

### Gateway Management
```
GET    /health                  - Health check
GET    /metrics                 - Prometheus metrics
GET    /api/routes              - List registered routes
POST   /api/routes              - Register new route
DELETE /api/routes/:id          - Remove route
```

### Authentication
```
POST   /auth/login              - User login
POST   /auth/refresh            - Refresh token
POST   /auth/logout             - Logout user
```

### Proxied Microservices
```
GET    /api/v1/users            → User Service
GET    /api/v1/products         → Product Service
POST   /api/v1/orders           → Order Service
```

## ⚙️ Configuration

### Rate Limiting

```typescript
const rateLimitConfig = {
  windowMs: 60000,        // 1 minute
  maxRequests: 100,       // 100 requests per minute
  strategy: 'token-bucket'
};
```

### Circuit Breaker

```typescript
const circuitBreakerConfig = {
  failureThreshold: 5,    // Open after 5 failures
  resetTimeout: 60000,    // Try again after 1 min
  monitoringPeriod: 10000 // 10 second window
};
```

## 🛠️ Development

```bash
# Install dependencies
npm install

# Run in development mode
npm run dev

# Run tests
npm run test
npm run test:integration

# Build for production
npm run build

# Start production server
npm start
```

## 🌍 Environment Variables

```env
# Gateway
GATEWAY_PORT=4000
NODE_ENV=production

# Database
DATABASE_URL=mysql://user:pass@localhost:3306/gateway

# Redis
REDIS_URL=redis://localhost:6379

# AWS SQS
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=your_key
AWS_SECRET_ACCESS_KEY=your_secret
SQS_QUEUE_URL=https://sqs.us-east-1.amazonaws.com/xxx/gateway-queue

# JWT
JWT_SECRET=your-super-secret-key
JWT_EXPIRES_IN=1h
JWT_REFRESH_EXPIRES_IN=7d

# Microservices
USER_SERVICE_URL=http://user-service:4001
PRODUCT_SERVICE_URL=http://product-service:4002
ORDER_SERVICE_URL=http://order-service:4003
```

## 📊 Monitoring & Observability

### Metrics Exposed

- `http_requests_total` - Total HTTP requests
- `http_request_duration_ms` - Request latency
- `rate_limit_exceeded_total` - Rate limit violations
- `circuit_breaker_state` - Circuit breaker status
- `service_health` - Microservice health

### Logging

```json
{
  "timestamp": "2025-11-06T14:00:00.000Z",
  "level": "info",
  "correlationId": "abc-123",
  "service": "gateway",
  "method": "GET",
  "path": "/api/v1/users",
  "statusCode": 200,
  "duration": 45
}
```

## 🔒 Security

- ✅ JWT token validation
- ✅ Rate limiting per IP/User
- ✅ CORS configuration
- ✅ Helmet.js security headers
- ✅ Input sanitization
- ✅ SQL injection protection
- ✅ XSS prevention
- ✅ HTTPS enforcement

## 🧪 Testing

```bash
# Unit tests
npm run test:unit

# Integration tests
npm run test:integration

# Load testing
npm run test:load

# Coverage
npm run test:coverage
```

## 🚧 Roadmap

- [ ] GraphQL Gateway support
- [ ] WebSocket proxying
- [ ] API versioning strategies
- [ ] Service mesh integration (Istio)
- [ ] Distributed tracing (Jaeger)
- [ ] API documentation generation
- [ ] Mock service responses
- [ ] Blue-green deployments

## 📖 Design Patterns Used

- **API Gateway Pattern**: Single entry point
- **Circuit Breaker**: Fault tolerance
- **Service Discovery**: Dynamic routing
- **Rate Limiting**: Abuse prevention
- **Load Balancing**: Request distribution
- **Retry Pattern**: Transient failure handling
- **Bulkhead**: Resource isolation

## 👤 Author

**André Lopes**
- GitHub: [@andremlopesbr](https://github.com/andremlopesbr)

## 🙏 Acknowledgments

- Full Cycle 3.0 microservices patterns
- AWS Well-Architected Framework
- Clean Architecture by Robert C. Martin
- Hexagonal Architecture by Alistair Cockburn

---

⭐ Star this repo if you find it useful!
