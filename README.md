# experimentado-flujos 
# Flujo ADMI
```mermaid
flowchart TD

    A[Inicio<br/>Administrador de sede] --> B[Iniciar sesión]
    B --> C[Panel administrativo]

    %% DISPONIBILIDAD
    C --> D[Gestionar disponibilidad]
    D --> D1[Definir jornadas<br/>mañana / tarde-noche]
    D1 --> D2[Asignar mecánicos a jornadas]

    %% AGENDA
    C --> E[Visualizar agenda diaria]
    E --> E1[Ver servicios técnicos programados]

    %% DIAGNÓSTICO
    C --> F[Registrar diagnóstico]
    F --> F1[Seleccionar placa de la motocicleta]
    F1 --> F2[Recuperar datos del servicio]
    F2 --> F3[Ingresar diagnóstico]
    F3 --> F4[Guardar diagnóstico]
    F4 --> F5[Generar radicado]
    F5 --> F6[Enviar correo al cliente]

    %% INDICADORES
    C --> G[Consultar gráficos e indicadores]
    G --> G1[Ver cotizaciones realizadas]
    G --> G2[Ver intereses en repuestos]

    %% CATÁLOGO
    C --> H[Gestionar catálogo]
    H --> H1[Agregar motocicleta]
    H --> H2[Editar motocicleta]
    H --> H3[Desactivar motocicleta]

    %% NOTIFICACIONES
    C --> I[Recibir notificaciones]
    I --> I1[Notificación de cancelación de cita]


