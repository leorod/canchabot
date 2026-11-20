# TPO Desarrollo de Aplicaciones I
## CanchaBot
> *"Canchas limpias, pelotas trazadas"*

**CanchaBot** es una aplicación móvil Android diseñada como interfaz de operación y supervisión (HMI/HRI) entre el personal o dueño de un club de tenis y pádel y un robot cuadrúpedo Unitree Go2. Ayudamos a los clubes a mantener sus canchas libres de pelotas perdidas sin dedicar horas de personal, registrando cada lote recolectado para devolverlo ante reclamos o destinarlo a reventa.

## Documentos

### Documento de Diseño y Análisis (Pre-entrega - Etapa 1)
El análisis completo, los requisitos detallados, el alcance, las justificaciones técnicas de la plataforma y las decisiones arquitectónicas del proyecto se irán documentando en el siguiente link:

**[(Google Docs) CanchaBot Pre-entrega](https://docs.google.com/document/d/1ctl0izc42J8nWT_1qMWcIu3bVYADMuzj/)**

### Diagramas y documentos auxiliares

En [docs/](/docs/) se encuentra el código Mermaid de los diagramas, los contratos JSON de telemetría/comandos y un ADR con las decisiones que se van tomando sobre la arquitectura. El ADR es un documento interno para usar como referencia a futuro a medida que avanzamos con el proyecto; el único documento *formal* es el Documento de Diseño.

Todos los documentos en esta carpeta son documentos vivos que se irán manteniendo conforme avance el proyecto.

---

## Propuesta de Valor y Problema

### El Problema
En un club de tenis y pádel las pelotitas se pierden constantemente detrás de los fondos, contra el cerco, en los rincones de las paredes de vidrio y bajo los bancos. Hoy ese trabajo se resuelve a mano al cierre o entre turnos, consumiendo horas de personal y dejando pelotas sueltas sin ningún tipo de registro: cuando un jugador reclama sus pelotas no hay forma de saber si aparecieron ni de qué cancha provienen, y las que nadie reclama no se aprovechan por falta de trazabilidad.

### La Solución
CanchaBot propone un sistema supervisado de recolección y trazabilidad compuesto por tres piezas desacopladas:
1. **Robot Recolector (Unitree Go2):** Recorre una cancha cerrada por vez mediante patrullaje de alto nivel, detecta pelotas con su cámara y las almacena en un compartimento acoplado, operando con límites de velocidad y reglas de seguridad activas.
2. **Gateway de Integración / Simulador:** Capa intermedia en Python (FastAPI + SDK Unitree) que concentra las reglas de seguridad (vigencia de comandos, autoridad única y *watchdog* de actividad) y permite operar contra un simulador embebido en el APK sin requerir hardware físico ni red externa.
3. **App Móvil Android (HMI/HRI):** Permite al operador supervisar la telemetría en tiempo real, ejecutar comandos discretos y control manual mantenido (*dead-man switch*), gestionar contingencias y administrar el inventario de lotes con plazos de reclamo y pase a reventa.

---

## User Journey principal

1. **Preparación:** El operador ubica el robot en el acceso de la cancha, abre **CanchaBot**, selecciona la cancha y completa la lista de chequeo previa confirmando que el área está libre de personas.
2. **Recolección Supervisada:** Al iniciar la misión, el robot barre la superficie mientras la app muestra en tiempo real la etapa, el conteo estimado de pelotas, el estado de conexión y la batería. Ante un obstáculo o alerta, se pausa y solicita decisión del operador.
3. **Cierre y Conteo:** Al finalizar, el robot regresa al punto seguro. El operador vacía el compartimento, confirma la cantidad real por tipo (tenis y pádel) y genera un lote trazable con cancha, fecha y plazo de reclamo (7 días por defecto).
4. **Reclamo y Reventa:** Cuando un jugador consulta en recepción por pelotas perdidas, el operador busca el lote de la cancha en el inventario y registra la entrega descontando las unidades. Una vez vencido el plazo de reclamo, el Dueño marca el remanente como *"Para reventa"*.

---

## Equipo

* **Leonardo Rodriguez** (LU 1048084) - https://github.com/leorod/
  * **Rol Principal:** Arquitectura, Integración con el Robot (Gateway/Simulador), Backend, Modelo de Datos y Desarrollo.
* **Facundo Reyes** (LU 1111386) - https://github.com/FacundoReyes
  * **Rol Principal:** Diseño UX/UI, HMI/HRI del Robot, Flujo de Experiencia de Usuario y Desarrollo Android.

---

## Repositorio y Metodología

* **Repositorio:** [https://github.com/leorod/canchabot](https://github.com/leorod/canchabot)
* **Branching Model:** *Trunk-based Development*. Tratamos el branch `main` como nuestro trunk principal. Trabajamos directamente sobre feature branches (con prefijo `feature/` o `feat/`) lo más atómicos posible, a mergear directamente a `main` a través de PRs.  
  *Nice to have, a definir: CI en Github Actions para garantizar estabilidad.*
* **Estructura de Documentación:** Todos los recursos gráficos, esquemas de recorrido, diagramas de arquitectura, contratos JSON y el PDF final consolidado se encuentran en la carpeta `docs/` de este repositorio.
