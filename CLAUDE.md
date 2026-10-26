# gastosCompartidos

App estática de un solo archivo ([index.html](index.html)): "La cuenta" — divide un gasto grupal (asado, viaje, etc.) entre participantes (persona o pareja) y calcula quién le transfiere a quién.

## Arquitectura

- Sin build, sin backend propio: un `index.html` con HTML/CSS/JS inline.
- Estado: `{ title, units:[{id,name,type}], expenses:[{id,desc,amount,by}], mode }`, guardado completo como un solo objeto.
- Persistencia:
  - Por defecto, `localStorage` (clave `la-cuenta-v1`), un solo dispositivo.
  - Si hay config de Firebase pegada (ver abajo), usa **Firestore** como almacenamiento compartido: todos los dispositivos que entren con el mismo "código de grupo" ven y editan el mismo doc. Colección: `cuentas/{grupoId}` (un doc = el estado completo).
  - Código de grupo: string random, se guarda en `localStorage` (`la-cuenta-group`) y se comparte manualmente (botón "Compartir / unirse"). No hay login ni autenticación — cualquiera con el código tiene acceso total (lectura y escritura).
  - Botón "Compartir la cuenta que ya cargué" migra datos que quedaron solo en local (antes de unirse a un grupo) hacia el doc compartido.

## Firebase

- Proyecto: `gastoscompartidos-b04ef`.
- Config (`apiKey`, `authDomain`, `projectId`) está hardcodeada en `index.html` (línea ~291) y también en `.env` (no se usa en runtime — el HTML no hace build step; `.env` es solo referencia/backup, está en `.gitignore`).
- Estos valores no son secretos sensibles (Firebase los expone del lado cliente igual), pero la seguridad real está en las **reglas de Firestore**, que deben restringir lectura/escritura a la colección `cuentas/{grupoId}`.
- Reglas actuales (producción, sin auth), pegadas en Firebase console → Firestore → Reglas (NO en `.env`, eso no es sintaxis de variables de entorno):
  ```
  rules_version = '2';
  service cloud.firestore {
    match /databases/{database}/documents {
      match /cuentas/{grupoId} { allow read, write: if true; }
    }
  }
  ```
  Protegido solo por lo difícil de adivinar el código de grupo, no por autenticación real.

## Hosting

- Pensado para GitHub Pages (archivo estático, sin build). No requiere Firebase Hosting.

## Convenciones

- Español para todo el texto de usuario (UI, mensajes de error).
- `units` pueden ser `persona` o `pareja`; el modo (`unidad`/`persona`) decide si una pareja cuenta como 1 o 2 partes al dividir.
