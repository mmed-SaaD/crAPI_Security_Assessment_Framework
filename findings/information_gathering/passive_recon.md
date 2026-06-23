## Target: crAPI

crAPI (Completely Ridiculous API) is a deliberately vulnerable application designed to demonstrate and teach the OWASP API Security Top 10 in a safe and controlled environment. It was developed by OWASP (Open Worldwide Application Security Project) as a training platform for API security testing and penetration testing.

## Business Context

The crAPI application is modeled as a business-to-consumer (B2C) platform that allows users to manage vehicle-related services. Users can create accounts, register and manage vehicles, search for mechanics, submit vehicle service requests, purchase car accessories, and participate in a community platform through blog posts and comments.

## Microservices Architecture

According to the official crAPI documentation, the application follows a microservices architecture composed of multiple interconnected services designed to simulate a modern production environment.

The primary documented services include:

* **Identity Service** – Manages user accounts, authentication, credentials, and JWT tokens.
* **Workshop Service** – Handles vehicle servicing requests, mechanic-related functionality, and e-commerce operations.
* **Community Service** – Manages community content, including blog posts, comments, and promotional coupons.

## Deployment Options

According to the official documentation, crAPI supports multiple deployment methods, including:

* Docker Compose
* Kubernetes (Helm Charts)

## Databases

The application uses a hybrid database architecture consisting of:

* PostgreSQL
* MongoDB

## Environment Configuration

According to the official documentation, application services are configured through environment variables. These variables can be defined through Docker Compose files, Kubernetes ConfigMaps, or directly within the runtime environment.

The microservices architecture enables isolated deployment, testing, and maintenance of individual services while allowing them to operate as a unified application platform.

## Initial Attack Surface Hypothesis

Based on publicly available documentation, the following functional areas are expected to be present and should be validated during active reconnaissance:

* Authentication and Authorization
* User Account Management
* Vehicle Management
* Workshop and Service Requests
* E-Commerce Functionality
* Community Features
* API Endpoints
* JWT-Based Authentication Mechanisms
