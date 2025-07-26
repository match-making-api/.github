## Development Pattern: Domain-Driven Design with Hexagonal Architecture

### Architecture Overview

The project implements an **architecture based on Domain-Driven Design (DDD)** combined with elements of **Hexagonal Architecture (Ports and Adapters)** and **Clean Architecture**. This approach promotes high cohesion, low coupling, and facilitates code maintainability and testability.

### Organizational Structure

#### 1. **Layer Separation**

```
pkg/
├── common/          # Shared utilities and interfaces
├── domain/          # Pure business logic
└── infra/           # Infrastructure and external adapters
```

#### 2. **Domain Layer**
- **Domain Entities**: Represent core business concepts (Game, Lobby, Pairing, Parties, Schedules)
- **Value Objects**: Like `Criteria` in pairing, encapsulate specific business rules
- **Ports**: Interfaces that define contracts for external communication
  - `in/`: Input ports (commands, use cases)
  - `out/`: Output ports (repositories, external services)

#### 3. **Infrastructure Layer**
- **Adapters**: Concrete implementations of ports
- **Configuration**: Centralized configuration management
- **IoC Container**: Dependency injection using `golobby/container`
- **External Integrations**: Billing, Squad, IAM via gRPC

### Implemented Patterns

#### 1. **Dependency Injection (DI)**
```go
// Each module has its own container
func Inject(c container.Container) error {
    // Registration of module-specific dependencies
}
```

#### 2. **Repository Pattern**
```go
// Generic interface for search operations
type Searchable[T any] interface {
    GetByID(ctx context.Context, id uuid.UUID) (*T, error)
    Search(ctx context.Context, s Search) ([]*T, error)
    Compile(ctx context.Context, searchParams []SearchAggregation, resultOptions SearchResultOptions) (*Search, error)
}
```

#### 3. **Generic Base Services**
```go
// BaseQueryService provides common functionality
type BaseQueryService[T] struct {
    // Reusable generic implementation
}
```

#### 4. **Command Query Responsibility Segregation (CQRS)**
- Clear separation between commands (modification) and queries (consultation)
- Specific interfaces for each type of operation

### Technical Characteristics

#### 1. **Type Safety with Generics**
- Extensive use of Go generics for type safety
- Code duplication reduction through generic implementations

#### 2. **Context-Driven Operations**
- All operations receive `context.Context`
- Facilitates timeout, cancellation, and value propagation

#### 3. **Configuration Management**
- Centralized configuration via environment variables
- Use of `godotenv` for local development
- Environment-specific configuration separation

#### 4. **Microservices Communication**
- Communication via gRPC with external services
- Responsibility isolation between services

### Advantages of the Adopted Pattern

#### 1. **Testability**
- Injected dependencies facilitate mocking and unit testing
- Clear separation between business logic and infrastructure

#### 2. **Maintainability**
- Code organized by business domains
- Low coupling between modules
- Well-defined interfaces

#### 3. **Scalability**
- Modular architecture allows independent evolution
- Easy addition of new domains or adapters

#### 4. **Flexibility**
- Implementation swapping without affecting business logic
- Support for multiple environments and configurations

### Design Considerations

#### 1. **Bounded Contexts**
Each domain (`game`, `iam`, `lobbies`, `pairing`, `parties`, `schedules`) represents a bounded context with its own rules and entities.

#### 2. **Anti-Corruption Layer**
The infrastructure layer acts as a barrier that protects the domain from changes in external systems.

#### 3. **Single Responsibility**
Each module has a well-defined responsibility, following the SOLID principle.

### Conclusion

This development pattern demonstrates architectural maturity, combining modern practices of DDD, Clean Architecture, and Go-specific patterns. The structure promotes clean, testable, and evolutionary code, suitable for complex systems like matchmaking that require high availability and performance.

The project showcases best practices in:
- **Separation of Concerns**: Clear boundaries between business logic and infrastructure
- **Dependency Management**: Proper use of dependency injection containers
- **Type Safety**: Leveraging Go's type system and generics
- **Modularity**: Well-organized package structure that supports growth
- **External Integration**: Clean abstraction of external service dependencies