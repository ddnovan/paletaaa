# Software Requirements Specification
## For Paletaaa (Accessible Palette Generator)

Version 1.0
Prepared by Dónovan Martín 
Organization: Estudiante de Ingeniería Informática / Portafolio Personal  
Date: Agosto 2026

## Table of Contents
* [1. Introduction](#1-introduction)
* [2. Product Overview](#2-product-overview)
* [3. Requirements](#3-requirements)
* [4. Verification Matrix](#4-verification-matrix)

## Revision History

| Name | Date | Reason For Changes | Version |
|------|------|--------------------|---------|
| Dónovan Martín |08/08/2026 | Redacción inicial de la especificación | 1.1 |
---

## 1. Introduction
### 1.1 Document Purpose
Define los requisitos funcionales y no funcionales para Paletaaa.

### 1.2 Product Scope
Herramienta web *Client-side* pura (SPA) para la generación de paletas de colores accesibles y verificación estricta de contraste según WCAG 2.1, incluye simulación de daltonismo en tiempo real.

### 1.3 Definitions, Acronyms, and Abbreviations
| Term | Definition |
|------|------------|
| WCAG | Web Content Accessibility Guidelines (v2.1) |
| SPA  | Single Page Application |
| Pub/Sub | Design Pattern (Publisher/Subscriber) |

### 1.4 References
- W3C Relative Luminance Definition & WCAG 2.1 Guidelines
- SDD.md (Software Design Description del proyecto)

---

## 2. Product Overview
### 2.1 Product Perspective
<!-- ¿Dónde se ejecuta la aplicación y cómo encaja en su entorno? -->
Aplicación web autónoma (standalone) ejecutable en el navegador sin backend ni dependencias de terceros.

### 2.2 Product Functions
<!-- Capacidades/características principales que ofrece la herramienta -->
- Generación y manipulación de paletas armónicas.
- Simulación de acromatopsia/daltonismo (Protanopía, Deuteranopía, Tritanopía).
- Cálculo en tiempo real de ratio de contraste WCAG y matriz de ratios de contraste.
- Vista previa de componentes ("Presentación Típica").
- Exportación (CSS/JSON) y persistencia local (`localStorage`).

### 2.3 Product Constraints
<!-- Limitaciones estrictas que condicionan la arquitectura y la programación -->
- **Sin Frameworks:** Limitado a HTML5, CSS3 Nativo y Vanilla JS (ES6 Modules).
- **Client-Side Pure:** Todo el procesamiento matemático de color y luminancia debe realizarse en el hilo principal del navegador.

### 2.4 User Characteristics
<!-- Quién usará el sistema y qué necesidades particulares tiene -->
* **Diseñadores UI/UX:** Requieren previsualizar estéticas de forma fácil y rápida para exportar valores cromáticos.
* **Desarrolladores Web:** Necesitan copiar códigos HEX/variables CSS de forma inmediata y verificar el cumplimiento de accesibilidad.
* **Auditores de Accesibilidad:** Buscan validar numéricamente ratios de contraste exactos según la WCAG 2.1.
* **Otros Artistas:** El contraste transciende a otros ámbitos, puede ser de especial ayuda para el resto de personas que interactúan con el mundo del arte de alguna forma.

---

## 3. Requirements

### 3.1 External Interfaces
#### 3.1.1 User Interfaces
<!-- Reglas y estructura de la interfaz gráfica e interacción -->
Interfaz responsive de 4 bloques principales:
* Encabezado: menú de opciones/capacidades de la herramienta.
* Panel de Control: selector de colores principales y simulador de daltonismo. 
* Lienzo de Muestras y Presentación Típica: paleta de colores generadas, portapapeles, regeneración y vista previa (junto con datos resultantes del contraste).
* Matriz de ratios de contraste.
#### 3.1.2 Software / Browser APIs
<!-- Integración con APIs nativas del navegador -->
* `Storage API` (`localStorage`): Para guardar la paleta actual y preferencias del usuario.
* `Clipboard API`: Para transferir los códigos de color al portapapeles del sistema.

### 3.2 Functional Requirements
<!-- Comportamientos específicos en formato de
 historias o requisitos verificables -->

Por el momento, cada **Verification Method** viene dado por *Test / Inspection / Demostration / Analysis*.

#### REQ-FUNC-01: Generación Aleatoria de Paleta
- **Statement:** El sistema debe generar una combinación de colores armónicos (aleatoria o respetando bloqueos) al accionar el botón "Generar", "Regenerar" o presionar la barra espaciadora.
- **Acceptance Criteria:** La interfaz debe actualizar las muestras en menos de 50ms sin recargar la página.

#### REQ-FUNC-02: Simulación de Daltonismo
- **Statement:** El sistema debe aplicar transformaciones de matriz de color a las muestras visibles según el filtro seleccionado (Protanopía, Deuteranopía, Tritanopía).
- **Acceptance Criteria:** Los valores perceptivos deben ajustarse en el Lienzo y en la Vista Previa manteniendo el valor HEX original accesible.

#### REQ-FUNC-03: Evaluación de Contraste WCAG
- **Statement:** El sistema debe proporcionar el resultado de la evaluación de contraste, con Ratio de Contraste, Nivel A superado y cumplimiento con WCAG.
- **Acceptance Criteria:** La interfaz debe actualizar el componente de vista previa con los datos resultantes correctos, además de la Presentación Típica.

#### REQ-FUNC-04: Bloqueo de Colores
- **Statement:** El sistema debe proporcionar un medio para bloquear cada color deseado para la regeneración de la paleta.
- **Acceptance Criteria:** Colores bloqueados se guardan en `localStorage` para tenerlos en cuenta en la regeneración de la paleta.

#### REQ-FUNC-05: Portapapeles de Colores
- **Statement:** El sistema debe proporcionar un medio para poder copiar cada color deseado.
- **Acceptance Criteria:** Color seleccionado es copiado al portapapeles, su código Hexadecimal.

#### REQ-FUNC-06: Matriz de Ratios de Contraste
- **Statement:** El sistema debe calcular y renderizar una matriz cruzada que muestre el ratio de contraste entre todos los pares de colores posibles presentes en la paleta activa.
- **Acceptance Criteria:** La tabla debe actualizarse automáticamente tras cualquier cambio en la paleta, resaltando mediante código de color/badges las combinaciones que cumplen con el nivel AA (≥ 4.5:1) y AAA (≥ 7:1).

### 3.3 Quality of Service (Non-Functional)
#### 3.3.1 Performance
- **REQ-PERF-01:** El cálculo de luminancia relativa y ratio de contraste para toda la paleta no debe exceder los 16ms por frame (60 FPS).

#### 3.3.2 Accessibility & Compliance
- **REQ-COMP-01:** La interfaz propia de Paletaaa debe cumplir de forma nativa con el nivel WCAG 2.1 AA (contraste mínimo 4.5:1 para elementos de control y texto).

### 3.4 Design & Implementation Constraints
- **REQ-IMPL-01:** Cero dependencias externas (`npm packages`, bundlers o CDNs) en el bundle final de producción.

---

## 4. Verification Matrix

| Requirement ID | Verification Method | Status  | Evidence / Test Case |
|----------------|---------------------|---------|----------------------|
| REQ-FUNC-01    | Demonstration       | Pending | Disparo con botón 'Generar', 'Regenerar' o barra espaciadora |
| REQ-FUNC-02    | Test                | Pending | Test visual y matemático con filtros SVG/matrices de daltonismo |
| REQ-FUNC-03    | Analysis            | Pending | Comparativa de salida del ContrastEngine vs. calculadora oficial W3C |
| REQ-FUNC-04    | Test                | Pending | Persistencia del estado 'locked' en memoria y localStorage |
| REQ-FUNC-05    | Inspection          | Pending | Verificación de escritura en portapapeles mediante Clipboard API |
| REQ-FUNC-06    | Test                | Pending | Renderizado correcto de $N \times N$ combinaciones en la tabla UI |
| REQ-PERF-01    | Analysis            | Pending | Rendimiento con Chrome DevTools (Performance panel) |
| REQ-COMP-01    | Analysis            | Pending | Auditoría con Axe / Lighthouse Accessibility |
| REQ-IMPL-01    | Inspection          | Pending | Inspección de `package.json` y árbol de archivos en build |