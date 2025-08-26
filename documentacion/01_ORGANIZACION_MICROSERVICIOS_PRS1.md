# 🏗️ DOCUMENTACIÓN OFICIAL - ORGANIZACIÓN DE MICROSERVICIOS PRS1

## 📋 PARTE 1: LISTA Y EXPLICACIÓN DE MICROSERVICIOS

### **🏷️ 1. LISTA COMPLETA DE MICROSERVICIOS**

| ID | Nombre del Microservicio | Descripción | Puerto | Base de Datos | Desarrollador |
|----|-------------------------|-------------|--------|---------------|---------------|
| **MS-VG-0001** | institution-management-service | Gestión Institucional (Configuración IE, Sedes, Niveles, Calendarios, Reportes) | 8001 | **MongoDB** | Dev 1 |
| **MS-VG-0002** | user-management-service | Gestión de Usuarios (Estudiantes, Profesores, Director, Secretaria, Auxiliar, Padres) | 8002 | **MongoDB** | Dev 2 |
| **MS-VG-0003** | auth-security-service | Autenticación y Autorización (JWT, Login, Roles, Permisos) | 8003 | **PostgreSQL** | Dev 3 |
| **MS-VG-0004** | academic-management-service | Gestión Académica (Matrículas, Cursos, Secciones, Aulas) | 8004 | **PostgreSQL** | Dev 4 |
| **MS-VG-0005** | teacher-assignment-service | Asignaciones Docentes (Profesor-Curso, Horarios, Evaluaciones Docentes) | 8005 | **PostgreSQL** | Dev 5 |
| **MS-VG-0006** | grades-evaluation-service | Evaluaciones y Notas (Calificaciones, Competencias, Promedios) | 8006 | **PostgreSQL** | Dev 6 |
| **MS-VG-0007** | attendance-control-service | Control de Asistencia (Faltas, Justificaciones, Patrones) | 8007 | **PostgreSQL** | Dev 7 |
| **MS-VG-0008** | psychology-welfare-service | Psicología Educativa (Citas, Comportamiento, Bienestar) | 8008 | **PostgreSQL** | Dev 8 |
| **MS-VG-0009** | discipline-management-service | Gestión Disciplinaria (Casos, Sanciones, UGEL) | 8009 | **PostgreSQL** | Dev 9 |
| **MS-VG-0010** | inventory-resources-service | Inventario y Recursos (Stock, Kali Warma, Distribución) | 8010 | **PostgreSQL** | Dev 10 |
| **MS-VG-0011** | notifications-communication-service | Notificaciones (Email, SMS, Push, Templates) | 8011 | **MongoDB** | Dev 11 |

---

## 🔍 **2. EXPLICACIÓN DETALLADA DE MICROSERVICIOS**

### **🏛️ MS-VG-0001: Institution Management Service**

**Base de Datos: MongoDB** 🍃

#### **¿Por qué MongoDB?**

- ✅ **Configuraciones flexibles**: Cada institución tiene parámetros únicos
- ✅ **Jerarquías complejas**: Sedes, niveles, grados con estructuras anidadas
- ✅ **Metadatos variables**: Diferentes tipos de instituciones requieren campos específicos
- ✅ **Escalabilidad**: Preparado para múltiples instituciones educativas

#### **Responsabilidades:**

```
🏛️ GESTIÓN INSTITUCIONAL:
├── 🏫 Configuración de Institución Educativa (datos básicos, código modular)
├── 📍 Gestión de Sedes (principal, anexos, coordenadas geográficas)
├── 🎓 Niveles Educativos (inicial, primaria, secundaria por sede)
├── 📚 Grados y Secciones (estructura académica por nivel)
├── 📅 Calendarios Académicos (años lectivos, períodos, feriados)
├── 🇵🇪 Fechas Cívicas (fechas patrias, efemérides, celebraciones nacionales)
├── ⚙️ Configuraciones del Sistema (parámetros, reglas de negocio)
├── 📊 Reportes Institucionales (estadísticas, indicadores UGEL)
└── 🏆 Logros y Reconocimientos (histórico institucional)
```

#### **APIs Principales:**

```bash
# Gestión de Institución
POST   /api/v1/institution                     # Crear/configurar institución
GET    /api/v1/institution/{institutionId}     # Obtener datos institución
PUT    /api/v1/institution/{institutionId}     # Actualizar institución
GET    /api/v1/institution/config             # Configuraciones activas

# Gestión de Sedes
POST   /api/v1/institution/campuses           # Crear sede
GET    /api/v1/institution/campuses           # Listar sedes
GET    /api/v1/institution/campuses/{campusId} # Obtener sede específica
PUT    /api/v1/institution/campuses/{campusId} # Actualizar sede

# Gestión de Niveles y Grados
GET    /api/v1/institution/education-levels   # Niveles educativos disponibles
POST   /api/v1/institution/grades             # Crear grado
GET    /api/v1/institution/grades/level/{levelId} # Grados por nivel

# Calendarios Académicos
POST   /api/v1/institution/academic-calendar  # Crear calendario
GET    /api/v1/institution/academic-calendar/current # Calendario actual
GET    /api/v1/institution/academic-calendar/holidays # Feriados del año

# Fechas Cívicas
GET    /api/v1/institution/civic-dates        # Listar fechas cívicas del año
GET    /api/v1/institution/civic-dates/month/{month} # Fechas cívicas por mes
GET    /api/v1/institution/civic-dates/current # Fechas cívicas del mes actual
GET    /api/v1/institution/civic-dates/upcoming # Próximas fechas cívicas (15 días)
POST   /api/v1/institution/civic-dates        # Crear/personalizar fecha cívica
PUT    /api/v1/institution/civic-dates/{civicDateId} # Actualizar fecha cívica
DELETE /api/v1/institution/civic-dates/{civicDateId} # Eliminar fecha cívica personalizada
GET    /api/v1/institution/civic-dates/calendar-view # Vista calendario con fechas cívicas
```

