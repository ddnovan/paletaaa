# Guía de Contribución y Convenciones

Para mantener un historial de código limpio, profesional y fácil de auditar, seguimos el estándar de **Conventional Commits**:

`<tipo>(<scope>): <descripción en imperativo/presente y minúsculas>`

## Tipos Permitidos

* `feat`: Nueva funcionalidad para el usuario.
* `fix`: Corrección de un error o bug.
* `docs`: Cambios exclusivamente en la documentación.
* `style`: Cambios de formato o CSS que no afectan la lógica.
* `refactor`: Reestructuración de código sin cambiar su comportamiento.
* `test`: Añadir o corregir pruebas unitarias.
* `chore`: Tareas de mantenimiento o configuración.

## Scopes (Ámbitos del Proyecto)

| Scope | Módulo / Área | Cuándo utilizarlo |
| :--- | :--- | :--- |
| **`core`** | Arquitectura base | Cambios en `EventBus`, `State` e inicialización global. |
| **`engine`** | Generador de paletas | Algoritmos de creación cromática y armonías. |
| **`contrast`** | Motor WCAG 2.1 | Cálculo de luminancia, Gamma Correction y ratios de contraste. |
| **`a11y`** | Accesibilidad y daltonismo | Matrices LMS, simulación de filtros y atributos WAI-ARIA. |
| **`storage`** | Persistencia de datos | Guardado/lectura en `localStorage` y estado de bloqueos (*locks*). |
| **`ui`** | Interfaz de usuario | Captura de eventos DOM, botones, copiado al portapapeles y avisos. |
| **`matrix`** | Matriz de ratios | Cálculo y renderizado de la tabla cruzada $N \times N$. |
| **`docs`** | Documentación técnica | Actualización de `SRS.md`, `SDD.md`, `README.md` o referencias. |