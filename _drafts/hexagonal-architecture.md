---
title: Hexagonal Architecture
excerpt: 'TODO TODO TODO TODO TODO'
categories:
  - blog
tags:
  - architecture
header:
  image: '/images/header.jpg'
---

# Intro

# Layered architecture

# Disadvantages or Layered architecture

- la capa de persistencia tiene más importancia (`data driven`). Esto se refleja en que el desarrollo siempre empieza por dicha capa y luego se pasa a las siguientes. Con los ORM y frameworks de la capa de acceso a datos, todavía toma mas predominancia y se genera un fuerte acoplamiento.
- al desarrollar un sistema, generamos un modelo que representa el negocio y que expone funcionalidades a los usuarios. Perdemos esta capacidad dado que estamos dando más importancia al `estado` (como se ha mencionado en el punto anterior, por enfatizar la capa de persistencia), que al `comportamiento` (es decir, al dominio)
- es facil tomar `shortcuts`, y empezar a mixear código de diferentes capas (por ejemplo, hacer que la capa web referencie directamente a la capa de persistencia). Se requiere disciplina en el equipo para evitar las `Broken Windows`.
- es dificil paralelizar (debido a que el desarrollo empieza desde la capa de persistencia y, hasta no tenerlo hecho, no se puede avanzar hacia las capas mas externas)
- es dificil identificar los `use case` en el código, dado que tenemos `fat services` (ej: `UserService`, por si a alguno le suena), es decir servicios que involucran una rebanada de varios casos de uso. Sumado a eso, gran parte del código se encuentra en la persistencia, y eso si no empezamos a mixear capas.

En aplicaciones `CRUD` puede ser una buena opción. No obstante, en aplicaciones que tengan muchas reglas de negocio y que tengan proyección de crecimiento, una arquitectura de capas tradicional no es la mejor opción para generar código mantenible y fácilmente adaptable a cambios.