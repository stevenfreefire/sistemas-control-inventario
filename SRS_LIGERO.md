| Equipo | Steven Ariel Amaya Rodriguez |
| Repositorio | Repositorio Ejemplo |
| Fecha | 16/07/2026 |
| Version | v1.0-SRS |
---
"El cliente no siempre tiene la razón, pero siempre tiene el problema."
$ git tag -a v1.0-srs -m "SRS base approved"
---
# Especificación de Requisitos de Software (SRS) - Sistema de Control de Inventario
*Código de Documento:* SRS-LIGERO-001  
*Proyecto:* Plataforma de Gestión de Inventario Local  
*Estado:* Entregable Final - Aprobado  
*Fecha de Emisión:* 16 de Julio, 2026  
---
## 1. Alcance y Fronteras del Software
### 1.1. Propósito y Alcance del Sistema (In-Scope)
El propósito del sistema es proveer una plataforma web centralizada, de alta confiabilidad y rendimiento, para la administración de existencias de una tienda de comercio minorista local[span_0](start_span)[span_0](end_span). El sistema aborda las siguientes áreas clave:
- *Registro de Productos:* Permite la creación, edición y visualización de un catálogo unificado de productos comercializados por la tienda[span_1](start_span)[span_1](end_span).
- *Validación Estricta de Códigos de Barra de El Salvador:* Incorporación obligatoria de validaciones sintácticas y de origen para códigos de barra de tipo EAN-13 que posean el prefijo asignado a la República de El Salvador (*741*), garantizando que todo producto nacional registrado cumpla con el estándar de GS1 El Salvador[span_2](start_span)[span_2](end_span).
- *Control de Existencias en Tiempo Real:* Seguimiento estricto de las entradas (compras/abastecimientos) y salidas (ventas/mermas) de inventario[span_3](start_span)[span_3](end_span).
- *Mecanismo Automático de Alertas:* Notificación visual inmediata y activación de banderas en base de datos cuando el inventario de un producto descienda por debajo de su umbral mínimo configurado (stock crítico)[span_4](start_span)[span_4](end_span).
### 1.2. Límites y Exclusiones (Out-of-Scope)
Para mantener un desarrollo acotado y enfocado en la eficiencia operativa, se definen explícitamente los siguientes elementos fuera del alcance:
- *Módulo de Facturación y Punto de Venta (POS) Completo:* El sistema no procesa transacciones monetarias directas, pasarelas de pago ni facturación electrónica. Se limita a registrar los movimientos de inventario que resulten de estas operaciones[span_5](start_span)[span_5](end_span).
- *Gestión de Proveedores y Compras Automatizadas:* No se contempla la generación automática de órdenes de compra ni comunicación directa mediante API con proveedores externos[span_6](start_span)[span_6](end_span).
- *Contabilidad General:* No calcula balances financieros ni se integra con sistemas contables tributarios complejos, más allá del costeo básico del inventario registrado[span_7](start_span)[span_7](end_span).
---
## 2. Restricciones Tecnológicas Obligatorias
Para garantizar la compatibilidad con la infraestructura de servidores preexistente del cliente y las normativas locales de codificación comercial, se establecen las siguientes limitantes de diseño y desarrollo:
### 2.1. Base de Datos Relacional Obligatoria (PostgreSQL)
- *Restricción:* El motor de persistencia de datos debe ser *PostgreSQL* (versión 14 o superior), utilizando el servidor físico/virtual preconfigurado por el departamento de IT de la tienda[span_8](start_span)[span_8](end_span).
- *Justificación:* Aprovechamiento de la infraestructura existente, licenciamiento de código abierto sin costos adicionales y soporte nativo para transaccionalidad ACID (Atomicity, Consistency, Isolation, Durability) en control de concurrencia de existencias[span_9](start_span)[span_9](end_span).
- *Implicación de Diseño:* No se permite el uso de bases de datos NoSQL para el core transaccional del inventario. El diseño de base de datos debe contemplar triggers o restricciones a nivel de esquema para impedir stocks negativos[span_10](start_span)[span_10](end_span).
### 2.2. Validación de Identidad Comercial (GS1 El Salvador)
- *Restricción:* Todo código de barra ingresado bajo la categoría de producto nacional debe ser validado como un código estructurado bajo la norma *EAN-13* y poseer el prefijo oficial de *El Salvador (741)*[span_11](start_span)[span_11](end_span).
- *Mecanismo de Validación:*
  1. El código debe contener exactamente 13 caracteres numéricos[span_12](start_span)[span_12](end_span).
  2. Los primeros 3 dígitos deben corresponder estrictamente a *741*[span_13](start_span)[span_13](end_span).
  3. Aplicar el algoritmo de validación del dígito verificador EAN-13 (ponderación alternativa de pesos x1 y x3 de derecha a izquierda)[span_14](start_span)[span_14](end_span).
