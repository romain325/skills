# DevSecOps Practices and Security Integration

## Shift-Left Security Philosophy

**Core principle**: Integrate security early and throughout the development lifecycle, not as a final gate.

### Security by Phase

**Development**:
- IDE security plugins (real-time feedback)
- Pre-commit hooks (secrets detection, linting)
- Secure coding training

**Build**:
- SAST (Static Application Security Testing)
- Dependency scanning
- License compliance checks

**Test**:
- DAST (Dynamic Application Security Testing)
- API security testing
- Security regression tests

**Deploy**:
- Container image scanning
- Infrastructure as Code (IaC) security scanning
- Configuration validation

**Runtime**:
- RASP (Runtime Application Self-Protection)
- Runtime vulnerability detection
- Security monitoring and SIEM integration

## Security Scanning Tools

### SAST (Static Application Security Testing)

**SonarQube**:
- ✅ Comprehensive language support, code quality + security
- ❌ Resource intensive, requires server
- Best for: Teams wanting integrated quality + security

**Semgrep**:
- ✅ Fast, customizable rules, developer-friendly
- ❌ Fewer out-of-box rules than commercial tools
- Best for: Custom security patterns, CI/CD integration

**Checkmarx / Veracode**:
- ✅ Enterprise-grade, extensive vulnerability database
- ❌ Expensive, slower scans
- Best for: Regulated industries, compliance requirements

**CodeQL**:
- ✅ Deep semantic analysis, GitHub native
- ❌ Slower than other tools
- Best for: GitHub projects, complex vulnerability patterns

### Dependency Scanning

**Snyk**:
- ✅ Developer-friendly, automatic PRs, extensive database
- ❌ Cost scales with team size
- Best for: Fast feedback, automated remediation

**OWASP Dependency-Check**:
- ✅ Free, open source, CI/CD friendly
- ❌ More false positives than commercial tools
- Best for: Budget-conscious teams

**GitHub Dependabot**:
- ✅ Free for GitHub, automatic PRs
- ❌ Limited to GitHub ecosystem
- Best for: GitHub-hosted projects

**Trivy**:
- ✅ Fast, container + dependency scanning, open source
- ❌ Fewer features than specialized tools
- Best for: Container-first workflows

### Container Security

**Trivy**:
- ✅ Fast, easy to use, comprehensive scanning
- ❌ Limited policy enforcement
- Best for: CI/CD pipelines, developer workflows

**Aqua Security**:
- ✅ Runtime protection, comprehensive platform
- ❌ Enterprise pricing
- Best for: Large-scale container deployments

**Anchore**:
- ✅ Policy-based, open source option available
- ❌ Steeper learning curve
- Best for: Custom policy requirements

**Clair**:
- ✅ Open source, static analysis
- ❌ Less maintained than alternatives
- Best for: Registry integration

### DAST (Dynamic Application Security Testing)

**OWASP ZAP**:
- ✅ Free, open source, active community
- ❌ Manual configuration required
- Best for: Budget-conscious, active security testing

**Burp Suite**:
- ✅ Powerful, extensive plugin ecosystem
- ❌ Manual testing focus, expensive pro version
- Best for: Security professionals, penetration testing

**StackHawk**:
- ✅ Developer-friendly, CI/CD native
- ❌ Commercial only
- Best for: Automated DAST in pipelines

### Infrastructure as Code (IaC) Security

**Checkov**:
- ✅ Multi-platform (Terraform, K8s, Docker), open source
- ❌ Can be noisy with default policies
- Best for: Multi-tool IaC environments

**tfsec**:
- ✅ Fast, Terraform-focused, clear output
- ❌ Terraform only
- Best for: Terraform-heavy workflows

**Terrascan**:
- ✅ Policy as code, compliance frameworks
- ❌ Slower than tfsec
- Best for: Compliance-driven organizations

**CloudFormation Guard**:
- ✅ AWS native, policy-based
- ❌ AWS CloudFormation only
- Best for: AWS-only shops

