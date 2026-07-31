Concepto: Servicio Inteligente de Consultoría para Hardware Personalizado (SICHP)

## 1. Definición Estratégica del Caso

### 1.1. Descripción del proceso de negocio elegido

El proceso es la selección de los componentes de una PC customizada en función de los gustos y las necesidades del cliente.

El circuito actual del proceso consiste en:

1. Cliente solicita asesoramiento
2. Vendedor entrevista al cliente
3. Analiza necesidades y presupuesto
4. Investiga componentes
5. Verifica compatibilidad
6. Compara precios y disponibilidad
7. Arma una propuesta
8. Explica la configuración
9. Cliente solicita cambios
10. Se modifica la configuración
11. Cliente aprueba
12. Venta

### 1.2. Problema actual o ineficiencia identificada

- La investigación manual tanto de los componentes como la compatibilidad no solo **consume mucho tiempo** sino que además abre la puerta a la **aparición de errores**.
- La comparación de precios entre distintas opciones de setups para cumplir con el presupuesto resulta en un **proceder repetitivo**.
- La dependencia de la experiencia del venderor resulta en un **output con calidad variable, afectando también los tiempos** implicados en función de la experiencia del empleado.

### 1.3. Objetivo de automatización

- **Reducir los tiempos de atención** al disminuir el tiempo necesario para elaborar una propuesta, automatizando la búsqueda y comparación de componentes.
- **Mejorar la calidad** de las recomendaciones al analizar miles de combinaciones de hardware en segundos.
- **Reducir errores de compatibilidad**.
- **Lograr mayor consistencia** al poder realizar que todas las recomendaciones sigan los mismos criterios y estándares.
- **Conseguir escalabilidad**, dado que varios clientes pueden iniciar el proceso simultáneamente, el supervisor humano solo interviene cuando es necesario.

### 1.4. Justificación de por qué el enfoque híbrido es adecuado

La IA automatiza el análisis y la búsqueda de información, permitiendo disminuir tiempos en las tareas más operativas y repetitivas, mientras que el humano aporta su experiencia en la revisión y la capacidad de negociar con el cliente.

## 2. Marco Persona – Tarea – Contexto

### 2.1. Persona

#### 2.1.1. Rol del agente digital

"SICHP (Servicio Inteligente de Consultoría para Hardware Personalizado)": actúa como un consultor especializado en hardware que interpreta las necesidades del cliente, analiza las alternativas disponibles, evalúa restricciones técnicas y comerciales, y genera una propuesta de configuración de PC personalizada y optimizada para su posterior validación por un supervisor humano.

#### 2.1.2. Capacidades técnicas

1. Consumir el Catálogo de Hardware para recuperar especificaciones técnicas, stock y precios de componentes.
2. Ejecutar reglas de validación para verificar la compatibilidad entre CPU, placa madre, memoria RAM, fuente y gabinete.
3. Invocar la API de Claude (modelo Haiku) para conversar con el usuario, detectar ambigüedades y formular preguntas de seguimiento en el Agente Encuestador, y para analizar la configuración elegida, verificar que cumpla los requisitos del cliente, generar una explicación clara de las decisiones tomadas y preparar la propuesta para el supervisor humano en el Agente Explicador.
4. Invocar la API de Claude (modelo Sonnet) para analizar el perfil del cliente y distribuir el presupuesto entre las distintas categorías de componentes (CPU, GPU, RAM, almacenamiento, fuente, gabinete, etc.) según el caso de uso, definiendo además las restricciones iniciales para la búsqueda en el Agente Planificador, y para consultar el Catálogo de Hardware, seleccionar componentes candidatos respetando el presupuesto asignado y construir una configuración completa en el Agente Configurador, ajustando la configuración si el Servicio de Validación Técnica detecta incompatibilidades.
5. Gestiona la ejecución mediante un plan iterativo con criterios de finalización, control de reintentos, detección de estados repetidos y escalamiento automático ante fallos no recuperables.
6. Registrar decisiones y configuraciones generadas mediante logs estructurados para auditoría.

#### 2.1.3. Límites de actuación

**No crea stock** de componentes. **No modifica precios** ni aplica descuentos comerciales. **No envía la propuesta** final al cliente. **No almacena información personal** innecesaria del cliente.

#### 2.1.4. Nivel de autonomía esperado

**Autonomía media (Nivel 3 de 5)**: ejecuta el pipeline completo de forma autónoma hasta la propuesta, pero **se detiene en un checkpoint humano obligatorio** y no presenta la propuesta al cliente. Replanifica ante errores recuperables, pero escala ante ambigüedad o fallos repetidos.