#### **Eventos Emitidos:**

```bash
InstitutionConfigured     # Institución configurada
CampusCreated            # Nueva sede creada
AcademicYearStarted      # Año académico iniciado
CalendarUpdated          # Calendario modificado
GradeStructureChanged    # Estructura de grados modificada
CivicDateAdded           # Nueva fecha cívica agregada
CivicDateUpdated         # Fecha cívica actualizada
CivicDateReminder        # Recordatorio de fecha cívica próxima
```

---

### **� MS-VG-0002: User Management Service**

**Base de Datos: MongoDB** 🍃

#### **¿Por qué MongoDB?**

- ✅ **Flexibilidad de esquemas**: Diferentes tipos de usuarios con campos variables
- ✅ **Documentos anidados**: Contactos, direcciones, información médica
- ✅ **Búsquedas complejas**: Filtros por múltiples criterios de usuario
- ✅ **Escalabilidad horizontal**: Gran volumen de usuarios

#### **Responsabilidades:**

```
👥 GESTIÓN DE USUARIOS:
├── 🎓 Estudiantes (datos personales, médicos, emergencia)
├── 👨‍🏫 Profesores (perfil profesional, especialidades, certificaciones)
├── 👩‍💼 Director/a (información administrativa, períodos de gestión)
├── 👩‍💻 Secretaria (datos de contacto, horarios, responsabilidades)
├── 🔧 Auxiliar (turnos, zonas asignadas, funciones específicas)
├── 👨‍👩‍👧‍👦 Padres/Tutores (contacto, relación con estudiantes)
└── 🔐 Administradores (niveles de acceso, permisos del sistema)
```

#### **APIs Principales:**

```bash
POST   /api/v1/users                    # Crear usuario
GET    /api/v1/users/{id}              # Obtener usuario por ID
GET    /api/v1/users/type/{userType}   # Obtener usuarios por tipo
PUT    /api/v1/users/{id}              # Actualizar usuario
DELETE /api/v1/users/{id}              # Eliminar usuario (soft delete)
GET    /api/v1/users/search            # Búsqueda avanzada
GET    /api/v1/users/students/{id}/parents # Obtener padres de estudiante
POST   /api/v1/users/bulk-import       # Importación masiva
GET    /api/v1/users/active            # Usuarios activos
```

#### **Eventos Emitidos:**

```bash
UserCreated           # Usuario creado
UserUpdated           # Usuario actualizado
UserDeactivated       # Usuario desactivado
UserTypeChanged       # Cambio de tipo de usuario
StudentParentLinked   # Padre vinculado a estudiante
```

---

### **🔐 MS-VG-0002: Auth & Security Service**

**Base de Datos: PostgreSQL** 🐘

#### **¿Por qué PostgreSQL?**

- ✅ **ACID compliant**: Transacciones críticas de seguridad
- ✅ **Consistencia**: Roles y permisos deben ser consistentes
- ✅ **Auditabilidad**: Logs de acceso y seguridad
- ✅ **Rendimiento**: Consultas rápidas de autenticación

#### **Responsabilidades:**

```
🔐 AUTENTICACIÓN Y AUTORIZACIÓN:
├── 🔑 Login/Logout (validación de credenciales)
├── 🎫 JWT Tokens (generación, validación, refresh)
├── 👑 Roles y Permisos (RBAC - Role-Based Access Control)
├── 📱 Sesiones (gestión de sesiones activas)
├── 🛡️ Seguridad (rate limiting, detección de ataques)
├── 📊 Logs de Auditoría (accesos, intentos fallidos)
└── 🔄 Recuperación de Contraseñas (reset, validación)
```

#### **APIs Principales:**

```bash
POST   /api/v1/auth/login              # Iniciar sesión
POST   /api/v1/auth/logout             # Cerrar sesión
POST   /api/v1/auth/refresh            # Renovar token
POST   /api/v1/auth/forgot-password    # Recuperar contraseña
POST   /api/v1/auth/reset-password     # Resetear contraseña
GET    /api/v1/auth/validate-token     # Validar token
GET    /api/v1/auth/user-permissions   # Obtener permisos de usuario
POST   /api/v1/auth/change-password    # Cambiar contraseña
GET    /api/v1/auth/active-sessions    # Sesiones activas
POST   /api/v1/auth/revoke-session     # Revocar sesión
```

