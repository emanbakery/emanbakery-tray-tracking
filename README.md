# Eman Bakery Tray Tracking System

> Production-ready internal tray management application for Eman Bakery with multi-branch support, comprehensive stock management, and complete audit trails.

## 📋 Project Overview

Eman Bakery Tray Tracking is an enterprise-grade internal management system designed to track tray movements across three bakery branches (Hera, Rahmaniyyah, Bawadi). The system manages:

- **Branch-based operations** for 3 independent locations
- **User management** with role-based access (Admin, Branch Staff)
- **Opening stock setup** for initial branch inventory
- **Tray issuance & returns** with real-time stock validation
- **Salesman profiles** with complete transaction history
- **Transaction auditing** with immutable records
- **Stock validation** with automatic blocking on insufficient inventory
- **Comprehensive dashboards** and reporting

## 🏗️ Architecture Overview

```
Eman Bakery Tray Tracking
├── Frontend (Next.js + React + TypeScript)
│   ├── AURA Dashboard Design System
│   ├── Pages: Login, Dashboard, Transactions, Salesman Profiles, Reports
│   ├── Real-time Stock Management
│   └── Audit Trail Viewer
├── Backend (Express.js + TypeScript)
│   ├── REST API with Request Validation
│   ├── JWT Authentication
│   ├── Role-Based Access Control
│   ├── Transaction Processing Pipeline
│   └── Stock Management Engine
├── Database (PostgreSQL via Supabase)
│   ├── Branch Management
│   ├── User Profiles & Authentication
│   ├── Salesman Records
│   ├── Stock Tracking (Immutable)
│   ├── Transaction History (Append-only)
│   └── Audit Logs
└── DevOps
    ├── Docker Configuration
    ├── GitHub Actions CI/CD
    ├── Vercel Frontend Deployment
    ├── Railway Backend Deployment
    └── Monitoring & Alerts
```

## 🚀 Quick Start

### Prerequisites

- Node.js 18.x or higher
- PostgreSQL 13+
- Docker & Docker Compose
- Git

### Local Development Setup

1. **Clone repository**
   ```bash
   git clone https://github.com/emanbakery/emanbakery-tray-tracking.git
   cd emanbakery-tray-tracking
   ```

2. **Install dependencies**
   ```bash
   npm run install:all
   ```

3. **Configure environment**
   ```bash
   cp .env.example .env
   # Edit .env with your configuration
   ```

4. **Start services**
   ```bash
   docker-compose up -d
   ```

5. **Run migrations**
   ```bash
   npm run migrate
   ```

6. **Start development servers**
   ```bash
   npm run dev
   ```

   - Frontend: http://localhost:3000
   - Backend: http://localhost:3001
   - API Docs: http://localhost:3001/api/docs

## 🗄️ Database Schema

### Core Tables

#### branches
```sql
id: UUID (PK)
name: STRING (Hera, Rahmaniyyah, Bawadi)
code: STRING (HER, RAH, BAW)
status: ENUM (active, inactive)
created_at: TIMESTAMP
updated_at: TIMESTAMP
```

#### users
```sql
id: UUID (PK)
branch_id: UUID (FK)
email: STRING (UNIQUE)
full_name: STRING
role: ENUM (admin, branch_staff)
status: ENUM (active, inactive)
created_at: TIMESTAMP
last_login: TIMESTAMP
```

#### salesman_profiles
```sql
id: UUID (PK)
name: STRING
phone: STRING
branch_id: UUID (FK)
status: ENUM (active, inactive)
total_outstanding_trays: INTEGER
created_at: TIMESTAMP
```

#### opening_stock
```sql
id: UUID (PK)
branch_id: UUID (FK)
initial_quantity: INTEGER
recorded_by: UUID (FK → users)
recorded_at: TIMESTAMP
-- Immutable record
```

#### tray_transactions
```sql
id: UUID (PK)
type: ENUM (issuance, return)
salesman_id: UUID (FK)
branch_id: UUID (FK)
quantity: INTEGER
recorded_by: UUID (FK)
timestamp: TIMESTAMP
status: ENUM (completed, blocked, reversed)
block_reason: TEXT
created_at: TIMESTAMP
-- Immutable record
```

#### audit_logs
```sql
id: UUID (PK)
action: STRING
user_id: UUID (FK)
resource_type: STRING
resource_id: UUID
changes: JSONB
ip_address: INET
timestamp: TIMESTAMP
-- Immutable record
```