### 2.2. Tarea

#### 2.2.1. Actividades específicas asignadas al agente

Dado un input {presupuesto + uso principal + preferencias + restricciones}, entregar una configuración de PC personalizada, técnicamente compatible y optimizada para su revisión por un asesor humano.

#### 2.2.2. Subtareas estructuradas

1. **Interpretación del input**: interpretar la solicitud del cliente y extraer los parámetros relevantes. Si la información es insuficiente, solicitar aclaraciones.
2. **Obtención de información técnica**: consultar fuentes de datos (Catálogo de Hardware) para recuperar componentes compatibles con los requisitos identificados.
3. **Construcción de configuraciones candidatas**: generar configuraciones preliminares seleccionando CPU, GPU, memoria RAM, motherboard, almacenamiento, fuente de alimentación, gabinete y refrigeración, en función de los parámetros iniciales.
4. **Validación de compatibilidad**: verificar la compatibilidad entre todos los componentes seleccionados (socket, chipset, memoria, consumo eléctrico, dimensiones del gabinete, conectividad, etc.), descartar configuraciones inválidas, indicando las incompatibilidades.
5. **Generación de la recomendación**: elaborar un informe con la configuración recomendada, incluyendo el detalle de los componentes, el costo estimado y la justificación técnica de cada elección. Si luego de 10 iteraciones no resulta apta ninguna configuración, se detectan conflictos entre los requisitos del cliente y las restricciones del problema (por ejemplo, presupuesto insuficiente para el nivel de rendimiento solicitado) y propone alternativas y solicita una redefinición de las prioridades.
6. **Checkpoint humano**: presentar la propuesta al asesor para su revisión y aprobación. Registrar la ejecución y las decisiones tomadas. Solo tras la validación humana la propuesta puede enviarse al cliente.

#### 2.2.3. Nivel de complejidad

**Nivel de complejidad: Alto.** El agente combina interpretación de requerimientos en lenguaje natural, integración con fuentes externas para obtener especificaciones, precios y disponibilidad de componentes, validación automática de compatibilidad mediante reglas técnicas, generación de configuraciones candidatas bajo restricciones de presupuesto y preferencias del cliente, confección de recomendaciones justificadas y un checkpoint de validación humana. El proceso requiere razonamiento iterativo, manejo de excepciones y replanificación ante cambios de disponibilidad o configuraciones inviables, por lo que supera un flujo lineal basado en un único prompt.

### 2.3. Contexto

#### 2.3.1. Sistemas involucrados

| **Sistema**                                 | **Función**                                                                                                    |
| ------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **Orquestador en OpenClaw AI**              | Ejecuta el workflow, controla iteraciones, reintentos y el checkpoint humano.                                  |
| **API del LLM de Anthropic (Claude)**       | Motor de razonamiento para los distintos agentes (Claude Haiku y Sonnet).                                      |
| **Catálogo de Hardware**                    | Fuente de especificaciones de componentes, precios y disponibilidad.                                           |
| **Servicio de Validación Técnica (Python)** | Aplica reglas para verificar la compatibilidad física, eléctrica y lógica entre los componentes seleccionados. |
| **Almacenamiento local (JSON)**             | Almacena configuraciones, historial de ejecuciones, resultados y logs.                                         |

#### 2.3.2. Restricciones técnicas o regulatorias

- **Técnicas**: Latencia de respuesta y cuota y costo por token de la API de Claude.
- **Regulatorias**: Ley de Protección de Datos Personales (Argentina, Ley 25.326).

#### 2.3.3. Riesgos asociados

- Información desactualizada del catálogo.
- Costos elevados si no se le pone límite al uso de de la API.
- Contactar al destinatario equivocado o con tono inadecuado.
- "Alucinaciones" del LLM.
- Indisponibilidad del LLM.
- Exposición de credenciales (API Keys).

## 3. Diseño del Agentic Workflow