#### **Roles del Sistema:**

```
ADMINISTRADOR_SISTEMA   # Acceso completo al sistema
DIRECTOR               # Gestión institucional completa
SUBDIRECTOR           # Gestión académica y disciplinaria
COORDINADOR_ACADEMICO  # Gestión de cursos y profesores
PROFESOR              # Gestión de sus cursos asignados
TUTOR                 # Gestión de su sección asignada
PSICOLOGO             # Gestión de bienestar estudiantil
SECRETARIA            # Gestión administrativa
AUXILIAR              # Funciones de apoyo
PADRE_FAMILIA         # Consulta de información de sus hijos
ESTUDIANTE            # Consulta de su información académica
```

---

### **📚 MS-VG-0003: Academic Management Service**

**Base de Datos: PostgreSQL** 🐘

#### **¿Por qué PostgreSQL?**

- ✅ **Relaciones complejas**: Cursos, secciones, matrículas
- ✅ **Integridad referencial**: FK entre entidades académicas
- ✅ **Consultas complejas**: Reportes académicos avanzados
- ✅ **Transacciones**: Matrículas requieren atomicidad

#### **Responsabilidades:**

```
📚 GESTIÓN ACADÉMICA:
├── 🏫 Gestión de Cursos (creación, modificación, estado)
├── 📖 Gestión de Secciones (A, B, C por grado)
├── 🎓 Proceso de Matrículas (inscripciones, traslados)
├── 🏠 Gestión de Aulas (capacidad, recursos, disponibilidad)
├── 📅 Períodos Académicos (años lectivos, bimestres, trimestres)
├── 📊 Grados y Niveles (inicial, primaria, secundaria)
└── 📋 Currícula Educativa (competencias por curso)
```

#### **APIs Principales:**

```bash
# Gestión de Cursos
POST   /api/v1/courses                 # Crear curso
GET    /api/v1/courses                 # Listar cursos
GET    /api/v1/courses/{id}            # Obtener curso
PUT    /api/v1/courses/{id}            # Actualizar curso

# Gestión de Secciones
POST   /api/v1/sections                # Crear sección
GET    /api/v1/sections/grade/{grade}  # Secciones por grado
GET    /api/v1/sections/{id}/students  # Estudiantes de sección

# Gestión de Matrículas
POST   /api/v1/enrollments             # Matricular estudiante
GET    /api/v1/enrollments/student/{id} # Matrículas de estudiante
PUT    /api/v1/enrollments/{id}/status # Cambiar estado matrícula

# Gestión de Aulas
POST   /api/v1/classrooms              # Crear aula
GET    /api/v1/classrooms/available    # Aulas disponibles
```

---

### **👨‍🏫 MS-VG-0004: Teacher Assignment Service**

**Base de Datos: PostgreSQL** 🐘

#### **¿Por qué PostgreSQL?**

- ✅ **Integridad**: Asignaciones requieren consistencia
- ✅ **Consultas complejas**: Horarios y conflictos
- ✅ **Transacciones**: Cambios atómicos de asignaciones
- ✅ **Rendimiento**: Búsquedas rápidas de disponibilidad

#### **Responsabilidades:**

```
👨‍🏫 ASIGNACIONES DOCENTES:
├── 📋 Asignación Profesor-Curso-Sección
├── ⏰ Gestión de Horarios Docentes
├── 📊 Distribución de Carga Académica
├── 🔍 Detección de Conflictos de Horario
├── 📈 Evaluaciones de Desempeño Docente
├── 📝 Responsabilidades por Curso
└── 📅 Planificación Académica
```

#### **APIs Principales:**

```bash
# Asignaciones
POST   /api/v1/assignments/teacher/{teacherId}/course/{courseId}
GET    /api/v1/assignments/teacher/{teacherId}
GET    /api/v1/assignments/section/{sectionId}/teachers
DELETE /api/v1/assignments/{assignmentId}

# Horarios
GET    /api/v1/assignments/teacher/{teacherId}/schedule
POST   /api/v1/assignments/bulk-assign
GET    /api/v1/assignments/conflicts
PUT    /api/v1/assignments/{id}/schedule

# Evaluaciones
POST   /api/v1/assignments/evaluations
GET    /api/v1/assignments/teacher/{teacherId}/evaluations
```

---

### **📊 MS-VG-0005: Grades Evaluation Service**

**Base de Datos: PostgreSQL** 🐘

#### **Responsabilidades:**

```
📊 EVALUACIONES Y NOTAS:
├── 📝 Registro de Calificaciones
├── 🎯 Evaluación por Competencias
├── 📈 Cálculo de Promedios
├── 📋 Tipos de Evaluación (oral, escrita, práctica)
├── 📊 Reportes de Rendimiento
├── 🏆 Reconocimientos Académicos
└── 📉 Alertas de Bajo Rendimiento
```

---

### **📅 MS-VG-0006: Attendance Control Service**

**Base de Datos: PostgreSQL** 🐘

#### **Responsabilidades:**

