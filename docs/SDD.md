# Software Design Description
## For Paletaaa (Accessible Palette Generator)

Version 0.2  
Prepared by: Dónovan Martín  
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
| Dónovan Martín | 06/07/2026 | Redacción inicial de la especificación | 0.1 |
| Dónovan Martín | 10/07/2026 | Redacción de 2. Design Overview | 0.2 |
| Dónovan Martín | 11/07/2026 | Redacción de 2.2.10. 2.2.13 y 4. | 0.3 |
---

## 1. Introduction

### 1.1 Document Purpose
Este documento define la arquitectura, estructura de componentes y decisiones de diseño del software *Paletaaa*. Está destinado a guiar la implementación del código fuente. 
Su objetivo es detallar *cómo* se estructurará e implementará internamente el código fuente para dar cumplimiento a los requisitos funcionales y de accesibilidad definidos en la [especificación de requisitos](SRS.md).

### 1.2 Subject Scope
El ámbito del sistema es una aplicación web *Client-Side* pura. No depende de bases de datos externas ni servicios de servidor (Backend). Garantiza un rendimiento óptimo y una portabilidad total a plataformas de despliegue estático como GitHub Pages.

### 1.3 Definitions, Acronyms, and Abbreviations

| Term | Definition |
|------|------------|
| WCAG | Web Content Accessibility Guidelines (Pautas de Accesibilidad al Contenido Web). |
| SPA  | Single Page Application (Aplicación de una sola página). |
| DOM  | Document Object Model. |

