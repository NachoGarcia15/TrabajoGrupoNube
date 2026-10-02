# Diseño de API REST - UrbanPulse

===

Este documento define la estructura de la API para UrbanPulse. La API debe estar centrada en recursos (usando sustantivos en plural) y no en acciones, delegando la intención de la operación al método HTTP.



1. ## Rutas Principales (Endpoints)

\---



### 1.1 Recurso: Incidencias (/api/incidents)



**POST /api/incidents:** Registra un nuevo reporte.



**GET /api/incidents:** Obtiene el catálogo de incidencias. Los filtros, la ordenación y la paginación se aplicarán utilizando parámetros de consulta (query parameters), por ejemplo: ?status=VALIDATED\&sort=-priority.

&#x20;  
**GET /api/incidents/{id}:** Consulta los detalles de una incidencia específica identificada por su parámetro de ruta (path parameter).



**PATCH /api/incidents/{id}:** Modifica parcialmente un recurso, enviando en el cuerpo (body) únicamente los campos que deben cambiar (ej. para pasar el estado a VALIDATED o asignar un técnico).



### 1.2 Recurso: Usuarios (/api/users)



**POST /api/users:** Crea un nuevo usuario en la plataforma.



**GET /api/users/{id}**: Recupera la información detallada de un usuario.



## 2\. Códigos de Estado HTTP



#### Éxito (2xx):



**200 OK:** La operación se realizó correctamente.



**201 Created:** El recurso se creó con éxito tras una petición POST.



**204 No Content:** Operación correcta, pero sin cuerpo de respuesta.



#### Errores del Cliente (4xx):



**400 Bad Request**: La petición está mal formada.



**401 Unauthorized:** Falta autenticación o el token es inválido.



**403 Forbidden:** El usuario carece de permisos para realizar esa acción.



**404 Not Found:** El recurso o identificador solicitado no existe.



**422 Unprocessable Entity:** Los datos tienen el formato correcto, pero fallan en las reglas de validación del dominio.



#### Errores del Servidor (5xx):



**500 Internal Server Error:** Error interno e inesperado en el servidor.



**503 Service Unavailable:** El servicio externo o la base de datos no están disponibles.



## 3\. Estructura Común de Errores

### 

Es recomendable separar el código HTTP, el código interno del backend, el mensaje humano, los detalles de campo y el identificador de trazabilidad. Todos los errores devueltos por la API de UrbanPulse seguirán estrictamente este formato unificado:



**Ejemplo de respuesta para un error:**



{

&#x20; "type": "https://api.urbanpulse.com/problems/validation-error",

&#x20; "title": "Validation failed",

&#x20; "status": 422,

&#x20; "code": "VALIDATION\_ERROR",

&#x20; "detail": "Two fields contain invalid values.",

&#x20; "instance": "/api/incidents",

&#x20; "requestId": "req\_8Xy3Z",

&#x20; "errors": \[

&#x20;   {

&#x20;     "field": "title",

&#x20;     "code": "MISSING\_FIELD",

&#x20;     "message": "The title is required."

&#x20;   }

&#x20; ]

}



## 4\. Estructura de Respuestas (DTOs)

Para evitar exponer el modelo de base de datos directamente, las respuestas utilizarán DTOs (Data Transfer Objects) en formato JSON\[cite: 8, 9].

**Ejemplo de respuesta para una Incidencia (`GET /api/incidents/{id}`):**

```json
{
  "id": "INC-1024",
  "title": "Semáforo averiado en Alameda Principal",
  "description": "El semáforo de peatones no cambia a verde.",
  "status": "VALIDATED",
  "category": "TRAFFIC\_LIGHT",
  "priority": "HIGH",
  "location": {
    "latitude": 36.7184,
    "longitude": -4.4201,
    "district": "Centro"
  },
  "createdAt": "2026-10-02T10:30:00Z"
}


