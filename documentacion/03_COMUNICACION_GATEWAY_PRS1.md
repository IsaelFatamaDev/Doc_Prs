# 🌐 DOCUMENTACIÓN OFICIAL - ORGANIZACIÓN DE MICROSERVICIOS PRS1

## 🔄 PARTE 3: COMUNICACIÓN Y ARQUITECTURA DISTRIBUIDA

### **🔗 5. COMUNICACIÓN SÍNCRONA Y ASÍNCRONA**

---

## 📡 **COMUNICACIÓN SÍNCRONA (REST APIs)**

### **🏛️ Patrones de Comunicación Directa**

#### **MS-VG-0003 (Auth) ↔ MS-VG-0002 (Users)**

```bash
# Flujo de Login
1. Frontend → POST /api/v1/auth/login
2. Auth Service → GET /api/v1/users/validate-credentials
3. Users Service → Respuesta con datos del usuario
4. Auth Service → Genera JWT + permisos
5. Frontend ← JWT Token + User Profile
```

#### **APIs de Integración Síncrona:**

```yaml
# MS-VG-0001: Institution Management
GET /api/v1/institution/{institutionId}
GET /api/v1/institution/config
GET /api/v1/institution/campuses
GET /api/v1/institution/academic-calendar/current

# MS-VG-0002: User Management
GET /api/v1/users/{userId}
GET /api/v1/users/type/{userType}
GET /api/v1/users/validate-credentials
POST /api/v1/users/bulk-validate

# MS-VG-0003: Auth & Security
POST /api/v1/auth/validate-token
GET /api/v1/auth/user-permissions/{userId}
POST /api/v1/auth/check-permission

# MS-VG-0004: Academic Management
GET /api/v1/academic/sections/{sectionId}
GET /api/v1/academic/courses/{courseId}
GET /api/v1/academic/enrollments/student/{studentId}

# MS-VG-0005: Teacher Assignment
GET /api/v1/assignments/teacher/{teacherId}/courses
GET /api/v1/assignments/section/{sectionId}/teachers
POST /api/v1/assignments/validate-availability

# MS-VG-0006: Grades Evaluation
POST /api/v1/grades/calculate-average
GET /api/v1/grades/student/{studentId}/period/{periodId}

# MS-VG-0007: Attendance Control
GET /api/v1/attendance/student/{studentId}/summary
POST /api/v1/attendance/mark-present

# MS-VG-0011: Notifications
POST /api/v1/notifications/send-immediate
GET /api/v1/notifications/templates/{templateId}
```

### **⚡ Timeouts y Circuit Breaker**

```yaml
# Configuración de timeouts por servicio
timeouts:
  user-management: 2000ms
  auth-security: 1000ms
  academic-management: 3000ms
  teacher-assignment: 2500ms
  grades-evaluation: 5000ms
  attendance-control: 1500ms
  notifications: 10000ms

# Circuit Breaker configuración
circuit_breaker:
  failure_threshold: 5
  recovery_timeout: 30000ms
  half_open_max_calls: 3
```

---

## 🚀 **COMUNICACIÓN ASÍNCRONA (Apache Kafka)**

### **📊 Arquitectura de Eventos**

#### **Kafka Topics Structure:**

```bash
# Eventos de Usuario
topic: user-events
├── user.created
├── user.updated
├── user.deactivated
├── user.role.changed
└── student.parent.linked

# Eventos Académicos
topic: academic-events
├── student.enrolled
├── student.transferred
├── section.created
├── course.assigned
└── academic.year.started

# Eventos de Asignaciones
topic: assignment-events
├── teacher.assigned.to.course
├── teacher.assignment.removed
├── schedule.created
├── schedule.conflict.detected
└── workload.updated

# Eventos de Evaluación
topic: grades-events
├── grade.recorded
├── grade.updated
├── period.average.calculated
├── student.performance.alert
└── competency.evaluated

# Eventos de Asistencia
topic: attendance-events
├── attendance.marked
├── absence.recorded
├── tardiness.recorded
├── absence.justified
└── attendance.pattern.alert

# Eventos de Notificación
topic: notification-events
├── notification.requested
├── notification.sent
├── notification.delivered
├── notification.failed
└── bulk.notification.completed
```

