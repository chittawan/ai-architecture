# Retry Process Flow - Business Process Documentation

## Executive Summary
The Retry Process Flow is designed to handle service request retries in a distributed system architecture. This process enables automatic retry mechanisms for failed service requests, ensuring system resilience and improved reliability. The process involves multiple specialized services working together to receive, schedule, execute, and monitor retry operations.

## Process Participants

| Role ID | Name | Type | Responsibility | System | Description |
|---|---|---|----|-----|-----|
| Service-Request-Retry | Service-Request-Retry | participant | Request Initiator | - | ผู้ใช้งานระบบ |
| Retry-Receiver-Service | Retry-Receiver-Service | lane | Receiver-Request | - | รับ Request ที่่ต้องการ Retry |
| Retry-Scheduler-Service | Retry-Scheduler-Service | lane | produce-retry-request | - | Scheduler-Job, GetList-Retry-Request, update-retry-request, produce-retry-request |
| Retry-Worker-Service | Retry-Worker-Service | lane | call retry , retry-request and response | - | retry-request, produce-retry-request_and_response |
| External-Service | External-Service | lane | receive-retry-request | - | receive-retry-request form Retry-Worker-Service |
| Retry-Result-Service | Retry-Result-Service | lane | result-retry-request | - | result-retry-request |

## Process Activities

| Activity ID | Name | Role | Type | Event | SLA | Risk | System | Description |
|----|---|---|---|----|-----|---|-----|-----|
| act0 | Service-Request-Retry | Service-Request-Retry | task | Start | - | - | - | ServiceRequestRetry, requester, intput and want to retry-request |
| act1 | Request-Retry | ServiceRequestRetry | task | Start | - | - | - | ServiceRequestRetry, requester, intput and want to retry-request |
| act2 | Retry-Receiver-Service | Retry-Receiver-Service | task | Task | - | - | - | receive-request |
| act3 | Retry-Scheduler-Service | Retry-Scheduler-Service | task | Task | - | - | - | produce-retry-request |
| act4 | Retry-Worker-Service | Retry-Worker-Service | task | Task | - | - | - | retry-request |
| act5 | External-Service | External-Service | task | Task | - | - | - | receive-retry-request |
| act6 | Retry-Result-Service | Retry-Result-Service | task | Task | - | - | - | result-retry-request |

## Process Flows

| Flow ID | From | To | Type | Condition | Description |
|---|---|-----|---|-----|-----|
| flow_act0_act1 | act0 | act1 | sequence | - | Initial request flow |
| flow_act1_act2 | act1 | act2 | sequence | - | Request to receiver service |
| flow_act2_act3 | act2 | act3 | sequence | - | Receiver to scheduler |
| flow_act3_act4 | act3 | act4 | sequence | - | Scheduler to worker |
| flow_act4_act5 | act4 | act5 | sequence | - | Worker to external service |
| flow_act5_act4 | act5 | act4 | sequence | - | External service response back to worker |
| flow_act4_act6 | act4 | act6 | sequence | - | Worker to result service |

## Integration Points
- **External Systems**: External-Service receives retry requests and provides responses
- **Internal Services**: 
  - Retry-Receiver-Service: Entry point for retry requests
  - Retry-Scheduler-Service: Manages retry scheduling and queuing
  - Retry-Worker-Service: Executes actual retry operations
  - Retry-Result-Service: Handles retry results and notifications
- **Message Exchanges**: Asynchronous communication between services through message flows

## Non-Functional Requirements
- **Reliability**: System must handle failed retry attempts gracefully
- **Scalability**: Multiple retry workers can process requests in parallel
- **Monitoring**: All retry attempts should be logged and monitored
- **Error Handling**: Failed retries should be captured and reported

## Process Flow Description

1. **Service Request Initiation**: The process begins when a service request retry is initiated by the requesting system
2. **Request Reception**: The Retry-Receiver-Service receives and validates the retry request
3. **Retry Scheduling**: The Retry-Scheduler-Service processes the request and schedules it for execution
4. **Retry Execution**: The Retry-Worker-Service picks up the scheduled retry and attempts to execute it
5. **External Service Call**: The External-Service receives the retry request and processes it
6. **Response Handling**: The External-Service sends back a response to the Retry-Worker-Service
7. **Result Processing**: The Retry-Result-Service handles the final result and provides feedback

## Appendix: Diagram Placeholders
### System Context Diagram
`[Placeholder for sa_002 output]`

### Sequence Diagram
`[Placeholder for sa_003 output]`

### Data Model
`[Placeholder for sa_005 output]`