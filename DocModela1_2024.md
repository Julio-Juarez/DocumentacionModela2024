# Módulo 1: Recepción de Productos

## Descripción General
Este módulo gestiona la llegada, descarga y clasificación de productos en el centro logístico. A lo largo del día, camiones provenientes de diversas fábricas transportan una mezcla de productos perecederos, electrónicos y artículos voluminosos. El objetivo es asegurar que los productos sean clasificados y distribuidos de manera eficiente para evitar demoras y maximizar el flujo dentro del centro.

## Objetivos del Módulo
- Recibir y procesar los productos transportados por camiones en intervalos programados.
- Optimizar el flujo de productos mediante el uso de vehículos guiados automáticamente (AGVs) y separadores.
- Minimizar tiempos de espera y evitar cuellos de botella en la estación de clasificación.

## Entradas y Salidas
- **Entradas**: Camiones con diferentes cantidades de productos perecederos, electrónicos y voluminosos según el horario programado.
- **Salidas**: Productos clasificados y transportados a áreas específicas de almacenamiento y procesamiento.

## Estructura y Componentes
- **Estación de descarga**: Punto de llegada y descarga de camiones.
- **Estación de clasificación**: Donde los productos se separan y clasifican según su tipo (perecederos, electrónicos, voluminosos).
- **AGVs**: Vehículos guiados que transportan productos a sus respectivas áreas de almacenamiento.
- **Separadores**: Dispositivos ubicados en la estación de clasificación para dirigir los productos hacia la línea de procesamiento correspondiente.

## Detalles de Implementación
Los camiones llegan al centro en intervalos específicos, detallados en la tabla de horarios de llegada.

![Configuración de la Rate Table](https://github.com/Julio-Juarez/DocumentacionModela2024/blob/main/Imagen/rateTable1.jpg)

La cantidad de productos por camión varía según el horario. El sistema monitorea en tiempo real el flujo de productos y ajusta las rutas de los AGVs y los separadores para responder a cambios en la demanda.

- Configura el **Source** para utilizar la **Rate Table**:
  - Selecciona el objeto Source que representa la llegada de los camiones.
  - En las propiedades del Source, cambia *Arrival Mode* a *Time Varying Arrival Rate*.
  - En *Rate Table*, selecciona la tabla creada en el paso anterior.
- Define los intervalos de tiempo y la tasa de llegada en la **Rate Table**.

## Proceso para Asignar Valor a los Productos
1. Abre la pestaña de **Procesos** en Simio y selecciona *Add Process*.

![variables de Productos](https://github.com/Julio-Juarez/DocumentacionModela2024/blob/main/Imagen/variablesProductos.jpg)
  
   ![Configuración del Proceso](https://github.com/Julio-Juarez/DocumentacionModela2024/blob/main/Imagen/ProcesoAsignacionCantidad.jpg)

2. Configura el **Trigger** del proceso para que se ejecute en intervalos específicos.
3. Añade un objeto **Decide** con la condición `DateTime.Hour(TimeNow) = hora` para verificar la hora.
4. Configura las variables de productos `cantidadPerecederos`, `cantidadElectronicos` y `cantidadVoluminosos` en un bloque **Assign** si la condición del Decide es True.

## Separadores
- Coloca tres objetos **Separator** en el área de clasificación para los tres flujos: perecederos, electrónicos y voluminosos.
  
  ![Configuración de Separadores](https://github.com/Julio-Juarez/DocumentacionModela2024/blob/main/Imagen/configuracionSeparadores.jpg)
  
- Configura las condiciones para cada separador con las variables correspondientes (`cantidadPerecederos`, `cantidadElectronicos`, `cantidadVoluminosos`).

---

# Módulo 2: Almacenamiento de Productos

## Descripción del Proceso
Los productos clasificados son almacenados temporalmente en áreas específicas según su tipo (perecederos, electrónicos, voluminosos). Cada área tiene una capacidad en metros cúbicos y tiempos de procesamiento distintos.


## Manejo y Procesamiento por Tipo
- **Perecederos**: 300 m³, tiempo de procesamiento 10-15 minutos.
- **Electrónica**: 500 m³, tiempo de procesamiento 5-20 minutos (común: 12 minutos).
- **Voluminosos**: 700 m³, tiempo de procesamiento 20-40 minutos.

## Gestión del Espacio de Almacenamiento
- **Perecederos**: 0.5 m³ por unidad.
- **Electrónica**: 0.2 m³ por unidad.
- **Voluminosos**: 1.0 m³ por unidad.

---

# Módulo 3: Empaque y Consolidación

## Descripción del Proceso
Productos almacenados se consolidan para despacho. Si un pedido incluye productos de distintas áreas, el tiempo de consolidación aumenta.

## Tipo de Empaque y Tiempos
- **Empaque Mixto**: para pedidos de múltiples categorías. Tiempo de consolidación: 10-25 minutos.

![Modulo 1 2 3 en 2D](https://github.com/Julio-Juarez/DocumentacionModela2024/blob/main/Imagen/modulo123_2D.jpg)
![Modulo 1 2 3 en 3D](https://github.com/Julio-Juarez/DocumentacionModela2024/blob/main/Imagen/modulo123_3D.jpg)

---

# Módulo 4: Despacho de Pedidos

## Descripción del Proceso
Los pedidos se despachan hacia sus zonas de destino. La eficiencia en el despacho es crucial y depende de la prioridad, capacidad de camiones y tiempos de viaje.

## Llegada de Pedidos y Despacho
![Tabla de Tiempos de Despacho](https://github.com/Julio-Juarez/DocumentacionModela2024/blob/main/Imagen/rateTAble2.jpg)

- **Probabilidad de Llegada por Zona**:
  - Zona Norte: 40%
  - Zona Sur: 35%
  - Zona Oeste: 25%
- **Tiempos de Entrega por Zona**:
  - Zona Norte: 1-1.5 horas.
  - Zona Sur: 1.5-2 horas.
  - Zona Oeste: 2-2.5 horas.
  
## Carga de Pedidos y Horario de Trabajo
- LogiExpress opera de lunes a viernes de 7:00 AM a 7:00 PM, sábados de 9:00 AM a 4:00 PM. Pausa de almuerzo: lunes a viernes de 12:00 PM a 1:00 PM, sábados de 12:00 PM a 12:30 PM. Cerrado los domingos.

![Horario de Trabajo](URL_de_la_imagen/HorarioTrabajo.png)

---

# Implementación del Word Schedule
Para gestionar el horario de trabajo en el centro de distribución, se utiliza un **Word Schedule** en Simio, el cual se aplica a todos los servidores involucrados en el proceso. Esta configuración asegura que los recursos estén disponibles solo durante los períodos de actividad.

## Configuración de Servidores
Cada servidor en el modelo se configura para reflejar los períodos de operación definidos en el Word Schedule, permitiendo una simulación precisa del flujo de trabajo.

## Manejo de Pedidos Durante Pausas
Aunque el personal de empaque y despacho no está operativo durante las pausas, los pedidos se encolan en el sistema, esperando a ser procesados cuando el personal retome sus labores.

## Simulación de Actividades
Es esencial que el modelo refleje los períodos de actividad e inactividad para una gestión eficiente de los recursos y una planificación adecuada de turnos, maximizando la eficacia operativa del centro de distribución.

![Gestión de Pausas y Tiempos de Actividad](https://github.com/Julio-Juarez/DocumentacionModela2024/blob/main/Imagen/horario.jpg)