### **🔄 Event Sourcing Patterns**

#### **Evento: student.enrolled**

```json
{
  "eventId": "evt-2025-000001",
  "eventType": "student.enrolled",
  "eventVersion": "1.0",
  "timestamp": "2025-08-26T10:00:00Z",
  "sourceService": "MS-VG-0003",
  "correlationId": "enroll-2025-0001",
  "aggregateId": "ENR-2025-000001",
  "aggregateType": "StudentEnrollment",
  "payload": {
    "enrollmentId": "ENR-2025-000001",
    "studentUserId": "USR-2025-000001",
    "sectionId": "SEC-2025-5A",
    "academicYearId": "2025",
    "enrollmentDate": "2025-03-01",
    "enrollmentType": "REGULAR",
    "previousInstitution": null
  },
  "metadata": {
    "triggeredBy": "USR-ADMIN-001",
    "ipAddress": "192.168.1.100",
    "userAgent": "Mozilla/5.0...",
    "requestId": "req-2025-000001"
  }
}
```

#### **Consumidores del Evento:**

```yaml
# MS-VG-0004: Teacher Assignment
- Acción: Habilitar asignación de profesores para nueva sección
- Handler: onStudentEnrolled()

# MS-VG-0005: Grades Evaluation
- Acción: Crear registros base para calificaciones
- Handler: createGradeRecords()

# MS-VG-0006: Attendance Control
- Acción: Habilitar registro de asistencia
- Handler: initializeAttendanceRecord()

# MS-VG-0010: Notifications
- Acción: Enviar notificación de bienvenida
- Handler: sendWelcomeNotification()
```

### **📋 Saga Pattern - Proceso de Matrícula**

```yaml
# Saga: StudentEnrollmentSaga
steps:
  1. CreateEnrollment (MS-VG-0003)
     - success: → ValidateUserExists
     - failure: → CompensateEnrollment

  2. ValidateUserExists (MS-VG-0001)
     - success: → AssignToSection
     - failure: → CompensateEnrollment

  3. AssignToSection (MS-VG-0003)
     - success: → InitializeGrades
     - failure: → CompensateUserValidation

  4. InitializeGrades (MS-VG-0005)
     - success: → InitializeAttendance
     - failure: → CompensateSectionAssignment

  5. InitializeAttendance (MS-VG-0006)
     - success: → SendWelcomeNotification
     - failure: → CompensateGradeInitialization

  6. SendWelcomeNotification (MS-VG-0010)
     - success: → CompleteEnrollment
     - failure: → Log warning (no compensation needed)

# Compensaciones
compensations:
  - CompensateEnrollment: DELETE enrollment record
  - CompensateUserValidation: Mark enrollment as invalid
  - CompensateSectionAssignment: Remove from section
  - CompensateGradeInitialization: Delete grade records
```

---

## 🌉 **6. API GATEWAY Y ARQUITECTURA DE SOPORTE**

### **🚪 Kong API Gateway Configuration**

#### **Configuración de Rutas:**