### 1.4 References
* [docs/REFERENCIAS.md](https://github.com/ddnovan/paletaaa/blob/f3e9aa0ea0fd7d2bb74370ec0922f039878a9093/docs/REFERENCIAS.md)
* W3C Web Content Accessibility Guidelines 2.1.
* Algoritmo de Simulación de Daltonismo basado en el espacio de color LMS (Brettel, Viénot & Murch).
* jam01/SDD-Template Plantilla para la especificación del software.
---

## 2. Design Overview

### 2.1 Stakeholder Concerns
<!-- Los interesados del proyecto y sus "requisitos" -->
* **Desarrollador:** Requiere de código modulable para facilitar pruebas de forma unitaria (especialmente las funciones matemáticas de contraste). Las pruebas unitarias vienen descritas desde la [especificación de requisitos](SRS.md) funcionales del software **Paletaaa**.
* **Usuario Final (Artista/Diseñador):** Requiere una interfaz fluida e instantánea (renderizado inmediato en el DOM al mover un slider de color).

### 2.2 Selected Viewpoints (Adaptado a Arquitectura Cliente)
<!-- Visión del software Paletaaa desde diferentes ángulos -->
#### 2.2.1 Context
<!-- Si Paletaaa es una caja negra, ¿qué hay fuera que interactúa con ella? -->
El sistema interactúa exclusivamente con el usuario a través del teclado, ratón o lector de pantallas.  
El entorno de ejecución es cualquier navegador moderno con soporte para ES6+ y la API de manipulación del DOM.

#### 2.2.2 Composition
<!-- Si Paletaaa es una caja negra, ¿en qué módulos se divide su interior? -->
El software se divide en tres subsistemas lógicos bien diferenciados:
1. **Lógica:** Clases encargadas de transformar formatos de color (HEX a RGB, RGB a LMS) y calcular ratios de contraste numéricos. No toca el HTML.
2. **Estado:** Almacena la paleta activa y notifica los cambios a la interfaz.
3. **Componentes UI:** Elementos del DOM accesibles controlados por teclado y modificados dinámicamente mediante JS Vanilla.

#### 2.2.3 Logical
<!-- Elementos concretos, clases, funciones, objetos a programar -->
Se implementará una arquitectura basada en módulos de JavaScript nativos (ES Modules). Las abstracciones o estructuras de datos son:
* `Color`: Clase modelo que contiene las propiedades del color en HEX/RGB y su luminancia.
* `Palette`: Colección de objetos `Color`.
* `ContrastValidator`: Módulo de utilidad que expone métodos como `getContrastRatio(color1, color2)`.
* `AccessibilityController`: Controlador encargado de inyectar los atributos `aria-live` y gestionar el foco (`focus-visible`).

#### 2.2.4 Physical
<!-- Plano físico, hardware, servidores, nubes y redes -->
*N/A: El sistema se ejecuta íntegramente en la CPU/GPU local del cliente (navegador).*

#### 2.2.7 Information
<!-- Cómo viajan y se guardan los datos -->
Los datos se manejan en la memoria RAM del navegador mientras **Paletaaa** está abierta, concretamente mediante los objetos de las estructuras de datos descritas anteriormente en JS. Uso de API `localStorage` para guardar paletas de usuario de forma local y asíncrona en formato JSON.
 
#### 2.2.10 Algorithm
<!-- Lógica y formulas matemáticas -->
Partimos de la definición de [*relative luminance*](https://www.w3.org/TR/WCAG21/relative-luminance.html) que es el brillo relativo de cualquier punto en un espacio de color (por ej. el espacio de color sRGB está comprimido o no es lineal, para adaptarse a cómo las pantallas muestran la luz) con valor 0 para el color más oscuro y 1 para el blanco más claro. 

**Linealización del color (o *Gamma Correction*)**
W3C exige descomprimir (linealizar) los valores de los canales Rojo, Verde y Azul, individualmente entre 255, para pasarlo a una escala de 0 a 1 y aplicarle una función condicional:

Si $C \le 0.03928$, entonces $c = \frac{C}{12.92}$, de lo contrario $c = \left(\frac{C + 0.055}{1.055}\right)^{2.4}$

**Cálculo de la Luminancia Relativa (*L*)**
Con los canales linealizados aplicamos la fórmula de la luminancia, cuyas constantes varían en base al ojo humano, cuyos fotorreceptores son más sensibles a la luz verde que a la azul, por ello el 7% para azul mientras un 71% para el verde.

$$L = 0.2126r + 0.7152g + 0.0722b$$

**Ratio de Contraste (*$Cr$*)**

Tras asumir las [consideraciones siguientes](#otras-consideraciones-a-tener-en-cuenta), la definición del *contrast ratio* nos establece que L1 y L2 son las *relative luminance* de los colores más claro (mayor número) y oscuro (menor número), respectivamente:

$$Cr = \frac{L_1 + 0.05}{L_2 + 0.05} \geq 1$$

L1 es el máximo entre LTexto y LFondo, L2 el mínimo.  

##### Otras consideraciones a tener en cuenta
La elección del espacio sRGB es indispensable para simplificar (al no haber escogido previamente otro espacio de color) [*relative luminance*: Nota 3]

Al no tener control sobre la configuración de usuario, el Desarrollador como autor debe conocer que el *contrast ratio* para texto puede ser evaluado con *anti-aliasing* apagado. [*contrast ratio*: Nota 2]  

Conocer que el blanco es el color asumido para fondos dónde no se especifique [*contrast ratio*: Nota 3]  

En ciertos criterios es un fallo especificar color de texto pero no color de fondo y viceversa, pues se desconoce el color de fondo o de texto del propio usuario personalizado. [*contrast color: Nota 4]  

Se tienen en cuenta las letras con bordes para el cálculo de contraste entre letra y fondo (Por ej. un borde fino se usa como letra y un borde grueso actúa como un halo y se considera fondo) [*contrast ratio*: Nota 5]  

Trabajar con pares de colores, un color de primer plano y un color de fondo.
[*contrast ratio*: Nota 6]  

#### 2.2.13 Patterns
<!-- Patrones de diseño de software para resolver problemas comunes -->
Por el momento, se aplicará el **Patrón Observador (Publisher-Subscriber)** para el manejo del estado: cuando la paleta cambia en *Estado*, los componentes de la interfaz (la vista del lienzo, el panel de daltonismo y el output del lector de pantallas) se actualizan automáticamente sin acoplarse entre sí.

---

## 3. Design Views

### 3.1 User Interface (UI) View
Describe la vista del sistema y cómo los módulos lógicos se traducen a elementos del DOM accesibles para el usuario.
La interfaz se compone de los siguientes bloques:

#### 3.1.1 Panel de control
Contiene los selectores de color (fondo y primer plano) operables por teclado e inputs de texto para valores HEX y RGB.

#### 3.1.2 Componente de Vista Previa
Desde [*contrast ratio*: Nota 6] el concepto de *Typical Presentation* para pares de colores supone no dar simplemente el resultado del *contrast ratio* (ej. 4.5:1), sino que podemos ir más allá: 
Maquetar un lienzo interactivo que renderiza en tiempo real un ejemplo de texto real y color de fondo elegido. Así garantiza al artista/diseñador verificar la legibilidad y contraste visualmente

#### 3.1.3 Panel de simulación (Daltonismo)
Establecer un área donde se aplican filtros SVG basados en las matrices LMS para alterar temporalmente la vista previa y simular Protanopía, Deuteranopía y Tritanopía.

---

## 4. Decisions
<!-- Architecture Decision Records, registros de "por qué elegir esto y no lo otro", cada vez que tenga que elegir entre dos tecnologías o caminos, crear un ADR -->

### ADR-001: Elección del Stack Tecnológico (Vanilla vs Frameworks)
* **Decisión:** Usar HTML5 Semántico, CSS3 Nativo (con Variables de CSS) y JavaScript Vanilla (ES6+).
* **Justificación:** Los frameworks añaden capas de abstracción sobre el DOM que a veces dificultan el control total de los eventos de foco nativos y atributos ARIA dinámicos. Utilizar código nativo demuestra maestría en los fundamentos web de ingeniería, reduce el peso de la app a pocos kilobytes y facilita la accesibilidad nativa y estricta sin dependencias de terceros.