```
📅 CONTROL DE ASISTENCIA:
├── ✅ Registro de Asistencia Diaria
├── ❌ Gestión de Faltas y Tardanzas
├── 📝 Justificaciones de Ausencias
├── 📊 Patrones de Asistencia
├── 🚨 Alertas de Inasistencia
├── 📈 Reportes de Asistencia
└── 👨‍👩‍👧‍👦 Notificaciones a Padres
```

---

### **🧠 MS-VG-0007: Psychology Welfare Service**

**Base de Datos: PostgreSQL** 🐘

#### **Responsabilidades:**

```
🧠 PSICOLOGÍA Y BIENESTAR:
├── 📅 Programación de Citas Psicológicas
├── 📊 Registro de Comportamiento Estudiantil
├── 📋 Fichas de Seguimiento
├── 🎯 Programas de Intervención
├── 👨‍👩‍👧‍👦 Orientación a Padres
├── 📈 Evaluaciones Psicopedagógicas
└── 🤝 Derivaciones Externas
```

---

### **⚖️ MS-VG-0008: Discipline Management Service**

**Base de Datos: PostgreSQL** 🐘

#### **Responsabilidades:**

```
⚖️ GESTIÓN DISCIPLINARIA:
├── 📋 Registro de Casos Disciplinarios
├── ⚖️ Aplicación de Sanciones
├── 📊 Seguimiento de Comportamiento
├── 📝 Reportes a UGEL
├── 🔄 Proceso de Apelaciones
├── 👨‍👩‍👧‍👦 Citaciones a Padres
└── 📈 Estadísticas Disciplinarias
```

---

### **📦 MS-VG-0009: Inventory Resources Service**

**Base de Datos: PostgreSQL** 🐘

#### **Responsabilidades:**

```
📦 INVENTARIO Y RECURSOS:
├── 📚 Control de Stock (libros, materiales)
├── 🍎 Gestión Qali Warma
├── 📋 Inventario de Equipos
├── 🔄 Distribución de Recursos
├── 📊 Reportes de Consumo
├── 🚨 Alertas de Stock Bajo
└── 📈 Planificación de Compras
```

---

### **📱 MS-VG-0010: Notifications Communication Service**

**Base de Datos: MongoDB** 🍃

#### **¿Por qué MongoDB?**

- ✅ **Volumen masivo**: Miles de notificaciones diarias
- ✅ **Estructura flexible**: Diferentes tipos de mensaje
- ✅ **Escalabilidad**: Crecimiento horizontal
- ✅ **Rendimiento**: Inserción rápida de mensajes

#### **Responsabilidades:**

```
📱 NOTIFICACIONES Y COMUNICACIÓN:
├── 📧 Envío de Emails
├── 📱 Notificaciones SMS
├── 🔔 Push Notifications
├── 📋 Templates de Mensajes
├── 📊 Tracking de Entregas
├── 🎯 Segmentación de Audiencias
├── 📅 Programación de Envíos
└── 📈 Métricas de Comunicación
```

#### **APIs Principales:**

```bash
POST   /api/v1/notifications/email     # Enviar email
POST   /api/v1/notifications/sms       # Enviar SMS
POST   /api/v1/notifications/push      # Push notification
GET    /api/v1/notifications/status/{id} # Estado de envío
POST   /api/v1/notifications/bulk      # Envío masivo
GET    /api/v1/notifications/templates # Templates disponibles
POST   /api/v1/notifications/schedule  # Programar envío
```

---

## 🎯 **JUSTIFICACIÓN DE ARQUITECTURA DE BASES DE DATOS**

### **🍃 MongoDB (3 servicios):**

```
MS-VG-0001: Institution Management → Configuraciones flexibles institucionales
MS-VG-0002: User Management       → Flexibilidad de esquemas de usuario
MS-VG-0011: Notifications         → Volumen masivo y escalabilidad
```

### **🐘 PostgreSQL (8 servicios):**

```
MS-VG-0003: Auth & Security        → Transacciones críticas
MS-VG-0004: Academic Management    → Relaciones complejas
MS-VG-0005: Teacher Assignment     → Integridad referencial
MS-VG-0006: Grades Evaluation      → Cálculos y agregaciones
MS-VG-0007: Attendance Control     → Datos transaccionales
MS-VG-0008: Psychology Welfare     → Seguimiento estructurado
MS-VG-0009: Discipline Management  → Procesos formales
MS-VG-0010: Inventory Resources    → Control de stock
```

### **✅ Ventajas de esta Distribución:**

- **Rendimiento optimizado** por tipo de datos
- **Escalabilidad diferenciada** según necesidades
- **Expertise del equipo** especializado por tecnología
- **Backup y mantenimiento** estratégico por tipo de servicio

---

## 📊 **CLASIFICACIÓN DE TABLAS POR MICROSERVICIO**

### **🏛️ MS-VG-0001: Institution Management Service**

#### **📋 Tablas Maestras:**

- `institutions` - Datos básicos de la institución educativa
- `campuses` - Sedes y ubicaciones de la institución
- `education_levels` - Niveles educativos (Inicial, Primaria, Secundaria)
- `grade_structure` - Estructura de grados por nivel
- `academic_calendar_templates` - Plantillas de calendarios académicos
- `civic_dates_catalog` - Catálogo de fechas cívicas nacionales
- `civic_date_types` - Tipos de fechas cívicas (patrias, efemérides, religiosas)

