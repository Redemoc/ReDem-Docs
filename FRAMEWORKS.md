# ReDem Platform - Frameworks and Technologies

## Overview

This document provides a comprehensive list of frameworks, libraries, and their versions used across all ReDem custom applications, along with the rationale for each selection.

## Framework Summary by Application

| Application | Primary Framework | Language | Runtime |
|-------------|-------------------|----------|---------|
| Frontend | Vue 3 + Vuetify 3 | TypeScript | Browser (Vite) |
| Backend | Express.js | TypeScript | Node.js |
| OES Microservice | Express.js | TypeScript | Node.js |
| CHS Microservice | Express.js | TypeScript | Node.js |
| TS Microservice | Express.js | TypeScript | Node.js |
| GQS Microservice | Quart | Python | Python 3.9 |
| BAS Microservice | Express.js | TypeScript | Node.js |
| Quality Check Hub | Express.js | TypeScript | Node.js |
| Jobs Handler | AWS Lambda | TypeScript | Node.js (Lambda) |
| Web Socket Server | Express.js + Socket.io | TypeScript | Node.js |
| API Gateway | Express.js | TypeScript | Node.js |

---

## Frontend Application

### Core Frameworks

| Framework | Version | Rationale |
|-----------|---------|-----------|
| **Vue.js** | 3.5.7 | Modern reactive framework with excellent TypeScript support, Composition API for better code organization, and a gentle learning curve. Selected over React for its simpler state management and template syntax. |
| **Vuetify** | 3.7.1 | Material Design component library providing comprehensive, accessible UI components out-of-the-box. Reduces development time for enterprise dashboards and ensures consistent design language. |
| **Vite** | 5.4.8 | Next-generation build tool offering instant server start, lightning-fast HMR (Hot Module Replacement), and optimized production builds. Chosen over Webpack for significantly better developer experience. |
| **Pinia** | 2.2.2 | Official Vue.js state management solution, replacing Vuex. Provides TypeScript-first design, modular stores, and devtools integration. |
| **Vue Router** | 4.2.5 | Official routing library for Vue.js with full TypeScript support, navigation guards, and dynamic routing capabilities. |

### Supporting Libraries

| Library | Version | Purpose |
|---------|---------|---------|
| **TypeScript** | ~5.3.3 | Static typing for improved code quality, IDE support, and maintainability |
| **Axios** | 1.7.6 | HTTP client for API communication with interceptor support |
| **Socket.io-client** | 4.8.1 | Real-time WebSocket communication for live updates |
| **@tanstack/vue-query** | 5.71.10 | Server state management with caching, background updates, and optimistic updates |
| **Vue I18n** | 9.9.1 | Internationalization support for multi-language UI |
| **Vue3 ApexCharts** | 1.5.2 | Data visualization charts and graphs |
| **VeeValidate + Yup** | 4.6.7 / 1.4.0 | Form validation with schema-based validation |
| **date-fns** | 3.6.0 | Modern date utility library (tree-shakeable alternative to Moment.js) |

---

## Backend Application

### Core Frameworks

| Framework | Version | Rationale |
|-----------|---------|-----------|
| **Express.js** | 4.21.0 | Minimal, flexible Node.js web framework. Industry standard for REST APIs with extensive middleware ecosystem. Chosen for its simplicity, performance, and vast community support. |
| **TypeScript** | 5.6.2 | Adds static typing to JavaScript, improving code quality, refactoring capabilities, and documentation through types. |

### Supporting Libraries

| Library | Version | Purpose |
|---------|---------|---------|
| **@aws-sdk/client-s3** | 3.848.0 | AWS S3 file storage operations |
| **@aws-sdk/client-sqs** | 3.848.0 | AWS SQS message queue integration |
| **@aws-sdk/client-lambda** | 3.857.0 | AWS Lambda function invocation |
| **@aws-sdk/client-cloudwatch** | 3.848.0 | AWS CloudWatch monitoring |
| **Axios** | 1.7.7 | HTTP client for external API calls |
| **Multer** | 1.4.5-lts.1 | Multipart form handling for file uploads |
| **xlsx** | 0.18.5 | Excel file parsing and generation |
| **node-cache** | 5.1.2 | In-memory caching for performance |
| **uuid** | 11.1.0 | UUID generation for unique identifiers |

---

## Quality Score Microservices (OES, CHS, TS, BAS)

### Core Frameworks

