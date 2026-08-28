# Tarjeta de lealtad digital — Püri Café

Sistema propio (sin apps de terceros), igual que el sitio de Riad Tulum-Cobá: un archivo HTML que subes a GitHub y despliegas en Netlify. Usa Firebase Firestore (gratis) como base de datos compartida, para que el staff y el cliente vean la misma tarjeta desde cualquier celular.

## ¿Por qué no abrió cuando pusiste tu celular?

Porque falta completar el Paso 1 de abajo (crear el proyecto de Firebase y pegar su configuración). Sin eso, los botones de "Buscar cliente" y "Ver mi tarjeta" no tienen a dónde conectarse — ahora el sitio te avisa con un mensaje si falta ese paso, en vez de quedarse en blanco.

## Archivos que debes subir a GitHub

Sube **toda la carpeta**, no solo `index.html`:
- `index.html`
- `LEEME.md` (opcional, es solo referencia tuya)
- carpeta `assets/` completa (el logo, la carita de la marca y las fotos del café — el sitio no se va a ver bien sin estos)

## Paso 1 — Crear el proyecto en Firebase (5 min, gratis)

1. Ve a https://console.firebase.google.com y entra con una cuenta de Google.
2. "Agregar proyecto" → nómbralo `puri-cafe` → sigue los pasos (puedes desactivar Google Analytics).
3. En el menú lateral, entra a **Compilación > Firestore Database** → "Crear base de datos" → modo de producción → elige la región más cercana (us-central o similar).
4. Ve a **Reglas** dentro de Firestore y pega esto para que el sitio pueda leer y escribir (luego podemos hacerlo más seguro con contraseña de staff si quieres):

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /clientes/{clienteId} {
      allow read, write: if true;
    }
  }
}
```

5. En el ícono de engrane (arriba izquierda) → **Configuración del proyecto** → baja hasta "Tus apps" → click en el ícono `</>` (Web) → regístrala con cualquier nombre → te va a mostrar un bloque de código con `firebaseConfig`.
6. Copia esos 6 valores (`apiKey`, `authDomain`, `projectId`, etc.)

## Paso 2 — Pegar la configuración en el archivo

Abre `index.html`, busca esta sección cerca del final:

```js
const firebaseConfig = {
  apiKey: "TU_API_KEY",
  authDomain: "TU_PROYECTO.firebaseapp.com",
  ...
};
```

Reemplaza cada valor con el que copiaste de Firebase. Guarda el archivo.

## Paso 3 — Subir a GitHub y desplegar en Netlify

Igual que hicimos con riadcoba:

1. Crea un repositorio nuevo en GitHub (ej. `puri-cafe-lealtad`) y sube `index.html`.
2. Entra a Netlify → "Add new site" → "Import an existing project" → conecta el repo de GitHub.
3. Deja la configuración por default (no hay build command, es HTML puro) → Deploy.
4. Netlify te da un link tipo `puri-cafe-lealtad.netlify.app` — ese es el que usa tu staff y el que le puedes compartir al cliente.

## Cómo se usa

- **Staff**: pestaña "Staff" → busca al cliente por celular → si es nuevo, le crea la tarjeta → botón "+1 sello" en cada visita.
- **Recompensas**: al llegar a 5 sellos se desbloquea "30% de descuento" (botón "Usar" lo marca como canjeado sin reiniciar los sellos). Al llegar a 10 sellos se desbloquea "Iced latte gratis" (excepto affogato latte) — el botón "Usar y reiniciar" reinicia la tarjeta a 0 para el siguiente ciclo.
- **Cliente**: pestaña "Mi tarjeta" → el cliente pone su celular y ve su progreso (solo lectura, no puede sellarse a sí mismo).

## Notificación de WhatsApp al staff (cliente nuevo)

Cuando un cliente se registra solo desde el link público, le llega un WhatsApp automático al número del staff avisando. Esto usa un servicio gratuito llamado **CallMeBot** — necesita una configuración de un solo paso:

1. Desde el celular del staff (984 182 5512), agrega este número de contacto: **+34 621 44 76 91**
2. Mándale por WhatsApp exactamente este mensaje: `I allow callmebot to send me messages`
3. En unos segundos te responde con un mensaje que incluye tu **API Key** (un número).
4. Copia ese número.
5. Abre `index.html`, busca la línea `const CALLMEBOT_APIKEY = "TU_APIKEY_CALLMEBOT";` y reemplázala por tu key, por ejemplo: `const CALLMEBOT_APIKEY = "1234567";`
6. Guarda y vuelve a subir el archivo a GitHub.

Listo — cada vez que alguien se registre solo desde el QR, le llega un WhatsApp automático al 984 182 5512 avisando su nombre y teléfono.