```yaml
# kong.yml
_format_version: "3.0"

services:
  - name: institution-management-service
    url: http://ms-vg-0001-institution:8001
    tags: ["microservice", "institution"]

  - name: user-management-service
    url: http://ms-vg-0002-users:8002
    tags: ["microservice", "users"]

  - name: auth-security-service
    url: http://ms-vg-0003-auth:8003
    tags: ["microservice", "auth"]

  - name: academic-management-service
    url: http://ms-vg-0004-academic:8004
    tags: ["microservice", "academic"]

  - name: teacher-assignment-service
    url: http://ms-vg-0005-teacher-assignment:8005
    tags: ["microservice", "assignments"]

  - name: grades-evaluation-service
    url: http://ms-vg-0006-grades:8006
    tags: ["microservice", "grades"]

  - name: attendance-control-service
    url: http://ms-vg-0007-attendance:8007
    tags: ["microservice", "attendance"]

  - name: psychology-welfare-service
    url: http://ms-vg-0008-psychology:8008
    tags: ["microservice", "psychology"]

  - name: discipline-management-service
    url: http://ms-vg-0009-discipline:8009
    tags: ["microservice", "discipline"]

  - name: inventory-resources-service
    url: http://ms-vg-0010-inventory:8010
    tags: ["microservice", "inventory"]

  - name: notifications-service
    url: http://ms-vg-0011-notifications:8011
    tags: ["microservice", "notifications"]

  - name: psychology-welfare-service
    url: http://ms-vg-0007-psychology:8007
    tags: ["microservice", "psychology"]

  - name: discipline-management-service
    url: http://ms-vg-0008-discipline:8008
    tags: ["microservice", "discipline"]

  - name: inventory-resources-service
    url: http://ms-vg-0009-inventory:8009
    tags: ["microservice", "inventory"]

  - name: notifications-service
    url: http://ms-vg-0010-notifications:8010
    tags: ["microservice", "notifications"]

routes:
  # Rutas de Autenticación (sin autenticación previa)
  - name: auth-login
    service: auth-security-service
    paths: ["/api/v1/auth/login"]
    methods: ["POST"]

  - name: auth-refresh
    service: auth-security-service
    paths: ["/api/v1/auth/refresh"]
    methods: ["POST"]

  # Rutas de Usuario (requieren autenticación)
  - name: users-routes
    service: user-management-service
    paths: ["/api/v1/users"]
    methods: ["GET", "POST", "PUT", "DELETE"]

  # Rutas Académicas
  - name: academic-routes
    service: academic-management-service
    paths: ["/api/v1/academic"]
    methods: ["GET", "POST", "PUT", "DELETE"]

  # Rutas de Asignaciones
  - name: assignments-routes
    service: teacher-assignment-service
    paths: ["/api/v1/assignments"]
    methods: ["GET", "POST", "PUT", "DELETE"]

  # Rutas de Calificaciones
  - name: grades-routes
    service: grades-evaluation-service
    paths: ["/api/v1/grades"]
    methods: ["GET", "POST", "PUT", "DELETE"]

  # Rutas de Asistencia
  - name: attendance-routes
    service: attendance-control-service
    paths: ["/api/v1/attendance"]
    methods: ["GET", "POST", "PUT", "DELETE"]

  # Rutas de Psicología
  - name: psychology-routes
    service: psychology-welfare-service
    paths: ["/api/v1/psychology"]
    methods: ["GET", "POST", "PUT", "DELETE"]

  # Rutas Disciplinarias
  - name: discipline-routes
    service: discipline-management-service
    paths: ["/api/v1/discipline"]
    methods: ["GET", "POST", "PUT", "DELETE"]

  # Rutas de Inventario
  - name: inventory-routes
    service: inventory-resources-service
    paths: ["/api/v1/inventory"]
    methods: ["GET", "POST", "PUT", "DELETE"]

  # Rutas de Notificaciones
  - name: notifications-routes
    service: notifications-service
    paths: ["/api/v1/notifications"]
    methods: ["GET", "POST", "PUT", "DELETE"]
```

#### **Plugins de Seguridad:**

```yaml
plugins:
  # Plugin JWT para todas las rutas excepto login
  - name: jwt
    route: users-routes
    config:
      secret_is_base64: false
      claims_to_verify: ["exp", "sub"]

  - name: jwt
    route: academic-routes
    config:
      secret_is_base64: false
      claims_to_verify: ["exp", "sub"]

  # Rate Limiting
  - name: rate-limiting
    route: auth-login
    config:
      minute: 5
      hour: 20
      policy: local

  # CORS
  - name: cors
    config:
      origins: ["http://localhost:3000", "https://sistema-educativo.com"]
      methods: ["GET", "POST", "PUT", "DELETE", "OPTIONS"]
      headers: ["Accept", "Content-Type", "Authorization"]

  # Request/Response Logging
  - name: http-log
    config:
      http_endpoint: "http://logstash:5000/api/logs"
      method: POST

  # Circuit Breaker
  - name: proxy-cache
    config:
      cache_ttl: 300
      vary_headers: ["Authorization"]
```