| Framework | Version | Rationale |
|-----------|---------|-----------|
| **Express.js** | 4.21.0 | Consistent with other Node.js services for maintainability. Lightweight and performant for single-purpose microservices. |
| **TypeScript** | 5.6.2 | Type safety across all microservices with shared type definitions via `@redem-gmbh/redem-3.0-db-schemas`. |

### Service-Specific Libraries

#### OES (Open-Ended Score)

| Library | Version | Purpose |
|---------|---------|---------|
| **OpenAI SDK** | 6.8.1 | AI-powered text analysis for quality scoring |
| **Vitest** | 4.0.12 | Fast unit testing framework |
| **Evalite** | 0.19.0 | AI model evaluation and testing |

#### CHS (Coherence Score)

| Library | Version | Purpose |
|---------|---------|---------|
| **OpenAI SDK** | 4.87.4 | AI coherence analysis |
| **Swagger (swagger-jsdoc + swagger-ui-express)** | 6.2.8 / 5.0.1 | API documentation |
| **Newman** | 6.2.1 | API testing and stress testing |

#### TS (Time Score)

| Library | Version | Purpose |
|---------|---------|---------|
| **@redem-gmbh/redem-3.0-db-schemas** | 1.3.3 | Shared MongoDB schemas for median time calculations |

#### BAS (Behavioral Analysis Score)

| Library | Version | Purpose |
|---------|---------|---------|
| **@redem-gmbh/redem-3.0-db-schemas** | 1.2.1 | Shared MongoDB schemas |
| **asciichart** | 1.5.25 | Terminal-based visualization for debugging |

---

## GQS Microservice (Python)

### Core Frameworks

| Framework | Version | Rationale |
|-----------|---------|-----------|
| **Quart** | ≥0.19.4 | Async Python web framework (Flask-compatible). Selected for native async/await support required for ML model inference without blocking. Compatible with existing Flask knowledge. |
| **TensorFlow** | 2.15.0 | Industry-standard ML framework for the pattern detection model. Provides SavedModel format for production deployment and GPU acceleration support. |
| **Keras** | 2.15.0 | High-level neural network API integrated with TensorFlow. Used for the matrix classification model. |

### Supporting Libraries

| Library | Version | Purpose |
|---------|---------|---------|
| **NumPy** | ≥1.26.4 | Numerical computing for matrix operations |
| **Hypercorn** | ≥0.16.0 | ASGI server for production deployment |
| **websockets** | ≥12.0 | WebSocket support for progress tracking |

---

## Quality Check Hub

### Core Frameworks

| Framework | Version | Rationale |
|-----------|---------|-----------|
| **Express.js** | 4.21.0 | Orchestration service requiring robust HTTP handling and middleware support for routing requests to downstream microservices. |
| **TypeScript** | 5.6.2 | Type safety for complex data transformations between services. |

### Supporting Libraries

| Library | Version | Purpose |
|---------|---------|---------|
| **Axios** | 1.7.7 | HTTP client for microservice communication |
| **@redem-gmbh/redem-3.0-db-schemas** | 1.4.2 | Shared database schemas and types |

---

## Jobs Handler (AWS Lambda)

### Core Frameworks

| Framework | Version | Rationale |
|-----------|---------|-----------|
| **AWS Lambda Runtime** | Node.js 18.x | Serverless compute for event-driven job processing. Selected for cost efficiency (pay-per-execution), automatic scaling, and native SQS integration. |
| **TypeScript** | 5.6.3 | Type safety for job processing logic and error handling. |

### Supporting Libraries

| Library | Version | Purpose |
|---------|---------|---------|
| **@aws-sdk/client-sqs** | 3.670.0 | SQS message handling |
| **aws-lambda** | 1.0.7 | Lambda types and utilities |
| **Winston** | 3.17.0 | Structured logging for CloudWatch |
| **Axios** | 1.7.7 | HTTP client for Quality Check Hub calls |
| **@redem-gmbh/redem-3.0-db-schemas** | 1.4.2 | Shared MongoDB schemas |

---

## Web Socket Server

### Core Frameworks

| Framework | Version | Rationale |
|-----------|---------|-----------|
| **Express.js** | 4.21.0 | HTTP server foundation for REST endpoints alongside WebSocket server. |
| **Socket.io** | 4.8.1 | Real-time bidirectional communication. Selected over raw WebSockets for automatic reconnection, room-based broadcasting, and fallback transports. |
| **TypeScript** | 5.6.2 | Type safety for socket event handling. |

### Supporting Libraries

