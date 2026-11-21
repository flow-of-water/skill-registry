# Architecture checklist

Apply only the style the repo actually uses. Detect it first (folder names, imports, neighbor files).

## DDD / Clean / Hexagonal

Signals: `domain/`, `application/`, `infrastructure/`, `interfaces/`, `ports/`, `adapters/`, aggregates, value objects, repositories.

- Dependencies point inward only. The domain layer must not import infrastructure, framework, ORM or HTTP types.
- Business rules live in entities, aggregates and domain services, not in controllers, repositories or DTOs.
- Avoid anemic domain models if the repo uses rich ones.
- Value objects are immutable and validated on creation.
- Repository interfaces live in the domain/application layer; implementations in infrastructure.
- Aggregate boundaries are respected: no direct modification of inner entities from outside the root; other aggregates are referenced by ID.
- Application services orchestrate and do not hold business logic; transactions are managed at that layer.
- Domain events are raised by the domain and handled outside it.
- Mapping between layers (DTO ↔ domain) happens at the boundary, not inside the domain.

## Layered MVC (Rails, Laravel, Spring, Express, Django, ...)

- Controllers/routes are thin: parse input, call a service, shape output.
- Services hold business logic; no data access in controllers or views.
- No cross-layer leaks (e.g. ORM entities returned directly from public APIs, HTTP request objects passed into services).
- Validation happens at the boundary using the framework's mechanism (form requests, schemas, DTOs).

## Scripts / modules (including VBA)

- Follow the existing module structure and naming conventions.
- Shared helpers go in the shared module instead of being copy-pasted.

## Placement

- A new module or file sits where the architecture says it belongs, with names matching its neighbors.
- New dependencies (libraries, services) are justified, and consistent with what the repo already uses for the same purpose.
