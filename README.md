# AuthAPI - Sistema de Autenticación y Autorización

## 📋 Descripción General
AuthAPI es un sistema completo de autenticación y autorización desarrollado en ASP.NET Core con Identity Framework. Proporciona funcionalidades robustas para manejo de usuarios, roles, autenticación JWT y gestión de contraseñas.

## 🚀 Características Principales
- ✅ Registro y autenticación de usuarios
- ✅ Gestión de roles y permisos
- ✅ Autenticación JWT con Refresh Tokens
- ✅ Recuperación y restablecimiento de contraseñas
- ✅ Envío de correos electrónicos
- ✅ Validación de datos robusta
- ✅ Autorización basada en roles

## 🔧 Configuración e Instalación

### Prerrequisitos
- .NET 9.0 SDK
- SQL Server o SQL Server Express
- Visual Studio 2022 o VS Code

### Configuración de Base de Datos
1. Actualizar la cadena de conexión en `appsettings.json`
2. Ejecutar las migraciones:
```bash
dotnet ef database update
```

### Configuración de Email (Opcional)
Configurar las credenciales SMTP en `appsettings.json`:
```json
{
  "MailSettings": {
    "SenderEmail": "tu-email@gmail.com",
    "SenderName": "AuthAPI System",
    "Server": "smtp.gmail.com",
    "Port": "587",
    "UserName": "tu-email@gmail.com",
    "Password": "tu-app-password"
  }
}
```

## 🌐 Base URL
```
Desarrollo: https://localhost:5000/api
```

---

## 📚 Documentación de Endpoints

### 1. 👤 AccountController (`/api/account`)

#### 1.1 Registro de Usuario
- **Endpoint:** `POST /api/account/register`
- **Autorización:** ❌ No requerida (`[AllowAnonymous]`)
- **Descripción:** Crea una nueva cuenta de usuario en el sistema
- **Datos requeridos:**
```json
{
  "emailAddress": "usuario@ejemplo.com",    // Requerido, formato email válido
  "fullname": "Juan Pérez Martínez",       // Requerido, nombre completo
  "password": "MiPassword123!",            // Requerido, mínimo 6 caracteres
  "roles": ["User", "Admin"]               // Opcional, si es null se asigna "User"
}
```
- **Respuesta exitosa (200):**
```json
{
  "isSuccess": true,
  "message": "Account Created Sucessfully!!!"
}
```

---