### 3.1. Diagrama del flujo (visual o esquemático).

                    ┌─────────────────────────┐
                    │ Solicitud del cliente   │
                    └───────────┬─────────────┘
                                │
                                ▼
                ┌───────────────────────────────────┐
                │ Orquestador (OpenClaw AI)         │
                │   - Requisitos del cliente        │
                │   - Presupuesto                   │
                │   - Configuración actual          │
                │   - Historial de configuraciones  │
                │   - Errores encontrados           │
                │   - Nº de iteraciones             │
                │   - Logs                          │
                └───────────────┬───────────────────┘
                                │
                                ▼
            ┌────────────────────────────────────────┐
            │ [S1] Agente Encuestador (Claude Haiku) │
            │   - Releva necesidades del cliente     │
            └───────────────────┬────────────────────┘
                                │
                                ▼
            ┌──────────────────────────────────────────┐
            │ [S2] Agente Planificador (Claude Sonnet) │
            │   - Distribuye el presupuesto            │◄──────────────────────────────────────────────┐
            │   - Define restricciones iniciales       │                                               │
            └───────────────────┬──────────────────────┘                                               │
                                │                                                                      │
                                ▼                                    ┌──────────────────────┐          │
            ┌──────────────────────────────────────────┐             │ Catálogo de Hardware │          │
            │ [S3] Agente Configurador (Claude Sonnet) │             │   - Componentes      │          │
            │   - Consulta Catálogo de Hardware        │─ Consulta ─►│   - Especificaciones │          │
            │   - Construye una configuración          │             │   - Precio           │          │
            └───────────────────┬──────────────────────┘             │   - Stock            │          │
                                │                  ▲                 └──────────────────────┘          │
                            Envía a validar        │                                                   │
                                │                  └─────────────────────────────────────┐             │
                                ▼                                                        │             │
                ┌─────────────────────────────────────┐                                  │             │
                │ [S4] Servicio de Validación Técnica │                                  No            Sí
                │ Control de Compatibilidad:          │                                  │             │
                │   - Socket                          │                                  └──────┬──────┘
                │   - RAM                             │                                         │
                │   - PSU                             │                               ┌─────────┴──────────┐
                │   - Tamaño GPU                      │                               │ ¿Configuración no  │
                │   - Altura Cooler                   │                               │ generada por error │
                │   - Conectores                      │                               │ de presupuesto?    │
                │   - Presupuesto                     │                               └─────────┬──────────┘
                └──────────────┬──────────────────────┘                                         │
                               │                                                                │
                            ¿Válida?                                                            │
                               │                                                                │
                        ┌──────┴──────┐                                   ┌── No ──► Reprocesa ─┘
                        │             │      ┌─────────────────────────┐  │
                        Sí            No  ┌─►│ ¿Máximo de iteraciones? │──┤
                        │             └───┘  └─────────────────────────┘  │
                        ▼                                                 └── Sí ──► Revisa ─┐
    ┌─────────────────────────────────────────────────────────┐                              │
    │ [S5] Agente Generador de Informe Técnico (Claude Haiku) │                              │
    │   - Informe técnico                                     │                              │
    └───────────────────────────┬─────────────────────────────┘                              │
                                │                                                            │
                                ▼                                                            │
                    ┌──────────────────────────┐                                             │
                    │ [HITL] Supervisor humano │◄────────────────────────────────────────────┘
                    │   - Analiza propuesta    │
                    └──────────┬───────────────┘
                               │
                        ┌──────┴──────┐
                        │             │
                    Aprueba       Rechaza
                        │             │
                        ▼             ▼
            ┌────────────────┐  ┌─────────────────┐
            │ Configuración  │  │ Ajustes manuales│
            │ final aprobada │  │ o nuevo proceso │
            │ y enviada al   │  └─────────────────┘
            │ cliente        │
            └────────────────┘

### 3.2. Estados del proceso.

INICIADO → RELEVADO → PLANIFICADO → CONFIGURANDO → VALIDANDO → CONFIGURACIÓN_VALIDADA → INFORME_GENERADO → EN_REVISIÓN_HUMANA → APROBADO/RECHAZADO → FINALIZADO

### 3.3. Transiciones.

- Cada estado avanza al siguiente solo si su validación de salida es exitosa (ej. `CONFIGURANDO → VALIDANDO` ocurre cuando el Agente Configurador genera una configuración candidata completa).
- Transición especial **`EN_REVISIÓN_HUMANA`**: única vía aprobación/rechazo humano explícito (no hay timeout que envíe automáticamente).

### 3.4. Puntos de iteración y replanificación.

- S3 (Agente Planificador): si el presupuesto asignado a una o más categorías no permite construir una configuración viable (por ejemplo, no existen componentes compatibles dentro del importe asignado), el Agente Planificador replanifica redistribuyendo el presupuesto entre las categorías según las prioridades del cliente. Máximo 3 replanificaciones.
- S4 (Agente Configurador): si el Agente Configurador no encuentra componentes que satisfagan simultáneamente los requisitos funcionales y las restricciones presupuestarias, replanifica ampliando el conjunto de componentes candidatos o seleccionando alternativas equivalentes dentro de las reglas definidas. Máximo 5 intentos.
- S5 (Servicio de Validación Técnica): si la Validación detecta incompatibilidades (socket, memoria, potencia de la fuente, dimensiones del gabinete, etc.), el flujo retorna al Agente Configurador para generar una nueva propuesta.Máximo 10 iteraciones antes de escalar el caso al supervisor humano.