## 📁 Project Structure

```
.
├── frontend/                    # Next.js Frontend
│   ├── public/                 # Static assets
│   ├── src/
│   │   ├── app/               # App router
│   │   ├── components/        # React components
│   │   ├── lib/               # Utilities & helpers
│   │   ├── types/             # TypeScript types
│   │   └── styles/            # Global styles
│   ├── package.json
│   ├── tsconfig.json
│   └── next.config.js
│
├── backend/                     # Express.js Backend
│   ├── src/
│   │   ├── routes/            # API routes
│   │   ├── controllers/       # Request handlers
│   │   ├── services/          # Business logic
│   │   ├── models/            # Database models
│   │   ├── middleware/        # Express middleware
│   │   ├── types/             # TypeScript types
│   │   └── utils/             # Utilities
│   ├── package.json
│   ├── tsconfig.json
│   └── server.ts
│
├── supabase/                    # Database
│   ├── migrations/             # SQL migrations
│   ├── seed/                   # Seed data
│   └── schema.sql              # Full schema
│
├── .github/
│   └── workflows/
│       ├── ci.yml             # CI/CD pipeline
│       ├── deploy.yml         # Deployment
│       └── tests.yml          # Test suite
│
├── docker-compose.yml           # Local development
├── Dockerfile.frontend
├── Dockerfile.backend
├── .env.example                 # Environment template
└── README.md                    # This file
```

## 🔑 Key Features

### 1. Branch Management
- Three independent branches (Hera, Rahmaniyyah, Bawadi)
- Separate stock tracking per branch
- Branch-specific dashboards

### 2. User Management
- **Admin Users**: Create users, manage all branches, generate reports
- **Branch Staff**: Record transactions for their branch only
- JWT-based authentication
- Secure password hashing

### 3. Opening Stock Setup
- One-time initial stock setup per branch
- Immutable historical record
- Required before transaction processing

### 4. Transaction Processing
- **Issuance**: Decrease stock when trays given to salesman
- **Return**: Increase stock when trays returned by salesman
- Real-time stock validation
- Prevents issuance if stock insufficient
- Prevents return if exceeds outstanding trays

### 5. Salesman Profiles
- Complete transaction history per salesman
- Outstanding tray balance
- Statement generation
- Historical statement details

### 6. Stock Validation Engine
- Real-time stock availability checks
- Automatic transaction blocking on insufficient stock
- Return quantity validation
- Outstanding tray tracking

### 7. Audit Trail
- Immutable transaction history
- Complete change tracking
- User action logging
- IP address recording
- No data modification or deletion

### 8. Dashboards & Reports
- Real-time branch stock dashboards
- Transaction history views
- Salesman performance reports
- Stock movement analytics
- Excel export functionality

## 🔐 Security Features

- **Authentication**: JWT tokens with secure refresh
- **Authorization**: Role-based access control (RBAC)
- **Data Validation**: Input sanitization & type checking
- **SQL Injection Protection**: Parameterized queries
- **CORS**: Properly configured cross-origin requests
- **Rate Limiting**: API endpoint protection
- **Audit Logging**: Complete action tracking
- **Error Handling**: Secure error responses
- **Secrets Management**: Environment-based configuration

## 🔄 API Endpoints

### Authentication
```
POST   /api/auth/register         # User registration
POST   /api/auth/login            # User login
POST   /api/auth/refresh          # Refresh JWT token
POST   /api/auth/logout           # User logout
```

### Branches
```
GET    /api/branches              # List all branches
GET    /api/branches/:id          # Get branch details
POST   /api/branches              # Create branch (Admin only)
PUT    /api/branches/:id          # Update branch (Admin only)
```

### Users
```
GET    /api/users                 # List users (Admin only)
GET    /api/users/:id             # Get user details
POST   /api/users                 # Create user (Admin only)
PUT    /api/users/:id             # Update user (Admin only)
```

### Stock Management
```
GET    /api/stock/balance/:branch_id          # Get branch stock
POST   /api/stock/opening-setup               # Setup initial stock
GET    /api/stock/history/:branch_id          # Stock movement history
```

### Transactions
```
GET    /api/transactions          # List transactions
POST   /api/transactions/issue    # Issue trays to salesman
POST   /api/transactions/return   # Return trays from salesman
GET    /api/transactions/:id      # Get transaction details
```

