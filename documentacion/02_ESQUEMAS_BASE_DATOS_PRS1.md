# 🗄️ DOCUMENTACIÓN OFICIAL - ORGANIZACIÓN DE MICROSERVICIOS PRS1

## 📊 PARTE 2: ESQUEMAS DE BASE DE DATOS Y TABLAS

### **🗃️ 3. TABLAS MAESTRAS, TRANSACCIONALES Y DISTRIBUIDAS**

---

## 🍃 **MONGODB SCHEMAS**

### **MS-VG-0001: Institution Management Service**

#### **Colección: institutions**

```javascript
{
  _id: ObjectId(),
  institutionId: "INST-2025-001",
  basicInfo: {
    name: "Institución Educativa Pública San José",
    shortName: "I.E.P. San José",
    modularCode: "1234567",
    ruc: "20123456789",
    ugel: "UGEL 02 - Rímac",
    dre: "DRELM - Lima Metropolitana",
    foundationDate: ISODate("1985-03-15"),
    educationLevels: ["INICIAL", "PRIMARIA", "SECUNDARIA"],
    institutionType: "PUBLICA|PRIVADA|PARROQUIAL",
    status: "ACTIVE|INACTIVE|SUSPENDED"
  },
  location: {
    mainCampus: {
      address: "Av. Principal 123",
      district: "Rímac",
      province: "Lima",
      department: "Lima",
      postalCode: "15001",
      coordinates: {
        lat: -12.0464,
        lng: -77.0428
      },
      ubigeo: "150101"
    },
    additionalCampuses: [
      {
        campusId: "CAMPUS-002",
        name: "Sede Anexo Norte",
        address: "Jr. Secundario 456",
        coordinates: {
          lat: -12.0500,
          lng: -77.0400
        },
        educationLevels: ["PRIMARIA"]
      }
    ]
  },
  academicStructure: {
    educationLevels: [
      {
        levelId: "INICIAL",
        levelName: "Educación Inicial",
        grades: [
          {
            gradeId: "3_ANOS",
            gradeName: "3 años",
            sections: ["A", "B"],
            maxStudentsPerSection: 20
          },
          {
            gradeId: "4_ANOS",
            gradeName: "4 años",
            sections: ["A", "B"],
            maxStudentsPerSection: 20
          },
          {
            gradeId: "5_ANOS",
            gradeName: "5 años",
            sections: ["A", "B", "C"],
            maxStudentsPerSection: 25
          }
        ]
      },
      {
        levelId: "PRIMARIA",
        levelName: "Educación Primaria",
        grades: [
          {
            gradeId: "1_PRIM",
            gradeName: "1er Grado",
            sections: ["A", "B", "C"],
            maxStudentsPerSection: 30
          }
          // ... más grados
        ]
      }
    ]
  },
  academicCalendar: {
    currentYear: 2025,
    startDate: ISODate("2025-03-04"),
    endDate: ISODate("2025-12-20"),
    periods: [
      {
        periodId: "BIMESTRE_1",
        periodName: "Primer Bimestre",
        startDate: ISODate("2025-03-04"),
        endDate: ISODate("2025-05-10"),
        isCurrent: true
      }
      // ... más períodos
    ],
    holidays: [
      {
        date: ISODate("2025-04-18"),
        name: "Viernes Santo",
        type: "NATIONAL_HOLIDAY"
      }
      // ... más feriados
    ]
  },
  configuration: {
    gradeSystem: "BIMESTRAL|TRIMESTRAL|SEMESTRAL",
    evaluationScale: {
      type: "LITERAL", // A, B, C o NUMERIC 0-20
      passingGrade: "C",
      excellentGrade: "AD"
    },
    attendancePolicy: {
      maxAbsencesPerPeriod: 30,
      tardinessPenalty: true,
      justificationRequired: true
    },
    disciplinePolicy: {
      sanctionLevels: ["VERBAL", "WRITTEN", "SUSPENSION", "EXPULSION"],
      appealProcess: true
    }
  },
  statistics: {
    totalStudents: 850,
    totalTeachers: 45,
    totalClassrooms: 35,
    studentsByLevel: {
      "INICIAL": 120,
      "PRIMARIA": 420,
      "SECUNDARIA": 310
    }
  },
  contacts: {
    principal: {
      name: "María Elena Rodríguez",
      email: "direccion@iesanjose.edu.pe",
      phone: "+51987654321"
    },
    secretary: {
      name: "Carmen López",
      email: "secretaria@iesanjose.edu.pe",
      phone: "+51987654322"
    }
  },
  metadata: {
    createdAt: ISODate("2025-08-26T00:00:00Z"),
    updatedAt: ISODate("2025-08-26T00:00:00Z"),
    createdBy: "ADMIN-SYSTEM",
    isActive: true,
    version: 1
  }
}
```