### 3.5. Condiciones de corte.

✅ Éxito: generación de una configuración válida.
⛔ Corte por presupuesto de herramientas: se alcanzó el tope de gasto/requests de la API.
⛔ Corte por error: fallo no recuperable de alguno de los servicios involucrados (catálogo de hardware inaccesible, Claude sin respuesta tras N reintentos, error del orquestador o credenciales inválidas).
⛔ Corte por presupuesto del cliente: no es posible construir una configuración que satisfaga los requisitos del cliente respetando el presupuesto máximo.
⛔ Corte por iteraciones máximas: se alcanza el número máximo de reconfiguraciones o replanificaciones sin obtener una solución válida. El caso se deriva al supervisor humano para su resolución.

### 3.6. Puntos de intervención humana.

1. **Checkpoint obligatorio (HITL)** antes de presentar la configuración definitiva al cliente.
2. **Escalamiento por excepción** ante ambigüedad, errores no recuperables o exceso de iteraciones.

## 4. Selección y Justificación Tecnológica

### 4.1. Asignación de herramienta por función

| **Función en el workflow**                                               | **Herramienta elegida**        | **Rol concreto**                                                                                                                   |
| ------------------------------------------------------------------------ | ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| **Orquestación**                                                         | **OpenClaw AI**                | Ejecuta el flujo agéntico end-to-end: secuencia los pasos, maneja reintentos, invoca herramientas y gestiona el checkpoint humano. |
| **Agentes de razonamiento (Encuestador y Generador de Informe Técnico)** | **Claude API (modelo Haiku)**  | Ejecuta el razonamiento de interpretación de requisitos y generación del informe técnico, con un bajo costo por token.             |
| **Agentes de razonamiento (Planificador y Configurador)**                | **Claude API (modelo Sonnet)** | Ejecuta el razonamiento de planificación del presupuesto y selección de componentes.                                               |
| **Fuente de datos**                                                      | **Catálogo de Hardware**       | Proporciona especificaciones técnicas, compatibilidad, precios y disponibilidad de los componentes.                                |
| **Persistencia**                                                         | **JSON**                       | Almacena configuraciones generadas, resultados de validación, historial de iteraciones y logs del workflow.                        |

### 4.2. Justificación comparativa

**¿Por qué OpenClaw AI como orquestador (y no un script o un único agente)?**
Un script lineal resulta adecuado para procesos secuenciales simples, pero no para un workflow con **múltiples agentes especializados, iteraciones, replanificaciones y puntos de intervención humana**. OpenClaw AI permite centralizar la coordinación del proceso, mantener el estado de la ejecución, controlar los límites de iteración, gestionar errores y decidir dinámicamente qué agente debe intervenir en cada etapa. Además, el orquestador desacopla la lógica del proceso de la lógica de cada agente, facilitando el mantenimiento y la incorporación de nuevos agentes sin modificar el workflow completo. Se descartan como orquestadores Claude Code y Codex en runtime porque están optimizados para tareas de codificación asistida, no para gestionar un workflow de negocio con checkpoints humanos persistentes, pero sí podrían ser utilizadas para construir el Servicio de Validación Técnica y el código del orquestador.

**¿Por qué distintos LLM para distintos agentes (y no un único modelo para todo)?**
Cada agente posee un nivel de complejidad diferente con el fin de **reducir el consumo de tokens sin comprometer la calidad del resultado**. Por ello se asigna el modelo según la naturaleza de la tarea: Encuestador y Generador de Informe Técnico con un modelo económico y rápido (Claude Haiku) y Planificador y Configurador con un modelo con mayor capacidad de razonamiento (Claude Sonnet).

**¿Por qué un Servicio de Validación Técnica (y no dejar toda la decisión al LLM)?**

Las comprobaciones de compatibilidad constituyen reglas determinísticas, no problemas de razonamiento abierto. Delegar estas verificaciones a un servicio específico **incrementa la confiabilidad del sistema, evita alucinaciones del LLM y facilita la auditoría de las decisiones técnicas**.

**¿Por qué un Catálogo de Hardware y no conocimiento propio del LLM?**

Los modelos de lenguaje poseen conocimiento general sobre componentes, pero no disponen de información actualizada sobre precios, disponibilidad, especificaciones técnicas, etc. El uso de un catálogo especializado **garantiza que todas las decisiones se basen en información vigente y verificable**.

