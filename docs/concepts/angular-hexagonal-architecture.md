# Angular Hexagonal Architecture

Hexagonal Architecture, also known as Ports and Adapters pattern or Clean Architecture, is an architectural pattern that allows an application to be equally driven by users, programs, automated tests, or batch scripts, and to be developed and tested in isolation from its eventual run-time devices and databases.

<iframe width="560" height="315" src="https://www.youtube.com/embed/nNIUTBHaiG8?si=oslTy6pmbodXX-PF" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Core Concepts

The hexagonal architecture pattern is based on three main principles:

1. **Explicit separation of concerns** through layered architecture
2. **Dependencies pointing inwards** - outer layers depend on inner layers
3. **Isolation of business rules** in the domain layer

## Architecture Layers

### 1. Domain Layer (Core)

The innermost layer containing:

- Business logic
- Domain models/entities 
- Domain services
- Interfaces (ports) that define how the domain interacts with outside layers

### 2. Application Layer

The layer that orchestrates the flow of data between the outer layers and the domain:

- Use cases/application services
- DTOs (Data Transfer Objects)
- Interface adapters
- State management

### 3. Infrastructure Layer

The outermost layer handling external concerns:

- API clients
- Database adapters
- Framework-specific code
- UI Components

## Implementation in Angular

### Project Structure

A typical Angular hexagonal architecture project structure:

```
src/
├── app/
│   ├── core/              # Domain layer
│   │   ├── models/        # Domain entities
│   │   ├── ports/         # Interface definitions
│   │   └── services/      # Domain services
│   │
│   ├── application/       # Application layer
│   │   ├── services/      # Use cases
│   │   ├── state/         # State management
│   │   └── facades/       # Interface adapters
│   │
│   ├── infrastructure/    # Infrastructure layer
│   │   ├── api/          # API clients
│   │   ├── storage/      # Storage adapters
│   │   └── ui/           # UI components
│   │
│   └── shared/           # Shared utilities and components
```

### Example Implementation

1. Domain Layer (Core):

```typescript
// core/ports/user.port.ts
export interface IUserPort {
  getUser(id: string): Promise<User>;
  updateUser(user: User): Promise<void>;
}

// core/models/user.model.ts
export class User {
  constructor(
    public id: string,
    public name: string,
    public email: string
  ) {}
}

// core/services/user.service.ts
export class UserService {
  constructor(private userPort: IUserPort) {}

  async updateUserProfile(userId: string, name: string): Promise<void> {
    const user = await this.userPort.getUser(userId);
    user.name = name;
    await this.userPort.updateUser(user);
  }
}
```

2. Application Layer:

```typescript
// application/services/user-facade.service.ts
@Injectable()
export class UserFacadeService {
  constructor(private userService: UserService) {}

  async updateProfile(userId: string, name: string): Promise<void> {
    try {
      await this.userService.updateUserProfile(userId, name);
    } catch (error) {
      // Handle application-level errors
    }
  }
}
```

3. Infrastructure Layer:

```typescript
// infrastructure/api/user-api.adapter.ts
@Injectable()
export class UserApiAdapter implements IUserPort {
  constructor(private http: HttpClient) {}

  async getUser(id: string): Promise<User> {
    const response = await this.http.get(`/api/users/${id}`).toPromise();
    return new User(response.id, response.name, response.email);
  }

  async updateUser(user: User): Promise<void> {
    await this.http.put(`/api/users/${user.id}`, user).toPromise();
  }
}
```

## Benefits and Advantages

1. **Framework Independence**
   - Business logic is isolated from Angular framework
   - Easier to migrate to new versions or different frameworks
   - Core domain can be shared between different applications

2. **Testability**
   - Domain logic can be tested without UI or external dependencies
   - Easy to mock external dependencies through ports
   - Clear separation makes unit testing simpler

3. **Maintainability**
   - Clear boundaries between layers
   - Changes in one layer don't affect others
   - Easier to understand and modify business logic

4. **Scalability**
   - New features can be added without modifying existing code
   - Easy to add new adapters for different data sources
   - Multiple UI implementations can share same domain logic

## Challenges and Considerations

1. **Initial Complexity**
   - More boilerplate code compared to traditional approaches
   - Steeper learning curve for team members
   - Additional planning required for layer separation

2. **Development Overhead**
   - Need to maintain strict boundaries between layers
   - More interfaces and abstractions to manage
   - May seem over-engineered for simple applications

3. **Performance Considerations**
   - Additional layers may impact performance
   - Need to carefully manage state across layers
   - More complex dependency injection setup

## Best Practices

1. **Layer Isolation**
   - Keep domain logic pure and framework-agnostic
   - Use interfaces (ports) to define layer boundaries
   - Avoid circular dependencies between layers

2. **State Management**
   - Use facades to abstract state management
   - Keep state close to where it's needed
   - Consider using observable patterns for reactivity

3. **Testing Strategy**
   - Write unit tests for domain logic first
   - Use test doubles (mocks/stubs) for external dependencies
   - Integration tests for adapter implementations

4. **Error Handling**
   - Define domain-specific errors in core layer
   - Transform technical errors to domain errors in adapters
   - Handle UI-specific error presentation in infrastructure layer

## When to Use

Hexagonal Architecture is particularly beneficial for:

- Large enterprise applications
- Applications with complex business logic
- Systems requiring high maintainability
- Projects expecting framework migrations
- Applications needing multiple UI implementations

For simpler applications or quick prototypes, a traditional layered architecture might be more appropriate.