#### **Índices MongoDB - institutions:**

```javascript
// Índices principales
db.institutions.createIndex({ "institutionId": 1 }, { unique: true })
db.institutions.createIndex({ "basicInfo.modularCode": 1 }, { unique: true })
db.institutions.createIndex({ "basicInfo.ruc": 1 }, { unique: true })
db.institutions.createIndex({ "basicInfo.ugel": 1 })

// Índices geoespaciales
db.institutions.createIndex({ "location.mainCampus.coordinates": "2dsphere" })
db.institutions.createIndex({ "location.additionalCampuses.coordinates": "2dsphere" })

// Índices de búsqueda
db.institutions.createIndex({
  "basicInfo.name": "text",
  "basicInfo.shortName": "text"
})

// Índices de configuración
db.institutions.createIndex({ "basicInfo.status": 1, "metadata.isActive": 1 })
```

---

### **MS-VG-0002: User Management Service**

#### **Colección: users**

```javascript
{
  _id: ObjectId(),
  userId: "USR-2025-000001",
  userType: "STUDENT|TEACHER|DIRECTOR|SECRETARY|AUXILIARY|PARENT|ADMIN",
  personalInfo: {
    firstName: "Juan Carlos",
    lastName: "Pérez García",
    documentType: "DNI|PASSPORT|CE",
    documentNumber: "12345678",
    birthDate: ISODate("2010-03-15"),
    gender: "M|F|OTHER",
    nationality: "PERUANA",
    address: {
      street: "Av. Lima 123",
      district: "San Martín de Porres",
      city: "Lima",
      postalCode: "15102",
      coordinates: {
        lat: -12.0464,
        lng: -77.0428
      }
    }
  },
  contactInfo: {
    email: "juan.perez@email.com",
    phone: "+51987654321",
    emergencyContact: {
      name: "María García",
      relationship: "MADRE",
      phone: "+51876543210"
    }
  },
  academicInfo: {
    // Para ESTUDIANTES
    grade: "5TO_PRIMARIA",
    section: "A",
    studentCode: "EST-2025-0001",
    enrollmentDate: ISODate("2025-03-01"),
    status: "ACTIVE|INACTIVE|TRANSFERRED|GRADUATED",
    medicalInfo: {
      allergies: ["Polen", "Mariscos"],
      medications: ["Ventolin"],
      bloodType: "O+",
      healthInsurance: "SIS",
      specialNeeds: "Ninguna"
    },
    // Para PROFESORES
    specialties: ["MATEMÁTICAS", "FÍSICA"],
    certifications: [
      {
        name: "Licenciatura en Educación",
        institution: "Universidad Nacional Mayor de San Marcos",
        year: 2020
      }
    ],
    experience: {
      totalYears: 5,
      institutions: [
        {
          name: "I.E. San José",
          years: 3,
          position: "PROFESOR_PRINCIPAL"
        }
      ]
    }
  },
  familyRelations: [
    {
      relatedUserId: "USR-2025-000002",
      relationship: "PADRE|MADRE|TUTOR|HERMANO",
      isPrimary: true
    }
  ],
  metadata: {
    createdAt: ISODate("2025-08-26T10:00:00Z"),
    updatedAt: ISODate("2025-08-26T10:00:00Z"),
    createdBy: "USR-2025-000000",
    isActive: true,
    version: 1
  }
}
```