### 4.3. Nivel de autonomía aportado por cada herramienta

- **OpenClaw AI (Orquestador)**: autonomía alta para coordinar el workflow, controlar iteraciones, replanificaciones y recuperación de errores. Su autonomía está **limitada por diseño**, ya que no puede aprobar una configuración ni entregarla al cliente sin pasar por el checkpoint de revisión humana.
- **Claude API (Haiku y Sonnet)**: autonomía media para interpretar requisitos, planificar, proponer configuraciones y redactar documentación. Sus decisiones son siempre propuestas, sujetas a validación técnica y revisión humana; se encuentra limitada por el orquestador.
- **Servicio de Validación Técnica**: autonomía baja y determinística. Aplica reglas predefinidas para verificar compatibilidades físicas, eléctricas y lógicas. No propone soluciones ni modifica configuraciones; únicamente acepta o rechaza una propuesta indicando las restricciones incumplidas.
- **Catálogo de Hardware**: autonomía nula. Actúa únicamente como fuente de información respondiendo consultas.
- **Almacenamiento (JSON)**: autonomía nula. Se limita a persistir configuraciones, resultados de validación, historial de iteraciones y logs de ejecución según las instrucciones del orquestador.

### 4.4. Riesgos técnicos asociados

| **Herramienta**                    | **Riesgo**                                                                                     | **Mitigación**                                                                                                                               |
| ---------------------------------- | ---------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **OpenClaw AI (Orquestador)**      | Bucles infinitos, replanificaciones excesivas, pérdida del estado del workflow.                | Límite de iteraciones, detección de estados repetidos, checkpoints, persistencia del estado y logs.                                          |
| **Claude API**                     | Alucinaciones, recomendaciones inconsistentes, costo por token, latencia.                      | Uso exclusivo de datos provenientes del catálogo, prompts estructurados, límites de tokens e iteraciones, validación técnica determinística. |
| **Servicio de Validación Técnica** | Reglas incompletas o desactualizadas, falsos positivos/negativos de compatibilidad.            | Mantenimiento periódico de las reglas, pruebas unitarias y validación contra especificaciones oficiales de los fabricantes.                  |
| **Catálogo de Hardware**           | Información desactualizada (precios, stock o especificaciones), indisponibilidad del servicio. | Consulta en tiempo real, reintentos, caché temporal, validación previa a la generación del informe.                                          |
| **Almacenamiento (JSON)**          | Pérdida o corrupción de datos, registros incompletos.                                          | Persistencia transaccional, copias de respaldo y registro estructurado de logs.                                                              |

## 5. Guardrails y Control de Riesgo

### 5.1. Validaciones automáticas

- **Validación de entrada**: comprobar que la solicitud del cliente contiene, como mínimo, el presupuesto, el uso principal del equipo y las preferencias relevantes (por ejemplo, marcas, formato o requisitos especiales). Si falta información crítica, el workflow se detiene y el Agente Encuestador solicita los datos faltantes.
- **Validación del presupuesto**: verificar que el presupuesto sea un valor numérico positivo y suficiente para construir una configuración mínima. Si está fuera de un rango razonable o es inconsistente con los requisitos (por ejemplo, "PC gaming 4K" con un presupuesto muy bajo), el sistema solicita una aclaración antes de continuar.
- **Control de iteraciones**: el orquestador registra el número de reconfiguraciones y replanificaciones realizadas. Si se alcanza el límite máximo el workflow se detiene y el caso se escala al supervisor humano.
- **Validación de datos del catálogo**: aceptar únicamente componentes que posean identificador, especificaciones técnicas, precio y disponibilidad válidos. Descartar registros incompletos, duplicados o con información inconsistente.
- **Salida estructurada de los LLM**: todas las respuestas de los agentes deben ajustarse a un esquema JSON predefinido. Si la salida no puede interpretarse correctamente, el orquestador reintenta la ejecución hasta el límite establecido y, si persiste el error, deriva el caso al supervisor.
- **Anti-alucinación**: los LLM solo pueden fundamentar sus decisiones utilizando información proveniente del Catálogo de Hardware. Se prohíbe inferir especificaciones técnicas, precios, compatibilidades o disponibilidad que no estén presentes en la fuente de datos.
- **Validación técnica obligatoria**: toda configuración propuesta debe superar el Servicio de Validación Técnica, verificando compatibilidad de socket, memoria, alimentación eléctrica, dimensiones físicas y cumplimiento del presupuesto antes de generar el informe.
- **Validación de la propuesta final**: comprobar que la configuración incluya todos los componentes obligatorios (CPU, motherboard, memoria, almacenamiento, fuente y gabinete), que el costo total no exceda el presupuesto y que el informe técnico haya sido generado correctamente antes de enviarlo al supervisor humano.