#### 1.2 Inicio de Sesión
- **Endpoint:** `POST /api/account/login`
- **Autorización:** ❌ No requerida (`[AllowAnonymous]`)
- **Descripción:** Autentica usuario y genera tokens JWT + Refresh Token
- **Datos requeridos:**
```json
{
  "email": "usuario@ejemplo.com",
  "password": "MiPassword123!"
}
```
- **Respuesta exitosa (200):**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "base64-refresh-token-string",
  "isSuccess": true,
  "message": "Login Success"
}
```

---

#### 1.3 Recuperar Contraseña
- **Endpoint:** `POST /api/account/forgot-password`
- **Autorización:** ❌ No requerida (`[AllowAnonymous]`)
- **Descripción:** Envía email con enlace para restablecer contraseña
- **Datos requeridos:**
```json
{
  "email": "usuario@ejemplo.com"
}
```
- **Respuesta exitosa (200):**
```json
{
  "isSuccess": true,
  "message": "Se ha enviado un correo con las instrucciones para restablecer la contraseña."
}
```

---

#### 1.4 Cambiar Contraseña
- **Endpoint:** `POST /api/account/change-password`
- **Autorización:** ✅ JWT Token requerido (`[Authorize]`)
- **Descripción:** Permite al usuario autenticado cambiar su contraseña actual
- **Datos requeridos:**
```json
{
  "email": "usuario@ejemplo.com",
  "currentPassword": "PasswordActual123!",
  "newPassword": "NuevaPassword123!"
}
```
- **Respuesta exitosa (200):**
```json
{
  "isSuccess": true,
  "message": "Password changed successfully"
}
```

---

#### 1.5 Restablecer Contraseña
- **Endpoint:** `POST /api/account/reset-password`
- **Autorización:** ❌ No requerida (`[AllowAnonymous]`)
- **Descripción:** Restablece contraseña usando token enviado por email
- **Datos requeridos:**
```json
{
  "email": "usuario@ejemplo.com",
  "token": "reset-token-from-email",
  "newPassword": "NuevaPassword123!"
}
```
- **Respuesta exitosa (200):**
```json
{
  "isSuccess": true,
  "message": "Password reset Successfully"
}
```

---

#### 1.6 Obtener Detalles del Usuario
- **Endpoint:** `GET /api/account/detail`
- **Autorización:** ✅ JWT Token requerido (`[Authorize]`)
- **Descripción:** Obtiene información completa del usuario autenticado
- **Datos requeridos:** Ninguno (token en Authorization header)
- **Respuesta exitosa (200):**
```json
{
  "id": "user-guid-id",
  "email": "usuario@ejemplo.com",
  "fullName": "Juan Pérez Martínez",
  "roles": ["User", "Admin"],
  "phoneNumber": "+34612345678",
  "phoneNumberConfirmed": false,
  "accessFailedCount": 0
}
```

---

#### 1.7 Listar Todos los Usuarios
- **Endpoint:** `GET /api/account`
- **Autorización:** ✅ JWT Token requerido (`[Authorize]`)
- **Descripción:** Obtiene lista completa de todos los usuarios registrados
- **Datos requeridos:** Ninguno (token en Authorization header)
- **Respuesta exitosa (200):**
```json
[
  {
    "id": "user-guid-1",
    "email": "juan@ejemplo.com",
    "fullName": "Juan Pérez",
    "roles": ["User"],
    "phoneNumber": null,
    "phoneNumberConfirmed": false,
    "accessFailedCount": 0
  },
  {
    "id": "user-guid-2",
    "email": "maria@ejemplo.com",
    "fullName": "María García",
    "roles": ["Admin", "User"],
    "phoneNumber": "+34698765432",
    "phoneNumberConfirmed": true,
    "accessFailedCount": 0
  }
]
```

---

### 2. 🛡️ RolesController (`/api/roles`)

#### 2.1 Crear Rol
- **Endpoint:** `POST /api/roles`
- **Autorización:** ✅ JWT Token + Rol Admin (`[Authorize(Roles = "Admin")]`)
- **Descripción:** Crea un nuevo rol en el sistema
- **Datos requeridos:**
```json
{
  "roleName": "Manager"
}
```
- **Respuesta exitosa (200):**
```json
{
  "message": "Rol creado con exito"
}
```

---

#### 2.2 Listar Roles
- **Endpoint:** `GET /api/roles`
- **Autorización:** ❌ No requerida (`[AllowAnonymous]`)
- **Descripción:** Obtiene todos los roles con estadísticas de usuarios
- **Datos requeridos:** Ninguno
- **Respuesta exitosa (200):**
```json
[
  {
    "id": "role-guid-1",
    "name": "Admin",
    "totalUsers": 2
  },
  {
    "id": "role-guid-2",
    "name": "User",
    "totalUsers": 15
  },
  {
    "id": "role-guid-3",
    "name": "Manager",
    "totalUsers": 5
  }
]
```

---

#### 2.3 Eliminar Rol
- **Endpoint:** `DELETE /api/roles/{id}`
- **Autorización:** ✅ JWT Token + Rol Admin (`[Authorize(Roles = "Admin")]`)
- **Descripción:** Elimina un rol específico del sistema
- **Parámetros:** 
  - `id` (string): ID del rol a eliminar
- **Ejemplo:** `DELETE /api/roles/role-guid-123`
- **Respuesta exitosa (200):**
```json
{
  "message": "Rol elimimnado correctamente"
}
```

---

#### 2.4 Asignar Rol a Usuario
- **Endpoint:** `POST /api/roles/assing`
- **Autorización:** ✅ JWT Token + Rol Admin (`[Authorize(Roles = "Admin")]`)
- **Descripción:** Asigna un rol específico a un usuario
- **Datos requeridos:**
```json
{
  "userId": "user-guid-123",
  "roleId": "Manager"           // Nombre del rol, no el ID
}
```
- **Respuesta exitosa (200):**
```json
{
  "message": "Rol asignado correctamente"
}
```

---

### 3. 🌤️ WeatherForecastController (`/weatherforecast`)

#### 3.1 Obtener Pronóstico del Tiempo (Demo)
- **Endpoint:** `GET /weatherforecast`
- **Autorización:** ❌ No requerida
- **Descripción:** Endpoint de demostración que devuelve datos meteorológicos ficticios
- **Datos requeridos:** Ninguno
- **Respuesta exitosa (200):**
```json
[
  {
    "date": "2024-10-15",
    "temperatureC": 25,
    "temperatureF": 77,
    "summary": "Warm"
  },
  {
    "date": "2024-10-16",
    "temperatureC": 18,
    "temperatureF": 64,
    "summary": "Cool"
  }
]
```

---

## 🔐 Configuración de Headers

### Para endpoints con autorización:
```http
Authorization: Bearer {tu_jwt_token}
Content-Type: application/json
```

### Para endpoints públicos:
```http
Content-Type: application/json
```

---

## 📊 Códigos de Estado HTTP

| Código | Descripción |
|--------|-------------|
| **200** | ✅ Operación exitosa |
| **400** | ❌ Solicitud inválida - datos incorrectos |
| **401** | 🔒 No autorizado - token inválido/expirado |
| **403** | 🚫 Prohibido - sin permisos suficientes |
| **404** | 🔍 Recurso no encontrado |
| **500** | ⚠️ Error interno del servidor |

---

## 💡 Notas Importantes

### Roles y Permisos
- **Rol por Defecto:** Al registrarse sin especificar roles, se asigna automáticamente `"User"`
- **Administración:** Solo usuarios con rol `"Admin"` pueden gestionar roles y usuarios
- **Múltiples Roles:** Un usuario puede tener múltiples roles asignados

### Tokens JWT
- **Expiración:** Los tokens JWT expiran en 24 horas
- **Refresh Tokens:** Incluidos para renovación automática de sesiones
- **Seguridad:** Tokens firmados con clave secreta configurable

### Sistema de Email
- **SMTP:** Configuración necesaria para recuperación de contraseñas
- **HTML Templates:** Emails con formato HTML profesional
- **Enlaces Seguros:** URLs con tokens codificados para seguridad

### Validación de Datos
- **Email:** Formato válido requerido
- **Contraseñas:** Políticas de ASP.NET Core Identity aplicadas
- **Campos Requeridos:** Validación automática con ModelState

---

## 🧪 Ejemplos de Uso con cURL

### Registro de Usuario:
```bash
curl -X POST "https://localhost:5000/api/account/register" \
  -H "Content-Type: application/json" \
  -d '{
    "emailAddress": "test@example.com",
    "fullname": "Usuario Test",
    "password": "Test123!",
    "roles": ["User"]
  }'