#### **Índices MongoDB - users:**

```javascript
// Índices principales
db.users.createIndex({ "userId": 1 }, { unique: true })
db.users.createIndex({ "userType": 1, "metadata.isActive": 1 })
db.users.createIndex({ "personalInfo.documentNumber": 1 }, { unique: true })
db.users.createIndex({ "contactInfo.email": 1 }, { sparse: true })

// Índices académicos
db.users.createIndex({ "academicInfo.grade": 1, "academicInfo.section": 1 })
db.users.createIndex({ "academicInfo.studentCode": 1 }, { sparse: true })

// Índices de búsqueda
db.users.createIndex({
  "personalInfo.firstName": "text",
  "personalInfo.lastName": "text"
})

// Índices geoespaciales
db.users.createIndex({ "personalInfo.address.coordinates": "2dsphere" })
```

---

### **MS-VG-0011: Notifications Service**

#### **Colección: notifications**

```javascript
{
  _id: ObjectId(),
  notificationId: "NOT-2025-000001",
  type: "EMAIL|SMS|PUSH|IN_APP",
  priority: "LOW|MEDIUM|HIGH|URGENT",
  template: {
    templateId: "TPL-ATTENDANCE-ALERT",
    name: "Alerta de Inasistencia",
    variables: {
      studentName: "Juan Pérez",
      absentDays: 3,
      parentName: "María García"
    }
  },
  recipients: [
    {
      userId: "USR-2025-000002",
      type: "PRIMARY",
      contact: {
        email: "maria.garcia@email.com",
        phone: "+51987654321"
      },
      deliveryStatus: "PENDING|SENT|DELIVERED|FAILED|READ",
      deliveredAt: ISODate("2025-08-26T10:30:00Z"),
      readAt: ISODate("2025-08-26T10:45:00Z"),
      attempts: 1
    }
  ],
  content: {
    subject: "Alerta: Inasistencia de su hijo Juan Pérez",
    body: "Estimada María García, le informamos que su hijo Juan Pérez...",
    htmlBody: "<html><body>...</body></html>",
    attachments: [
      {
        fileName: "reporte_asistencia.pdf",
        fileUrl: "https://storage.example.com/files/...",
        fileSize: 1024
      }
    ]
  },
  scheduling: {
    scheduledFor: ISODate("2025-08-26T08:00:00Z"),
    timezone: "America/Lima",
    isRecurring: false,
    recurringPattern: "DAILY|WEEKLY|MONTHLY"
  },
  tracking: {
    campaignId: "CAMP-ATTENDANCE-2025",
    sourceService: "MS-VG-0006",
    correlationId: "CORR-ATT-001",
    metrics: {
      opens: 1,
      clicks: 0,
      bounces: 0
    }
  },
  metadata: {
    createdAt: ISODate("2025-08-26T07:30:00Z"),
    sentAt: ISODate("2025-08-26T08:00:00Z"),
    createdBy: "SYSTEM",
    retryCount: 0,
    maxRetries: 3
  }
}
```

#### **Colección: notification_templates**

```javascript
{
  _id: ObjectId(),
  templateId: "TPL-ATTENDANCE-ALERT",
  name: "Alerta de Inasistencia",
  category: "ATTENDANCE|GRADES|DISCIPLINE|GENERAL",
  type: "EMAIL|SMS|PUSH",
  isActive: true,
  variables: [
    {
      name: "studentName",
      type: "STRING",
      required: true,
      description: "Nombre del estudiante"
    },
    {
      name: "absentDays",
      type: "NUMBER",
      required: true,
      description: "Días de ausencia"
    }
  ],
  content: {
    subject: "Alerta: Inasistencia de su hijo {{studentName}}",
    bodyTemplate: "Estimado/a {{parentName}}, le informamos que su hijo {{studentName}} ha faltado {{absentDays}} días...",
    htmlTemplate: "<html><body><h1>Alerta de Inasistencia</h1>...</body></html>",
    smsTemplate: "Alerta: {{studentName}} faltó {{absentDays}} días. Contactar IE."
  },
  metadata: {
    createdAt: ISODate("2025-08-26T00:00:00Z"),
    updatedAt: ISODate("2025-08-26T00:00:00Z"),
    createdBy: "USR-ADMIN-001",
    version: 1
  }
}
```

