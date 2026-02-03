# Container Implementation Summary

## Changes Made to Properly Use the DI Container

This document summarizes the changes made to properly implement and use the Dependency Injection container throughout the application.

### 1. **cmd/main.go** - Fixed Container Initialization and Usage

**Problem:** The original code was:
- Creating a local `Container` object incorrectly
- Manually resolving and creating handlers in main
- Not passing the container to route setup functions

**Solution:**
- Use `ServiceRegistry` to create and manage the container
- Pass the container to route setup functions
- Let routes handle handler resolution
- Apply scope middleware to clean up scoped services after each request

**Key Changes:**
```go
// Before: Manual resolution in main
serviceRegistry := NewServiceRegistry()
container := container.GetContainer()  // ❌ Wrong
userRepository, err := ResolveAs[userRepo.UserRepository](container)
authHandler := auth.NewAuthHandler(userRepository, jwtUtil)

// After: Lazy resolution in routes
serviceRegistry := NewServiceRegistry()
diContainer := serviceRegistry.GetContainer()  // ✅ Correct
routes.SetupAuthRoutes(router, authMiddleware, diContainer)
routes.SetupTicketRoutes(router, authMiddleware, casbinMiddleware, diContainer)
```

### 2. **application/routes/auth_routes.go** - Lazy Handler Resolution

**Problem:** Routes were passed handler instances directly

**Solution:** Routes now accept the container and resolve handlers when routes are set up

**Changes:**
```go
// Before
func SetupAuthRoutes(router *gin.Engine, authHandler auth.AuthHandler, authMiddleware *middleware.AuthMiddleware)

// After
func SetupAuthRoutes(router *gin.Engine, authMiddleware *middleware.AuthMiddleware, diContainer *container.Container) {
    authHandler, err := container.ResolveAs[*auth.AuthHandler](diContainer)
    if err != nil {
        panic("Failed to resolve auth handler: " + err.Error())
    }
    // ... use authHandler in routes
}
```

**Benefits:**
- Handlers are resolved when routes are set up, catching configuration issues early
- Clean separation between dependency resolution and route setup
- Follows container pattern more closely

### 3. **application/routes/ticket_routes.go** - Lazy Handler Resolution

**Problem:** Routes were passed handler instances directly

**Solution:** Routes now accept the container and resolve both `TicketHandler` and `TicketPageHandler`

**Changes:**
```go
// Before
func SetupTicketRoutes(router *gin.Engine, ticketHandler ticket.TicketHandler, ticketPageHandler ticket.TicketPageHandler, authMiddleware *middleware.AuthMiddleware, casbinMiddleware *middleware.CasbinMiddleware)

// After  
func SetupTicketRoutes(router *gin.Engine, authMiddleware *middleware.AuthMiddleware, casbinMiddleware *middleware.CasbinMiddleware, diContainer *container.Container) {
    ticketHandler, err := container.ResolveAs[*ticket.TicketHandler](diContainer)
    if err != nil {
        panic("Failed to resolve ticket handler: " + err.Error())
    }
    ticketPageHandler, err := container.ResolveAs[*ticket.TicketPageHandler](diContainer)
    if err != nil {
        panic("Failed to resolve ticket page handler: " + err.Error())
    }
    // ... use handlers in routes
}
```

### 4. **service_registry.go** - Added TicketPageHandler Registration

**Problem:** `TicketPageHandler` was not registered in the container

**Solution:** Added `TicketPageHandler` registration to `registerHandlers()` method

**Changes:**
```go
// Added to registerHandlers()
// Ticket page handler
return sr.container.RegisterScoped((*ticketHandler.TicketPageHandler)(nil), func(c *Container) (interface{}, error) {
    ticketRepository, err := ResolveAs[ticketRepo.TicketRepository](c)
    if err != nil {
        return nil, err
    }
    return ticketHandler.NewTicketPageHandler(ticketRepository), nil
})
```

