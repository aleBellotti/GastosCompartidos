# gastosCompartidos

App estática de un solo archivo ([index.html](index.html)) para anotar gastos compartidos entre varias personas (pareja, amigos, grupo).

## Arquitectura

- Sin build, sin backend propio: un `index.html` con HTML/CSS/JS inline.
- Persistencia:
  - Por defecto, `localStorage` (clave `gastos-app-v1`), un solo dispositivo.
  - Si hay config de Firebase pegada (ver abajo), usa **Firestore** como almacenamiento compartido: todos los dispositivos que entren con el mismo "código de grupo" ven y editan los mismos datos. Colección: `grupos/{grupoId}/items`.
  - Código de grupo: string random, se guarda en `localStorage` (`gastos-app-group`) y se comparte manualmente (botón "Compartir / unirse a grupo"). No hay login ni autenticación — cualquiera con el código tiene acceso total (lectura y escritura).

## Firebase

- Proyecto: `gastoscompartidos-b04ef`.
- Config (`apiKey`, `authDomain`, `projectId`) está hardcodeada en `index.html` (línea ~291) y también en `.env` (no se usa en runtime — el HTML no hace build step; `.env` es solo referencia/backup, está en `.gitignore`).
- Estos valores no son secretos sensibles (Firebase los expone del lado cliente igual), pero la seguridad real está en las **reglas de Firestore**, que deben restringir lectura/escritura a la colección `grupos/{grupoId}/items`.
- Reglas actuales (producción, sin auth): `allow read, write: if true;` en esa ruta — protegido solo por lo difícil de adivinar el código de grupo, no por autenticación real.

## Hosting

- Pensado para GitHub Pages (archivo estático, sin build). No requiere Firebase Hosting.

## Convenciones

- Español para todo el texto de usuario (UI, mensajes de error).
- Categorías de gasto están hardcodeadas en el array `CATS` dentro de `index.html`.