#### **💾 Tablas Transaccionales Básicas:**

- `academic_years` - Años lectivos específicos
- `academic_periods` - Períodos académicos (bimestres, trimestres)
- `holidays` - Feriados y días no laborables
- `institution_configurations` - Configuraciones dinámicas del sistema
- `civic_dates` - Fechas cívicas activas por año e institución
- `civic_date_customizations` - Personalizaciones de fechas cívicas por institución

#### **🔄 Tablas Distribuidas:**

- `cross_institution_references` - Referencias a otras instituciones
- `sync_events_institution` - Eventos de sincronización con otros servicios
- `civic_dates_sync_events` - Eventos de sincronización de fechas cívicas
- `web_display_events` - Eventos para mostrar fechas cívicas en la web

---

### **👥 MS-VG-0002: User Management Service**

#### **📋 Tablas Maestras:**

- `user_types` - Tipos de usuario del sistema
- `document_types` - Tipos de documento de identidad
- `relationship_types` - Tipos de relación familiar
- `user_status_catalog` - Estados de usuario (activo, inactivo, etc.)

#### **💾 Tablas Transaccionales Básicas:**

- `users` - Información principal de usuarios
- `user_profiles` - Perfiles específicos por tipo de usuario
- `family_relationships` - Relaciones familiares entre usuarios
- `user_medical_info` - Información médica de estudiantes
- `user_emergency_contacts` - Contactos de emergencia

#### **🔄 Tablas Distribuidas:**

- `user_institution_assignments` - Asignación de usuarios a instituciones
- `user_sync_events` - Eventos de sincronización de usuarios
- `cross_service_user_refs` - Referencias de usuarios en otros servicios

---

### **🔐 MS-VG-0003: Auth & Security Service**

#### **📋 Tablas Maestras:**

- `roles` - Roles del sistema
- `permissions` - Permisos disponibles
- `role_permissions` - Permisos asignados a roles
- `security_policies` - Políticas de seguridad

#### **💾 Tablas Transaccionales Básicas:**

- `auth_users` - Credenciales de autenticación
- `user_role_assignments` - Asignación de roles a usuarios
- `user_sessions` - Sesiones activas de usuarios
- `password_reset_tokens` - Tokens de recuperación de contraseña
- `login_attempts` - Intentos de inicio de sesión

#### **🔄 Tablas Distribuidas:**

- `security_audit_log` - Log de auditoría de seguridad
- `cross_service_auth_events` - Eventos de autenticación distribuidos
- `federation_tokens` - Tokens para federación entre servicios

---

### **📚 MS-VG-0004: Academic Management Service**

#### **📋 Tablas Maestras:**

- `courses_catalog` - Catálogo de cursos disponibles
- `classroom_types` - Tipos de aulas (regular, laboratorio, etc.)
- `enrollment_types` - Tipos de matrícula
- `academic_status_catalog` - Estados académicos

#### **💾 Tablas Transaccionales Básicas:**

- `courses` - Cursos activos por período
- `sections` - Secciones de cursos
- `classrooms` - Aulas disponibles
- `student_enrollments` - Matrículas de estudiantes
- `section_schedules` - Horarios de secciones

#### **🔄 Tablas Distribuidas:**

- `academic_sync_events` - Eventos académicos para sincronización
- `cross_service_academic_refs` - Referencias académicas distribuidas
- `enrollment_workflow_states` - Estados de workflow de matrícula

---

### **👨‍🏫 MS-VG-0005: Teacher Assignment Service**

#### **📋 Tablas Maestras:**

- `assignment_types` - Tipos de asignación docente
- `workload_categories` - Categorías de carga laboral
- `evaluation_criteria` - Criterios de evaluación docente

#### **💾 Tablas Transaccionales Básicas:**

- `teacher_assignments` - Asignaciones docente-curso-sección
- `teacher_schedules` - Horarios específicos de profesores
- `teacher_workload` - Carga laboral por profesor
- `assignment_evaluations` - Evaluaciones de desempeño

#### **🔄 Tablas Distribuidas:**

- `assignment_sync_events` - Eventos de asignación para otros servicios
- `conflict_resolution_log` - Log de resolución de conflictos
- `cross_service_assignment_refs` - Referencias de asignaciones distribuidas

---

### **📊 MS-VG-0006: Grades Evaluation Service**

#### **📋 Tablas Maestras:**

- `evaluation_types` - Tipos de evaluación (oral, escrita, práctica)
- `grading_scales` - Escalas de calificación
- `competency_catalog` - Catálogo de competencias
- `achievement_levels` - Niveles de logro

#### **💾 Tablas Transaccionales Básicas:**

- `student_grades` - Calificaciones de estudiantes
- `competency_evaluations` - Evaluaciones por competencias
- `grade_periods` - Períodos de calificación
- `grade_reports` - Reportes de calificaciones
- `remedial_programs` - Programas de reforzamiento

#### **🔄 Tablas Distribuidas:**

