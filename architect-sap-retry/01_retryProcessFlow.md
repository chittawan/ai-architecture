# SAP Retry Process Flow Documentation

## Overview
This document describes the SAP Retry Process Flow, which handles retry mechanisms for failed service requests in a distributed system architecture. The process implements a cyclic retry pattern with clearly defined service responsibilities.

## Roles

| Role ID | Role Name | Responsibility | Description |
|---------|-----------|----------------|-------------|
| Retry-Receiver-Service | Retry-Receiver-Service | Receiver-Request | รับ Request ที่่ต้องการ Retry |
| Retry-Scheduler-Service | Retry-Scheduler-Service | produce-retry-request | Scheduler-Job, GetList-Retry-Request, update-retry-request, produce-retry-request |
| Retry-Worker-Service | Retry-Worker-Service | call retry , retry-request and response | retry-request, produce-retry-request_and_response |
| ServiceRequestRetry (Input) | Service Request Retry Input | Input Processing | Handles initial service request input that requires retry processing |
| Retry-Result-Service | Retry-Result-Service | Result Processing | Processes and handles the results of retry operations |

## Activities

| Activity ID | Activity Name | Role | Event Type | Description |
|-------------|---------------|------|------------|-------------|
| act1 | Request-Retry | ServiceRequestRetry (Input) | Start | ServiceRequestRetry - Initial event that triggers the retry process |
| act2 | Retry-Receiver-Service | Retry-Receiver-Service | Task | receive-request - Receives and processes retry requests |
| act3 | Retry-Scheduler-Service | Retry-Scheduler-Service | Task | produce-retry-request - Schedules and manages retry operations |
| act4 | Retry-Worker-Service | Retry-Worker-Service | Task | retry-request - Executes the actual retry operations |
| act5 | Retry-Result-Service | Retry-Result-Service | Task | result-retry-request - Processes the results of retry operations |

## Process Flow

| From Activity | To Activity | Flow Description |
|---------------|-------------|------------------|
| act1 (Request-Retry) | act2 (Retry-Receiver-Service) | Initial request flows to receiver service |
| act2 (Retry-Receiver-Service) | act3 (Retry-Scheduler-Service) | Received request flows to scheduler |
| act3 (Retry-Scheduler-Service) | act4 (Retry-Worker-Service) | Scheduled request flows to worker |
| act4 (Retry-Worker-Service) | act5 (Retry-Result-Service) | Worker results flow to result service |
| act5 (Retry-Result-Service) | act1 (Request-Retry) | Results loop back to input for potential re-processing |

## Process Description

The SAP Retry Process Flow implements a cyclic retry mechanism with the following stages:

1. **Request Initiation (act1)**: The process begins when a service request input requires retry processing
2. **Request Reception (act2)**: The Retry-Receiver-Service receives and validates the retry request
3. **Request Scheduling (act3)**: The Retry-Scheduler-Service manages the timing and queuing of retry operations
4. **Request Execution (act4)**: The Retry-Worker-Service performs the actual retry operations
5. **Result Processing (act5)**: The Retry-Result-Service processes the outcomes and determines next actions
6. **Cycle Continuation**: Results are fed back to the receiver service for potential re-processing

## Architecture Notes

- The process includes a feedback loop from the result service back to the receiver service
- Each service operates in its own lane (swimlane) indicating clear separation of responsibilities
- The cyclic nature allows for multiple retry attempts until success or maximum attempts are reached
- The input layer (ServiceRequestRetry) serves as the entry point for external requests requiring retry processing

## Service Responsibilities

### Retry-Receiver-Service
- **Primary Function**: Receives and validates incoming retry requests
- **Key Operations**: Request validation, initial processing, queue management
- **Integration**: Accepts requests from input layer and forwards to scheduler

### Retry-Scheduler-Service
- **Primary Function**: Manages scheduling and timing of retry operations
- **Key Operations**: Job scheduling, retry list management, request updates, retry production
- **Integration**: Receives from receiver service, coordinates with worker service

### Retry-Worker-Service
- **Primary Function**: Executes actual retry operations
- **Key Operations**: Retry request execution, response handling, result production
- **Integration**: Processes scheduled requests, sends results to result service

### Retry-Result-Service
- **Primary Function**: Processes and manages retry operation results
- **Key Operations**: Result analysis, success/failure determination, feedback generation
- **Integration**: Receives worker results, provides feedback for cycle continuation

## Future Mermaid Diagrams

This section is reserved for additional diagrams that may be generated:

### System Context Diagram
- Will show the external systems and actors that interact with this retry process
- Will include data flow between external systems and the retry services

### Sequence Diagram
- Will detail the temporal flow of messages between services
- Will show the timing and order of interactions

### Entity Relationship Diagram (ERD)
- Will show the data entities involved in the retry process
- Will include relationships between retry requests, results, and configuration data