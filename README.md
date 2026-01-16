# Backend Booking Service

A microservices-based room booking system built with NestJS. The system allows users to book meeting rooms, manage reservations, process payments, and receive notifications.

## 🏗️ Architecture

This is a microservices architecture consisting of:

- **API Gateway** - Single entry point, authentication, and request routing
- **User Service** - User management and authentication
- **Room Service** - Room management and building types
- **Meeting Service** - Meeting/reservation management
- **Payment Service** - Payment processing
- **Notification Service** - Email notifications

## 🚀 Technologies

- **Framework**: NestJS 10.x
- **Database**: PostgreSQL with Prisma ORM
- **Message Queue**: RabbitMQ (AMQP) for inter-service communication
- **Authentication**: JWT, Google OAuth
- **File Storage**: AWS S3 (for room images)
- **Email**: Nodemailer with @nestjs-modules/mailer
- **Logging**: Winston with Elasticsearch integration
- **Validation**: class-validator, class-transformer

## 📋 Requirements

- Node.js >= 20.x
- PostgreSQL >= 12.x
- RabbitMQ
- AWS S3 (for file storage)
- npm or yarn

## 🔧 Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd backend-booking-service
```

2. Install dependencies for each service:
```bash
cd services/api-gateway && npm install
cd ../user-service && npm install
cd ../room-service && npm install
cd ../meeting-service && npm install
cd ../payment-service && npm install
cd ../notification-service && npm install
```

3. Set up environment variables for each service (see [Environment Variables](#environment-variables))

4. Set up databases and run Prisma migrations:
```bash
# For each service with Prisma
cd services/user-service
npx prisma migrate dev

cd ../room-service
npx prisma migrate dev

cd ../meeting-service
npx prisma migrate dev

cd ../payment-service
npx prisma migrate dev

cd ../notification-service
npx prisma migrate dev
```

## ⚙️ Environment Variables

Each service requires its own `.env` file. Here are the common variables needed:

### API Gateway
```env
PORT=3000
JWT_SECRET=your_jwt_secret
JWT_EXPIRES_IN=1d
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
GOOGLE_CALLBACK_URL=http://localhost:3000/auth/google/callback
RABBITMQ_URL=amqp://localhost:5672
```

### User Service
```env
DATABASE_URL=postgresql://user:password@localhost:5432/user_db
JWT_SECRET=your_jwt_secret
JWT_EXPIRES_IN=1d
REFRESH_TOKEN_EXPIRES_IN=7d
RABBITMQ_URL=amqp://localhost:5672
```

### Room Service
```env
DATABASE_URL=postgresql://user:password@localhost:5432/room_db
AWS_ACCESS_KEY_ID=your_aws_access_key
AWS_SECRET_ACCESS_KEY=your_aws_secret_key
AWS_REGION=us-east-1
AWS_S3_BUCKET=your_bucket_name
RABBITMQ_URL=amqp://localhost:5672
```

### Meeting Service
```env
DATABASE_URL=postgresql://user:password@localhost:5432/meeting_db
RABBITMQ_URL=amqp://localhost:5672
```

### Payment Service
```env
DATABASE_URL=postgresql://user:password@localhost:5432/payment_db
RABBITMQ_URL=amqp://localhost:5672
# Add payment gateway credentials (Stripe, PayPal, etc.)
```

### Notification Service
```env
DATABASE_URL=postgresql://user:password@localhost:5432/notification_db
MAIL_HOST=smtp.example.com
MAIL_PORT=587
MAIL_USER=your_email@example.com
MAIL_PASSWORD=your_email_password
MAIL_FROM=noreply@example.com
RABBITMQ_URL=amqp://localhost:5672
```

## 🏃 Running Services

### Development Mode

Start each service in a separate terminal:

```bash
# Terminal 1 - API Gateway
cd services/api-gateway
npm run start:dev

# Terminal 2 - User Service
cd services/user-service
npm run start:dev

# Terminal 3 - Room Service
cd services/room-service
npm run start:dev

# Terminal 4 - Meeting Service
cd services/meeting-service
npm run start:dev

# Terminal 5 - Payment Service
cd services/payment-service
npm run start:dev

# Terminal 6 - Notification Service
cd services/notification-service
npm run start:dev
```

### Production Mode

```bash
# Build each service
cd services/api-gateway && npm run build
cd ../user-service && npm run build
cd ../room-service && npm run build
cd ../meeting-service && npm run build
cd ../payment-service && npm run build
cd ../notification-service && npm run build