---
## 3. Requisitos de Calidad (IEEE 830)
De acuerdo con el estándar IEEE 830, el sistema debe cumplir con los siguientes atributos de calidad específicos[span_15](start_span)[span_15](end_span):
### 3.1. Confiabilidad (Reliability)
- *Tolerancia a Fallos:* El sistema no debe permitir inconsistencias de inventario en transacciones simultáneas. Se implementará bloqueo optimista/pesimista en base de datos para evitar condiciones de carrera (race conditions) en ventas masivas[span_16](start_span)[span_16](end_span).
- *Recuperación:* Ante caídas de conexión con PostgreSQL, el sistema debe retener de forma local/sesión los datos de la transacción actual y reintentar de forma segura sin duplicar registros de inventario[span_17](start_span)[span_17](end_span).
### 3.2. Usabilidad (Usability)
- *Alertas Claras:* El indicador de stock bajo debe ser visualmente prominente (utilizando código de color de contraste como naranja/rojo) y accesible desde cualquier pantalla del catálogo en menos de 2 clics[span_18](start_span)[span_18](end_span).
- *Facilidad de Entrada:* El campo de código de barra debe admitir enfoque automático (autofocus) para permitir el uso directo de pistolas lectoras de códigos de barra sin necesidad de interacción con el mouse[span_19](start_span)[span_19](end_span).
### 3.3. Rendimiento (Performance)
- *Tiempo de Respuesta:* La consulta de stock de cualquier producto mediante la lectura de su código de barras debe resolverse en un tiempo máximo de *500 milisegundos* bajo condiciones normales de red[span_20](start_span)[span_20](end_span).
---
## 4. Historias de Usuario Integradas (User Stories)
### Historia de Usuario 1 (US-01): Registro de Producto con Validación EAN-13 de El Salvador
- *Como:* Encargado de Bodega[span_21](start_span)[span_21](end_span)
- *Quiero:* Registrar un nuevo producto escaneando o digitando su código de barras[span_22](start_span)[span_22](end_span).
- *Para:* Incorporarlo al catálogo oficial y asegurar que pertenece a la codificación local autorizada de El Salvador[span_23](start_span)[span_23](end_span).
- *Criterios de Aceptación:*
  - *Escenario 1 (Código Válido):* Si el código tiene 13 dígitos, inicia con "741" y el dígito verificador es correcto, el sistema permite registrar el producto exitosamente[span_24](start_span)[span_24](end_span).
  - *Escenario 2 (Código Inválido):* Si el código no inicia con "741" o el dígito verificador falla, el sistema bloquea el registro y muestra un mensaje de error: "Código de barras inválido o no correspondiente a la región de El Salvador (Prefijo 741 requerido)"[span_25](start_span)[span_25](end_span).
### Historia de Usuario 2 (US-02): Control de Alerta de Stock Mínimo
- *Como:* Administrador de Tienda[span_26](start_span)[span_26](end_span)
- *Quiero:* Definir un umbral de inventario mínimo para cada producto y recibir alertas visibles cuando se alcance[span_27](start_span)[span_27](end_span).
- *Para:* Realizar pedidos de reabastecimiento antes de que ocurra una ruptura de stock[span_28](start_span)[span_28](end_span).
- *Criterios de Aceptación:*
  - *Escenario 1 (Stock Óptimo):* Mientras el stock actual sea mayor al stock mínimo, el indicador de estado del producto se muestra verde o neutro[span_29](start_span)[span_29](end_span).
  - *Escenario 2 (Stock Bajo):* Si una venta reduce las existencias de un producto a un nivel igual o inferior a su stock mínimo configurado, se activa una alerta en tiempo real en la interfaz de usuario y se envía una bandera de estado crítico a la fila de la base de datos PostgreSQL[span_30](start_span)[span_30](end_span).
---
## 5. Matriz de Trazabilidad de Requisitos
A continuación, se define la trazabilidad del primer grupo de requisitos para garantizar la cobertura del ciclo de vida del software[span_31](start_span)[span_31](end_span):

| ID Requisito | Tipo de Requisito | Descripción del Requisito | Historia de Usuario (US) | Componente del Sistema (PostgreSQL / Frontend) | Casos de Prueba Relacionados |
| :--- | :--- | :--- | :--- | :--- | :--- |
| *REQ-001* | Funcional | Validación obligatoria de prefijo EAN-13 salvadoreño (741)[span_32](start_span)[span_32](end_span). | US-01[span_33](start_span)[span_33](end_span) | Frontend (Validación JS) / Backend (Modelo PostgreSQL)[span_34](start_span)[span_34](end_span) | TC-EAN13-01, TC-EAN13-02[span_35](start_span)[span_35](end_span) |
| *REQ-002* | Tecnológico | Persistencia de transacciones de inventario en servidor preconfigurado[span_36](start_span)[span_36](end_span). | US-01, US-02[span_37](start_span)[span_37](end_span) | Backend (Conexión Pool PostgreSQL)[span_38](start_span)[span_38](end_span) | TC-DB-01 (Prueba de persistencia ACID)[span_39](start_span)[span_39](end_span) |
| *REQ-003* | Funcional / Calidad | Alerta visual en tiempo real al caer por debajo del umbral mínimo de stock[span_40](start_span)[span_40](end_span). | US-02[span_41](start_span)[span_41](end_span) | Interfaz de Usuario / Base de datos (Trigger de alerta)[span_42](start_span)[span_42](end_span) | TC-ALERT-01, TC-ALERT-02[span_43](start_span)[span_43](end_span) |

---
*Elaborado por:* Steven Ariel Amaya Rodriguez  
*Aprobado por:* Dirección Técnica de Proyecto
