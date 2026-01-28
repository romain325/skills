# DevOps Architecture Patterns

## Infrastructure Patterns

### Immutable Infrastructure
**Pattern**: Replace infrastructure rather than modify it in place
**Implementation**:
- Build new VM/container images for changes
- Deploy new instances, decommission old ones
- Never SSH into servers to make manual changes

**Benefits**:
- ✅ Consistent environments, no configuration drift
- ✅ Easy rollback (deploy previous image)
- ✅ Simplified disaster recovery

**Tradeoffs**:
- ❌ Longer deployment time (build + deploy vs update)
- ❌ Requires image registry and build pipeline
- ❌ Not suitable for stateful services without planning

**When to use**: Cloud-native applications, containerized workloads, need for consistency

### Pet vs Cattle
**Pets**: Named, manually configured servers, treated individually
**Cattle**: Numbered, automated provisioning, disposable

**Modern approach**: Cattle for applications, managed services for data
- Application servers: Cattle (auto-scaled, auto-replaced)
- Databases: Managed services (RDS, CloudSQL) or operators (K8s)
- State: External storage (S3, Cloud Storage)

### Infrastructure as Code (IaC)

**Declarative vs Imperative**:
- **Declarative** (Terraform, CloudFormation): Define desired state, tool handles how
- **Imperative** (Ansible, scripts): Define steps to reach desired state

**Declarative advantages**:
- Idempotent by design
- Plan before apply (preview changes)
- Better for cloud resource management

**Imperative advantages**:
- More flexible for complex workflows
- Better for configuration management
- Easier to understand for procedural tasks

**Tool selection**:
- **Terraform**: Multi-cloud, large ecosystem, state management
- **CloudFormation**: AWS-native, no state file, tight integration
- **Pulumi**: Real programming languages, familiar for developers
- **Ansible**: Configuration management, hybrid IaC, agentless
- **Chef/Puppet**: Traditional config management, agent-based

### GitOps
**Pattern**: Git as single source of truth for infrastructure and application state

**Workflow**:
1. Desired state defined in Git
2. GitOps operator (Flux, ArgoCD) monitors Git
3. Operator reconciles cluster state with Git state
4. Changes only through Git commits/PRs

**Benefits**:
- ✅ Audit trail (Git history)
- ✅ Declarative, version-controlled
- ✅ Easy rollback (Git revert)
- ✅ PR-based review process

**Tradeoffs**:
- ❌ Requires GitOps tooling
- ❌ Learning curve for operations team
- ❌ Secrets management complexity

**Tools**:
- **ArgoCD**: Rich UI, multi-cluster, app-centric
- **Flux**: Lightweight, GitOps Toolkit modular approach
- **Jenkins X**: Opinionated, full CI/CD platform

## Application Deployment Patterns

### 12-Factor App Principles
1. **Codebase**: One codebase in version control, many deploys
2. **Dependencies**: Explicitly declare and isolate dependencies
3. **Config**: Store config in environment variables
4. **Backing services**: Treat as attached resources
5. **Build, release, run**: Strictly separate stages
6. **Processes**: Execute as stateless processes
7. **Port binding**: Export services via port binding
8. **Concurrency**: Scale out via process model
9. **Disposability**: Fast startup, graceful shutdown
10. **Dev/prod parity**: Keep environments similar
11. **Logs**: Treat logs as event streams (stdout)
12. **Admin processes**: Run as one-off processes

**Benefits**: Cloud-native, scalable, portable applications

### Microservices vs Monolith

**Monolith**:
- ✅ Simple deployment, easier to debug, no network overhead
- ❌ Scaling constraints, technology lock-in, deployment risk

**When to use monolith**:
- Small team (<10 developers)
- Simple domain
- Starting new project (avoid premature microservices)

**Microservices**:
- ✅ Independent scaling, technology flexibility, team autonomy
- ❌ Operational complexity, distributed system challenges, debugging difficulty

**When to use microservices**:
- Large team (>20 developers)
- Complex domain with bounded contexts
- Need for independent scaling/deployment

**Migration path**: Start with monolith, extract microservices as needed (strangler fig pattern)

