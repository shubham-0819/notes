# Angular Interview Topics

## Core Concepts & Architecture

### Components & Component Communication
- Smart vs Presentational components
- Component lifecycle hooks and their use cases
- Input/Output decorators and EventEmitter
- Content projection (ng-content)
- ViewChild/ViewChildren and ContentChild/ContentChildren
- Dynamic component loading
- Component inheritance patterns

### Change Detection
- Zone.js and how change detection works
- ChangeDetectionStrategy.OnPush and performance optimization
- Manual change detection triggering
- NgZone service and runOutsideAngular
- Handling large lists with trackBy
- Virtual scrolling for performance

### Dependency Injection & Services
- Hierarchical injector system
- Provider scopes and ModuleWithProviders
- useClass, useValue, useFactory, useExisting
- Resolution modifiers (@Optional, @Self, @SkipSelf)
- Tree-shakeable providers with providedIn
- Service lifecycle and initialization

### State Management
- NgRx architecture and core concepts
- Effects for side effects management
- Selectors and memoization
- Entity pattern for data management
- NGRX vs Services + BehaviorSubject
- State persistence strategies

### Routing & Navigation
- Route guards and resolvers
- Child routes and lazy loading
- Route parameters and data passing
- Navigation events and hooks
- Custom route reuse strategies
- Preloading strategies

## Advanced Topics

### Performance Optimization
- AOT compilation benefits
- Tree shaking and dead code elimination
- Lazy loading and preloading strategies
- Memory leak prevention
- Bundle size optimization
- Server-side rendering (Angular Universal)

### Testing Strategies
- Unit testing components and services
- Integration testing with TestBed
- E2E testing with Protractor/Cypress
- Mocking dependencies and HTTP requests
- Testing async operations
- Coverage and test organization

### Forms & Validation
- Template-driven vs Reactive forms
- Custom form controls
- Complex validation scenarios
- Dynamic form generation
- Form Arrays and nested forms
- Custom validators and async validation

### RxJS Integration
- Common operators and their use cases
- Subject types and behavior
- Error handling strategies
- Subscription management
- Hot vs Cold observables
- Custom operators

### Security
- XSS prevention and sanitization
- CSRF protection
- Content Security Policy
- Route guards for authentication
- Secure HTTP communication
- Common security pitfalls

## Best Practices

### Architecture & Organization
- Feature modules and shared modules
- Core module patterns
- Scalable folder structure
- Smart/dumb component pattern
- Service organization strategies
- State management patterns

### Error Handling
- Global error handling
- Error boundaries
- Retry strategies
- Error logging and monitoring
- User feedback patterns
- Graceful degradation

### Performance Patterns
- Change detection optimization
- Memory management
- Lazy loading strategies
- Caching mechanisms
- Bundle optimization
- Runtime performance

### Development Workflow
- Angular CLI usage
- Build configuration
- Environment management
- CI/CD considerations
- Version upgrade strategies
- Development tools and debugging

### Accessibility & Internationalization
- ARIA attributes and roles
- Keyboard navigation
- Screen reader compatibility
- i18n implementation
- RTL support
- Accessibility testing