| Library | Version | Purpose |
|---------|---------|---------|
| **amqplib** | 0.10.7 | RabbitMQ client for message queue consumption |
| **@redem-gmbh/redem-3.0-db-schemas** | 1.4.2 | Shared MongoDB schemas |
| **xlsx** | 0.18.5 | Excel file processing |
| **uuid** | 11.1.0 | UUID generation |

---

## API Gateway

### Core Frameworks

| Framework | Version | Rationale |
|-----------|---------|-----------|
| **Express.js** | 4.21.0 | Robust routing and middleware for versioned API endpoints. Handles request validation, authentication, and SQS message publishing. |
| **TypeScript** | 5.6.2 | Type safety for API contracts and request/response handling. |

### Supporting Libraries

| Library | Version | Purpose |
|---------|---------|---------|
| **Joi** | 17.13.3 | Schema-based request validation |
| **@aws-sdk/client-sqs** | 3.682.0 | SQS message publishing for job queue |
| **@aws-sdk/client-lambda** | 3.876.0 | Lambda function invocation |
| **Winston** | 3.17.0 | Structured logging with daily rotation |
| **uuid** | 11.0.2 | UUID generation for job IDs |

---

## Shared Infrastructure

### Database

| Technology | Version | Rationale |
|------------|---------|-----------|
| **MongoDB** | 6.x+ | Document database ideal for flexible survey schemas and respondent data. Supports complex aggregations for analytics and scales horizontally. |
| **Mongoose** | (via db-schemas) | ODM for MongoDB providing schema validation, middleware, and query building. |

### Shared Package

| Package | Version | Purpose |
|---------|---------|---------|
| **@redem-gmbh/redem-3.0-db-schemas** | 1.4.2 | Centralized MongoDB schemas ensuring consistency across all services |

### Message Queues

| Technology | Purpose |
|------------|---------|
| **AWS SQS** | Decoupled job processing for quality checks |
| **RabbitMQ** | Real-time message passing for WebSocket updates |

### Cloud Services (AWS)

| Service | Purpose |
|---------|---------|
| **S3** | File storage for data imports |
| **SQS** | Job queue for respondent processing |
| **Lambda** | Serverless job handler execution |
| **CloudWatch** | Logging and monitoring |

---

## Development & Build Tools

| Tool | Version | Purpose |
|------|---------|---------|
| **ESLint** | 8.x / 9.x | Code linting and style enforcement |
| **Prettier** | 3.x | Code formatting |
| **Husky** | 9.1.x | Git hooks for pre-commit/pre-push validation |
| **Jest** | 29.7.0 | Testing framework (Backend) |
| **Mocha + Chai** | 10.x / 5.x | Testing framework (Microservices) |
| **Vitest** | 4.0.12 | Testing framework (OES) |
| **SonarQube** | - | Code quality analysis |
| **Docker** | - | Containerization for deployment |
| **Bitbucket Pipelines** | - | CI/CD automation |

---

## Framework Selection Rationale Summary

### Why Express.js (Node.js)?
- **Consistency**: Single language (TypeScript/JavaScript) across frontend and backend reduces context switching
- **Performance**: Non-blocking I/O ideal for API services with many concurrent requests
- **Ecosystem**: Vast npm ecosystem with mature middleware and utilities
- **Developer Pool**: Large community ensures maintainability and talent availability

### Why Vue.js over React/Angular?
- **Learning Curve**: Gentler learning curve for team onboarding
- **Template Syntax**: HTML-like templates more intuitive for UI development
- **Vuetify**: Excellent Material Design component library
- **Bundle Size**: Smaller runtime compared to Angular
- **Composition API**: Modern patterns without React's Hook complexity

### Why Python for GQS?
- **ML Ecosystem**: TensorFlow and scientific computing libraries are Python-native
- **Quart**: Async Python framework compatible with ML workloads
- **Model Deployment**: Seamless integration with Keras/TensorFlow SavedModel format

### Why AWS Lambda for Jobs Handler?
- **Cost Efficiency**: Pay only for execution time, not idle servers
- **Scalability**: Automatic scaling based on queue depth
- **Integration**: Native SQS trigger support
- **Maintenance**: No server management required

### Why Socket.io over Raw WebSockets?
- **Reliability**: Automatic reconnection and heartbeat
- **Features**: Room-based broadcasting for survey-specific updates
- **Fallbacks**: Falls back to long-polling if WebSocket unavailable
- **Developer Experience**: Simplified event-based API

