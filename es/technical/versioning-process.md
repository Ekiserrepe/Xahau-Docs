---
description: >-
  Este documento describe nuestro flujo de trabajo de versionado en GitHub, para optimizar el desarrollo, pruebas y despliegue de nuevas funcionalidades y correcciones.
---

# Proceso de Versionado

Este documento describe el proceso de versionado que utilizamos en nuestro repositorio de GitHub. Usamos un flujo de trabajo basado en tres ramas: `dev`, `candidate` y `release`. Este proceso asegura que todas las nuevas funcionalidades y correcciones se prueben exhaustivamente antes de su lanzamiento.

### Rama Dev

La rama dev es la rama principal para el desarrollo en curso. Todas las nuevas características, correcciones de errores y mejoras se fusionan en esta rama. Es la rama más activa, y es donde los desarrolladores deben basar su trabajo.

Para contribuir a la rama dev, sigue estos pasos:

1. Haz un fork del repositorio en tu propia cuenta de GitHub.
2. Clona el repositorio forkeado en tu máquina local.
3. Crea una nueva rama para tu funcionalidad o corrección de errores.
4. Realiza tus cambios y haz commit en tu rama.
5. Haz push de tu rama a tu repositorio forkeado en GitHub.
6. Crea un pull request desde tu rama hacia la rama dev en el repositorio principal.

{% hint style="warning" %}
Todas los merges en la rama dev deben ser hechas con la opción "squash".
{% endhint %}

Los mantenedores del código revisarán tu pull request y proporcionarán retroalimentación. Una vez que el código sea aprobado, se fusionará en la rama dev.

### Ramas Candidate

Una vez que las características en la rama `dev` estén listas para ser probadas, se fusionan en una rama `candidate`. Esta rama sirve como área de preparación para el código que está casi listo para su lanzamiento.

El código en la rama `candidate` se prueba exhaustivamente. Cualquier error o problema encontrado se corrige en la rama `dev` y luego se vuelve a fusionar en la rama `candidate`.

La rama `candidate` normalmente está en esta fase de pruebas durante aproximadamente 2 semanas. Sin embargo, este período puede ser más largo o más corto, dependiendo de la urgencia de las correcciones o del tamaño de las nuevas características.

1. Crea una nueva rama `candidate` desde `dev`.
2. Crea un pull request desde la rama candidate hacia la rama release con un nombre como "Proposed".
3. Actualiza la "Descripción General de Cambios" para incluir los Pull Requests de `candidate`. Actualiza el "Contexto de los Cambios" para incluir cualquier nota adicional sobre los PR.

### Rama Release

Después de que el código en la rama `candidate` haya sido probado exhaustivamente y todos los problemas hayan sido resueltos, se fusiona en la rama `release`. Este es el paso final antes de que el código se publique.

La rama `release` contiene el código que está actualmente en producción o que está a punto de ser lanzado. Solo el código completamente probado y estable debería estar en esta rama.

Una vez que el código está en la rama `release`, se etiqueta con un número de versión. Este número de versión se utiliza para rastrear el lanzamiento y también se utiliza al crear notas de la versión.

En la rama `release`, se construye un binario que se publica en https://build.xahau.tech/. Este binario es el producto final que se entrega a los usuarios finales.

Para lanzar el código, sigue estos pasos:

1. Fusiona el pull request de `candidate` en la rama release.
2. Elimina la rama `candidate`.

{% hint style="warning" %}
Todos los merges en la rama release deben realizarse con \`git merge --ff-only candidate\`.
{% endhint %}

### Resumen

Este flujo de trabajo de tres ramas asegura que todo el código se pruebe exhaustivamente antes de su lanzamiento. Nos permite detectar y corregir problemas antes de que lleguen a producción, y proporciona un camino claro para mover el código desde el desarrollo hasta el lanzamiento. El uso de una LAN personalizada para pruebas y métricas de perfil asegura que nuestro código no solo sea funcional, sino también eficiente y con un buen rendimiento.