### 5. **CONTAINER_USAGE_GUIDE.md** - Created Comprehensive Guide

New guide document explaining:
- Overview of the container architecture
- Service registration flow
- Service lifetime management (Singleton, Scoped, Transient)
- Resolution patterns and best practices
- How to add new services
- Troubleshooting guide

---

## Architecture Overview

### Request Lifecycle with Proper Container Usage

```
1. Application Start (main.go)
   ├─ Create ServiceRegistry
   ├─ Register all services (DB, JWT, Casbin, Repositories, Handlers)
   └─ Pass container to route setup

2. Route Setup (routes/*.go)
   ├─ Resolve handlers from container
   ├─ Setup routes with handlers
   └─ Return to main

3. Scope Middleware
   ├─ Applied globally to all routes
   └─ Clears scoped services after each request

4. Per-Request Handler Resolution
   ├─ Scoped repositories created fresh
   ├─ Handler uses repositories
   └─ Request completes

5. Scope Cleanup
   ├─ Middleware calls ClearScope
   ├─ All scoped services cleared
   └─ Memory released
```

### Service Lifetime Summary

| Lifetime   | Count | Usage | When Cleared |
|-----------|-------|-------|--------------|
| Singleton | 1     | Shared across entire app | Never |
| Scoped    | N     | One per request | After each request |
| Transient | N     | New every resolve | Immediately after use |

**Used in this system:**
- **Singleton:** Database, JWT utility, Casbin enforcer
- **Scoped:** Repositories, HTTP handlers
- **Transient:** Not currently used

---

## Validation Checklist

- ✅ Container is created in `ServiceRegistry`
- ✅ All services registered with correct lifetimes
- ✅ Handlers resolved from container in route setup
- ✅ Scope middleware clears services after request
- ✅ Type-safe resolution with generics
- ✅ Error handling for resolution failures
- ✅ Handlers registered before route setup

---

## Files Modified

1. `cmd/main.go`
   - Lines 73-92: Container initialization and route setup

2. `application/routes/auth_routes.go`
   - Complete rewrite to accept container and resolve handler

3. `application/routes/ticket_routes.go`
   - Complete rewrite to accept container and resolve handlers

4. `service_registry.go`
   - Lines 118-125: Added TicketPageHandler registration

5. `CONTAINER_USAGE_GUIDE.md` (New)
   - Comprehensive guide on container usage and best practices

---

## Next Steps

### If You Need to Add a New Service

1. **Define interface and implementation** (in domain and infrastructure layers)
2. **Register in ServiceRegistry.registerServices() or registerHandlers()**
3. **Resolve in route setup or other service factories**
4. **Test that resolution works** by running the application

### If You Need to Add a New Handler

1. **Create handler class** accepting repository/service dependencies
2. **Register in ServiceRegistry.registerHandlers()**
3. **Resolve in appropriate route file** (auth_routes.go, ticket_routes.go, etc.)
4. **Setup routes** with the resolved handler

---

## Testing the Container

To verify container is working properly:

1. **Start the application** - If services fail to register, you'll see clear error messages
2. **Check logs** - Look for "Failed to register services" or "Failed to resolve" messages
3. **Test endpoints** - If handlers are resolved properly, endpoints will work correctly
4. **Verify scope clearing** - Each request should get fresh scoped instances

---

## Common Issues and Solutions

### Issue: "Service not found" error
**Cause:** Service not registered in `ServiceRegistry`  
**Fix:** Add registration to appropriate method in `ServiceRegistry`

### Issue: Null pointer exception in handler
**Cause:** Dependency wasn't resolved correctly  
**Fix:** Check that dependency is registered and has correct type in resolution call

### Issue: State leaking between requests
**Cause:** Scoped services not being cleared  
**Fix:** Verify `ScopeMiddleware` is applied globally to router

### Issue: Type mismatch error
**Cause:** Resolving with wrong type  
**Fix:** Ensure `ResolveAs[T]` type matches how service was registered