---

## 🐘 **POSTGRESQL SCHEMAS**

### **MS-VG-0003: Auth & Security Service**

#### **Tabla: auth_users**

```sql
CREATE TABLE auth_users (
    id SERIAL PRIMARY KEY,
    user_id VARCHAR(50) NOT NULL UNIQUE,
    username VARCHAR(100) NOT NULL UNIQUE,
    email VARCHAR(255) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    salt VARCHAR(100) NOT NULL,
    is_active BOOLEAN DEFAULT true,
    is_verified BOOLEAN DEFAULT false,
    failed_login_attempts INTEGER DEFAULT 0,
    locked_until TIMESTAMP NULL,
    last_login_at TIMESTAMP NULL,
    password_changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Índices para auth_users
CREATE INDEX idx_auth_users_user_id ON auth_users(user_id);
CREATE INDEX idx_auth_users_username ON auth_users(username);
CREATE INDEX idx_auth_users_email ON auth_users(email);
CREATE INDEX idx_auth_users_active ON auth_users(is_active);
```

#### **Tabla: user_roles**

```sql
CREATE TABLE user_roles (
    id SERIAL PRIMARY KEY,
    role_id VARCHAR(50) NOT NULL UNIQUE,
    role_name VARCHAR(100) NOT NULL,
    description TEXT,
    is_system_role BOOLEAN DEFAULT false,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Datos maestros de roles
INSERT INTO user_roles (role_id, role_name, description, is_system_role) VALUES
('ADMIN_SISTEMA', 'Administrador del Sistema', 'Acceso completo al sistema', true),
('DIRECTOR', 'Director', 'Gestión institucional completa', true),
('SUBDIRECTOR', 'Subdirector', 'Gestión académica y disciplinaria', true),
('COORDINADOR_ACADEMICO', 'Coordinador Académico', 'Gestión de cursos y profesores', true),
('PROFESOR', 'Profesor', 'Gestión de sus cursos asignados', true),
('TUTOR', 'Tutor', 'Gestión de su sección asignada', true),
('PSICOLOGO', 'Psicólogo', 'Gestión de bienestar estudiantil', true),
('SECRETARIA', 'Secretaria', 'Gestión administrativa', true),
('AUXILIAR', 'Auxiliar', 'Funciones de apoyo', true),
('PADRE_FAMILIA', 'Padre de Familia', 'Consulta información de hijos', true),
('ESTUDIANTE', 'Estudiante', 'Consulta información académica', true);
```

#### **Tabla: user_role_assignments**

```sql
CREATE TABLE user_role_assignments (
    id SERIAL PRIMARY KEY,
    user_id VARCHAR(50) NOT NULL,
    role_id VARCHAR(50) NOT NULL,
    assigned_by VARCHAR(50) NOT NULL,
    assigned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NULL,
    is_active BOOLEAN DEFAULT true,

    FOREIGN KEY (role_id) REFERENCES user_roles(role_id),
    UNIQUE(user_id, role_id)
);

CREATE INDEX idx_user_roles_user_id ON user_role_assignments(user_id);
CREATE INDEX idx_user_roles_role_id ON user_role_assignments(role_id);
CREATE INDEX idx_user_roles_active ON user_role_assignments(is_active);
```

#### **Tabla: permissions**

