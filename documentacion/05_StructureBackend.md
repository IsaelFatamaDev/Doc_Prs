# 📦 Estructura del Proyecto: `vg-ms-users`



```bash
vg-ms-users/
│
├── src/main/java/pe/edu/vallegrande/ms/users/
│   │
│   ├── domain/                                         # CAPA DE DOMINIO (Reglas de negocio)
│   │   ├── model/
│   │   │   └── User.java                               # Entidad del dominio
│   │   │
│   │   ├── enums/
│   │   │   ├── UserStatus.java                         # ACTIVE, INACTIVE, DELETED
│   │   │   └── UserRole.java                           # ADMIN, USER, TEACHER, STUDENT
│   │   │
│   │   ├── repository/
│   │   │   └── UserRepository.java                     # Interface (Puerto de salida)
│   │   │
│   │   └── exception/
│   │       ├── UserNotFoundException.java
│   │       ├── UserAlreadyExistsException.java
│   │       └── InvalidUserDataException.java
│   │
│   ├── application/                                    # CAPA DE APLICACIÓN (Casos de uso)
│   │   ├── service/
│   │   │   ├── UserService.java                        # Interface
│   │   │   └── impl/
│   │   │       └── UserServiceImpl.java                # Implementación
│   │   │
│   │   ├── dto/
│   │   │   └── UserDTO.java                            # DTO para comunicación interna
│   │   │
│   │   └── mapper/
│   │       └── UserMapper.java                         # User (Domain) ↔ UserDTO
│   │
│   └── infrastructure/                                 # CAPA DE INFRAESTRUCTURA (Detalles técnicos)
│       │
│       ├── adapter/
│       │   │
│       │   ├── input/                                  # Adaptadores de ENTRADA
│       │   │   └── rest/
│       │   │       ├── UserController.java             # Controlador REST
│       │   │       │
│       │   │       ├── dto/
│       │   │       │   ├── request/
│       │   │       │   │   ├── CreateUserRequest.java
│       │   │       │   │   └── UpdateUserRequest.java
│       │   │       │   └── response/
│       │   │       │       └── UserResponse.java
│       │   │       │
│       │   │       └── mapper/
│       │   │           └── UserRestMapper.java         # Request/Response ↔ UserDTO
│       │   │    
│       │   └── output/                                 # Adaptadores de SALIDA
│       │       │
│       │       ├── persistence/
│       │       │   ├── adapter/
│       │       │   │   └── UserRepositoryAdapter.java  # Implementa UserRepository (Domain)
│       │       │   │
│       │       │   ├── repository/
│       │       │   │   └── UserReactiveMongoRepository.java  # Spring Data Reactive
│       │       │   │
│       │       │   ├── document/
│       │       │   │   └── UserDocument.java           # Entidad MongoDB
│       │       │   │
│       │       │   └── mapper/
│       │       │       └── UserPersistenceMapper.java  # User (Domain) ↔ UserDocument
│       │       │
│       │       └── client/
│       │           ├── InstitutionWebClient.java       # Cliente HTTP Reactivo
│       │           └── dto/
│       │               └── InstitutionDTO.java         # DTO para comunicación externa
│       │
│       ├── config/                                     # Configuraciones
│       │   ├── MongoConfig.java
│       │   ├── WebClientConfig.java
│       │   └── OpenApiConfig.java
│       │
│       │
│       └── exception/                                  # Manejo global de excepciones
│           ├── GlobalExceptionHandler.java
│           └── dto/
│               ├── ApiResponse.java                    # Respuesta estándar
│               └── ErrorResponse.java                  # Respuesta de error
│
├── src/main/resources/
│   ├── application.yml                                 # Configuración principal
│   ├── application-dev.yml                             # Configuración desarrollo
│   └── application-prod.yml                            # Configuración producción
│
│
│── Dockerfile
└── pom.xml
```