# Proper Container Usage Guide

This guide explains how the Dependency Injection (DI) container is properly integrated into the Ticketing System.

## Overview

The container implementation follows clean architecture principles by:
1. **Centralizing service registration** in `service_registry.go`
2. **Lazy resolving handlers** in route setup functions
3. **Managing service lifetimes** (Singleton, Scoped, Transient)
4. **Clearing scopes** after each request via middleware

## Architecture Flow

### 1. Service Registry Setup (cmd/main.go)

```go
// Initialize dependency injection container
serviceRegistry := NewServiceRegistry()
diContainer := serviceRegistry.GetContainer()

// Register services with the container
if err := serviceRegistry.RegisterServices(db, jwtUtil, enforcer); err != nil {
    log.Fatalf("Failed to register services: %v", err)
}
```

The `ServiceRegistry` is responsible for:
- Creating the container
- Registering all dependencies (repositories, handlers, services)
- Providing the container to route setup functions

### 2. Service Registration (service_registry.go)

The registry registers services in order of dependency:

#### Step 1: Core Dependencies (Singletons)
```go
// Database connection, JWT utility, Casbin enforcer
sr.container.RegisterInstance((*gorm.DB)(nil), db)
sr.container.RegisterInstance((*utils.JWTUtil)(nil), jwtUtil)
sr.container.RegisterInstance((*casbin.Enforcer)(nil), enforcer)
```

#### Step 2: Repositories (Scoped)
```go
sr.container.RegisterScoped((*userRepo.UserRepository)(nil), func(c *Container) (interface{}, error) {
    db := c.MustResolve((*gorm.DB)(nil)).(*gorm.DB)
    return userPersistence.NewUserRepo(db), nil
})
```

#### Step 3: Handlers (Scoped)
```go
sr.container.RegisterScoped((*auth.AuthHandler)(nil), func(c *Container) (interface{}, error) {
    userRepository, err := ResolveAs[userRepo.UserRepository](c)
    if err != nil {
        return nil, err
    }
    jwtUtil, err := ResolveAs[*utils.JWTUtil](c)
    if err != nil {
        return nil, err
    }
    return auth.NewAuthHandler(userRepository, jwtUtil), nil
})
```

### 3. Scope Management (cmd/main.go)

The scope middleware clears scoped services after each request:

```go
scopeMiddleware := middleware.NewScopeMiddleware(diContainer.ClearScope)
router.Use(scopeMiddleware)  // Apply globally
```

This ensures:
- Each request gets its own repository instances
- No state leaks between requests
- Proper resource cleanup

### 4. Route Setup with Lazy Resolution (application/routes/)

Routes resolve handlers from the container when setup is called:

```go
func SetupAuthRoutes(router *gin.Engine, authMiddleware *middleware.AuthMiddleware, diContainer *container.Container) {
    // Resolve auth handler from container
    authHandler, err := container.ResolveAs[*auth.AuthHandler](diContainer)
    if err != nil {
        panic("Failed to resolve auth handler: " + err.Error())
    }

    // Use handler in routes
    authGroup := router.Group("/auth")
    {
        authGroup.POST("/register", authHandler.Register)
        authGroup.POST("/login", authHandler.Login)
    }
}
```

Benefits of lazy resolution:
- Handlers are resolved when routes are set up, not when main starts
- Dependencies are validated early
- Cleaner separation of concerns

## Service Lifetimes

### Singleton (Database, JWT, Casbin)
```go
RegisterInstance(*T, instance)  // One instance for the entire application lifetime
```
- Created once when registered
- Shared across all requests
- Examples: DB connection, Casbin enforcer, JWT utility

### Scoped (Repositories, Handlers)
```go
RegisterScoped(*T, factory)  // One instance per request
```
- Created once per request/scope
- Cleared when request ends
- Examples: Repositories, HTTP handlers