### **🔧 Service Discovery - Consul**

#### **Configuración de Servicios:**

```json
{
  "services": [
    {
      "id": "ms-vg-0001-users-1",
      "name": "user-management-service",
      "tags": ["microservice", "users", "v1"],
      "address": "ms-vg-0001-users",
      "port": 8001,
      "meta": {
        "version": "1.0.0",
        "database": "mongodb",
        "team": "dev-1"
      },
      "check": {
        "http": "http://ms-vg-0001-users:8001/health",
        "interval": "10s",
        "timeout": "3s"
      }
    },
    {
      "id": "ms-vg-0002-auth-1",
      "name": "auth-security-service",
      "tags": ["microservice", "auth", "v1"],
      "address": "ms-vg-0002-auth",
      "port": 8002,
      "meta": {
        "version": "1.0.0",
        "database": "postgresql",
        "team": "dev-2"
      },
      "check": {
        "http": "http://ms-vg-0002-auth:8002/health",
        "interval": "10s",
        "timeout": "3s"
      }
    }
  ]
}
```

### **📊 Monitoring - Prometheus + Grafana**

#### **Métricas por Microservicio:**

```yaml
# prometheus.yml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'microservices'
    static_configs:
      - targets:
        - 'ms-vg-0001-users:8001'
        - 'ms-vg-0002-auth:8002'
        - 'ms-vg-0003-academic:8003'
        - 'ms-vg-0004-teacher-assignment:8004'
        - 'ms-vg-0005-grades:8005'
        - 'ms-vg-0006-attendance:8006'
        - 'ms-vg-0007-psychology:8007'
        - 'ms-vg-0008-discipline:8008'
        - 'ms-vg-0009-inventory:8009'
        - 'ms-vg-0010-notifications:8010'
    metrics_path: '/metrics'
    scrape_interval: 5s

  - job_name: 'api-gateway'
    static_configs:
      - targets: ['kong:8001']
    metrics_path: '/metrics'

  - job_name: 'kafka'
    static_configs:
      - targets: ['kafka:9092']

  - job_name: 'databases'
    static_configs:
      - targets:
        - 'postgres-primary:5432'
        - 'postgres-replica:5433'
        - 'mongodb-primary:27017'
        - 'redis:6379'
```

#### **Alertas Críticas:**

```yaml
# alerts.yml
groups:
  - name: microservices-alerts
    rules:
      - alert: ServiceDown
        expr: up == 0
        for: 30s
        labels:
          severity: critical
        annotations:
          summary: "Service {{ $labels.job }} is down"

      - alert: HighResponseTime
        expr: http_request_duration_seconds_p95 > 2
        for: 2m
        labels:
          severity: warning
        annotations:
          summary: "High response time on {{ $labels.job }}"

      - alert: DatabaseConnectionFailure
        expr: database_connections_failed_total > 5
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Database connection failures on {{ $labels.service }}"

      - alert: KafkaConsumerLag
        expr: kafka_consumer_lag > 1000
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High Kafka consumer lag on {{ $labels.topic }}"
```

### **🔄 Load Balancing**

#### **HAProxy Configuration:**