### 5.2. Reglas de control

- **Topes duros**: máximo de iteraciones de configuración por ejecución; máximo de replanificaciones presupuestarias; límite de consumo de tokens por ejecución.
- **Allowlist de acciones**: el orquestador solo puede invocar las herramientas registradas (Agentes LLM, Catálogo de Hardware, Servicio de Validación Técnica y Almacenamiento). Cualquier otra acción está prohibida.
- **Sin modificaciones de fuentes**: el sistema no puede modificar precios, disponibilidad ni especificaciones del Catálogo de Hardware.
- **Validación obligatoria**: ninguna configuración puede avanzar a la etapa de generación del informe si no ha sido aprobada previamente por el Servicio de Validación Técnica.

### 5.3. Límites de autonomía

- **Entrega de la propuesta**: se encuentra prohibido al orquestador y los agentes. La configuración solo puede considerarse aprobada y presentarse al cliente tras la revisión y aprobación explícita del supervisor humano.
- **Checkpoint humano obligatorio**: toda configuración validada e informe técnico deben pasar por un punto de revisión humana antes de finalizar el workflow o comunicarse al cliente.
- **Sin auto-aprobación por timeout**: si el supervisor no revisa la propuesta, la ejecución permanece en estado EN_REVISIÓN_HUMANA. El sistema nunca aprueba automáticamente una configuración por ausencia de respuesta.
- **Sin modificación de requisitos del cliente**: el agente no puede alterar el presupuesto, cambiar las preferencias o relajar restricciones por iniciativa propia. Si determina que los requisitos son incompatibles, solicita una replanificación al Agente Planificador o escala el caso al supervisor humano.

### 5.4. Protocolos de escalamiento

| **Disparador**                                          | **Acción**                                                                                                                             |
| ------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| **Requisitos ambiguos o incompletos**                   | Pausar el workflow y solicitar aclaraciones al cliente o al supervisor humano antes de continuar.                                      |
| **≥ N fallos consecutivos de API/LLM**                  | Detener la ejecución, registrar el error y notificar al supervisor para revisión.                                                      |
| **Máximo de iteraciones o replanificaciones alcanzado** | Finalizar el proceso automático y escalar el caso al supervisor humano con el historial de intentos realizados.                        |
| **No existe una configuración compatible**              | Detener la configuración automática y solicitar una decisión humana (ajustar presupuesto, modificar requisitos o cancelar el proceso). |
| **Fallo de validación técnica repetido**                | Escalar el caso con el detalle de las incompatibilidades detectadas para evitar ciclos de reconfiguración.                             |

### 5.5. Mecanismos de logging y auditoría

- **Log estructurado por ejecución**: registrar el input del cliente, los requisitos relevados, la distribución del presupuesto, las configuraciones candidatas generadas, los resultados de la validación técnica, el informe final, el número de iteraciones y replanificaciones, los timestamps, el consumo estimado de tokens/API y las decisiones del supervisor (aprobado, ajustes solicitados o rechazado).
- **Retención y acceso**: logs versionados localmente; sin almacenar credenciales en texto plano (variables de entorno/secret manager).

### 5.6. Escenarios de fallo y su gestión

| **Escenario**                                                                          | **Gestión**                                                                                                                                                                            |
| -------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Catálogo de Hardware no disponible o sin respuesta**                                 | Reintentos con _backoff exponencial_; si el servicio continúa indisponible, detener la ejecución, registrar el error y notificar al supervisor humano.                                 |
| **LLM no responde / rate limit / error de API**                                        | Reintentos con _backoff exponencial_; si se supera el máximo permitido, finalizar la ejecución y registrar el incidente para revisión.                                                 |
| **Salida del LLM inválida (JSON mal formado o datos incompletos)**                     | Validar el esquema de salida, regenerar la respuesta hasta el límite de reintentos y, si persiste el error, escalar el caso al supervisor humano.                                      |
| **No existe una configuración compatible con los requisitos y el presupuesto**         | Solicitar una replanificación presupuestaria; si se alcanza el máximo de replanificaciones, escalar el caso al supervisor humano.                                                      |
| **Fallo repetido de la validación técnica**                                            | Registrar las incompatibilidades detectadas, detener nuevas iteraciones al alcanzar el límite configurado y derivar el caso al supervisor humano.                                      |
| **Credenciales inválidas o expiradas (API del catálogo o LLM)**                        | Interrumpir el workflow de forma controlada, registrar el fallo y solicitar la renovación de las credenciales antes de reanudar el proceso.                                            |
| **Información inconsistente en el catálogo**                                           | Descartar los registros inconsistentes y registrar la incidencia para auditoría.                                                                                                       |
| **El Configurador solicita replanificación de forma repetida sin mejorar la solución** | El orquestador detecta la falta de progreso, cancela nuevas replanificaciones automáticas y escala el caso al supervisor humano con el historial completo de decisiones e iteraciones. |

