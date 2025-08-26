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
```

#### **Eventos Emitidos:**

```bash
InstitutionConfigured     # Institución configurada
CampusCreated            # Nueva sede creada
AcademicYearStarted      # Año académico iniciado
CalendarUpdated          # Calendario modificado
GradeStructureChanged    # Estructura de grados modificada
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