```

### Login:
```bash
curl -X POST "https://localhost:5000/api/account/login" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "test@example.com",
    "password": "Test123!"
  }'
```

### Obtener Detalles de Usuario:
```bash
curl -X GET "https://localhost:5000/api/account/detail" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Content-Type: application/json"
```

### Crear Rol (Admin):
```bash
curl -X POST "https://localhost:5000/api/roles" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Content-Type: application/json" \
  -d '{
    "roleName": "Manager"
  }'
```

### Asignar Rol a Usuario:
```bash
curl -X POST "https://localhost:5000/api/roles/assing" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "Content-Type: application/json" \
  -d '{
    "userId": "user-guid-123",
    "roleId": "Manager"
  }'
```

---

## 🛠️ Tecnologías Utilizadas

- **Framework:** ASP.NET Core 9.0
- **Autenticación:** ASP.NET Core Identity + JWT
- **Base de Datos:** Entity Framework Core + SQL Server
- **Email:** MimeKit + MailKit
- **Seguridad:** Bcrypt para hashing de contraseñas
- **Validación:** Data Annotations + FluentValidation

---

## 👥 Contribución

1. Fork del proyecto
2. Crear rama para feature (`git checkout -b feature/nueva-funcionalidad`)
3. Commit de cambios (`git commit -am 'Agregar nueva funcionalidad'`)
4. Push a la rama (`git push origin feature/nueva-funcionalidad`)
5. Crear Pull Request

---

## 📄 Licencia

Este proyecto está bajo la Licencia MIT. Ver archivo `LICENSE` para más detalles.

---

## 📞 Contacto

**Desarrollador:** JesusDGCA  
**Email:** [tu-email@ejemplo.com]  
**GitHub:** [@JesusDGCA](https://github.com/JesusDGCA)

---

*Última actualización: 14 de octubre de 2025*