```bash
# haproxy.cfg
global
    daemon
    maxconn 4096

defaults
    mode http
    timeout connect 5000ms
    timeout client 50000ms
    timeout server 50000ms

# API Gateway Load Balancing
frontend api_gateway_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/sistema-educativo.pem
    redirect scheme https if !{ ssl_fc }
    default_backend api_gateway_backend

backend api_gateway_backend
    balance roundrobin
    option httpchk GET /health
    server gateway1 kong-1:8000 check
    server gateway2 kong-2:8000 check
    server gateway3 kong-3:8000 check

# Database Load Balancing - PostgreSQL
backend postgres_read_backend
    balance roundrobin
    option pgsql-check user postgres
    server pg-read-1 postgres-replica-1:5432 check
    server pg-read-2 postgres-replica-2:5432 check
    server pg-read-3 postgres-replica-3:5432 check

backend postgres_write_backend
    server pg-primary postgres-primary:5432 check

# MongoDB Replica Set
backend mongodb_backend
    balance roundrobin
    option tcp-check
    server mongo-1 mongodb-1:27017 check
    server mongo-2 mongodb-2:27017 check
    server mongo-3 mongodb-3:27017 check
```

### **🐳 Docker Compose - Infraestructura Completa**

```yaml
# docker-compose.infrastructure.yml
version: '3.8'

services:
  # API Gateway
  kong:
    image: kong:3.4
    container_name: api-gateway
    environment:
      KONG_DATABASE: postgres
      KONG_PG_HOST: postgres-kong
      KONG_PG_USER: kong
      KONG_PG_PASSWORD: kong_password
      KONG_PROXY_ACCESS_LOG: /dev/stdout
      KONG_ADMIN_ACCESS_LOG: /dev/stdout
      KONG_PROXY_ERROR_LOG: /dev/stderr
      KONG_ADMIN_ERROR_LOG: /dev/stderr
      KONG_ADMIN_LISTEN: 0.0.0.0:8001
    ports:
      - "8000:8000"
      - "8001:8001"
      - "8443:8443"
      - "8444:8444"
    depends_on:
      - postgres-kong

  # Kong Database
  postgres-kong:
    image: postgres:15
    container_name: postgres-kong
    environment:
      POSTGRES_DB: kong
      POSTGRES_USER: kong
      POSTGRES_PASSWORD: kong_password
    volumes:
      - kong_postgres_data:/var/lib/postgresql/data
    ports:
      - "5434:5432"

  # Message Broker
  kafka:
    image: confluentinc/cp-kafka:7.4.0
    container_name: message-broker
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_AUTO_CREATE_TOPICS_ENABLE: true
    ports:
      - "9092:9092"
    depends_on:
      - zookeeper
    volumes:
      - kafka_data:/var/lib/kafka/data

  zookeeper:
    image: confluentinc/cp-zookeeper:7.4.0
    container_name: zookeeper
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
    ports:
      - "2181:2181"
    volumes:
      - zookeeper_data:/var/lib/zookeeper/data

  # Service Discovery
  consul:
    image: consul:1.16
    container_name: service-discovery
    ports:
      - "8500:8500"
    command: consul agent -dev -client=0.0.0.0
    volumes:
      - consul_data:/consul/data

  # Caching
  redis:
    image: redis:7-alpine
    container_name: cache-store
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    command: redis-server --appendonly yes

  # Monitoring
  prometheus:
    image: prom/prometheus:v2.45.0
    container_name: monitoring-prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./monitoring/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus

  grafana:
    image: grafana/grafana:10.0.0
    container_name: monitoring-grafana
    ports:
      - "3000:3000"
    environment:
      GF_SECURITY_ADMIN_PASSWORD: admin_password
    volumes:
      - grafana_data:/var/lib/grafana
      - ./monitoring/grafana/dashboards:/etc/grafana/provisioning/dashboards
      - ./monitoring/grafana/datasources:/etc/grafana/provisioning/datasources

  # Logging
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.8.0
    container_name: logging-elasticsearch
    environment:
      discovery.type: single-node
      ES_JAVA_OPTS: "-Xms512m -Xmx512m"
      xpack.security.enabled: false
    ports:
      - "9200:9200"
    volumes:
      - elasticsearch_data:/usr/share/elasticsearch/data

  logstash:
    image: docker.elastic.co/logstash/logstash:8.8.0
    container_name: logging-logstash
    ports:
      - "5000:5000"
    volumes:
      - ./logging/logstash.conf:/usr/share/logstash/pipeline/logstash.conf
    depends_on:
      - elasticsearch

  kibana:
    image: docker.elastic.co/kibana/kibana:8.8.0
    container_name: logging-kibana
    ports:
      - "5601:5601"
    environment:
      ELASTICSEARCH_HOSTS: http://elasticsearch:9200
    depends_on:
      - elasticsearch

volumes:
  kong_postgres_data:
  kafka_data:
  zookeeper_data:
  consul_data:
  redis_data:
  prometheus_data:
  grafana_data:
  elasticsearch_data:

networks:
  default:
    name: sistema-educativo-network
    external: true
```

