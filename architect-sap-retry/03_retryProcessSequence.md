# Retry Process Sequence Diagram

## Sequence Flow Overview
This diagram shows the detailed interaction flow between all participants in the retry process, illustrating the chronological order of operations and message exchanges.

## Sequence Diagram

```mermaid
sequenceDiagram
    title: Retry Process Flow Sequence
    
    participant REQ as Service Request Initiator
    participant REC as Retry Receiver Service
    participant SCH as Retry Scheduler Service
    participant WRK as Retry Worker Service
    participant EXT as External Service
    participant RES as Retry Result Service
    
    %% Initial Request Flow
    REQ->>REQ: Service-Request-Retry
    Note over REQ: ServiceRequestRetry, requester, input and want to retry-request
    
    REQ->>REC: Request-Retry
    activate REC
    Note over REC: receive-request
    
    %% Scheduling Phase
    REC->>SCH: Forward Request
    activate SCH
    Note over SCH: produce-retry-request<br/>Scheduler-Job, GetList-Retry-Request, update-retry-request
    
    %% Worker Processing
    SCH->>WRK: Queue Retry Request
    activate WRK
    Note over WRK: retry-request<br/>produce-retry-request_and_response
    
    %% External Service Call
    WRK->>EXT: Execute Retry Request
    activate EXT
    Note over EXT: receive-retry-request from Retry-Worker-Service
    
    %% Response Handling
    EXT-->>WRK: Service Response
    deactivate EXT
    
    %% Retry Loop (Optional)
    alt Retry Required
        WRK->>WRK: Process Retry Logic
        WRK->>EXT: Retry Request
        activate EXT
        EXT-->>WRK: Retry Response
        deactivate EXT
    else Success
        Note over WRK: Retry successful, proceed
    end
    
    %% Result Processing
    WRK->>RES: Process Result
    activate RES
    Note over RES: result-retry-request
    
    %% Cleanup and Notifications
    RES-->>WRK: Result Processed
    deactivate RES
    WRK-->>SCH: Retry Complete
    deactivate WRK
    SCH-->>REC: Request Fulfilled
    deactivate SCH
    REC-->>REQ: Final Response
    deactivate REC
    
    %% Final Notification
    Note over REQ,RES: Process completed with result notification
```

## Sequence Flow Description

### Phase 1: Request Initiation
1. **Service Request Initiator** generates a retry request
2. Request is sent to **Retry Receiver Service** for initial processing

### Phase 2: Request Processing
3. **Retry Receiver Service** validates and forwards the request
4. **Retry Scheduler Service** schedules the retry operation and manages the queue

### Phase 3: Retry Execution
5. **Retry Worker Service** picks up the scheduled request
6. Worker service executes the retry against the **External Service**
7. External service processes the request and returns a response

### Phase 4: Retry Logic (Conditional)
8. If retry is required, the worker service implements retry logic
9. Additional attempts are made until success or maximum retries reached

### Phase 5: Result Processing
10. **Retry Result Service** processes the final outcome
11. Results are propagated back through the chain
12. Final notification is sent to the original requester

## Timing and SLA Considerations
- Each service interaction should have defined timeout values
- Retry attempts should follow exponential backoff strategies
- Maximum retry attempts should be configurable
- Process monitoring should track end-to-end execution time

## Error Handling Scenarios
- **Network Failures**: Automatic retry with backoff
- **Service Unavailable**: Queue request for later processing
- **Invalid Requests**: Immediate rejection with error details
- **Timeout Scenarios**: Graceful degradation and notification

## Activity Mapping Table

| Activity ID | Sequence Step | Participant | Description |
|---|---|---|---|
| act0 | 1 | Service Request Initiator | Initial service request retry trigger |
| act1 | 2 | Service Request Initiator | Request-Retry submission |
| act2 | 3-4 | Retry Receiver Service | receive-request processing |
| act3 | 5-6 | Retry Scheduler Service | produce-retry-request scheduling |
| act4 | 7-11 | Retry Worker Service | retry-request execution and loop handling |
| act5 | 8-10 | External Service | receive-retry-request processing |
| act6 | 12-13 | Retry Result Service | result-retry-request final processing |
