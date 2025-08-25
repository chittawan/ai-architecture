# Retry Process - Container Diagram

## Container Architecture Overview

This diagram shows the container-level architecture of the Retry Process System, illustrating how the retry mechanism is distributed across multiple specialized microservices.

## Mermaid C4 Container Diagram

```mermaid
graph TB
    %% External Systems and Users
    RequestingSystem[🖥️ Requesting System<br/>SAP/ERP/External App<br/>Initiates Retry Requests]
    ExternalService[🌐 External Service<br/>Third-party API<br/>Target Service for Retry]
    
    %% System Boundary
    subgraph RetrySystemBoundary["🔄 Retry Process System"]
        direction TB
        
        %% API Layer
        subgraph APILayer["API & Gateway Layer"]
            RetryReceiver[📨 Retry Receiver Service<br/>Spring Boot/Node.js<br/>Port: 8081]
        end
        
        %% Processing Layer
        subgraph ProcessingLayer["Processing Services"]
            RetryScheduler[⏰ Retry Scheduler Service<br/>Spring Boot<br/>Port: 8082<br/>Cron Jobs & Queue Management]
            RetryWorker[⚙️ Retry Worker Service<br/>Java/Go<br/>Port: 8083<br/>Async Request Processing]
            RetryResult[📊 Retry Result Service<br/>Node.js/Python<br/>Port: 8084<br/>Result Aggregation]
        end
        
        %% Data Layer
        subgraph DataLayer["Data & Infrastructure"]
            Database[(🗄️ Retry Database<br/>PostgreSQL<br/>Request Status & History)]
            MessageQueue[📨 Message Queue<br/>Apache Kafka/RabbitMQ<br/>Async Communication)]
            Cache[(⚡ Redis Cache<br/>Retry Configuration<br/>& Rate Limiting)]
        end
        
        %% Monitoring Layer
        subgraph MonitoringLayer["Monitoring & Observability"]
            Monitoring[📈 Monitoring Service<br/>Prometheus + Grafana<br/>Metrics & Alerting]
            Logging[📝 Centralized Logging<br/>ELK Stack<br/>Request Tracing]
        end
    end
    
    %% External Relationships
    RequestingSystem -->|HTTP POST<br/>Retry Request| RetryReceiver
    RetryWorker -->|HTTP/gRPC<br/>Retry Call| ExternalService
    ExternalService -->|Response<br/>Success/Failure| RetryWorker
    
    %% Internal Service Communication
    RetryReceiver -->|Publish<br/>Retry Event| MessageQueue
    RetryScheduler -->|Subscribe<br/>Schedule Jobs| MessageQueue
    RetryScheduler -->|Produce<br/>Worker Tasks| MessageQueue
    RetryWorker -->|Subscribe<br/>Process Tasks| MessageQueue
    RetryWorker -->|Publish<br/>Results| MessageQueue
    RetryResult -->|Subscribe<br/>Aggregate Results| MessageQueue
    
    %% Data Access
    RetryReceiver -->|INSERT<br/>Request Log| Database
    RetryScheduler -->|SELECT/UPDATE<br/>Retry Schedule| Database
    RetryWorker -->|UPDATE<br/>Attempt Status| Database
    RetryResult -->|INSERT<br/>Final Results| Database
    
    %% Cache Usage
    RetryScheduler -->|GET/SET<br/>Config & Rules| Cache
    RetryWorker -->|GET<br/>Rate Limits| Cache
    RetryResult -->|SET<br/>Results Cache| Cache
    
    %% Monitoring & Logging
    RetryReceiver -.->|Metrics| Monitoring
    RetryScheduler -.->|Metrics| Monitoring
    RetryWorker -.->|Metrics| Monitoring
    RetryResult -.->|Metrics| Monitoring
    
    RetryReceiver -.->|Logs| Logging
    RetryScheduler -.->|Logs| Logging
    RetryWorker -.->|Logs| Logging
    RetryResult -.->|Logs| Logging
    
    %% Styling
    classDef external fill:#ff9999,stroke:#ff6666,stroke-width:2px
    classDef api fill:#87CEEB,stroke:#4682B4,stroke-width:2px
    classDef service fill:#90EE90,stroke:#32CD32,stroke-width:2px
    classDef data fill:#DDA0DD,stroke:#9370DB,stroke-width:2px
    classDef monitoring fill:#FFE4B5,stroke:#DEB887,stroke-width:2px
    classDef system fill:#F0E68C,stroke:#BDB76B,stroke-width:3px
    
    class RequestingSystem,ExternalService external
    class RetryReceiver api
    class RetryScheduler,RetryWorker,RetryResult service
    class Database,MessageQueue,Cache data
    class Monitoring,Logging monitoring
    class RetrySystemBoundary system
```