### **🔐 Seguridad y Compliance**

#### **JWT Configuration:**

```yaml
# JWT Settings
jwt:
  secret: "${JWT_SECRET}" # 256-bit secret
  expiration:
    access_token: 15m
    refresh_token: 7d
  issuer: "sistema-educativo-prs1"
  audience: "microservices"

# HTTPS/TLS Configuration
tls:
  certificate_path: "/etc/ssl/certs/sistema-educativo.crt"
  private_key_path: "/etc/ssl/private/sistema-educativo.key"
  min_version: "1.2"
  cipher_suites:
    - "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384"
    - "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256"

# CORS Policy
cors:
  allowed_origins:
    - "https://sistema-educativo.com"
    - "https://admin.sistema-educativo.com"
  allowed_methods: ["GET", "POST", "PUT", "DELETE", "OPTIONS"]
  allowed_headers: ["Authorization", "Content-Type", "X-Requested-With"]
  max_age: 86400
```

---

## 📈 **MÉTRICAS Y KPIs DEL SISTEMA**

### **Performance Metrics:**

```yaml
response_times:
  target_p95: < 500ms
  target_p99: < 1000ms

availability:
  target_uptime: 99.9%
  max_downtime_per_month: 43.2m

throughput:
  peak_requests_per_second: 10000
  concurrent_users: 5000

database_performance:
  query_response_time_p95: < 100ms
  connection_pool_utilization: < 80%

kafka_metrics:
  consumer_lag_max: 1000 messages
  producer_throughput: 50000 msg/s
```

### **Business Metrics:**

```yaml
user_activity:
  daily_active_users: "Estudiantes, profesores, padres activos"
  login_success_rate: "> 99%"

academic_operations:
  grade_entries_per_day: "Registro de calificaciones diario"
  attendance_marks_per_day: "Marcado de asistencia diario"

notification_delivery:
  email_delivery_rate: "> 95%"
  sms_delivery_rate: "> 98%"
  push_notification_open_rate: "> 70%"
```

---

## 🎯 **RESUMEN DE ARQUITECTURA COMPLETA**

### **✅ Características Principales:**

- **10 Microservicios** especializados con responsabilidades claras
- **API Gateway** centralizado con Kong para routing y seguridad
- **Event-Driven Architecture** con Apache Kafka para comunicación asíncrona
- **Service Discovery** con Consul para detección automática de servicios
- **Monitoring completo** con Prometheus + Grafana + ELK Stack
- **Load Balancing** con HAProxy para alta disponibilidad
- **Databases distribuidas** MongoDB + PostgreSQL según necesidades
- **Security layers** JWT + HTTPS + CORS + Rate Limiting
- **Scalabilidad horizontal** con Docker containers
- **Fault tolerance** con Circuit Breaker + Retry patterns

### **🚀 Preparado para 10 Desarrolladores:**

- Cada desarrollador tiene un microservicio asignado
- Documentación completa de APIs y eventos
- Ambiente de desarrollo dockerizado
- CI/CD pipeline ready con GitHub Actions
- Testing strategy con test containers
- Monitoring y alertas configuradas