```sql
CREATE TABLE permissions (
    id SERIAL PRIMARY KEY,
    permission_id VARCHAR(50) NOT NULL UNIQUE,
    permission_name VARCHAR(100) NOT NULL,
    resource VARCHAR(100) NOT NULL,
    action VARCHAR(50) NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Ejemplos de permisos
INSERT INTO permissions (permission_id, permission_name, resource, action, description) VALUES
('USER_CREATE', 'Crear Usuarios', 'USERS', 'CREATE', 'Crear nuevos usuarios en el sistema'),
('USER_READ', 'Leer Usuarios', 'USERS', 'READ', 'Consultar información de usuarios'),
('USER_UPDATE', 'Actualizar Usuarios', 'USERS', 'UPDATE', 'Modificar información de usuarios'),
('USER_DELETE', 'Eliminar Usuarios', 'USERS', 'DELETE', 'Eliminar usuarios del sistema'),
('GRADES_CREATE', 'Crear Notas', 'GRADES', 'CREATE', 'Registrar calificaciones'),
('GRADES_READ', 'Leer Notas', 'GRADES', 'READ', 'Consultar calificaciones'),
('ATTENDANCE_CREATE', 'Registrar Asistencia', 'ATTENDANCE', 'CREATE', 'Marcar asistencia'),
('REPORTS_GENERATE', 'Generar Reportes', 'REPORTS', 'CREATE', 'Crear reportes del sistema');
```

#### **Tabla: role_permissions**

```sql
CREATE TABLE role_permissions (
    id SERIAL PRIMARY KEY,
    role_id VARCHAR(50) NOT NULL,
    permission_id VARCHAR(50) NOT NULL,
    granted_by VARCHAR(50) NOT NULL,
    granted_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (role_id) REFERENCES user_roles(role_id),
    FOREIGN KEY (permission_id) REFERENCES permissions(permission_id),
    UNIQUE(role_id, permission_id)
);
```

#### **Tabla: user_sessions**

```sql
CREATE TABLE user_sessions (
    id SERIAL PRIMARY KEY,
    session_id VARCHAR(255) NOT NULL UNIQUE,
    user_id VARCHAR(50) NOT NULL,
    jwt_token_hash VARCHAR(255) NOT NULL,
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,
    last_activity_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_active BOOLEAN DEFAULT true,
    logout_at TIMESTAMP NULL
);

CREATE INDEX idx_sessions_user_id ON user_sessions(user_id);
CREATE INDEX idx_sessions_session_id ON user_sessions(session_id);
CREATE INDEX idx_sessions_active ON user_sessions(is_active);
CREATE INDEX idx_sessions_expires ON user_sessions(expires_at);
```

#### **Tabla: security_audit_log**

```sql
CREATE TABLE security_audit_log (
    id SERIAL PRIMARY KEY,
    event_id VARCHAR(50) NOT NULL,
    user_id VARCHAR(50),
    event_type VARCHAR(50) NOT NULL, -- LOGIN, LOGOUT, FAILED_LOGIN, PERMISSION_CHANGE, etc.
    ip_address INET,
    user_agent TEXT,
    details JSONB,
    severity VARCHAR(20) DEFAULT 'INFO', -- INFO, WARN, ERROR, CRITICAL
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_audit_user_id ON security_audit_log(user_id);
CREATE INDEX idx_audit_event_type ON security_audit_log(event_type);
CREATE INDEX idx_audit_created_at ON security_audit_log(created_at);
CREATE INDEX idx_audit_severity ON security_audit_log(severity);
```

---

### **MS-VG-0004: Academic Management Service**

#### **Tabla: academic_years**

```sql
CREATE TABLE academic_years (
    id SERIAL PRIMARY KEY,
    year_id VARCHAR(20) NOT NULL UNIQUE,
    year_number INTEGER NOT NULL,
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    is_current BOOLEAN DEFAULT false,
    status VARCHAR(20) DEFAULT 'PLANNING', -- PLANNING, ACTIVE, COMPLETED, CANCELLED
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE UNIQUE INDEX idx_academic_years_current ON academic_years(is_current) WHERE is_current = true;
```

