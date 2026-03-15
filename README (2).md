# Flujo ADMINISTRADOR
```mermaid
flowchart TD

    A[Inicio<br/>Administrador de sede] --> B[Iniciar sesión]
    B --> C[Panel administrativo]

    %% AGENDA
    C --> D[Visualizar agenda de servicios]
    D --> D1[Consultar citas por fecha]
    D1 --> D2[Ver citas por franja horaria]

    %% DIAGNÓSTICO
    C --> E[Registrar diagnóstico del servicio]
    E --> E1[Seleccionar placa de la motocicleta]
    E1 --> E2[Recuperar datos del servicio]
    E2 --> E3[Ingresar diagnóstico]
    E3 --> E4[Guardar diagnóstico]
    E4 --> E5[Generar radicado]
    E5 --> E6[Enviar correo al cliente]

    %% INDICADORES
    C --> F[Consultar gráficos e indicadores]
    F --> F1[Ver cotizaciones realizadas]
    F --> F2[Ver intereses en repuestos]

    %% CATÁLOGO
    C --> G[Gestionar catálogo de motocicletas]
    G --> G1[Agregar motocicleta]
    G --> G2[Editar motocicleta]
    G --> G3[Desactivar motocicleta]

    %% NOTIFICACIONES
    C --> H[Recibir notificaciones]
    H --> H1[Notificación de cancelación de cita]