## Secrets Management

### Secret Detection

**Tools**:
- **TruffleHog**: Deep git history scanning
- **GitLeaks**: Fast, regex-based detection
- **detect-secrets**: Baseline and ongoing detection

**Best practices**:
- Scan commits before push (pre-commit hook)
- Scan entire repository regularly
- Scan third-party dependencies
- Alert on detected secrets immediately

### Secret Storage Solutions

**HashiCorp Vault**:
- ✅ Dynamic secrets, audit logging, multi-cloud
- ❌ Operational complexity, learning curve
- Best for: Large organizations, dynamic secret needs

**AWS Secrets Manager**:
- ✅ Managed, automatic rotation, AWS native
- ❌ AWS lock-in, cost per secret
- Best for: AWS-centric architectures

**Azure Key Vault**:
- ✅ Managed, Azure integration, HSM support
- ❌ Azure lock-in
- Best for: Azure workloads

**GCP Secret Manager**:
- ✅ Managed, GCP integration, versioning
- ❌ GCP lock-in
- Best for: GCP workloads

**Doppler**:
- ✅ Developer-friendly, universal sync
- ❌ Commercial service
- Best for: Multi-cloud, developer experience priority

### Secret Rotation

**Rotation strategies**:
- **Automatic**: Secrets Manager handles rotation (AWS RDS, etc.)
- **Manual triggered**: Scheduled rotation via automation
- **On-demand**: Rotation after suspected compromise

**Rotation frequency**:
- Critical secrets: 30-90 days
- Service accounts: 90-180 days
- User credentials: Per policy (90 days common)
- Compromised secrets: Immediate

## Supply Chain Security

### Software Bill of Materials (SBOM)

**Generate SBOMs**:
- Use Syft, CycloneDX, or SPDX tools
- Include in artifact metadata
- Track dependencies across environments

**Verify SBOMs**:
- Compare against known vulnerabilities (CVE databases)
- Audit license compliance
- Track dependency provenance

### Artifact Signing and Verification

**Sigstore/Cosign**:
- Sign container images, binaries, SBOMs
- Keyless signing with OIDC
- Verify signatures before deployment

**Pattern**:
```bash
# Sign image
cosign sign my-image:tag

# Verify signature
cosign verify my-image:tag

# Admission controller enforces verification in K8s
```

### Provenance and Attestation

**SLSA Framework** (Supply-chain Levels for Software Artifacts):
- Level 1: Documentation of build process
- Level 2: Version control + build service
- Level 3: Source and build platform hardened
- Level 4: Hermetic builds + two-person review

**Implementation**:
- Use SLSA GitHub generator for attestations
- Store provenance with artifacts
- Verify provenance before deployment

## Compliance and Governance

### Common Frameworks

**SOC 2**:
- Focus: Security, availability, confidentiality
- Requirements: Access controls, monitoring, incident response
- Audit: Annual + continuous monitoring

**ISO 27001**:
- Focus: Information security management system (ISMS)
- Requirements: Risk assessment, security controls, documentation
- Audit: Annual surveillance + 3-year recertification

**PCI-DSS**:
- Focus: Payment card data protection
- Requirements: Network segmentation, encryption, access control
- Audit: Annual + quarterly scans

**HIPAA**:
- Focus: Healthcare data protection
- Requirements: Encryption, access logs, business associate agreements
- Audit: Self-assessment + periodic HHS audits

**GDPR**:
- Focus: Personal data protection (EU)
- Requirements: Consent, data minimization, right to deletion
- Enforcement: Fines up to 4% of revenue

### Policy as Code

**Open Policy Agent (OPA)**:
- Policy language: Rego
- Use cases: Kubernetes admission control, CI/CD gates, API authorization
- Benefits: Declarative, testable, version-controlled policies

**Example use cases**:
- Enforce resource limits on pods
- Require security labels on deployments
- Block privileged containers
- Validate Terraform plans against policies

