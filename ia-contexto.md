# Contexto del Proyecto: UrbanPulse

## 1. Descripción y Propósito
UrbanPulse es una plataforma cloud para la gestión inteligente de incidencias urbanas que integra reportes de la ciudadanía con datos contextuales de la ciudad de Málaga[cite: 1].
* **Enfoque arquitectónico inicial:** Monolito sencillo en Spring Boot con base de datos relacional PostgreSQL[cite: 1]. No se introducen microservicios ni sistemas distribuidos complejos hasta que exista un problema observable y una decisión arquitectónica documentada[cite: 1, 9].
* **Documentación técnica:** Se sigue el modelo C4 para diagramas de arquitectura (`workspace.dsl`) y Architecture Decision Records (ADRs) en `docs/adr/` para justificar decisiones clave[cite: 2, 9].

## 2. Dominio y Ciclo de Vida
* **Entidad central:** `Incident` (descripción, categoría, localización, marcas temporales, estado)[cite: 1, 4].
* **Estados del ciclo de vida:** `REPORTED`, `VALIDATED`, `REJECTED`, `ASSIGNED`, `IN_PROGRESS`, `RESOLVED`, `REOPENED`, `CLOSED`[cite: 5].
* **Entidades auxiliares:** `User` (roles: Ciudadano, Operador, Técnico, Admin, Analista)[cite: 2, 4], `Assignment` (asignación a departamentos/técnicos)[cite: 4], `Attachment` (evidencias multimedia fuera de la base relacional)[cite: 4], `UrbanAsset` (activos urbanos como semáforos o paradas)[cite: 4], `UrbanContext` (tráfico, clima, barrio y distrito)[cite: 4].
* **Regla de degradación controlada:** Las fuentes externas enriquecen la información, pero su caída o indisponibilidad jamás debe impedir registrar una incidencia en base de datos[cite: 2, 4].

## 3. Normas de Diseño de la API REST
* **Estrategia Design-First:** Se definen los contratos y DTOs antes de implementar la lógica[cite: 10].
* **Rutas centradas en recursos:** Uso de sustantivos en plural (`/api/incidents`, `/api/users`), nunca verbos en la URL[cite: 10].
* **Parámetros de consulta:** Filtrado múltiple (estado, prioridad, categoría, distrito), paginación (`page`, `size`) y ordenación (`sort`) combinados en `GET /api/incidents` mediante *query parameters*[cite: 10, 11].
* **Modificación parcial:** Empleo de `PATCH` para transiciones de estado y modificaciones puntuales[cite: 10, 11].
* **DTOs obligatorios:** Separación estricta entre entidades de persistencia (JPA) y objetos de transferencia para peticiones y respuestas[cite: 10, 11].

## 4. Gestión de Excepciones y Errores
Todos los errores de la API devuelven una estructura unificada basada en una clase `ApiError` con los campos: `type`, `title`, `status` (código HTTP), `code` (código interno de negocio), `detail`, `instance`, `requestId` (UUID de trazabilidad) y `errors` (lista de `FieldError`)[cite: 11]. Nunca se exponen trazas o excepciones internas del servidor al cliente[cite: 11].

## 5. Instrucciones para la IA
* Genera código en Java con Spring Boot limpio y estructurado en capas (`controller`, `service`, `repository`, `domain`, `dto`, `exception`).
* Prioriza la simplicidad (*Keep It Simple*): no propongas Kafka, Kubernetes o microservicios a menos que se soliciten explícitamente[cite: 1, 2].
* Asegura que el manejo de errores utilice `@RestControllerAdvice` y respete el contrato de `ApiError`[cite: 11].
