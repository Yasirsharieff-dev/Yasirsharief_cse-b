# System Architecture

## 1. Architecture principles

1. **API-first:** each capability has a defined interface.
2. **Loose coupling:** asynchronous events decouple producers and consumers.
3. **Stateless services:** application state is externalised to data services.
4. **Observable by default:** metrics, logs and traces are collected across services.
5. **Infrastructure as code:** deployment configuration is declarative and version-controlled.

## 2. Architecture planes

- **Application plane:** reference AI-enabled application and its services.
- **Platform plane:** gateway, event broker, model serving, data and observability.
- **Infrastructure plane:** containers, Kubernetes and CI/CD.

## 3. Component responsibilities

| Component | Responsibility |
|---|---|
| API gateway | Entry point for routing, authentication and rate limiting |
| Request service | Accepts and validates inference requests |
| Inference orchestrator | Coordinates synchronous inference and service calls |
| Event broker | Carries request/result events, retries and dead-letter messages |
| Inference worker | Processes asynchronous inference jobs |
| Model server | Exposes a stable model inference interface |
| Result service | Makes completed results available to clients |
| Data services | Persist requests, results and metadata; provide caching |
| Observability stack | Collects and visualises metrics, logs and traces |
| Kubernetes | Schedules services, supports health checks and scaling |
| CI/CD pipeline | Builds, tests and deploys versioned application artefacts |

## 4. Communication patterns

### Synchronous
Client → API gateway → request service → inference orchestrator → model server → response.

Use synchronous calls for low-latency request/response interactions. REST or gRPC are candidate protocols.

### Asynchronous
Request service → `inference.requests` topic → inference worker → model server → `inference.results` topic → result service/data store.

Use events for long-running or batched inference. The design includes retries, a dead-letter topic, correlation IDs and idempotent consumers. At-least-once delivery is assumed; duplicate delivery must be handled safely.

## 5. Model-serving integration

The application communicates with a model server through a stable inference API. A pluggable adapter is intended to reduce coupling to a specific serving runtime. Models may be loaded and kept warm within model-serving pods; the impact on cold-start and tail latency must be tested.

## 6. Scaling and resilience

- HPA may scale services based on CPU or request-related metrics.
- KEDA may scale workers using event-broker lag or other supported triggers.
- Readiness and liveness probes support health management.
- Replicas, anti-affinity and disruption budgets are candidates for availability.
- Retries, timeouts and dead-letter handling address transient and repeated failures.
- Controlled fault injection will be used during evaluation.

## 7. Security considerations

- Authenticate requests at the gateway.
- Keep secrets out of source code and use Kubernetes Secrets or an appropriate secret manager.
- Apply network policies between services.
- Use TLS for external traffic.
- Include container image scanning in CI.

This is a practical project security scope, not a formal security certification.
