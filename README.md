# Coding Challenge: Soporte TechCorp 🚨

## 🛠️ Resolución del Challenge (Entregado por Luis)

### 1. Reporte de Tickets Solucionados
A continuación detallo el diagnóstico y la solución aplicada a los 4 problemas críticos reportados por José en el ambiente de producción:

**Ticket 1 (Botón 'Resolver' en móvil):**
*   **Causa:** El contenedor principal no tenía margen inferior en diseño responsive, por lo que el footer flotante en móviles bloqueaba físicamente el acceso al último ticket de la lista y a su botón de 'Resolver'.
*   **Solución:** Se agregó la clase de Tailwind `pb-20` (padding bottom) en el contenedor principal de `page.tsx`.

**Ticket 2 (Recarga de página para ver estados):**
*   **Causa:** Violación directa de inmutabilidad en el estado de React. Se alteraba la misma referencia en memoria del arreglo original de tickets (`tickets[index] = updatedTicket`), provocando que React silenciara el re-render en la interfaz.
*   **Solución:** Se utilizó el método inmutable `.map()` dentro de `setTickets()` para retornar un arreglo completamente nuevo con el estado individual de cada ticket actualizado al vuelo.

**Ticket 3 (Carga infinita en casos Urgentes):**
*   **Causa:** Una vulnerabilidad lógica en la función simulada de correo `sendEmailNotification`. La Promesa generada nunca invocaba el contrato `resolve()`, dejando el Request del backend bloqueado a la espera infinita (timeout).
*   **Solución:** Se implementó un `setTimeout` que ejecuta correctamente un `resolve(true)` liberando el hilo del servidor en `api/tickets/[id]/route.ts`.

**Ticket 4 (Fuga de datos entre empresas):**
*   **Causa:** Vulnerabilidad de filtrado lateral. La ruta GET principal estaba lanzando un `findMany` en la capa ORM (Prisma) sin aplicar ninguna cláusula o discriminador por compañía del usuario actual.
*   **Solución:** Se aisló la entrega de datos acoplando explícitamente la cláusula `where: { companyId: 'TechCorp' }` para evitar cruces en `api/tickets/route.ts`.

### 2. 🔍 Observaciones de Deuda Técnica
Durante la revisión iterativa del código para resolver los tickets asignados, noté los siguientes puntos de vulnerabilidad en el sistema. Para respetar estrictamente el alcance de los tickets actuales, no modifiqué este código aislado; sin embargo, sugiero refactorizarlo en nuestro próximo sprint:

*   **1. "Hardcodeo" del Sistema de Seguridad:** El FIX implementado para el Bug 4 protege el entorno temporalmente. Pero para llegar a producción real, la variable `TechCorp` debe volverse dinámica, desencriptándose desde el payload de un Token JWT en las cabeceras REST del request o de Cookies protegidas por el servidor. 
*   **2. Patrón Anticuado de Traer Datos:** Usar un `useEffect()` montando un `fetch()` raw al vuelo causa cascadas en la red, impide los re-intentos automáticos del navegador y no ofrece control de caché. El panel de soporte escalará robustamente si migramos esa carga hacia *Server Components* de SSR (Next 14+) o en su defecto a herramientas del ecosistema como *SWR*.
*   **3. Falta de manejo de estado de error visual (Frontend):** Si la base de datos o el DNS del backend caen, `fetchTickets` logueará el status en la consola pero el agente de soporte verá un texto que confunde diciendo _"No hay tickets pendientes. ¡Buen trabajo!"_ en vez de un fallback visual o una Alerta de Error de conexión. 

### 3. Uso de Herramientas de IA (Agentes)
Con base en los requerimientos del Challenge corporativo, utilicé Inteligencia Artificial como "Pair/Rubber Duck Programmer" principalmente como apoyo para:
*   Acelerar el diagnóstico analítico de la pirámide de dependencias de React para focalizar las líneas de código del Bug visual.
*   Recibir confirmación síncrona de sintaxis de Promesas en JavaScript.
*   Mantenimiento pulcro y atómico del flujo en la terminal para crear `git commits` descriptivos y bien estructurados.

---

## 📋 Contexto Original del Reto

¡Hola! Gracias por aplicar. Para esta prueba técnica, queremos simular un escenario real de nuestro día a día. No hay requerimientos abstractos, sino un problema real de soporte que debes resolver.

Se evaluará tu capacidad para:
- Entender código existente (Node.js, React/Next.js, Tailwind).
- Utilizar herramientas de IA (Claude, Cursor, Gemini, etc.) para acelerar tu diagnóstico y resolución.
- Priorizar tareas críticas bajo presión.
- Mantener el orden y las buenas prácticas al corregir bugs.

Acabas de iniciar tu día y recibes el siguiente mensaje por Slack de José (Project Manager):

> **De:** José
> **Para:** Equipo de Soporte
> 
> "Hola chicos, buenos días. Les paso contexto de unos inconvenientes urgentes que tenemos en la plataforma de TechCorp. Austin (del cliente) me indica que no puede ingresar a resolver los tickets desde su celular, el botón de 'Resolver' simplemente no le hace nada. 
>
> Además, al parecer están teniendo que recargar toda la página para ver cuando un ticket cambia de estado. Es un tema urgente porque las personas de soporte de ellos no pueden gestionar los casos marcados como 'Urgente', dicen que el sistema se queda cargando y nunca termina. Ya estoy creando los tickets en Jira.
> 
> Y por último, y esto es lo más crítico: me acaban de confirmar que un usuario pudo ver los tickets de OTRA empresa. Necesitamos revisar qué está interfiriendo ahí con la base de datos o el servicio, no podemos tener esa fuga de datos.
>
> Me confirman cuando lo tengan listo para coordinar pruebas finales con ellos. Mil gracias."

## Tu Misión

1. Clona este repositorio e instala las dependencias (`npm install`).
2. Levanta la base de datos local poblada de prueba (`npm run db:setup`) y el servidor (`npm run dev`).
3. Identifica y resuelve los 4 problemas mencionados por José en su mensaje.
4. Sube tu código a un repositorio público (GitHub/GitLab) y envíanos el enlace.

**Nota:** Tienes total libertad de usar herramientas de IA para apoyarte. Lo que nos importa es cómo analizas el problema, cómo guías a la IA y la calidad de la solución final. ¡Éxitos!