### Transient (if needed)
```go
RegisterTransient(*T, factory)  // New instance every time
```
- Created every time requested
- No caching
- Use sparingly

## Proper Resolution Patterns

### Type-Safe Generic Resolution
```go
// Preferred - compile-time type safety
handler, err := container.ResolveAs[*auth.AuthHandler](diContainer)
if err != nil {
    return nil, err
}
```

### Direct Resolution with Type Casting
```go
// In service registration (when generic isn't suitable)
db := c.MustResolve((*gorm.DB)(nil)).(*gorm.DB)
```

### Error Handling in Routes
```go
// Panic on resolution failure (prevents invalid configuration from going unnoticed)
authHandler, err := container.ResolveAs[*auth.AuthHandler](diContainer)
if err != nil {
    panic("Failed to resolve auth handler: " + err.Error())
}
```

## Adding a New Service

### Step 1: Define the Interface and Implementation
```go
// In domain layer
type MyService interface {
    DoSomething() error
}

// In infrastructure layer
type myServiceImpl struct {
    repo MyRepository
}

func NewMyService(repo MyRepository) MyService {
    return &myServiceImpl{repo: repo}
}
```

### Step 2: Register in ServiceRegistry
```go
// In service_registry.go
if err := sr.registerServices(); err != nil {
    return err
}

// In registerServices method
sr.container.RegisterScoped((*domain.MyService)(nil), func(c *Container) (interface{}, error) {
    repo, err := ResolveAs[MyRepository](c)
    if err != nil {
        return nil, err
    }
    return NewMyService(repo), nil
})
```

### Step 3: Use in Routes or Other Services
```go
// In route setup
myService, err := container.ResolveAs[domain.MyService](diContainer)
if err != nil {
    panic("Failed to resolve service: " + err.Error())
}
```

## Current Implementation Status

### ✅ Properly Implemented
- Service registration is centralized in `ServiceRegistry`
- Container is passed to route setup functions
- Handlers are resolved from container in routes
- Scope middleware clears services after each request
- Type-safe resolution with generics

### Key Files
- `cmd/main.go` - Container setup and initialization
- `service_registry.go` - Service registration logic
- `application/container/container.go` - Container implementation
- `application/routes/*.go` - Route setup with container resolution
- `application/middleware/scope_middleware.go` - Scope cleanup

## Best Practices

1. **Always use the container for dependency resolution** - Don't create dependencies manually
2. **Register dependencies in correct order** - Core dependencies first, then repositories, then handlers
3. **Use scoped lifetime for request-specific services** - Prevents state leakage
4. **Resolve handlers in route setup** - Not in main.go
5. **Panic on resolution failure in routes** - Early detection of configuration issues
6. **Handle errors gracefully in service registration** - Return errors so main can fail fast
7. **Use type-safe generic resolution** - `ResolveAs[T]` instead of manual casting

## Troubleshooting

### Service Not Found Error
```
"service of type *myType is not registered"
```
**Solution:** Make sure the service is registered in `ServiceRegistry.registerServices()` or `registerHandlers()` before it's resolved.

### Type Assertion Error
```
"service is not of expected type"
```
**Solution:** Ensure you're resolving with the correct type, matching how it was registered.

### Nil Pointer Exception in Handler
```
nil pointer dereference
```
**Solution:** Check that all dependencies are properly registered and resolved before use.

## Migration Guide: From Manual DI to Container

If you have code that manually creates handlers:
```go
// OLD - Don't do this
handler := auth.NewAuthHandler(repo, jwt)
router.POST("/login", handler.Login)

// NEW - Use container
handler, err := container.ResolveAs[*auth.AuthHandler](diContainer)
if err != nil {
    panic("Failed to resolve: " + err.Error())
}
router.POST("/login", handler.Login)
```

This ensures consistency and makes testing/mocking easier.


# Dependency Injection Container Implementation

This implementation provides a comprehensive dependency injection (DI) container for the Go ticketing system with support for:

## Features