- `grade_sync_events` - Eventos de calificaciones
- `performance_analytics` - Analíticas de rendimiento distribuidas
- `cross_service_grade_refs` - Referencias de calificaciones

---

### **📅 MS-VG-0007: Attendance Control Service**

#### **📋 Tablas Maestras:**

- `attendance_types` - Tipos de asistencia (presente, ausente, tardanza)
- `justification_types` - Tipos de justificación
- `attendance_policies` - Políticas de asistencia

#### **💾 Tablas Transaccionales Básicas:**

- `daily_attendance` - Asistencia diaria de estudiantes
- `attendance_justifications` - Justificaciones de ausencias
- `attendance_patterns` - Patrones de asistencia
- `attendance_alerts` - Alertas de inasistencia

#### **🔄 Tablas Distribuidas:**

- `attendance_sync_events` - Eventos de asistencia
- `attendance_reports_cache` - Cache de reportes distribuidos
- `cross_service_attendance_refs` - Referencias de asistencia

---

### **🧠 MS-VG-0008: Psychology Welfare Service**

#### **📋 Tablas Maestras:**

- `intervention_types` - Tipos de intervención psicológica
- `assessment_tools` - Herramientas de evaluación
- `referral_categories` - Categorías de derivación

#### **💾 Tablas Transaccionales Básicas:**

- `psychology_appointments` - Citas psicológicas
- `student_assessments` - Evaluaciones psicopedagógicas
- `intervention_plans` - Planes de intervención
- `behavior_tracking` - Seguimiento de comportamiento
- `family_counseling` - Orientación familiar

#### **🔄 Tablas Distribuidas:**

- `psychology_sync_events` - Eventos de bienestar estudiantil
- `welfare_indicators` - Indicadores de bienestar distribuidos
- `cross_service_psychology_refs` - Referencias psicológicas

---

### **⚖️ MS-VG-0009: Discipline Management Service**

#### **📋 Tablas Maestras:**

- `disciplinary_categories` - Categorías de faltas disciplinarias
- `sanction_types` - Tipos de sanciones
- `appeal_procedures` - Procedimientos de apelación

#### **💾 Tablas Transaccionales Básicas:**

- `disciplinary_cases` - Casos disciplinarios
- `sanctions_applied` - Sanciones aplicadas
- `appeal_processes` - Procesos de apelación
- `behavior_reports` - Reportes de comportamiento
- `ugel_reports` - Reportes enviados a UGEL

#### **🔄 Tablas Distribuidas:**

- `discipline_sync_events` - Eventos disciplinarios
- `behavior_analytics` - Analíticas de comportamiento
- `cross_service_discipline_refs` - Referencias disciplinarias

---

### **📦 MS-VG-0010: Inventory Resources Service**

#### **📋 Tablas Maestras:**

- `resource_categories` - Categorías de recursos
- `unit_measurements` - Unidades de medida
- `supplier_catalog` - Catálogo de proveedores
- `distribution_rules` - Reglas de distribución

#### **💾 Tablas Transaccionales Básicas:**

- `inventory_items` - Items de inventario
- `stock_movements` - Movimientos de stock
- `qali_warma_distributions` - Distribuciones Qali Warma
- `resource_requests` - Solicitudes de recursos
- `stock_alerts` - Alertas de stock

#### **🔄 Tablas Distribuidas:**

- `inventory_sync_events` - Eventos de inventario
- `consumption_analytics` - Analíticas de consumo
- `cross_service_inventory_refs` - Referencias de inventario

---

### **📱 MS-VG-0011: Notifications Service**

#### **📋 Tablas Maestras:**

- `notification_types` - Tipos de notificación
- `message_templates` - Plantillas de mensajes
- `delivery_channels` - Canales de entrega
- `audience_segments` - Segmentos de audiencia

#### **💾 Tablas Transaccionales Básicas:**

- `notifications` - Notificaciones enviadas
- `notification_recipients` - Destinatarios de notificaciones
- `delivery_status` - Estados de entrega
- `scheduled_notifications` - Notificaciones programadas
- `notification_campaigns` - Campañas de comunicación

#### **🔄 Tablas Distribuidas:**

- `notification_sync_events` - Eventos de notificación
- `delivery_analytics` - Analíticas de entrega
- `cross_service_notification_refs` - Referencias de notificaciones

---

## 🔄 **ESTRATEGIA DE DATOS DISTRIBUIDOS**

### **📊 Tipos de Sincronización:**

#### **🔄 Sincronización en Tiempo Real:**

- Eventos críticos de autenticación
- Cambios de estado de usuarios importantes
- Alertas de seguridad y disciplina

#### **⏰ Sincronización Programada:**

- Reportes académicos consolidados
- Analíticas de rendimiento estudiantil
- Estadísticas institucionales

#### **📦 Sincronización por Lotes:**

- Migración de datos históricos
- Backup y replicación de datos maestros
- Consolidación de reportes mensuales

### **🛡️ Estrategias de Consistencia:**

#### **🎯 Consistencia Eventual:**

- Datos de notificaciones
- Métricas y analíticas
- Datos de caché

#### **⚡ Consistencia Fuerte:**

- Datos de autenticación
- Información financiera
- Registros de calificaciones