### Salesman
```
GET    /api/salesmen              # List all salesmen
GET    /api/salesmen/:id          # Get salesman profile
GET    /api/salesmen/:id/history  # Get salesman transaction history
GET    /api/salesmen/:id/statement # Get salesman statement
POST   /api/salesmen              # Create salesman (Admin only)
```

### Reports
```
GET    /api/reports/stock-summary        # Stock summary report
GET    /api/reports/transaction-history  # Transaction report
GET    /api/reports/salesman-statement   # Salesman statement report
GET    /api/reports/audit-trail          # Audit log report
GET    /api/reports/export/:type         # Export to Excel
```

## 📊 Dashboard Pages

### 1. Login Page
- Email/password authentication
- Error handling
- Forgot password link

### 2. Dashboard
- Real-time stock levels
- Recent transactions
- Branch performance metrics
- Quick action buttons

### 3. Transactions
- Issue trays to salesman
- Return trays from salesman
- Transaction history
- Filter & search

### 4. Salesman Profiles
- Salesman directory
- Individual profile pages
- Complete transaction history
- Statement details
- Outstanding balance

### 5. Reports
- Stock summary reports
- Transaction analytics
- Salesman performance
- Audit trail viewer
- Excel export

## 🧪 Testing

```bash
# Run all tests
npm run test

# Run frontend tests
npm run test:frontend

# Run backend tests
npm run test:backend

# Run with coverage
npm run test:coverage
```

## 🔄 Database Migrations

```bash
# Run pending migrations
npm run migrate

# Rollback last migration
npm run migrate:rollback

# Create new migration
npm run migrate:create migration_name

# Seed database
npm run seed
```

## 🚀 Deployment

### Frontend (Vercel)

1. Connect GitHub repository to Vercel
2. Set environment variables in Vercel dashboard
3. Automatic deployment on push to main

```bash
# Manual deployment
npm run deploy:frontend
```

### Backend (Railway)

1. Connect GitHub repository to Railway
2. Configure environment variables
3. Set build command: `npm run build:backend`
4. Set start command: `npm run start:backend`

```bash
# Manual deployment
npm run deploy:backend
```

### Docker Deployment

```bash
# Build images
docker-compose build

# Start services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

## 📈 Monitoring & Logging

### Sentry Integration
- Frontend error tracking
- Backend exception tracking
- Performance monitoring
- Release tracking

### Application Logs
- Structured logging with Winston
- Log levels: error, warn, info, debug
- Rotating log files
- Console output in development

### Health Checks
```
GET /api/health           # System health status
GET /api/health/db        # Database connectivity
GET /api/health/cache     # Cache status
```

## 🔔 Notifications

### Slack Integration
- Deployment notifications
- Critical error alerts
- Daily transaction summaries
- Stock low warnings

## 🛠️ Development Commands

```bash
# Install all dependencies
npm run install:all

# Start development servers
npm run dev

# Build for production
npm run build

# Start production servers
npm run start

# Code linting
npm run lint

# Code formatting
npm run format

# Type checking
npm run type-check

# Database migrations
npm run migrate

# Database seeding
npm run seed

# Run tests
npm run test

# Generate API documentation
npm run docs:generate
```

## 🤝 Contributing

1. Create feature branch: `git checkout -b feature/your-feature`
2. Make changes and commit: `git commit -am 'Add feature'`
3. Push to branch: `git push origin feature/your-feature`
4. Create Pull Request with detailed description

## 📝 Environment Configuration

See `.env.example` for all available configuration options. Key variables:

- `DATABASE_URL`: PostgreSQL connection string
- `SUPABASE_URL`: Supabase project URL
- `JWT_SECRET`: JWT signing secret (change in production)
- `NEXT_PUBLIC_API_URL`: Backend API endpoint
- `SENTRY_DSN`: Sentry error tracking
- `SLACK_WEBHOOK_URL`: Slack notifications

## 📞 Support

For issues or questions:
1. Check existing GitHub issues
2. Create detailed issue with reproduction steps
3. Include environment details and logs

## 📄 License

Internal use only - Eman Bakery

## 🎯 Roadmap

- [x] Core infrastructure setup
- [x] Database schema design
- [x] Authentication system
- [x] Transaction processing
- [ ] Mobile app (React Native)
- [ ] Advanced analytics
- [ ] Machine learning forecasting
- [ ] Multi-language support

---

**Last Updated**: May 2026  
**Version**: 1.0.0  
**Status**: Production Ready