#### **Tabla: academic_periods**

```sql
CREATE TABLE academic_periods (
    id SERIAL PRIMARY KEY,
    period_id VARCHAR(50) NOT NULL UNIQUE,
    academic_year_id VARCHAR(20) NOT NULL,
    period_name VARCHAR(50) NOT NULL, -- "Primer Bimestre", "Segundo Trimestre"
    period_number INTEGER NOT NULL,
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    is_current BOOLEAN DEFAULT false,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (academic_year_id) REFERENCES academic_years(year_id)
);
```

#### **Tabla: education_levels**

```sql
CREATE TABLE education_levels (
    id SERIAL PRIMARY KEY,
    level_id VARCHAR(20) NOT NULL UNIQUE,
    level_name VARCHAR(50) NOT NULL, -- "INICIAL", "PRIMARIA", "SECUNDARIA"
    description TEXT,
    min_age INTEGER,
    max_age INTEGER,
    is_active BOOLEAN DEFAULT true
);

-- Datos maestros
INSERT INTO education_levels (level_id, level_name, description, min_age, max_age) VALUES
('INICIAL', 'Educación Inicial', 'Nivel inicial de 3 a 5 años', 3, 5),
('PRIMARIA', 'Educación Primaria', 'Nivel primario de 6 a 11 años', 6, 11),
('SECUNDARIA', 'Educación Secundaria', 'Nivel secundario de 12 a 17 años', 12, 17);
```

#### **Tabla: grades**

```sql
CREATE TABLE grades (
    id SERIAL PRIMARY KEY,
    grade_id VARCHAR(20) NOT NULL UNIQUE,
    level_id VARCHAR(20) NOT NULL,
    grade_name VARCHAR(50) NOT NULL, -- "1ERO PRIMARIA", "5TO SECUNDARIA"
    grade_number INTEGER NOT NULL,
    is_active BOOLEAN DEFAULT true,

    FOREIGN KEY (level_id) REFERENCES education_levels(level_id)
);

-- Datos maestros para primaria
INSERT INTO grades (grade_id, level_id, grade_name, grade_number) VALUES
('1_PRIM', 'PRIMARIA', '1ERO PRIMARIA', 1),
('2_PRIM', 'PRIMARIA', '2DO PRIMARIA', 2),
('3_PRIM', 'PRIMARIA', '3ERO PRIMARIA', 3),
('4_PRIM', 'PRIMARIA', '4TO PRIMARIA', 4),
('5_PRIM', 'PRIMARIA', '5TO PRIMARIA', 5),
('6_PRIM', 'PRIMARIA', '6TO PRIMARIA', 6);
```

#### **Tabla: sections**

```sql
CREATE TABLE sections (
    id SERIAL PRIMARY KEY,
    section_id VARCHAR(50) NOT NULL UNIQUE,
    grade_id VARCHAR(20) NOT NULL,
    section_letter VARCHAR(10) NOT NULL, -- "A", "B", "C"
    academic_year_id VARCHAR(20) NOT NULL,
    classroom_id VARCHAR(50),
    max_students INTEGER DEFAULT 30,
    current_students INTEGER DEFAULT 0,
    tutor_user_id VARCHAR(50),
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (grade_id) REFERENCES grades(grade_id),
    FOREIGN KEY (academic_year_id) REFERENCES academic_years(year_id),
    UNIQUE(grade_id, section_letter, academic_year_id)
);
```

#### **Tabla: courses**