## 6. KPIs Operativos y Métricas

### 6.1. KPIs mínimos definidos

| **KPI**                          | **Definición**                                                                                                                                                   | **Objetivo**                        |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| **Latencia esperada**            | Tiempo total desde la recepción de los requisitos del cliente hasta la generación de una configuración validada y el informe técnico listo para revisión humana. | **< 5 min** por solicitud estándar. |
| **Tasa de éxito objetivo**       | Porcentaje de ejecuciones que completan el workflow generando una configuración técnicamente válida y un informe sin requerir escalamiento por error.            | **≥ 90%**                           |
| **Costo por tarea estimado**     | Costo de una ejecución considerando consumo de tokens de los LLMs.                                                                                               | **< USD 0.50 por configuración**    |
| **Ratio de intervención humana** | Porcentaje de configuraciones que el supervisor humano edita/descarta.                                                                                           | **≤ 10%**                           |

### 6.2. KPIs complementarios

| **KPI complementario**       | **Definición**                                                                                                                 | Objetivo  |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------ | --------- |
| **Ratio de reprocesamiento** | % de solicitudes que deben volver a la etapa de Planificación o Configuración tras la validación técnica o la revisión humana. | **≤ 25%** |

### 6.3. Baseline actual

| **Métrica**                                    | **Baseline (proceso manual)**                               | **Esperado (con SICHP)**                                                                                   |
| ---------------------------------------------- | ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| **Tiempo de elaboración de una configuración** | ~30–60 min por cliente                                      | **< 5 min** por solicitud                                                                                  |
| **Configuraciones atendidas por jornada**      | ~8–12 solicitudes de clientes                               | **100+ solicitudes**                                                                                       |
| **Consistencia de las recomendaciones**        | Depende de la experiencia del asesor; difícil de auditar    | **Criterios uniformes**, trazables y reproducibles                                                         |
| **Detección de incompatibilidades**            | Revisión manual, propensa a omisiones                       | **Validación automática** de compatibilidad física, eléctrica y presupuestaria antes de la revisión humana |
| **Justificación de la recomendación**          | Explicación verbal o documentación variable según el asesor | **Informe técnico estandarizado**, generado automáticamente y listo para revisión                          |

### 6.4. Impacto esperado

- **Reducción de ~85–90%** del tiempo necesario para elaborar una configuración personalizada (de aproximadamente 30–60 minutos a menos de 5 minutos por solicitud).
- **Aumento de 10x** en la capacidad de atención, permitiendo procesar muchas más solicitudes de clientes por jornada gracias a la automatización del análisis, la validación y la generación del informe.

### 6.5. Acciones ante caída de KPIs

| **KPI bajo umbral**                    | **Acción correctiva**                                                                                                                                                                                                 |
| -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Latencia > 5 min**                   | Optimizar el workflow, paralelizar consultas al Catálogo de Hardware, reducir llamadas innecesarias a los LLMs o utilizar modelos más livianos para tareas de menor complejidad.                                      |
| **Tasa de éxito < 90%**                | Revisar el manejo de errores del orquestador, mejorar la estrategia de reintentos y actualizar las reglas del Servicio de Validación Técnica para reducir fallos evitables.                                           |
| **Costo por tarea > USD 0.5**          | Analizar el consumo por agente, reasignar tareas de baja complejidad a modelos más económicos, limitar iteraciones.                                                                                                   |
| **Ratio de intervención humana > 10%** | Ajustar los prompts del Planificador y del Configurador, revisar los criterios de distribución del presupuesto y mejorar las reglas de selección de componentes para aumentar la calidad de las propuestas iniciales. |
| **Ratio de reprocesamiento > 25%**     | Analizar las causas de las replanificaciones, optimizar la estrategia inicial de asignación del presupuesto y ampliar las reglas del Configurador para generar configuraciones más robustas desde el primer intento.  |

## 7. Roadmap de Implementación

### Fase 1 — Diagnóstico (Semana 1)

