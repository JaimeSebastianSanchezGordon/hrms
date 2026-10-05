# Roadmap: HRMS

## 1. Visión y Estrategia de Arquitectura

HRMS se concibe como un **Monolito Modular** orientado al dominio del ciclo de vida del talento, donde cada módulo representa una rebanada vertical de negocio (experiencia de usuario, lógica de dominio y persistencia de datos) con autonomía funcional y límites explícitos. La estrategia evita la fragmentación en servicios distribuidos y prioriza un despliegue único, seguro y de bajo mantenimiento, coherente con el requisito de go-live en 6 meses y con la integración a SSO, nómina y calendario corporativo.

El desacoplamiento se logra mediante **Vertical Slicing**: cada módulo expone capacidades de negocio completas (por ejemplo, "solicitar vacaciones" abarca su interfaz de autoservicio, su motor de aprobación y su registro persistente) y se comunica con otros módulos a través de contratos internos bien definidos, no de dependencias ocultas. El **Expediente Digital del Empleado** actúa como núcleo fundacional del que dependen los demás módulos, ya que provee las entidades maestras de persona, posición y estructura organizacional sobre las que se apoyan desempeño, competencias, aprendizaje, compensación, sucesión y analítica.

La **gestión de competencias y habilidades** se trata como eje transversal del dominio: alimenta las recomendaciones de aprendizaje, los planes de sucesión y los dashboards de brechas, por lo que su módulo se ubica temprano en el orden de ejecución. La **analítica de personas** se diseña como módulo de solo lectura sobre los datos consolidados de los demás módulos, garantizando que los indicadores ejecutivos (rotación, headcount, absentismo, diversidad, retención) reflejen una única fuente de verdad. Los flujos de aprobación configurables y el autoservicio se modelan como capacidades compartidas dentro del módulo de autoservicio y portal, evitando duplicar lógica de permisos y jerarquías en cada rebanada.

---

## 2. Módulos (Specs)

### 1. Expediente Digital del Empleado y Estructura Organizacional

- **Tipo de Módulo:** core_foundation
- **Propósito:** Constituir la fuente única de verdad de personas, posiciones, jerarquías y datos contractuales/salariales, habilitando autenticación, roles, permisos y el repositorio documental del empleado.
- **Interacciones de Usuario (Alto Nivel):**
  - El administrador de RR. HH. crea y mantiene el expediente digital de cada empleado (datos personales, contractuales, salariales, históricos de posición y documentos) para asegurar trazabilidad y cumplimiento normativo.
  - El líder de RR. HH. define y actualiza la estructura organizacional (áreas, posiciones y jerarquías) para reflejar la realidad de la plantilla y habilitar flujos de aprobación.
  - El empleado consulta y actualiza sus datos personales y de contacto desde su expediente para mantener la información vigente sin intermediación de RR. HH.
  - El equipo de IT configura la integración con SSO/Active Directory y asigna roles y permisos por perfil (RR. HH., gerente, empleado) para garantizar acceso seguro y segmentado.
  - Primer Uso (Setup Wizard): El sistema debe detectar si es la primera ejecución (sin usuarios en la base de datos) y presentar un asistente de configuración inicial para crear la cuenta del administrador principal.
- **Dependencias:** Ninguna. (Módulo Fundacional)

### 2. Reclutamiento, Onboarding y Movilidad Interna

- **Tipo de Módulo:** feature_slice
- **Propósito:** Gestionar el pipeline de vacantes internas, candidatos, entrevistas, ofertas y la transición del candidato seleccionado hacia el expediente digital, incluyendo postulaciones a movimientos internos.
- **Interacciones de Usuario (Alto Nivel):**
  - El reclutador de RR. HH. publica vacantes internas y da seguimiento al pipeline de candidatos por etapa para asegurar trazabilidad del proceso selectivo.
  - El gerente de línea revisa candidatos, programa entrevistas y registra feedback para decidir avances en el proceso de selección.
  - El empleado postula a movimientos internos y consulta el estado de su candidatura para planificar su desarrollo de carrera dentro de la organización.
  - El reclutador genera y envía cartas de oferta salarial al candidato seleccionado para formalizar la incorporación.
  - El administrador de RR. HH. convierte al candidato contratado en un nuevo expediente digital, activando el flujo de onboarding y sus tareas asociadas.
- **Dependencias:**
  - **Expediente Digital del Empleado y Estructura Organizacional**: Requiere las entidades de persona, posición y estructura organizacional para asociar vacantes, candidatos y conversiones de contratación al expediente correcto.

### 3. Gestión de Competencias, Desempeño y Aprendizaje

- **Tipo de Módulo:** feature_slice
- **Propósito:** Administrar el diccionario de competencias por rol, la matriz de niveles esperados, la evaluación de brechas, los ciclos de desempeño (objetivos, 360°, feedback continuo, calibración) y el catálogo de aprendizaje vinculado a brechas.
- **Interacciones de Usuario (Alto Nivel):**
  - El líder de RR. HH. define el diccionario de competencias por rol y la matriz de niveles esperados para estandarizar las expectativas de talento en la organización.
  - El gerente de línea lanza un ciclo de evaluación semestral, recopila autoevaluaciones y feedback 360°, y consolida resultados para decidir promociones o planes de mejora.
  - El empleado completa su autoevaluación, registra feedback continuo y consulta su matriz de competencias y brechas para orientar su desarrollo.
  - El líder de RR. HH. identifica brechas en equipos críticos y recomienda rutas de aprendizaje o movimientos internos para cerrarlas.
  - El empleado accede al catálogo de formaciones y rutas de aprendizaje recomendadas por rol o brecha, y registra el avance de sus certificaciones.
