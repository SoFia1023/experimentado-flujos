# experimentado-flujos
flowchart TD

    A[Inicio<br/>Administrador de sede] --> B[Iniciar sesión]

    B -->|Credenciales válidas| C[Panel administrativo]
    B -->|Credenciales inválidas| Z[Mostrar mensaje de error]

    %% OPERACIÓN DEL TALLER
    C --> D[Gestionar disponibilidad del taller]
    D --> D1[Definir jornadas<br/>mañana / tarde-noche]
    D1 --> D2[Asignar mecánicos por jornada]
    D2 --> D3[Registrar excepciones<br/>no disponibles]

    C --> E[Visualizar agenda diaria]
    E --> E1[Ver servicios técnicos por fecha]
    E1 --> E2[Consultar hora y categoría]

    C --> F[Registrar diagnóstico]
    F --> F1[Seleccionar servicio atendido]
    F1 --> F2[Ingresar diagnóstico]
    F2 --> F3[Generar radicado]
    F3 --> F4[Enviar correo al cliente]

    %% GESTIÓN DE CATÁLOGO
    C --> G[Gestionar catálogo]
    G --> G1[Agregar motocicleta]
    G --> G2[Editar motocicleta]
    G --> G3[Actualizar precios y descripciones]
    G --> G4[Activar / desactivar visibilidad]

    %% ANÁLISIS Y CONTROL
    C --> H[Consultar indicadores]
    H --> H1[Ver cotizaciones realizadas]
    H --> H2[Ver intereses en repuestos]
    H --> H3[Ver carga del taller]

    %% NOTIFICACIONES
    C --> I[Recibir notificaciones]
    I --> I1[Recibir cancelación de cita]
    I1 --> I2[Reorganizar agenda]