```sql
CREATE TABLE courses (
    id SERIAL PRIMARY KEY,
    course_id VARCHAR(50) NOT NULL UNIQUE,
    course_name VARCHAR(100) NOT NULL,
    course_code VARCHAR(20) NOT NULL,
    level_id VARCHAR(20) NOT NULL,
    area VARCHAR(50), -- "COMUNICACIÓN", "MATEMÁTICA", "CIENCIAS", etc.
    weekly_hours INTEGER DEFAULT 0,
    is_mandatory BOOLEAN DEFAULT true,
    description TEXT,
    competencies JSONB,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (level_id) REFERENCES education_levels(level_id)
);

-- Ejemplos de cursos
INSERT INTO courses (course_id, course_name, course_code, level_id, area, weekly_hours) VALUES
('MATH_PRIM', 'Matemática', 'MAT', 'PRIMARIA', 'MATEMÁTICA', 6),
('COMM_PRIM', 'Comunicación', 'COM', 'PRIMARIA', 'COMUNICACIÓN', 6),
('CIENC_PRIM', 'Ciencia y Tecnología', 'CYT', 'PRIMARIA', 'CIENCIAS', 3),
('PERS_SOC_PRIM', 'Personal Social', 'PS', 'PRIMARIA', 'PERSONAL SOCIAL', 3),
('ART_PRIM', 'Arte y Cultura', 'ART', 'PRIMARIA', 'ARTE', 2),
('EF_PRIM', 'Educación Física', 'EF', 'PRIMARIA', 'EDUCACIÓN FÍSICA', 3),
('REL_PRIM', 'Educación Religiosa', 'REL', 'PRIMARIA', 'EDUCACIÓN RELIGIOSA', 2);
```

#### **Tabla: classrooms**

```sql
CREATE TABLE classrooms (
    id SERIAL PRIMARY KEY,
    classroom_id VARCHAR(50) NOT NULL UNIQUE,
    classroom_name VARCHAR(100) NOT NULL,
    building VARCHAR(50),
    floor VARCHAR(10),
    capacity INTEGER NOT NULL,
    classroom_type VARCHAR(50), -- "AULA_REGULAR", "LABORATORIO", "BIBLIOTECA", etc.
    equipment JSONB,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### **Tabla: student_enrollments**

```sql
CREATE TABLE student_enrollments (
    id SERIAL PRIMARY KEY,
    enrollment_id VARCHAR(50) NOT NULL UNIQUE,
    student_user_id VARCHAR(50) NOT NULL,
    section_id VARCHAR(50) NOT NULL,
    academic_year_id VARCHAR(20) NOT NULL,
    enrollment_date DATE NOT NULL,
    enrollment_type VARCHAR(30) DEFAULT 'REGULAR', -- REGULAR, TRANSFER, READMISSION
    status VARCHAR(20) DEFAULT 'ACTIVE', -- ACTIVE, TRANSFERRED, WITHDRAWN, GRADUATED
    previous_institution VARCHAR(200),
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (section_id) REFERENCES sections(section_id),
    FOREIGN KEY (academic_year_id) REFERENCES academic_years(year_id),
    UNIQUE(student_user_id, academic_year_id)
);

CREATE INDEX idx_enrollments_student ON student_enrollments(student_user_id);
CREATE INDEX idx_enrollments_section ON student_enrollments(section_id);
CREATE INDEX idx_enrollments_year ON student_enrollments(academic_year_id);
```

---

### **MS-VG-0005: Teacher Assignment Service**

#### **Tabla: teacher_assignments**

```sql
CREATE TABLE teacher_assignments (
    id SERIAL PRIMARY KEY,
    assignment_id VARCHAR(50) NOT NULL UNIQUE,
    teacher_user_id VARCHAR(50) NOT NULL,
    course_id VARCHAR(50) NOT NULL,
    section_id VARCHAR(50) NOT NULL,
    academic_year_id VARCHAR(20) NOT NULL,
    assignment_type VARCHAR(30) DEFAULT 'REGULAR', -- REGULAR, SUBSTITUTE, SUPPORT
    weekly_hours INTEGER NOT NULL,
    start_date DATE NOT NULL,
    end_date DATE,
    is_primary_teacher BOOLEAN DEFAULT true,
    status VARCHAR(20) DEFAULT 'ACTIVE', -- ACTIVE, SUSPENDED, TERMINATED
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_by VARCHAR(50) NOT NULL,

    UNIQUE(teacher_user_id, course_id, section_id, academic_year_id)
);