### API Gateway Pattern
**Purpose**: Single entry point for client requests to microservices

**Responsibilities**:
- Request routing
- Authentication/authorization
- Rate limiting
- Request/response transformation
- Protocol translation (REST to gRPC)
- API composition (aggregating multiple services)

**Options**:
- **Kong**: Open source, plugin ecosystem, high performance
- **AWS API Gateway**: Managed, AWS integration, pay-per-use
- **Azure API Management**: Enterprise features, Azure-native
- **Apigee**: Google Cloud, advanced analytics
- **Tyk**: Open source/commercial, GraphQL support
- **Envoy Proxy**: Service mesh integration, modern architecture

### Backend for Frontend (BFF)
**Pattern**: Dedicated backend per client type (web, mobile, IoT)

**Structure**:
```
Web App → Web BFF → Microservices
Mobile App → Mobile BFF → Microservices
Partner API → Partner BFF → Microservices
```

**Benefits**:
- ✅ Optimized responses per client
- ✅ Independent evolution of client experiences
- ✅ Reduced chatty communication

**When to use**: Multiple client types with different needs

### Sidecar Pattern
**Pattern**: Deploy helper container alongside main application container

**Common sidecars**:
- **Logging**: Fluentd/Fluent Bit for log aggregation
- **Monitoring**: Prometheus exporter
- **Security**: Istio Envoy proxy for mTLS
- **Configuration**: Consul agent for service discovery

**Benefits**:
- ✅ Separation of concerns
- ✅ Reusable sidecars across services
- ✅ Polyglot-friendly

**Kubernetes implementation**: Multiple containers in same pod

### Ambassador Pattern
**Pattern**: Proxy for external communication

**Use cases**:
- Connection pooling to databases
- Circuit breaking for external services
- Retry logic
- Monitoring/logging of external calls

**Example**: Ambassador container handles database connections, main app connects to localhost

### Adapter Pattern
**Pattern**: Standardize interface/output from heterogeneous systems

**Use cases**:
- Normalize log formats from different applications
- Translate metrics to common format (Prometheus)
- Protocol adaptation

## Data Management Patterns

### Database per Service
**Pattern**: Each microservice owns its database

**Benefits**:
- ✅ Service independence
- ✅ Technology flexibility (polyglot persistence)
- ✅ Clear ownership

**Challenges**:
- ❌ Data consistency (eventual consistency)
- ❌ Joins across services complex
- ❌ Transaction management

**Strategies**:
- Event-driven consistency (saga pattern)
- CQRS for queries across services
- API composition for reads

### Saga Pattern
**Pattern**: Manage distributed transactions across services

**Choreography approach**:
- Services publish events
- Other services react to events
- No central coordinator

**Orchestration approach**:
- Central orchestrator coordinates transaction
- Explicit compensation logic

**Example: Order processing saga**:
1. Create order (Order Service)
2. Reserve inventory (Inventory Service)
3. Process payment (Payment Service)
4. Ship order (Shipping Service)
5. If any step fails, compensate previous steps

### Event Sourcing
**Pattern**: Store state changes as sequence of events

**Benefits**:
- ✅ Complete audit trail
- ✅ Time travel (replay to any point)
- ✅ Event-driven architecture natural fit

**Challenges**:
- ❌ Complexity, learning curve
- ❌ Event schema evolution
- ❌ Eventual consistency

**When to use**: Audit requirements, complex domain logic, event-driven systems

### CQRS (Command Query Responsibility Segregation)
**Pattern**: Separate models for writes (commands) and reads (queries)

**Structure**:
- Write model: Optimized for consistency, business logic
- Read model: Optimized for queries, often denormalized

**Benefits**:
- ✅ Independent scaling of reads/writes
- ✅ Optimized query models
- ✅ Fits event sourcing well

**When to use**: Complex domains, high read-to-write ratio, event sourcing

## Resilience Patterns

### Circuit Breaker
**Pattern**: Prevent cascading failures by failing fast

**States**:
1. **Closed**: Normal operation, requests pass through
2. **Open**: Failures exceed threshold, requests immediately fail
3. **Half-open**: Test if service recovered, limited requests allowed

