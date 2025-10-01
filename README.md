# Ejercicio Práctico 3.2 – Integración con Angular

## 📌 Resumen del Proyecto
Este ejercicio muestra cómo integrar una aplicación **Angular** con un backend .NET mediante:
- **API Gateway pattern** para centralizar acceso y seguridad.
- **Authentication/Authorization** con **JWT + Refresh Tokens**.
- **CORS configuration** para permitir comunicación segura con el frontend.
- **API versioning strategy** para mantener compatibilidad entre versiones.

El objetivo es garantizar un frontend robusto y un backend escalable, seguro y mantenible.

---

## 🏗️ Diagrama de Arquitectura (UML)

![Diagrama de Componentes](diagrama_componentes_angular.png)

**Descripción del flujo:**
1. Angular SPA realiza llamadas HTTP → API Gateway.  
2. API Gateway enruta a microservicios (AuthService, UserService, ProductService).  
3. AuthService emite `access_token` (JWT de corta duración) y `refresh_token` (larga duración en cookie segura).  
4. Angular usa un **AuthInterceptor** para adjuntar el `access_token` y refrescarlo al expirar.  
5. API Gateway aplica CORS, versionado y validación de seguridad.  

---

## ✅ Beneficios del Diseño
- **Centralización**: un único punto de entrada (API Gateway).  
- **Seguridad**: JWT + Refresh Tokens con rotación y revocación.  
- **Escalabilidad**: permite añadir nuevos microservicios sin cambios en Angular.  
- **Compatibilidad**: versionado de API asegura evolución sin romper clientes existentes.  
- **Mantenibilidad**: Angular abstrae llamadas HTTP con `ApiService`.  

---

## ⚙️ Estrategias Técnicas

### API Gateway Pattern
- Implementado con **YARP** (Microsoft) u **Ocelot** en .NET.  
- Responsable de enrutamiento, CORS, rate limiting, logging y validación inicial de JWT.  

### Authentication / Authorization
- **Access Token** (JWT, 15 min).  
- **Refresh Token** (7–30 días, rotación en cada uso).  
- Guardado en cookie `HttpOnly; Secure; SameSite=Strict`.  
- Endpoint `/auth/refresh` emite nuevo `access_token`.  

### CORS Configuration
Ejemplo en .NET:
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AngularApp", policy =>
    {
        policy.WithOrigins("http://localhost:4200", "https://app.mycompany.com")
              .AllowAnyHeader()
              .AllowAnyMethod()
              .AllowCredentials();
    });
});
