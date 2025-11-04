# SmartConseil - Digital Council Management System

A comprehensive microservices-based platform for managing academic councils and planning sessions at educational institutions. SmartConseil provides tools for council management, planning, reporting, rectification, and user management with role-based access control.

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation & Setup](#installation--setup)
- [Running the Application](#running-the-application)
- [Microservices](#microservices)
- [API Documentation](#api-documentation)
- [Database](#database)
- [Docker Deployment](#docker-deployment)
- [Development](#development)
- [Contributing](#contributing)

## 🎯 Project Overview

SmartConseil is a digital platform designed to streamline academic council management processes. It enables:

- **Council Management**: Create, schedule, and manage academic councils
- **Planning**: Organize and plan council sessions with participants
- **Reporting**: Generate comprehensive reports on council proceedings
- **Rectification**: Handle grade corrections and amendments
- **User Management**: Manage users with role-based access control (Admin, Chef, Enseignant, Rapporteur)
- **Real-time Communication**: WebSocket support for live updates

## 🏗️ Architecture

SmartConseil follows a **microservices architecture** with the following components:

```
┌─────────────────────────────────────────────────────────────┐
│                    Frontend (Angular 16)                     │
│                    Port: 4200                                │
└────────────────────────┬────────────────────────────────────┘
                         │
┌────────────────────────┴────────────────────────────────────┐
│                    API Gateway                              │
│                    Port: 8080                               │
└────────────────────────┬────────────────────────────────────┘
                         │
        ┌────────────────┼────────────────┬──────────────┐
        │                │                │              │
   ┌────▼────┐    ┌─────▼──────┐  ┌─────▼──────┐  ┌───▼────┐
   │ Conseil │    │Planification│  │  Rapport   │  │Rectifi-│
   │Service  │    │  Service    │  │  Service   │  │cation  │
   │Port:8081│    │ Port: 8090  │  │ Port: 8087 │  │Service │
   └────┬────┘    └─────┬──────┘  └─────┬──────┘  │Port:8089│
        │                │                │        └───┬────┘
        │          ┌─────▼──────┐        │            │
        │          │User Service│        │            │
        │          │Port: 8088  │        │            │
        │          └─────┬──────┘        │            │
        │                │                │            │
        └────────────────┼────────────────┼────────────┘
                         │
                    ┌────▼────┐
                    │  MySQL   │
                    │Port: 3306│
                    └──────────┘
```

## 💻 Tech Stack

### Backend
- **Framework**: Spring Boot 3.5.3
- **Language**: Java 17
- **Build Tool**: Maven
- **Architecture**: Microservices with Eureka Service Discovery
- **Database**: MySQL 5.7
- **API Gateway**: Spring Cloud Gateway
- **Service Discovery**: Netflix Eureka

### Frontend
- **Framework**: Angular 16
- **Language**: TypeScript 5.1
- **Build Tool**: Angular CLI
- **Testing**: Karma & Jasmine
- **Real-time Communication**: STOMP/WebSocket, SockJS
- **Data Export**: XLSX (Excel)
- **Webcam Support**: ngx-webcam

### DevOps
- **Containerization**: Docker
- **Orchestration**: Docker Compose
- **CI/CD**: Jenkins

## 📁 Project Structure

```
digitalConseil-main/
├── BackEsprit/
│   └── SmartConseil-Back/
│       ├── eureka/                          # Service Discovery Server
│       ├── gateway/                         # API Gateway
│       ├── microservices/
│       │   ├── microserviceConseil/         # Council Management Service
│       │   ├── microservicePlanification/   # Planning Service
│       │   ├── microserviceRapport/         # Report Service
│       │   ├── microserviceRectification/   # Rectification Service
│       │   └── microserviceUser/            # User Management Service
│       ├── pom.xml                          # Parent POM
│       └── Dockerfile
├── FrontEsprit/
│   └── SmartConseil-Front/
│       ├── src/
│       │   ├── app/
│       │   │   ├── admin-setup/
│       │   │   ├── CouncilManagement/
│       │   │   ├── dashboard/
│       │   │   ├── PlanificationConseil/
│       │   │   ├── rectification/
│       │   │   ├── report-management/
│       │   │   ├── utilisateur/
│       │   │   ├── guards/                  # Auth & Role Guards
│       │   │   ├── interceptors/            # JWT Interceptor
│       │   │   └── services/
│       │   ├── assets/
│       │   └── index.html
│       ├─�� package.json
│       ├── angular.json
│       ├── Dockerfile
│       └── README.md
├── db_init/
│   └── init.sql                             # Database initialization script
├── docker-compose.yml                       # Docker Compose configuration
├── Jenkinsfile                              # CI/CD Pipeline
└── users.sql                                # User data initialization
```

## 📋 Prerequisites

Before you begin, ensure you have the following installed:

- **Java 17** or higher
- **Node.js 18+** and npm
- **Maven 3.8+**
- **Docker** and **Docker Compose**
- **MySQL 5.7** (if running without Docker)
- **Git**

## 🚀 Installation & Setup

### 1. Clone the Repository

```bash
git clone <repository-url>
cd digitalConseil-main
```

### 2. Backend Setup (Without Docker)

#### Build the Backend

```bash
cd BackEsprit/SmartConseil-Back
mvn clean install
```

#### Configure Database

1. Create a MySQL database named `conseil`
2. Run the initialization scripts:

```bash
mysql -u root -p conseil < ../../db_init/init.sql
mysql -u root -p conseil < ../../users.sql
```

#### Start Eureka Server

```bash
cd eureka
mvn spring-boot:run
```

Eureka will be available at `http://localhost:8761`

#### Start API Gateway

```bash
cd gateway
mvn spring-boot:run
```

Gateway will be available at `http://localhost:8080`

#### Start Microservices

In separate terminals, start each microservice:

```bash
# Conseil Service
cd microservices/microserviceConseil
mvn spring-boot:run

# Planification Service
cd microservices/microservicePlanification
mvn spring-boot:run

# Rapport Service
cd microservices/microserviceRapport
mvn spring-boot:run

# Rectification Service
cd microservices/microserviceRectification
mvn spring-boot:run

# User Service
cd microservices/microserviceUser
mvn spring-boot:run
```

### 3. Frontend Setup

```bash
cd FrontEsprit/SmartConseil-Front
npm install
```

## 🏃 Running the Application

### Option 1: Using Docker Compose (Recommended)

```bash
# From the project root directory
docker-compose up -d
```

This will start:
- MySQL Database (Port 3306)
- Conseil Service (Port 8081)
- Planification Service (Port 8090)
- Rapport Service (Port 8087)
- Rectification Service (Port 8089)
- User Service (Port 8088)
- Frontend Application (Port 4200)

Access the application at: `http://localhost:4200`

### Option 2: Manual Development Setup

#### Terminal 1 - Backend Services

```bash
cd BackEsprit/SmartConseil-Back
mvn clean install
cd eureka
mvn spring-boot:run
```

#### Terminal 2 - API Gateway

```bash
cd BackEsprit/SmartConseil-Back/gateway
mvn spring-boot:run
```

#### Terminal 3 - Microservices (start each in separate terminals)

```bash
cd BackEsprit/SmartConseil-Back/microservices/microserviceConseil
mvn spring-boot:run
```

#### Terminal 4 - Frontend

```bash
cd FrontEsprit/SmartConseil-Front
npm install
npm start
```

Access the application at: `http://localhost:4200`

## 🔧 Microservices

### 1. **Conseil Service** (Port 8081)
Manages academic councils including creation, scheduling, and council details.

### 2. **Planification Service** (Port 8090)
Handles planning and scheduling of council sessions with participant management.

### 3. **Rapport Service** (Port 8087)
Generates and manages reports on council proceedings and outcomes.

### 4. **Rectification Service** (Port 8089)
Manages grade corrections and rectification requests.

### 5. **User Service** (Port 8088)
Handles user management, authentication, and role-based access control.

### 6. **API Gateway** (Port 8080)
Routes requests to appropriate microservices and handles cross-cutting concerns.

### 7. **Eureka Server** (Port 8761)
Service discovery and registration for all microservices.

## 📚 API Documentation

Each microservice exposes REST APIs. Access Swagger/OpenAPI documentation (if configured):

- Conseil Service: `http://localhost:8081/swagger-ui.html`
- Planification Service: `http://localhost:8090/swagger-ui.html`
- Rapport Service: `http://localhost:8087/swagger-ui.html`
- Rectification Service: `http://localhost:8089/swagger-ui.html`
- User Service: `http://localhost:8088/swagger-ui.html`

## 🗄️ Database

### Database Name
`conseil`

### Initialization
The database is automatically initialized when using Docker Compose. For manual setup, run:

```bash
mysql -u root -p conseil < db_init/init.sql
mysql -u root -p conseil < users.sql
```

### Connection Details
- **Host**: localhost (or mysql service in Docker)
- **Port**: 3306
- **Username**: root
- **Password**: (empty by default)
- **Database**: conseil

## 🐳 Docker Deployment

### Build Docker Images

```bash
# Build all services
docker-compose build

# Build specific service
docker-compose build conseil-service
```

### Run with Docker Compose

```bash
# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down

# Remove volumes
docker-compose down -v
```

### Docker Network
All services communicate through the `smartconseil-net` bridge network.

## 👨‍💻 Development

### Frontend Development

```bash
cd FrontEsprit/SmartConseil-Front

# Start development server
npm start

# Run tests
npm test

# Build for production
npm run build

# Watch mode
npm run watch
```

### Backend Development

```bash
cd BackEsprit/SmartConseil-Back

# Build all modules
mvn clean install

# Run specific service
cd microservices/microserviceConseil
mvn spring-boot:run

# Run tests
mvn test
```

### Code Structure

#### Frontend Components
- **admin-setup**: Admin configuration and setup
- **CouncilManagement**: Council CRUD operations
- **dashboard**: User dashboards (Admin, Chef, Enseignant, Rapporteur)
- **PlanificationConseil**: Council planning and scheduling
- **rectification**: Grade rectification management
- **report-management**: Report generation and viewing
- **utilisateur**: User management interface

#### Frontend Services
- **Guards**: Authentication and role-based authorization
- **Interceptors**: JWT token handling
- **Services**: API communication and business logic

#### Backend Modules
- **microserviceConseil**: Council entity management
- **microservicePlanification**: Planning and scheduling logic
- **microserviceRapport**: Report generation and storage
- **microserviceRectification**: Rectification workflow
- **microserviceUser**: User authentication and authorization

## 🔐 Authentication & Authorization

The application uses JWT (JSON Web Tokens) for authentication with role-based access control:

### Roles
- **ADMIN**: Full system access
- **CHEF**: Council management and oversight
- **ENSEIGNANT**: Teacher/Instructor access
- **RAPPORTEUR**: Report generation and viewing

### JWT Implementation
- Token generation in User Service
- Token validation via JWT Interceptor in frontend
- Role-based route guards in Angular

## 🧪 Testing

### Frontend Tests

```bash
cd FrontEsprit/SmartConseil-Front
npm test
```

### Backend Tests

```bash
cd BackEsprit/SmartConseil-Back
mvn test
```

## 📝 CI/CD Pipeline

The project includes a Jenkinsfile for automated CI/CD:

```bash
# View pipeline configuration
cat Jenkinsfile
```

## 🐛 Troubleshooting

### Port Already in Use
If a port is already in use, modify the port mappings in `docker-compose.yml` or use different ports when running services manually.

### Database Connection Issues
- Ensure MySQL is running
- Verify connection credentials in application properties
- Check database initialization scripts have been executed

### Service Discovery Issues
- Ensure Eureka server is running before starting microservices
- Check service registration in Eureka dashboard: `http://localhost:8761`

### Frontend Connection Issues
- Verify backend services are running
- Check browser console for CORS errors
- Ensure API Gateway is accessible

## 📞 Support & Contact

For issues, questions, or contributions, please refer to the project documentation or contact the development team.

## 📄 License

This project is proprietary software developed for Esprit institution.

## 🤝 Contributing

1. Create a feature branch (`git checkout -b feature/AmazingFeature`)
2. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
3. Push to the branch (`git push origin feature/AmazingFeature`)
4. Open a Pull Request

---

**Last Updated**: 2024
**Version**: 1.0.0