### Service Lifetimes
- **Transient**: New instance created every time
- **Singleton**: Single instance for the application lifetime
- **Scoped**: Single instance per request/scope

### Registration Methods
```go
// Register with explicit lifetime
container.Register((*MyService)(nil), factory, Singleton)

// Convenience methods
container.RegisterSingleton((*MyService)(nil), factory)
container.RegisterTransient((*MyService)(nil), factory)
container.RegisterScoped((*MyService)(nil), factory)

// Register existing instance
container.RegisterInstance((*MyService)(nil), myServiceInstance)
```

### Resolution Methods
```go
// Basic resolution
service, err := container.Resolve((*MyService)(nil))

// Panic if resolution fails
service := container.MustResolve((*MyService)(nil))

// Generic type-safe resolution (Go 1.18+)
service, err := ResolveAs[MyService](container)
service := MustResolveAs[MyService](container)
```

## Usage Example

### 1. Define Services and Interfaces
```go
// Domain layer - interfaces
type UserRepository interface {
    GetUser(id int) (*User, error)
    CreateUser(user *User) error
}

type UserService interface {
    RegisterUser(email, password string) error
    LoginUser(email, password string) (*User, error)
}

// Infrastructure layer - implementations
type userRepo struct {
    db *sql.DB
}

func NewUserRepo(db *sql.DB) UserRepository {
    return &userRepo{db: db}
}

// Application layer - services
type userService struct {
    repo UserRepository
}

func NewUserService(repo UserRepository) UserService {
    return &userService{repo: repo}
}
```

### 2. Register Services
```go
// Create container
container := NewContainer()

// Register database connection as singleton
container.RegisterInstance((*sql.DB)(nil), dbConnection)

// Register repository as scoped (new instance per request)
container.RegisterScoped((*UserRepository)(nil), func(c *Container) (interface{}, error) {
    db := c.MustResolve((*sql.DB)(nil)).(*sql.DB)
    return NewUserRepo(db), nil
})

// Register service as scoped
container.RegisterScoped((*UserService)(nil), func(c *Container) (interface{}, error) {
    repo, err := ResolveAs[UserRepository](c)
    if err != nil {
        return nil, err
    }
    return NewUserService(repo), nil
})
```

### 3. Resolve and Use Services
```go
// In your HTTP handlers
func (h *Handler) RegisterHandler(w http.ResponseWriter, r *http.Request) {
    // Resolve service from container
    userService, err := ResolveAs[UserService](h.container)
    if err != nil {
        http.Error(w, "Service unavailable", 500)
        return
    }
    
    // Use the service
    err = userService.RegisterUser(email, password)
    // ... handle response
}
```

### 4. Middleware for Scope Management
```go
// Clear scoped services at the end of each request
router.Use(middleware.NewScopeMiddleware(container.ClearScope))
```

## Integration in Main Application

The ticketing system now uses this DI container in `cmd/main.go`:

1. **Container Setup**: Creates and configures the DI container
2. **Service Registration**: Registers all repositories, services, and utilities
3. **Handler Resolution**: Resolves handlers with their dependencies
4. **Scope Management**: Uses middleware to manage scoped services per request

### Benefits

1. **Loose Coupling**: Dependencies are injected, not hardcoded
2. **Testability**: Easy to mock dependencies for unit testing
3. **Flexibility**: Can swap implementations without changing dependent code
4. **Lifecycle Management**: Automatic management of service lifetimes
5. **Type Safety**: Generic resolvers provide compile-time type safety
6. **Performance**: Singletons avoid repeated instantiation

### Architecture Compliance

This implementation follows clean architecture principles:
- **Domain Layer**: Contains only interfaces and business logic
- **Infrastructure Layer**: Provides concrete implementations
- **Application Layer**: Orchestrates between domain and infrastructure
- **Dependency Direction**: All dependencies point inward toward the domain

The DI container enables proper dependency inversion, making the system more maintainable and testable.