**Implementation**:
- Use Resilience4j (Java), Polly (.NET), or service mesh
- Configure failure threshold, timeout, half-open duration

**When to use**: Calls to external services, prevent resource exhaustion

### Retry with Backoff
**Pattern**: Retry failed requests with increasing delays

**Strategies**:
- **Linear backoff**: Fixed delay between retries
- **Exponential backoff**: Delay doubles each retry (1s, 2s, 4s, 8s)
- **Exponential backoff with jitter**: Add randomness to prevent thundering herd

**Best practices**:
- Limit retry attempts (3-5 typical)
- Only retry transient failures (5xx, timeouts, not 4xx)
- Use idempotency keys to prevent duplicate operations

### Bulkhead
**Pattern**: Isolate resources to limit blast radius

**Example**: Separate thread pools per downstream service
- Service A fails → Only its thread pool exhausted
- Service B unaffected, continues using its thread pool

**Kubernetes implementation**: Resource quotas, namespace separation

### Timeout
**Pattern**: Set maximum wait time for operations

**Best practices**:
- Set timeouts on all network calls
- Cascade timeouts (client timeout < server timeout)
- Use context propagation (Go context, OpenTelemetry)

### Fallback
**Pattern**: Provide alternative response when primary fails

**Strategies**:
- Return cached data
- Return default/degraded response
- Fail gracefully with user-friendly message

**Example**: Product recommendations fail → Show popular items instead

### Health Checks
**Pattern**: Expose endpoints for monitoring service health

**Types**:
- **Liveness**: Is service running? (restart if fails)
- **Readiness**: Can service handle requests? (remove from load balancer if fails)
- **Startup**: Has service finished initialization? (allow slow startup)

**Best practices**:
- Check critical dependencies (database, cache)
- Use shallow checks (fast, not resource intensive)
- Return 200 OK for healthy, 503 for unhealthy

## Observability Patterns

### Structured Logging
**Pattern**: Log events as structured data (JSON), not plain text

**Benefits**:
- ✅ Queryable, filterable logs
- ✅ Consistent format across services
- ✅ Easy to parse and analyze

**Example**:
```json
{
  "timestamp": "2024-01-15T10:30:00Z",
  "level": "ERROR",
  "service": "order-service",
  "trace_id": "abc123",
  "user_id": "user456",
  "message": "Payment processing failed",
  "error": "CardDeclined",
  "amount": 99.99
}
```

### Correlation IDs / Trace IDs
**Pattern**: Propagate unique ID across service calls

**Implementation**:
- Generate trace ID at entry point (API gateway)
- Pass in headers (X-Request-ID, traceparent)
- Log trace ID in all log messages
- Include in error responses

**Benefits**: Track requests across distributed system

### Distributed Tracing
**Pattern**: Track request flow across services

**Components**:
- **Trace**: End-to-end request flow
- **Span**: Single operation within trace
- **Context propagation**: Pass trace context between services

**Tools**:
- **Jaeger**: CNCF project, popular choice
- **Zipkin**: Twitter's original implementation
- **Tempo**: Grafana's backend (no index, cost-efficient)
- **AWS X-Ray**: AWS-native

### Metrics Aggregation
**Pattern**: Collect and aggregate metrics from all services

**RED method** (for services):
- **Rate**: Requests per second
- **Errors**: Failed requests per second
- **Duration**: Latency distribution

**USE method** (for resources):
- **Utilization**: % time resource busy
- **Saturation**: Queue depth, wait time
- **Errors**: Error count

**Tools**:
- **Prometheus + Grafana**: Open source standard
- **Datadog**: Commercial, full observability platform
- **New Relic**: APM + infrastructure monitoring
- **CloudWatch**: AWS-native

### Centralized Logging
**Pattern**: Aggregate logs from all services to central location

**Architecture**:
```
Services → Log Shipper (Fluentd/Fluent Bit) → Storage (Elasticsearch/Loki) → UI (Kibana/Grafana)
```

**Best practices**:
- Buffer logs locally during outages
- Sample high-volume logs if needed
- Set retention policies (30-90 days typical)
- Index critical fields for fast search