**Kyverno** (Kubernetes-specific alternative):
- Policy language: YAML (simpler than Rego)
- Use cases: K8s admission control, validation, mutation
- Benefits: K8s-native, easier learning curve

## Vulnerability Management

### Vulnerability Lifecycle

1. **Detection**: Automated scanning (SAST, SCA, container scans)
2. **Triage**: Assess severity, exploitability, business impact
3. **Prioritization**: CVSS score + context (internet-facing, data access)
4. **Remediation**: Patch, upgrade, workaround, or accept risk
5. **Verification**: Re-scan to confirm fix
6. **Documentation**: Track in security register

### Prioritization Framework

**CVSS Score + Context**:
- Critical (9.0-10.0) + exploitable: Fix within 24-48 hours
- High (7.0-8.9) + production: Fix within 7 days
- Medium (4.0-6.9): Fix within 30 days
- Low (0.1-3.9): Fix opportunistically

**Consider**:
- Is vulnerability actively exploited? (Check CISA KEV)
- Is the vulnerable code path reachable?
- What data/systems are at risk?
- Is a workaround available?

### Patch Management

**Strategies**:
- **Automated patching**: Non-prod environments, OS security patches
- **Scheduled patching**: Production, monthly maintenance window
- **Emergency patching**: Critical vulnerabilities, out-of-band

**Best practices**:
- Test patches in staging before production
- Maintain rollback plan
- Monitor for patch-related issues
- Document exceptions and risk acceptances

## Security Monitoring and Incident Response

### Security Information and Event Management (SIEM)

**Key events to monitor**:
- Authentication failures (brute force attempts)
- Privilege escalation
- Unusual network traffic
- Configuration changes
- Data exfiltration indicators

**SIEM platforms**:
- **Splunk**: Enterprise, powerful, expensive
- **Elastic Stack**: Open source, flexible, DIY
- **Datadog Security**: Cloud-native, modern UX
- **AWS GuardDuty**: AWS-native threat detection

### Incident Response Phases

1. **Preparation**: Runbooks, contact lists, access to tools
2. **Detection**: Alerts, anomaly detection, threat intelligence
3. **Containment**: Isolate affected systems, prevent spread
4. **Eradication**: Remove threat, patch vulnerabilities
5. **Recovery**: Restore services, verify integrity
6. **Lessons Learned**: Post-mortem, update procedures

### Security Metrics

**Leading indicators**:
- Vulnerabilities detected per scan
- Mean time to patch (MTTP)
- Security training completion rate
- Security issues found in code review

**Lagging indicators**:
- Security incidents per quarter
- Mean time to detect (MTTD)
- Mean time to respond (MTTR)
- Compliance audit findings

## Zero Trust Architecture

### Core Principles

1. **Never trust, always verify**: No implicit trust based on network location
2. **Least privilege access**: Minimal permissions necessary
3. **Assume breach**: Design assuming attackers are inside
4. **Verify explicitly**: Authenticate and authorize every request

### Implementation Components

**Identity and Access Management (IAM)**:
- Strong authentication (MFA required)
- Just-in-time (JIT) access provisioning
- Regular access reviews
- Service account management

**Network Segmentation**:
- Micro-segmentation
- Network policies (Kubernetes, cloud security groups)
- Private networks for sensitive services

**End-to-end Encryption**:
- TLS for all communication
- Mutual TLS (mTLS) for service-to-service
- Encryption at rest for data storage

**Continuous Verification**:
- Real-time risk assessment
- Device posture checks
- Behavioral analytics
- Adaptive authentication

### Zero Trust Network Access (ZTNA)

**Traditional VPN vs ZTNA**:
- VPN: Network-level access (overly broad)
- ZTNA: Application-level access (least privilege)

**ZTNA solutions**:
- **BeyondCorp** (Google): Identity-aware proxy
- **Cloudflare Access**: Zero Trust network access
- **Zscaler Private Access**: Cloud-native ZTNA
- **Tailscale**: WireGuard-based mesh network