#### **🔄 Patrón Saga:**

- Proceso de matrícula completo
- Asignación de profesores a cursos
- Workflow de casos disciplinarios

---

## 🇵🇪 **FUNCIONALIDAD NUEVA: FECHAS CÍVICAS**

### **🎯 DESCRIPCIÓN GENERAL**

La funcionalidad de **Fechas Cívicas** es una nueva característica que permitirá a las instituciones educativas gestionar, mostrar y celebrar las fechas patrias, efemérides y celebraciones nacionales importantes del Perú. Esta funcionalidad se integrará tanto en el **dashboard general de la institución** como en la **página web pública** de la institución.

### **🏛️ INTEGRACIÓN EN MS-VG-0001: Institution Management Service**

#### **¿Por qué en Institution Management?**

✅ **Contexto institucional**: Cada institución puede personalizar sus fechas cívicas
✅ **Configuración centralizada**: Gestión desde el mismo lugar que calendarios académicos
✅ **Multiorganización**: Cada institución puede tener fechas específicas adicionales
✅ **Sincronización**: Integración natural con calendarios académicos existentes

### **📅 TIPOS DE FECHAS CÍVICAS**

#### **🇵🇪 Fechas Nacionales (Predefinidas):**

```bash
📅 FECHAS PATRIAS PRINCIPALES:
├── 28 de Julio - Día de la Independencia del Perú
├── 29 de Julio - Día de la Gran Parada Militar
├── 30 de Agosto - Día de Santa Rosa de Lima
├── 8 de Octubre - Día de la Batalla de Angamos
├── 1 de Noviembre - Día de Todos los Santos
└── 9 de Diciembre - Día de la Batalla de Ayacucho

📚 EFEMÉRIDES EDUCATIVAS:
├── 23 de Abril - Día del Idioma Español
├── 2 de Mayo - Día del Trabajador
├── 7 de Junio - Día de la Bandera
├── 6 de Julio - Día del Maestro
├── 27 de Agosto - Día de la Defensa Nacional
└── 16 de Octubre - Día de la Alimentación

🌱 FECHAS AMBIENTALES:
├── 22 de Marzo - Día Mundial del Agua
├── 22 de Abril - Día de la Tierra
├── 5 de Junio - Día Mundial del Medio Ambiente
└── 21 de Septiembre - Día de la Paz
```

#### **🏛️ Fechas Institucionales (Personalizables):**

- **Aniversario de la institución educativa**
- **Día del santo patrono local**
- **Festividades regionales específicas**
- **Logros históricos de la institución**
- **Fechas commemorativas locales**

### **🌐 FUNCIONALIDADES WEB Y DASHBOARD**

#### **📊 Dashboard Institucional:**

```bash
🎛️ PANEL DE CONTROL FECHAS CÍVICAS:
├── 📅 Calendario visual con fechas marcadas
├── 📋 Lista de próximas fechas cívicas (30 días)
├── 🔔 Notificaciones de fechas próximas (7 días antes)
├── ⚙️ Configuración de fechas personalizadas
├── 📝 Gestión de descripciones y actividades
├── 🎨 Personalización de colores por tipo de fecha
├── 📊 Estadísticas de participación en actividades
└── 📱 Programación de notificaciones automáticas
```

#### **🌐 Página Web Pública:**

```bash
🌍 MÓDULO WEB FECHAS CÍVICAS:
├── 🏠 Widget en página principal con fecha actual
├── 📅 Calendario interactivo público
├── 📖 Página dedicada "Fechas Cívicas"
├── 🎯 Detalle de cada fecha con:
│   ├── 📜 Historia y significado
│   ├── 🎭 Actividades programadas
│   ├── 📷 Galería de fotos (años anteriores)
│   └── 📝 Recursos educativos
├── 🔍 Buscador de fechas por mes/tipo
├── 📱 Versión responsive para móviles
└── 📤 Compartir en redes sociales
```

### **🛠️ IMPLEMENTACIÓN TÉCNICA**

#### **📦 Estructura de Datos:**

```typescript
interface CivicDate {
  id: string;
  institutionId: string;
  name: string;
  description: string;
  date: Date;
  type: 'NATIONAL' | 'REGIONAL' | 'INSTITUTIONAL';
  category: 'PATRIA' | 'EFEMERIDE' | 'AMBIENTAL' | 'EDUCATIVA' | 'RELIGIOSA';
  isActive: boolean;
  activities?: CivicActivity[];
  resources?: CivicResource[];
  customizations?: {
    color: string;
    icon: string;
    displayOrder: number;
  };
  webDisplay: {
    showOnHomepage: boolean;
    showInCalendar: boolean;
    allowPublicView: boolean;
  };
  notifications: {
    enabled: boolean;
    daysBefore: number[];
    recipients: string[];
  };
  createdAt: Date;
  updatedAt: Date;
}

interface CivicActivity {
  id: string;
  civicDateId: string;
  title: string;
  description: string;
  startTime: Date;
  endTime: Date;
  location: string;
  responsibleTeacher: string;
  targetGrades: string[];
  materials: string[];
}
```

#### **🔗 APIs Específicas Fechas Cívicas:**

