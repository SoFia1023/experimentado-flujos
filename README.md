# experimentado-flujos 
# Flujo ADMI
```mermaid
flowchart TD

    A[Inicio<br/>Administrador de sede] --> B[Iniciar sesión]

    B -->|Correcto| C[Panel administrativo]
    B -->|Error| Z[Mensaje de error]

    %% DISPONIBILIDAD
    C --> D[Gestionar disponibilidad]
    D --> D1[Definir jornadas<br/>mañana / tarde-noche]
    D1 --> D2[Asignar mecánicos a jornadas]

    %% AGENDA
    C --> E[Visualizar agenda diaria]
    E --> E1[Ver servicios técnicos]
    E1 --> E2[Ver carga del taller]

    %% DIAGNÓSTICO
    C --> F[Registrar diagnóstico]
    F --> F1[Seleccionar placa]
    F1 --> F2[Recuperar datos del servicio]
    F2 --> F3[Ingresar diagnóstico]
    F3 --> F4[Guardar diagnóstico]
    F4 --> F5[Generar radicado]
    F5 --> F6[Enviar correo al cliente]

    %% GRÁFICOS
    C --> G[Consultar gráficos]
    G --> G1[Ver cotizaciones]
    G --> G2[Ver intereses en repuestos]
    G --> G3[Ver carga del taller]

    %% CATÁLOGO
    C --> H[Gestionar catálogo]
    H --> H1[Agregar motocicleta]
    H --> H2[Editar motocicleta]
    H --> H3[Desactivar motocicleta]

    %% NOTIFICACIONES
    C --> I[Recibir notificaciones]
    I --> I1[Notificación de cancelación]
