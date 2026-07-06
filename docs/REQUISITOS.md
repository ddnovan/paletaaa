# Software Design Description
## For Paletaaa (Accessible Palette Generator)

Version 0.1  
Prepared by: [Dónovan Martín]  
Organization: Estudiante de Ingeniería Informática / Portafolio Personal  
Date: Julio 2026

## Table of Contents
* [1. Introduction](#1-introduction)
* [2. Design Overview](#2-design-overview)
* [3. Design Views](#3-design-views)
* [4. Decisions](#4-decisions)

## Revision History

| Name | Date | Reason For Changes | Version |
|------|------|--------------------|---------|
| [Dónovan Martín] | 06/07/2026 | Redacción inicial de la especificación | 0.1 |

---

## 1. Introduction

### 1.1 Document Purpose
Este documento define los requisitos del software *Paletaaa*, a partir de historias de usuario. Está destinado a guiar la implementación del código fuente.

### 1.2 Subject Scope
El ámbito del sistema es una aplicación web *Client-Side* pura. No depende de bases de datos externas ni servicios de servidor (Backend). Garantiza un rendimiento óptimo y una portabilidad total a plataformas de despliegue estático como GitHub Pages.

### 1.3 Definitions, Acronyms, and Abbreviations

| Term | Definition |
|------|------------|
| WCAG | Web Content Accessibility Guidelines (Pautas de Accesibilidad al Contenido Web). |
| SPA  | Single Page Application (Aplicación de una sola página). |
| DOM  | Document Object Model. |

### 1.4 References
* W3C Web Content Accessibility Guidelines 2.1.
* Algoritmo de Simulación de Daltonismo basado en el espacio de color LMS (Brettel, Viénot & Murch).
* jam01/SDD-Template Plantilla para la especificación del software.
---

## 2. Design Overview

### 2.1 Stakeholder Concerns
* **Desarrollador:** Requiere un código modulable y fácil de testear unitariamente (especialmente las funciones matemáticas de contraste).
* **Usuario Final (Artista/Diseñador):** Requiere una interfaz fluida e instantánea (renderizado inmediato en el DOM al mover un slider de color).

### 2.2 Selected Viewpoints (Adaptado a Arquitectura Cliente)

#### 2.2.1 Context
El sistema interactúa exclusivamente con el usuario a través del teclado, ratón o lector de pantallas. El entorno de ejecución es cualquier navegador moderno con soporte para ES6+ y la API de manipulación del DOM.

#### 2.2.2 Composition
El software se divide en tres subsistemas lógicos bien diferenciados:
1. **Lógica:** Clases encargadas de transformar formatos de color (HEX a RGB, RGB a LMS) y calcular ratios de contraste numéricos. No toca el HTML.
2. **Estado:** Almacena la paleta activa y notifica los cambios a la interfaz de forma centralizada.
3. **Componentes UI:** Elementos del DOM accesibles controlados por teclado y modificados dinámicamente mediante JS Vanilla.

#### 2.2.3 Logical
Se implementará una arquitectura basada en módulos de JavaScript nativos (ES Modules). Las abstracciones clave son:
* `Color`: Clase modelo que contiene las propiedades del color en HEX/RGB y su luminancia.
* `Palette`: Colección de objetos `Color`.
* `ContrastValidator`: Módulo de utilidad estática que expone métodos como `getContrastRatio(color1, color2)`.
* `AccessibilityController`: Controlador encargado de inyectar los atributos `aria-live` y gestionar el foco (`focus-visible`).

#### 2.2.4 Physical
*N/A: El sistema se ejecuta íntegramente en la CPU/GPU local del cliente.*

#### 2.2.7 Information
Los datos se manejan eficientemente en memoria volátil mediante objetos estructurados en JS. Para el requisito de persistencia del MVP, se utilizará la API `localStorage` del navegador para salvar las paletas del usuario de forma local y asíncrona.

#### 2.2.10 Algorithm
La computación crítica del sistema radica en el cálculo del ratio de contraste estándar de la WCAG 2.1:

1. **Linealización sRGB (para cada canal $R, G, B$ escalado de 0 a 1):**
   Si $C \le 0.03928$, entonces $c = \frac{C}{12.92}$, de lo contrario $c = \left(\frac{C + 0.055}{1.055}\right)^{2.4}$
2. **Luminancia Relativa ($Y$):**
   $$Y = 0.2126 \times r + 0.7152 \times g + 0.0722 \times b$$
3. **Ratio de Contraste ($Cr$):**
   $$Cr = \frac{Y_{claro} + 0.05}{Y_{oscuro} + 0.05}$$

#### 2.2.13 Patterns
Se aplicará el **Patrón Observador (Publisher-Subscriber)** para el manejo del estado: cuando la paleta cambia en el *State Manager*, los componentes de la interfaz (la vista del lienzo, el panel de daltonismo y el output del lector de pantallas) se actualizan automáticamente sin acoplarse entre sí.

---

## 4. Decisions

### ADR-001: Elección del Stack Tecnológico (Vanilla JS vs Frameworks)
* **Decisión:** Usar HTML5 Semántico, CSS3 Nativo (con Variables de CSS) y JavaScript Vanilla (ES6+).
* **Justificación:** Los frameworks añaden capas de abstracción sobre el DOM que a veces dificultan el control total de los eventos de foco nativos y atributos ARIA dinámicos. Utilizar código nativo demuestra maestría en los fundamentos web de ingeniería, reduce el peso de la app a pocos kilobytes y facilita la accesibilidad nativa y estricta sin dependencias de terceros.
