# Adeciones App — versión para desplegar en Vercel

Esta es la versión del proyecto lista para subir a GitHub y publicarla en
Vercel, con una base de datos real (Firebase Firestore) en vez del
almacenamiento interno de Claude. Así funciona igual en cualquier
dispositivo, sin depender de Claude.

Seguí estos pasos **en orden**. No hace falta saber programar, es
copiar/pegar.

---

## 1. Crear el proyecto en Firebase (la base de datos)

1. Entrá a **https://console.firebase.google.com** con tu cuenta de Google.
2. Click en **"Agregar proyecto"**, ponele un nombre (ej: `adeciones-app`) y
   seguí los pasos (podés desactivar Google Analytics, no hace falta).
3. Dentro del proyecto, en el menú izquierdo entrá a **Firestore Database**
   → **Crear base de datos**.
   - Elegí **modo de producción**.
   - Elegí la ubicación más cercana (ej: `southamerica-east1`).
4. Andá a **Reglas** (pestaña arriba de Firestore) y reemplazá el contenido
   por esto, para que la app pueda leer y escribir durante la beta:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /{document=**} {
         allow read, write: if true;
       }
     }
   }
   ```

   ⚠️ Esto deja la base de datos abierta a cualquiera que conozca la URL del
   proyecto — está bien para la beta interna del grupo, pero **no la dejes
   así si vas a manejar datos sensibles a largo plazo**. Más adelante se
   puede restringir con autenticación.

5. Ahora andá a la **rueda de configuración (⚙️)** arriba a la izquierda →
   **Configuración del proyecto**. Bajá hasta "Tus apps" y click en el
   ícono `</>` (Web) para registrar una app web. Ponele un nombre y
   confirmá.
6. Firebase te va a mostrar un bloque de código con `firebaseConfig = {...}`.
   Copiá esos valores (apiKey, authDomain, projectId, etc.).

## 2. Pegar la configuración en el proyecto

Abrí el archivo `src/firebase.js` de esta carpeta y reemplazá los valores
de ejemplo por los que copiaste de Firebase:

```js
const firebaseConfig = {
  apiKey: "AIza......",
  authDomain: "tu-proyecto.firebaseapp.com",
  projectId: "tu-proyecto",
  storageBucket: "tu-proyecto.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef",
};
```

Guardá el archivo.

## 3. Subir el proyecto a GitHub

Abrí tu terminal (MINGW64/Git Bash) **dentro de esta carpeta** y ejecutá
los comandos **uno por uno** (no todos pegados juntos):

```
git init
git add .
git commit -m "Primera version de la app de adeciones"
git branch -M main
git remote add origin https://github.com/Alexstrox/adeciones-2.0.git
git push -u origin main
```

Si el repositorio en GitHub ya tenía archivos (por un intento anterior) y
el `push` te da error de que hay conflictos, ejecutá primero:
```
git pull origin main --allow-unrelated-histories
```
y después repetí el `git push -u origin main`.

## 4. Publicar en Vercel

1. Entrá a **https://vercel.com** e iniciá sesión con tu cuenta de GitHub.
2. Click en **"Add New..." → "Project"**.
3. Elegí el repositorio `Alexstrox/adeciones-2.0` y click en **Import**.
4. Vercel detecta solo que es un proyecto **Vite** — no toques la
   configuración, dejá los valores por defecto.
5. Click en **Deploy** y esperá 1-2 minutos.
6. Al terminar te da un link público (algo como
   `adeciones-2-0.vercel.app`) — ese es el que le pasás a todo el grupo.

Cada vez que hagas cambios y los subas a GitHub (`git add .` → `git commit`
→ `git push`), Vercel vuelve a publicar la app sola, automáticamente.

---

## ¿Cómo se guardan los datos ahora?

Antes usaba `window.storage` (una función que solo existe dentro de
Claude). Esta versión usa **Firebase Firestore**: todos los datos
(miembros, ventas, ajustes) viven en un documento en la nube y se
actualizan **en tiempo real** — si dos personas tienen la página abierta a
la vez, ambas ven los cambios al instante, sin recargar.

El nombre del vendedor que la app recuerda en cada dispositivo (para no
tener que escribirlo cada vez) ahora se guarda con `localStorage`, algo
propio de cada navegador/dispositivo.

## Probarlo en tu compu antes de subirlo (opcional)

Si tenés Node.js instalado, podés correr la app en tu máquina antes de
publicarla:

```
npm install
npm run dev
```

Te va a dar un link tipo `http://localhost:5173` para probarla en tu
navegador.

## Estado del proyecto

Versión **beta**. Cuando definan el país y el menú, se puede convertir el
campo de "platillo" (hoy texto libre) en una lista fija, agregar precios,
restringir mejor las reglas de Firestore, etc.