## Service Mapping Table

| Activity ID | Activity Name | Service Name | Technology Stack | Database Access | Integration Pattern |
|-------------|---------------|--------------|------------------|-----------------|-------------------|
| act1 | Request-Retry | Retry Receiver Service | Spring Boot/Node.js | INSERT requests | HTTP REST API |
| act2 | Retry-Receiver-Service | Retry Receiver Service | Spring Boot | Log retry requests | Message Publishing |
| act3 | Retry-Scheduler-Service | Retry Scheduler Service | Spring Boot + Quartz | Schedule management | Message Queue + Cron |
| act4 | Retry-Worker-Service | Retry Worker Service | Java/Go | Status updates | HTTP Client + Queue |
| act5 | External-Service | External Service | Various | N/A (External) | HTTP/gRPC API |
| act6 | Retry-Result-Service | Retry Result Service | Node.js/Python | Result storage | Message Subscriber |

## Technology Stack Rationale

### Service Technologies
- **Retry Receiver Service**: Spring Boot for robust REST API handling and validation
- **Retry Scheduler Service**: Spring Boot with Quartz for advanced job scheduling capabilities
- **Retry Worker Service**: Java/Go for high-performance async processing and external API calls
- **Retry Result Service**: Node.js/Python for flexible result processing and aggregation

### Data & Infrastructure
- **PostgreSQL**: Primary database for ACID compliance and complex queries on retry history
- **Apache Kafka/RabbitMQ**: Message queue for reliable async communication between services
- **Redis**: Cache for retry configurations, rate limiting, and temporary result storage

### Monitoring & Observability
- **Prometheus + Grafana**: Metrics collection and visualization for system monitoring
- **ELK Stack**: Centralized logging for request tracing and debugging

## Container Responsibilities

### Retry Receiver Service
- Accepts incoming retry requests from external systems
- Validates request format and business rules
- Publishes retry events to message queue
- Provides synchronous API response to requestors

### Retry Scheduler Service
- Manages retry scheduling logic and policies
- Implements exponential backoff and retry intervals
- Monitors queue depth and worker capacity
- Produces retry tasks for worker services

### Retry Worker Service
- Processes retry tasks from the queue
- Executes actual HTTP/gRPC calls to external services
- Implements circuit breaker and timeout patterns
- Handles response processing and error classification

### Retry Result Service
- Aggregates retry results and statistics
- Provides reporting and analytics capabilities
- Notifies requesting systems of final outcomes
- Maintains audit trail for compliance

## Integration Patterns

### Asynchronous Messaging
- **Event-Driven Architecture**: Services communicate through domain events
- **Publish-Subscribe**: Loose coupling between retry components
- **Message Durability**: Ensures no retry requests are lost

### Data Management
- **Event Sourcing**: Complete audit trail of retry attempts
- **CQRS**: Separate read and write models for performance
- **Caching Strategy**: Reduces database load and improves response times

### Resilience Patterns
- **Circuit Breaker**: Protects external services from overload
- **Bulkhead**: Isolates retry processing from other system functions
- **Timeout & Retry**: Configurable retry policies with exponential backoff

## Deployment Considerations

### Scalability
- **Horizontal Scaling**: Worker services can be scaled independently
- **Queue Partitioning**: Message queue can be partitioned for higher throughput
- **Database Sharding**: Retry history can be sharded by time or requestor

### High Availability
- **Service Redundancy**: Multiple instances of each service
- **Data Replication**: Database and cache replication for failover
- **Health Checks**: Kubernetes/Docker health probes for auto-recovery

### Security
- **API Authentication**: JWT/OAuth for requesting system authentication
- **Network Isolation**: Services communicate through internal networks
- **Audit Logging**: Complete audit trail for security compliance

## Performance Characteristics

### Expected Load
- **Concurrent Requests**: 1,000+ retry requests per minute
- **External API Calls**: Variable latency (100ms - 30s)
- **Queue Throughput**: 10,000+ messages per minute
- **Database TPS**: 500+ transactions per second

### SLA Targets
- **Request Acceptance**: < 100ms response time
- **Retry Execution**: Within scheduled interval ±10%
- **System Availability**: 99.9% uptime
- **Data Durability**: 99.99% message delivery guarantee
