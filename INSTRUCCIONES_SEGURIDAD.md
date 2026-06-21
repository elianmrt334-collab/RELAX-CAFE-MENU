# Instrucciones de Seguridad para la API Key de Google

Google te enviará alertas de seguridad si tu clave de API (API Key) está expuesta en un repositorio público de GitHub sin restricciones. Sigue estos pasos para asegurar tu proyecto y eliminar las alertas.

---

## 1. Ocultar la API Key en tu Repositorio de Git
Ya hemos configurado tu proyecto para que no suba la clave real a GitHub:
1. **`.gitignore`**: Ya configuramos este archivo para ignorar `firebase-config.js`. Esto evita que Git rastree y suba tu configuración real.
2. **`firebase-config.example.js`**: Este es el archivo de plantilla que sí se sube a GitHub. Contiene `"TU_API_KEY_DE_GOOGLE"` en lugar de la clave real.

### ¿Cómo clonar y correr el proyecto en otro lugar?
Cuando alguien clone tu repositorio de GitHub:
1. Deberá renombrar/copiar `firebase-config.example.js` a `firebase-config.js`.
2. Llenar el campo `apiKey` con su clave real de Firebase.

---

## 2. Restringir la API Key en Google Cloud Console (¡Muy Importante!)
Las API Keys de Firebase son públicas por diseño (se cargan en el navegador), por lo que **la forma correcta de protegerlas es restringiéndolas en el panel de Google Cloud**, no solo ocultándolas.

Sigue estos pasos para restringir tu clave actual:

1. Ve a la consola de **[Google Cloud Console Credentials](https://console.cloud.google.com/apis/credentials)**.
2. Asegúrate de seleccionar tu proyecto `relax-cafe-ba41c` en la barra superior.
3. Busca tu API Key en la sección **Claves de API** (normalmente se llama *Browser key*, *Auto-created...* o similar, y coincide con la que empieza con `AIzaSyBZy...`).
4. Haz clic en el nombre de la clave para editarla.
5. Configura las siguientes restricciones:

### A. Restricciones de aplicaciones (Dónde se puede usar)
* Selecciona **Direcciones de referencia de HTTP (sitios web)**.
* Bajo **Restricciones del sitio web**, añade las URL permitidas donde correrá tu menú:
  - `http://localhost:*` (Para tus pruebas de desarrollo local)
  - `http://127.0.0.1:*` (Para pruebas locales alternativas)
  - `https://relax-cafe-ba41c.web.app/*` (Tu dominio de Firebase Hosting)
  - `https://relax-cafe-ba41c.firebaseapp.com/*` (Tu dominio secundario de Firebase Hosting)

### B. Restricciones de API (Qué servicios puede usar)
* Selecciona **Restringir clave**.
* En el menú desplegable, selecciona únicamente los servicios de Firebase que usa tu aplicación:
  - **Identity Toolkit API** (Necesaria para Auth Anónimo)
  - **Cloud Firestore API** (Necesaria para guardar/leer los platos del menú)
  - **Firebase Installations API**
* Haz clic en **Guardar**.

---

## 3. Configurar las Reglas de Firestore
Hemos creado el archivo `firestore.rules` en la raíz de tu proyecto. Asegura que:
* Cualquiera pueda ver el menú (lectura pública).
* Solo los usuarios que iniciaron sesión (como el administrador mediante autenticación anónima) puedan agregar, editar o borrar platillos.

Para aplicar estas reglas desde tu terminal local usando Firebase CLI, ejecuta:
```bash
firebase deploy --only firestore:rules
```
O simplemente haz un despliegue completo de tu sitio web:
```bash
firebase deploy
```
