**Retirement Portfolio Management System - Development Guide**
=====================================================================

This document provides information about the proposed design principles and development process. 

## Architecture and Tools
The system will be developed with a clear separation of frontend, backend, and database layers. 

### Architectural Principles
- **Modularity**: Modular monolith design

### Frontend
The user-facing web application provides an interactive dashboard.
- **Framework**: React provides the core structure for the component-based UI.
- **Visualisation Library**: Plotly.js, integrated via react-plotly.js, handles high-performance, interactive financial charts.
- **Data Fetching**: Axios (preferred over native Fetch API) communicates with the Python backend's API, with robust error handling.
- **Routing**: React Router navigates between dashboard pages or sections.
- **Accessibility**: Libraries like react-aria ensure WCAG 2.1 compliance for elderly users.
- **Package Manager**: npm manages frontend libraries and dependencies, with version locking via a lockfile.

### Backend
The application logic processes financial data and manages the database.
- **Language**: Python.
- **Framework**: Microservices architecture build on Redis Pub/Sub, FastAPI (with uvicorn+Gunicorn) provides high-performance API endpoints; Flask is a fallback for prototyping.
- **Web Server**: Gunicorn, paired with uvicorn, runs the Python application in production.
- **Database Adapter**: Psycopg2 connects Python to the PostgreSQL database, with connection pooling via SQLAlchemy.
- **API Client**: requests connects to external market data providers, with caching (e.g., Redis) for offline syncs.
- **Package Manager**: pip manages Python libraries, with pinned versions in requirements.txt.


### Database
The persistent storage layer ensures data integrity.
- **Database Server**: PostgreSQL provides strong concurrency, ACID compliance, and JSONB support for financial applications.
- **Migration**: Alembic manages database schema changes.
- **Backup**: Scheduled backups are implemented for Ubuntu deployment.

### Additional Tools
- **Deployment**: Docker containerises the application for consistent Ubuntu deployment.
- **Testing**: pytest ensures unit and integration tests for the Python backend; Jest is used for React frontend testing.
- **CI/CD**: GitHub Actions automates builds and testing in later phases.

## Development Environment
The tools used to build and test the application locally.
- **Code Editor**: Visual Studio Code, or a preferred IDE, with extensions for React, Python, and PostgreSQL.
- **Local Backend**: uvicorn runs the Python backend locally (Flask's development server as a fallback), ensuring API responsiveness.
- **Local Frontend**: npm run dev runs the React development server with hot-reloading, with a proxy (e.g., "http://localhost:8000") routing API calls.
- **Testing**: pytest tests the Python backend; Jest tests the React frontend; built-in browser tools (e.g., Chrome DevTools) supplement UI testing. Linting (flake8, ESLint) and type checking (mypy, TypeScript) ensure code quality.
- **Version Control**: Git with branching (e.g., feature/main) and a .gitignore file excluding sensitive data. The repository is publicly accessible from https://github.com/SRKershaw/Bouquet

## Deployment Sequence

### Phase 1: Local Development on PC
This phase focuses on rapid development and iteration.
- **Code the Full Stack Locally**: Write and run both React frontend and Python backend on your PC, using a single Git repository.
- **Run Development Servers**: Use npm run dev for React with hot-reloading and uvicorn for the Python backend, with a proxy in React config routing API calls.
- **Local Database**: Install and run PostgreSQL on your PC for local operations.
- **Test Functionality**: Use this setup for development, debugging, and feature-building. Use Git branching (e.g., feature/main) and .gitignore for sensitive files.

### Phase 2: Ubuntu Server on a Private Network
This phase introduces production practices.
- **Set Up the Server and Git**: Install Ubuntu 24.04, update packages (apt update/upgrade), install required dependencies (e.g., docker.io, python3), and clone your Git repository.
- **Containerise Your Services**: Install Docker and Docker Compose. Write Dockerfile and docker-compose.yml for the Python backend, PostgreSQL database, and Nginx. Dockerfile includes base image (e.g., python:3.11-slim), dependency install (pip), and app startup (gunicorn -w 4). docker-compose.yml defines services: backend (FastAPI), db (postgres:15), nginx (nginx:latest), with volumes and ports (e.g., 8000:8000).
- **Run the Application Stack**: Use docker-compose up to start the stack, leveraging Docker for isolation.
- **Database Migration**: Back up your local PostgreSQL with pg_dump and restore into the new PostgreSQL container, using Alembic for schema changes. If migration fails, restore from local pg_dump backup.
- **Access from Your Network**: Access via the server's local IP (e.g., http://192.168.1.100) from home devices.
- **Secure Internally**: Enable SSH keys and non-root users.
- **Monitoring**: Initial setup of performance metrics using Prometheus and Grafana for real-time insights. Install via Docker, configure Prometheus to scrape backend metrics (e.g., /metrics endpoint).
- **Firewall**: Configure UFW to allow ports 80, 443, 8000, and SSH (22).

### Phase 3: Public Availability via Firewall
This phase secures external access.
- **Secure External Access**: Enable HTTPS with Nginx and Certbot; fortify SSH with key-based authentication and Fail2Ban. Disable password login, use only SSH keys, and set PermitRootLogin no in sshd_config.
- **Configure Router Firewall**: Set up port forwarding for HTTPS (port 443) to the Ubuntu server. Allow only port 443 via UFW, block all others except SSH (22) from Phase 2.
- **Launch**: Accessible from the internet. Implement basic auth (e.g., Nginx htpasswd) for a small group of trusted users. Limit access to trusted IPs via Nginx geo module or firewall rules. Perform load testing with 10 users using Locust or JMeter, targeting 50 requests/min. Verify backups (pgBackRest) post-launch.
- **Performance Monitoring**: Add initial performance metrics using tools like Prometheus and Grafana.

### Phase 4: Migrate to a Cloud Service (AWS)
This phase scales with product-market fit.
- **Choose AWS Services**: Use Amazon Elastic Container Service (ECS) or AWS Fargate for Docker containers, Amazon RDS for PostgreSQL, AWS S3 for static React files, and an Application Load Balancer for traffic.
- **Set Up an AWS Environment**: Create a Virtual Private Cloud (VPC) with public and private subnets, configure security groups, and define IAM roles with least privilege access.
- **Deploy to AWS**: Push Docker images to Amazon Elastic Container Registry (ECR). Deploy services using ECS, connecting to RDS, and upload React static files to S3 with CloudFront distribution.
- **Automate Deployment (CI/CD)**: Use AWS CodePipeline with GitHub Actions for automated builds and deployments, triggered by Git pushes, with unit tests (pytest, Jest) as a gate.
- **Decommission the Ubuntu Server**: Validate AWS stability with a 48-hour soak test, then shut down the server, transferring backups to S3 and enabling failover to a secondary AWS region.
- **Cost Consideration**: Plan for £40-£80/month AWS costs, including ECS, RDS, and S3; monitor with AWS Cost Explorer and test hybrid local/AWS failover.
- **Advanced Monitoring**: Implement comprehensive monitoring with AWS CloudWatch, integrating logs and metrics from ECS, RDS, and S3, with alerts for anomalies.