# Start each service
npm run start:prod
```

## 🗂️ Services Overview

### API Gateway (Port 3000)

The main entry point for all client requests. Handles:
- Authentication (JWT, Google OAuth)
- Request routing to microservices
- File uploads (Multer)
- Role-based access control

**Main Controllers:**
- `UserController` - User operations
- `RoomController` - Room operations
- `BuildingTypeController` - Building type management
- `MeetingController` - Meeting operations
- `PaymentController` - Payment operations

### User Service

Manages user accounts, authentication, and authorization:
- User registration and login
- JWT token generation and refresh
- Google OAuth integration
- Password reset functionality
- User roles and permissions

**Database Models:**
- `User` - User accounts
- `RefreshSession` - Refresh token sessions
- `ResetPassword` - Password reset tokens

### Room Service

Manages rooms and building types:
- Room CRUD operations
- Building type management
- Image upload to AWS S3
- Room availability checking

**Database Models:**
- `Room` - Room information
- `BuildingType` - Building type categories
- `ImageLink` - Room image links
- `RoomImageLink` - Room-image relationships

### Meeting Service

Handles meeting reservations:
- Create, update, and cancel meetings
- Check room availability
- Meeting scheduling

**Database Models:**
- `Meeting` - Meeting reservations
- `MeetingUser` - User-meeting relationships

### Payment Service

Processes payments for room bookings:
- Payment processing
- Payment status tracking
- Integration with payment gateways

### Notification Service

Sends email notifications:
- Booking confirmations
- Meeting reminders
- Payment receipts
- System notifications

## 📡 API Endpoints

### Authentication
- `POST /auth/register` - Register new user
- `POST /auth/login` - User login
- `POST /auth/refresh` - Refresh access token
- `GET /auth/google` - Google OAuth login
- `GET /auth/google/callback` - Google OAuth callback

### Users
- `GET /users/:id` - Get user by ID
- `PUT /users/:id` - Update user
- `DELETE /users/:id` - Delete user

### Rooms
- `GET /rooms` - Get all rooms
- `GET /rooms/:id` - Get room by ID
- `POST /rooms` - Create room (Admin)
- `PUT /rooms/:id` - Update room (Admin)
- `DELETE /rooms/:id` - Delete room (Admin)
- `POST /rooms/:id/images` - Upload room images

### Building Types
- `GET /building-types` - Get all building types
- `POST /building-types` - Create building type (Admin)
- `PUT /building-types/:id` - Update building type (Admin)
- `DELETE /building-types/:id` - Delete building type (Admin)

### Meetings
- `GET /meetings` - Get user's meetings
- `POST /meetings` - Create meeting
- `PUT /meetings/:id` - Update meeting
- `DELETE /meetings/:id` - Cancel meeting

### Payments
- `POST /payments` - Process payment
- `GET /payments/:id` - Get payment status

## 🔄 Inter-Service Communication

Services communicate via RabbitMQ message queues:

- `users_queue` - User service messages
- `rooms_queue` - Room service messages
- `meetings_queue` - Meeting service messages
- `payments_queue` - Payment service messages
- `notifications_queue` - Notification service messages

## 🗄️ Database Migrations

Each service uses Prisma for database management:

```bash
# Generate migration
npx prisma migrate dev --name migration_name

# Apply migrations
npx prisma migrate deploy

# Generate Prisma Client
npx prisma generate
```

## 🧪 Testing

```bash
# Run tests for a specific service
cd services/<service-name>
npm run test

# Run tests in watch mode
npm run test:watch

# Run tests with coverage
npm run test:cov

# E2E tests
npm run test:e2e
```

## 🛠️ Development

### Code Formatting
```bash
npm run format
```

### Linting
```bash
npm run lint
```

### Build
```bash
npm run build
```

## 📦 Key Dependencies

### Common across services:
- `@nestjs/core` - NestJS core
- `@nestjs/microservices` - Microservices support
- `@nestjs/config` - Configuration management
- `@prisma/client` - Prisma ORM client
- `amqplib` - RabbitMQ client
- `winston` - Logging
- `class-validator` - Validation
- `class-transformer` - Data transformation

### API Gateway specific:
- `@nestjs/jwt` - JWT authentication
- `@nestjs/passport` - Authentication strategies
- `passport-google-oauth20` - Google OAuth
- `multer` - File upload handling

### Room Service specific:
- `@aws-sdk/client-s3` - AWS S3 integration

### Notification Service specific:
- `@nestjs-modules/mailer` - Email sending
- `nodemailer` - SMTP client

## 🔒 Security

- JWT-based authentication
- Password hashing with bcrypt
- Role-based access control (RBAC)
- Input validation with class-validator
- CORS configuration
- Secure session management
- Refresh token rotation

## 📝 Features

- **Microservices Architecture** - Scalable and maintainable
- **JWT Authentication** - Secure token-based auth
- **Google OAuth** - Social login support
- **File Upload** - AWS S3 integration for room images
- **Message Queue** - Reliable inter-service communication
- **Email Notifications** - Automated email sending
- **Role-Based Access** - Admin and user roles
- **Logging** - Winston with Elasticsearch integration
- **Database Migrations** - Prisma migrations

## 🔗 External Services

- **PostgreSQL** - Primary database
- **RabbitMQ** - Message broker
- **AWS S3** - File storage
- **SMTP Server** - Email delivery
- **Google OAuth** - Authentication provider

---

**Note**: Make sure RabbitMQ is running before starting the services. Each service requires its own PostgreSQL database.