- **Dependencias:**
  - **Expediente Digital del Empleado y Estructura Organizacional**: Necesita las entidades de empleado, posición y jerarquía para asociar competencias, evaluaciones y rutas de aprendizaje a cada persona y rol.

### 4. Compensación, Beneficios y Sucesión

- **Tipo de Módulo:** feature_slice
- **Propósito:** Gestionar bandas salariales, administración de beneficios, visibilidad de compensación total, así como la identificación de talento crítico, mapas de sucesión y planes de carrera.
- **Interacciones de Usuario (Alto Nivel):**
  - El líder de RR. HH. define bandas salariales y administra el catálogo de beneficios para asegurar equidad interna y competitividad externa.
  - El gerente de línea consulta la compensación total de su equipo y la posición de cada persona respecto a su banda para sustentar decisiones de ajuste o promoción.
  - El empleado consulta su compensación total (salario, beneficios y valoraciones) para entender su paquete retributivo sin solicitudes manuales a RR. HH.
  - El líder de RR. HH. identifica talento crítico y construye mapas de sucesión para posiciones clave, vinculando candidatos internos con planes de carrera.
  - El empleado consulta su plan de carrera y las oportunidades de sucesión para las que ha sido identificado, alineando su desarrollo con la estrategia de talento.
- **Dependencias:**
  - **Expediente Digital del Empleado y Estructura Organizacional**: Requiere datos contractuales, salariales y de posición para calcular bandas, compensación total y mapas de sucesión.
  - **Gestión de Competencias, Desempeño y Aprendizaje**: Utiliza la matriz de competencias, las evaluaciones de desempeño y las brechas identificadas para sustentar decisiones de sucesión y planes de carrera.

### 5. Autoservicio, Portal del Empleado y Analítica de Personas

- **Tipo de Módulo:** reporting_analytics
- **Propósito:** Proveer el portal de autoservicio (solicitudes de vacaciones y permisos, recibos, organigrama, directorio) con flujos de aprobación configurables, y ofrecer dashboards ejecutivos de rotación, headcount, absentismo, diversidad, retención, desempeño, competencias y sucesión.
- **Interacciones de Usuario (Alto Nivel):**
  - El empleado solicita vacaciones o permisos desde el portal, consulta su saldo disponible y obtiene la aprobación del gerente sin correos manuales.
  - El gerente de línea aprueba o rechaza solicitudes de su equipo y consulta el organigrama y directorio para gestionar la operación diaria.
  - El empleado consulta sus recibos de nómina integrados y actualiza sus datos personales desde el portal para reducir la carga administrativa de RR. HH.
  - El analista de RR. HH. extrae dashboards en tiempo real de rotación, headcount, absentismo, diversidad y retención, segmentando por área, antigüedad, desempeño y motivo de salida para presentar al comité de dirección.
  - El líder de RR. HH. monitorea indicadores de desempeño, competencias y sucesión para planificar estratégicamente el talento y cumplir normativas internas.
- **Dependencias:**
  - **Expediente Digital del Empleado y Estructura Organizacional**: Requiere las entidades maestras de empleado, posición y jerarquía para resolver permisos, aprobaciones y segmentaciones analíticas.
  - **Gestión de Competencias, Desempeño y Aprendizaje**: Consume datos de desempeño, competencias y aprendizaje para alimentar los dashboards ejecutivos y las recomendaciones del portal.
  - **Compensación, Beneficios y Sucesión**: Consume datos de compensación y sucesión para completar los indicadores de retención, diversidad y talento crítico.

---

## 3. Matriz de Dependencias y Orden de Ejecución

| Módulo | Módulos Upstream requeridos | Tipo de Acoplamiento | Razonamiento de Orden |
|---|---|---|---|
| Expediente Digital del Empleado y Estructura Organizacional | Ninguno | Fundacional | Punto de entrada; provee entidades maestras de persona, posición, jerarquía, roles y permisos sobre las que se construyen todos los demás módulos. |
| Reclutamiento, Onboarding y Movilidad Interna | Expediente Digital del Empleado y Estructura Organizacional | Fuerte | Requiere entidades de persona, posición y estructura organizacional para asociar vacantes, candidatos y conversiones de contratación al expediente correcto. |
| Gestión de Competencias, Desempeño y Aprendizaje | Expediente Digital del Empleado y Estructura Organizacional | Fuerte | Necesita empleados, posiciones y jerarquías para asociar competencias, evaluaciones y rutas de aprendizaje a cada persona y rol. |
| Compensación, Beneficios y Sucesión | Expediente Digital del Empleado y Estructura Organizacional; Gestión de Competencias, Desempeño y Aprendizaje | Fuerte | Requiere datos contractuales y salariales del expediente, y la matriz de competencias, evaluaciones y brechas para sustentar bandas, compensación total y mapas de sucesión. |
| Autoservicio, Portal del Empleado y Analítica de Personas | Expediente Digital del Empleado y Estructura Organizacional; Gestión de Competencias, Desempeño y Aprendizaje; Compensación, Beneficios y Sucesión | Fuerte | Consume datos consolidados de los módulos previos para resolver permisos, aprobaciones, recibos, organigrama y dashboards ejecutivos de talento. |