- **Entregables**: documento de proceso actual, baseline medido, definición de criterios de scoring y "fit" de prospecto.
- **Nota de stack**: se utilizará OpenClaw AI como orquestador del workflow y el razonamiento se resuelve con la API de Claude, modelos Haiku y Sonnet.
- **Responsable**: responsable del proceso de consultoría + autor del proyecto.
- **Riesgo de adopción**: requisitos del proceso mal relevados o criterios técnicos inconsistentes. → _Mitigación_: entrevistas con asesores de hardware y validación conjunta de los criterios técnicos.

### Fase 2 — Diseño (Semana 2)

- **Entregables**: blueprint de la célula híbrida, definición de agentes, prompts, guardrails, arquitectura del workflow, modelo de datos, diseño de logs y reglas del Servicio de Validación Técnica.
- **Responsable**: autor / rol técnico.
- **Riesgo**: reglas de compatibilidad incompletas o arquitectura sobredimensionada → _Mitigación:_ validar tempranamente los casos de uso más frecuentes y construir un prototipo del motor de reglas.

### Fase 3 — Desarrollo (Semanas 3–4)

- **Entregables**: implementación del workflow en OpenClaw AI, integración con los LLMs y el Catálogo de Hardware, desarrollo del Servicio de Validación Técnica, sistema de logging y auditoría.
- **Responsable**: autor / rol técnico.
- **Riesgo**: fallos de integración entre APIs, límites de consumo o credenciales inválidas → _Mitigación:_ entorno de pruebas, límites de consumo, reintentos con backoff y monitoreo de errores.

### Fase 4 — Piloto (Semana 5)

- **Entregables**: ejecución de solicitudes reales o simuladas, medición de KPIs frente al baseline, ajuste de prompts, reglas de compatibilidad y distribución del presupuesto.
- **Responsable**: autor + asesor técnico "early adopter".
- **Riesgo**: baja confianza en las recomendaciones generadas por IA → _Mitigación:_ mantener el checkpoint humano, comparar las configuraciones generadas con configuraciones elaboradas manualmente y mostrar ahorro de tiempo.

### Fase 5 — Despliegue controlado (Semana 6)

- **Entregables**: utilización del sistema dentro del proceso de consultoría con supervisión humana obligatoria, documentación técnica, manual de usuario y protocolos de escalamiento.
- **Responsable**: responsable del área de consultoría.
- **Riesgo**: dependencia excesiva de la IA o aceptación de configuraciones sin revisión adecuada → _Mitigación:_ checkpoint obligatorio y capacitación de los usuarios.

### Fase 6 — Monitoreo y optimización (Continuo)

- **Entregables**: seguimiento continuo de KPIs, revisión de logs, actualización de reglas de compatibilidad, optimización de prompts y evaluación periódica del costo por configuración.
- **Responsable**: responsable del proceso + autor del proyecto.
- **Riesgo**: degradación del rendimiento por cambios de precios o nuevas generaciones de hardware → _Mitigación:_ alertas por KPI bajo umbral.

### Estrategia de gestión del cambio

- **Comunicar el rol del agente como "asistente", no reemplazo**: el objetivo es automatizar las tareas repetitivas y dejar la decisión final bajo supervisión humana, libera tiempo para el consultor, no lo sustituye.
- **Capacitación breve** en el funcionamiento de la célula híbrida, método de solicitud de modificaciones y el proceso de revisión de configuraciones e informes técnicos.
- **Implementación gradual**, comenzando con un grupo reducido de solicitudes y ampliando el uso del sistema conforme se validen los KPIs definidos.
- **Aumento progresivo de autonomía** condicionado al cumplimiento sostenido de los KPIs de calidad, costo, tasa de éxito y ratio de intervención humana, manteniendo siempre la aprobación final bajo control del supervisor.

## 8. Checklist de Autoevaluación

| **Consigna**                                                                     | **Check** | **Ubicación**                            |
| -------------------------------------------------------------------------------- | --------- | ---------------------------------------- |
| Descripción detallada del agente usando el marco Persona-Tarea-Contexto          | ✅        | 2. Marco Persona – Tarea – Contexto      |
| Justificación y descripción del stack tecnológico (Claude Code, Codex, OpenClaw) | ✅        | 4. Selección y Justificación Tecnológica |
| Diagrama claro del flujo de razonamiento y toma de decisiones del agente         | ✅        | 3. Diseño del Agentic Workflow           |
| Protocolos de supervisión y guardrails implementados                             | ✅        | 5. Guardrails y Control de Riesgo        |
| Definición y análisis de KPIs operativos                                         | ✅        | 6. KPIs Operativos y Métricas            |
| Roadmap de adopción con fases y entregables                                      | ✅        | 7. Roadmap de Implementación             |