```bash
# Gestión CRUD
GET    /api/v1/institution/civic-dates                    # Listar todas
GET    /api/v1/institution/civic-dates/active             # Solo activas
GET    /api/v1/institution/civic-dates/month/{yyyy-mm}    # Por mes
GET    /api/v1/institution/civic-dates/type/{type}        # Por tipo
POST   /api/v1/institution/civic-dates                    # Crear nueva
PUT    /api/v1/institution/civic-dates/{id}               # Actualizar
DELETE /api/v1/institution/civic-dates/{id}               # Eliminar

# APIs para Web Pública
GET    /api/v1/public/civic-dates/institution/{id}        # Fechas públicas
GET    /api/v1/public/civic-dates/calendar/{yyyy}/{mm}    # Calendario público
GET    /api/v1/public/civic-dates/{id}/details            # Detalle público
GET    /api/v1/public/civic-dates/upcoming                # Próximas fechas

# Notificaciones y Recordatorios
POST   /api/v1/institution/civic-dates/{id}/notify        # Enviar notificación
GET    /api/v1/institution/civic-dates/reminders          # Recordatorios pendientes
POST   /api/v1/institution/civic-dates/schedule-reminders # Programar recordatorios
```

### **🎨 COMPONENTES FRONTEND PRINCIPALES**

#### **📅 CivicDatesCalendar.tsx:**

- Calendario interactivo con fechas marcadas
- Vista mensual, semanal y diaria
- Colores diferenciados por tipo de fecha
- Click para ver detalles de cada fecha

#### **🃏 CivicDateCard.tsx:**

- Tarjeta individual de fecha cívica
- Información resumida (fecha, título, tipo)
- Botones de acción (editar, ver actividades)
- Indicador de notificaciones programadas

#### **📝 CivicDateForm.tsx:**

- Formulario para crear/editar fechas cívicas
- Campos para personalización (color, icono)
- Configuración de actividades asociadas
- Configuración de notificaciones

#### **🌐 CivicDatesWebDisplay.tsx:**

- Componente para página web pública
- Vista optimizada para visitantes
- Información histórica y educativa
- Galería de fotos y recursos

### **🔔 SISTEMA DE NOTIFICACIONES**

#### **📱 Notificaciones Automáticas:**

```bash
🔔 TIPOS DE NOTIFICACIONES:
├── 📅 Recordatorio 7 días antes
├── 📅 Recordatorio 3 días antes
├── 📅 Recordatorio día actual
├── 🎯 Notificación de actividades programadas
├── 📧 Email a padres de familia
├── 💬 SMS a profesores responsables
└── 🔔 Push notification en app móvil
```

#### **👥 Destinatarios Configurables:**

- **Directores y subdirectores**
- **Profesores coordinadores**
- **Padres de familia**
- **Estudiantes** (según grado)
- **Personal administrativo**

### **📊 MÉTRICAS Y ANALÍTICAS**

#### **📈 Indicadores de Participación:**

```bash
📊 MÉTRICAS FECHAS CÍVICAS:
├── 👥 Participación de estudiantes por actividad
├── 📅 Fechas más populares/participativas
├── 🎯 Cumplimiento de actividades programadas
├── 📱 Interacción en página web
├── 🔔 Efectividad de notificaciones
├── 📚 Uso de recursos educativos
└── 📈 Tendencias año tras año
```

### **🚀 BENEFICIOS ESPERADOS**

#### **✅ Para la Institución:**

- **Mayor engagement** con la comunidad educativa
- **Fortalecimiento** de la identidad nacional
- **Mejora** en la organización de actividades cívicas
- **Visibilidad web** aumentada con contenido relevante

#### **✅ Para Estudiantes y Familias:**

- **Educación cívica** integrada al calendario escolar
- **Participación activa** en fechas importantes
- **Acceso** a información histórica y educativa
- **Recordatorios** oportunos de actividades

#### **✅ Para Profesores:**

- **Herramientas** para planificar actividades cívicas
- **Recursos educativos** organizados por fecha
- **Notificaciones** automáticas de responsabilidades
- **Seguimiento** de participación estudiantil

### **🎯 PLAN DE IMPLEMENTACIÓN**

#### **📋 Fase 1: Backend (2 semanas)**

- Creación de modelos de datos
- Implementación de APIs CRUD
- Sistema de notificaciones básico
- Integración con calendario académico

#### **📋 Fase 2: Dashboard Admin (2 semanas)**

- Componentes de gestión
- Calendario interactivo
- Formularios de configuración
- Panel de notificaciones

#### **📋 Fase 3: Web Pública (1.5 semanas)**

- Página dedicada fechas cívicas
- Widget para homepage
- Optimización responsive
- SEO y compartir social

#### **📋 Fase 4: Notificaciones Avanzadas (1 semana)**

- Configuración granular
- Plantillas personalizables
- Integración con otros módulos
- Testing y optimización

**⏱️ Tiempo total estimado: 6.5 semanas**

Esta nueva funcionalidad fortalecerá significativamente la propuesta de valor del sistema educativo, integrando aspectos cívicos y culturales importantes para la formación integral de los estudiantes. 🇵🇪
