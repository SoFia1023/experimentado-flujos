# experimentado-flujos
```mermaid
flowchart LR
    A[Inicio<br/>Administrador de sede] --> B[Iniciar sesión]
    B -->|Credenciales válidas| C[Panel administrativo]
    B -->|Credenciales inválidas| X[Mostrar mensaje de error]
    C --> D[Gestionar disponibilidad del taller]
    C --> E[Visualizar agenda del taller]
    C --> F[Registrar diagnóstico de servicio]
    C --> G[Gestión de catálogo de motocicletas]
    C --> H[Consultar indicadores y resúmenes]
    C --> I[Recibir notificaciones del sistema]
