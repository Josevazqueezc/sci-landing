# Racha: hábitos con amigos

App web de hábitos (diarios, semanales y mensuales) con XP, rachas, retos y grupos.
Se entra con Google. Cada persona ve solo sus hábitos; el grupo ve un resumen
(nombre, foto, nivel, racha, hábitos de la semana) y los objetivos comunes.

Sin configurar Firebase, la app funciona en **modo local** (sin login ni grupos).

## Puesta en marcha (unos 15 minutos, se puede hacer desde el móvil)

### 1. Crear el proyecto de Firebase
1. Entra en <https://console.firebase.google.com> con tu cuenta de Google.
2. **Crear un proyecto** → nombre `racha` → desactiva Google Analytics (no hace falta) → Crear.

### 2. Activar el login con Google
1. Menú **Compilación → Authentication → Comenzar**.
2. Pestaña **Método de acceso** → **Google** → Habilitar → elige tu email de asistencia → Guardar.
3. Pestaña **Configuración → Dominios autorizados** → **Agregar dominio** →
   `josevazqueezc.github.io`

### 3. Crear la base de datos
1. **Compilación → Firestore Database → Crear base de datos**.
2. Ubicación: `southamerica-east1 (São Paulo)` (la más cercana a Argentina). No se puede cambiar después.
3. Elige **modo de producción**.
4. Pestaña **Reglas** → borra lo que hay → pega el contenido de [`firestore.rules`](firestore.rules) → **Publicar**.

### 4. Conectar la app
1. Rueda dentada → **Configuración del proyecto** → sección "Tus apps" → icono **`</>`** (Web).
2. Nombre: `Racha`. No marques Firebase Hosting. Registrar.
3. Copia el bloque `const firebaseConfig = { ... }`.
4. Pégalo en [`firebase-config.js`](firebase-config.js) reemplazando `null`, para que quede:
   ```js
   export const firebaseConfig = {
     apiKey: "...",
     authDomain: "racha-xxxx.firebaseapp.com",
     projectId: "racha-xxxx",
     storageBucket: "...",
     messagingSenderId: "...",
     appId: "..."
   };
   ```
   (O pásale el bloque a Claude y lo sube por ti. Estos valores no son secretos.)

### 5. Publicar la app (GitHub Pages)
1. En GitHub: repositorio `sci-landing` → **Settings → Pages**.
2. Source: **Deploy from a branch** → rama `claude/habit-improvement-app-skkpnm` (o `main` si la fusionas) → carpeta `/ (root)` → Save.
3. En uno o dos minutos la app queda en:
   **https://josevazqueezc.github.io/sci-landing/habitos/**

### 6. Invitar a tus amigos
1. Entra en la app con Google → pestaña **Grupo** → crea el grupo.
2. Toca **Copiar enlace** y mándalo por WhatsApp. Tus amigos lo abren, entran con Google y tocan **Unirme**.
3. En iPhone: Safari → Compartir → **Agregar a inicio** para tenerla como app.

## Cómo está organizado

| Archivo | Qué es |
| --- | --- |
| `index.html` | Toda la app (pantallas, lógica, estilos) |
| `firebase-config.js` | La configuración de tu proyecto de Firebase |
| `firestore.rules` | Quién puede leer y escribir qué |

Datos en Firestore:

- `users/{uid}/private/state`: hábitos e historial de cada persona. Solo ella puede leerlos.
- `groups/{código}`: nombre del grupo y su creador. Se puede leer si conoces el código.
- `groups/{código}/members/{uid}`: el resumen de cada miembro. Solo lo leen los miembros; cada uno escribe solo el suyo.
- `groups/{código}/goals/{id}`: objetivos comunes. Los crea cualquier miembro y los borra quien los creó.

## Costo

El plan gratuito de Firebase (Spark) incluye 50.000 lecturas y 20.000 escrituras por día.
Un grupo de 5 personas usa una fracción mínima de eso.

## Probar en local con el emulador

```bash
npx firebase-tools emulators:start --project demo-racha --only auth,firestore
```
y en `firebase-config.js` usa `{ apiKey: "fake", authDomain: "demo-racha.firebaseapp.com", projectId: "demo-racha", appId: "1:1:web:1", useEmulator: true }`.