CREATE INDEX idx_assignments_teacher ON teacher_assignments(teacher_user_id);
CREATE INDEX idx_assignments_course ON teacher_assignments(course_id);
CREATE INDEX idx_assignments_section ON teacher_assignments(section_id);
```

#### **Tabla: teacher_schedules**

```sql
CREATE TABLE teacher_schedules (
    id SERIAL PRIMARY KEY,
    schedule_id VARCHAR(50) NOT NULL UNIQUE,
    assignment_id VARCHAR(50) NOT NULL,
    day_of_week INTEGER NOT NULL, -- 1=Lunes, 7=Domingo
    start_time TIME NOT NULL,
    end_time TIME NOT NULL,
    classroom_id VARCHAR(50),
    academic_period_id VARCHAR(50),
    is_active BOOLEAN DEFAULT true,

    FOREIGN KEY (assignment_id) REFERENCES teacher_assignments(assignment_id)
);

CREATE INDEX idx_schedules_assignment ON teacher_schedules(assignment_id);
CREATE INDEX idx_schedules_day_time ON teacher_schedules(day_of_week, start_time);
```

#### **Tabla: teacher_workload**

```sql
CREATE TABLE teacher_workload (
    id SERIAL PRIMARY KEY,
    teacher_user_id VARCHAR(50) NOT NULL,
    academic_year_id VARCHAR(20) NOT NULL,
    total_assigned_hours INTEGER DEFAULT 0,
    max_allowed_hours INTEGER DEFAULT 30,
    sections_count INTEGER DEFAULT 0,
    courses_count INTEGER DEFAULT 0,
    last_calculated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    UNIQUE(teacher_user_id, academic_year_id)
);
```

---

### **🔄 4. CONEXIÓN ENTRE MICROSERVICIOS**

#### **Tabla de Referencias Distribuidas**

```sql
-- Tabla para mantener referencias entre servicios
CREATE TABLE cross_service_references (
    id SERIAL PRIMARY KEY,
    source_service VARCHAR(50) NOT NULL,
    source_entity VARCHAR(50) NOT NULL,
    source_id VARCHAR(50) NOT NULL,
    target_service VARCHAR(50) NOT NULL,
    target_entity VARCHAR(50) NOT NULL,
    target_id VARCHAR(50) NOT NULL,
    relationship_type VARCHAR(50) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    UNIQUE(source_service, source_entity, source_id, target_service, target_entity)
);

-- Ejemplos de referencias cruzadas:
-- MS-VG-0003 (Academic) -> MS-VG-0001 (Users) para estudiantes en secciones
-- MS-VG-0004 (Assignments) -> MS-VG-0001 (Users) para profesores
-- MS-VG-0005 (Grades) -> MS-VG-0003 (Academic) para cursos y secciones
```

#### **Eventos de Sincronización**

```sql
CREATE TABLE synchronization_events (
    id SERIAL PRIMARY KEY,
    event_id VARCHAR(50) NOT NULL UNIQUE,
    event_type VARCHAR(50) NOT NULL,
    source_service VARCHAR(50) NOT NULL,
    target_services TEXT[], -- Array de servicios destinatarios
    entity_type VARCHAR(50) NOT NULL,
    entity_id VARCHAR(50) NOT NULL,
    event_data JSONB NOT NULL,
    status VARCHAR(20) DEFAULT 'PENDING', -- PENDING, PROCESSING, COMPLETED, FAILED
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    processed_at TIMESTAMP NULL,
    retry_count INTEGER DEFAULT 0,
    max_retries INTEGER DEFAULT 3,
    error_message TEXT
);

CREATE INDEX idx_sync_events_status ON synchronization_events(status);
CREATE INDEX idx_sync_events_target ON synchronization_events USING GIN(target_services);
```
