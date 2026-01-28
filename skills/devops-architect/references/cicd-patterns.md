# CI/CD Patterns and Best Practices

## Pipeline Architecture Patterns

### 1. Trunk-Based Development
**Pattern**: Single main branch, short-lived feature branches, frequent integration
**When to use**: Teams with strong testing culture, need for rapid deployment
**Tradeoffs**:
- ✅ Fast feedback, simple branching model, reduced merge conflicts
- ❌ Requires mature testing automation, feature flags for incomplete work

### 2. GitFlow
**Pattern**: Multiple long-lived branches (main, develop, release, hotfix)
**When to use**: Scheduled releases, multiple production versions
**Tradeoffs**:
- ✅ Clear release process, parallel development tracks
- ❌ Complex merging, slower integration, merge conflict risk

### 3. Environment Promotion Pipeline
**Pattern**: Code progresses through dev → staging → prod environments
**When to use**: Need for environment parity testing, compliance requirements
**Tradeoffs**:
- ✅ Validates in prod-like environments, clear promotion path
- ❌ Slower deployment, resource overhead for multiple environments

### 4. Progressive Delivery
**Pattern**: Canary, blue-green, or feature-flag-based gradual rollouts
**When to use**: High-traffic systems, risk-averse deployments
**Tradeoffs**:
- ✅ Risk mitigation, quick rollback, A/B testing capability
- ❌ Infrastructure complexity, monitoring requirements

## Quality Gates

### Essential Gates
1. **Static Analysis**: Linting, code standards, security scanning (SAST)
2. **Unit Tests**: 70%+ coverage threshold, fast execution (<5min)
3. **Integration Tests**: API contracts, database migrations, service interactions
4. **Security Scans**: Dependency vulnerabilities, secrets detection, SAST/DAST
5. **Performance Tests**: Load testing for critical paths (optional gate)

### Gate Placement Strategy
- **Pre-commit**: Linting, formatting (git hooks)
- **PR/Merge**: Unit tests, static analysis, security scans (blocking)
- **Post-merge**: Integration tests, E2E tests (blocking or advisory)
- **Pre-production**: Performance tests, security scans (blocking)
- **Post-deployment**: Smoke tests, health checks (automated rollback trigger)

## Build Strategies

### Monorepo vs Polyrepo
**Monorepo**:
- ✅ Atomic cross-project changes, unified tooling, easier refactoring
- ❌ Larger checkouts, CI complexity, access control challenges
- Best for: Tightly coupled services, shared libraries, small-medium teams

**Polyrepo**:
- ✅ Independent deployment cycles, clear ownership, simpler CI
- ❌ Dependency management overhead, code duplication risk
- Best for: Microservices, multiple teams, independent release cycles

### Artifact Management
- **Container images**: Tag with git SHA + semantic version
- **Versioning**: SemVer for libraries, CalVer for applications
- **Retention**: Keep production artifacts indefinitely, prune dev/feature builds after 30d
- **Scanning**: Scan artifacts for vulnerabilities before promotion

## Deployment Strategies

### Blue-Green Deployment
**How**: Maintain two identical environments, switch traffic atomically
**When**: Zero-downtime requirement, easy rollback needed
**Considerations**: 2x infrastructure cost, database migration complexity

### Canary Deployment
**How**: Route small percentage of traffic to new version, gradually increase
**When**: High-risk changes, need for real-world validation
**Considerations**: Requires traffic splitting, robust monitoring, rollback automation

### Rolling Deployment
**How**: Gradually replace instances with new version
**When**: Kubernetes/cloud-native, backward-compatible changes
**Considerations**: Mixed version state during rollout, requires health checks

### Recreate
**How**: Stop all instances, deploy new version, start instances
**When**: Development environments, incompatible version changes
**Considerations**: Downtime required, simplest approach

## CI/CD Tool Selection Criteria

### GitHub Actions
- ✅ Native GitHub integration, generous free tier, marketplace ecosystem
- ❌ Limited self-hosted runner management, YAML complexity for advanced workflows
- Best for: GitHub-hosted projects, cloud-native workflows

### GitLab CI/CD
- ✅ Integrated platform, excellent Kubernetes support, powerful pipeline features
- ❌ Resource intensive self-hosted, steeper learning curve
- Best for: Self-hosted needs, Kubernetes deployments, security-conscious teams

### Jenkins
- ✅ Mature ecosystem, highly customizable, extensive plugin library
- ❌ Maintenance overhead, UI/UX dated, security management burden
- Best for: Complex enterprise needs, existing Jenkins investment

### CircleCI
- ✅ Fast execution, excellent caching, simple configuration
- ❌ Cost scales quickly, limited self-hosted options
- Best for: Startups, SaaS preference, need for speed

### Azure DevOps
- ✅ Microsoft ecosystem integration, mature enterprise features
- ❌ Complex pricing, less intuitive for non-Microsoft stacks
- Best for: Azure-hosted, .NET projects, enterprise Microsoft shops

## Testing Strategy

### Test Pyramid
```
    E2E (5%)         ← Few, slow, brittle
   ╱────────╲
  Integration (15%) ← Moderate, service boundaries
 ╱──────────╲
Unit Tests (80%)    ← Many, fast, isolated
```

### Test Types by Purpose
- **Unit**: Business logic, algorithms, transformations
- **Integration**: Database queries, API calls, message queues
- **Contract**: API schemas, event formats (consumer-driven)
- **E2E**: Critical user journeys only
- **Performance**: Load, stress, spike testing
- **Security**: Penetration, fuzzing, vulnerability scanning

### Test Data Management
- **Synthetic data**: Generate realistic data for non-prod
- **Production snapshots**: Anonymize/mask sensitive data
- **Test fixtures**: Version-controlled seed data
- **Database per test**: Isolated state, parallel execution

## Monitoring and Observability

### Three Pillars
1. **Metrics**: Quantitative measurements (request rate, latency, error rate)
2. **Logs**: Event records with context (structured JSON preferred)
3. **Traces**: Request flow across services (distributed tracing)

### Key Metrics (Four Golden Signals)
1. **Latency**: Response time distribution (p50, p95, p99)
2. **Traffic**: Requests per second
3. **Errors**: Error rate and types
4. **Saturation**: Resource utilization (CPU, memory, disk)

### Alerting Strategy
- **SLO-based**: Alert on Service Level Objective breaches
- **Symptom-based**: Alert on user impact, not internal metrics
- **Actionable**: Every alert requires clear remediation action
- **Avoid noise**: Tune thresholds to prevent alert fatigue
