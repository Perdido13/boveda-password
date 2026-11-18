# 🗝️ Suite Criptográfica · Bóveda de Contraseñas

Proyecto educativo que demuestra, de forma interactiva, cómo se protege una bóveda de contraseñas usando **Argon2id + AES-256-GCM**, y qué pasa cuando alguien intenta atacarla o alterarla.

Todo vive en **un solo archivo (`index.html`)**: no hay servidor, no hay base de datos y no hay que instalar nada.

> 🎓 Proyecto Integrador · Escenario 1 (Argon2id + AES-256-GCM) · Materia V2271 · UDG · CUGDL

---

## 🌐 Demo en línea

👉 **https://perdido13.github.io/boveda-password/**

Solo abre el enlace en tu navegador y listo.

---

## 📦 ¿Qué incluye?

El archivo contiene **dos aplicaciones**, que se cambian con la barra superior:

### 1. 🧪 Pruebas Interactivas
Laboratorio con 8 pruebas criptográficas. Permite:

- Ejecutar las pruebas de la bóveda y ver sus resultados (esperado vs. obtenido).
- Cifrar una entrada con AES-256-GCM y ver su *nonce* y texto cifrado.
- **Alterar** el texto cifrado (1 bit) o el AAD (nombre del servicio) y ver cómo AES-GCM lo rechaza con `InvalidTag`.
- Simular un **ataque de diccionario**: MD5 sin sal vs. Argon2id (los tiempos de esta comparación son de demostración).

### 2. 🔐 Bóveda de Contraseñas
Tiene dos modos (pestañas internas):

- **📖 Modo historia:** escenas paso a paso que explican en lenguaje sencillo los conceptos: sal, KDF, nonce, AAD, AEAD, qué se lleva un ladrón y por qué una contraseña maestra débil lo arruina todo.
- **🔐 Bóveda real:** un flujo de 6 pasos funcional:
  1. Ver el "Excel" original con contraseñas en texto plano.
  2. Derivar la llave a partir de la contraseña maestra + sal.
  3. Sellar cada contraseña en su propio sobre AES-GCM.
  4. Ver el archivo que robaría un ladrón (`boveda.json`) y probar alteraciones.
  5. Atacar tu propia bóveda con un diccionario de contraseñas comunes.
  6. Desbloquear con la contraseña maestra correcta y agregar nuevas entradas.

---

## 🔒 Criptografía utilizada

| Componente | Detalle |
|---|---|
| **KDF** | Argon2id (librería `hash-wasm`) → llave de 32 bytes |
| Parámetros Argon2id (Bóveda real) | `t=2` pasadas · `m=19 MiB` · `p=1` hilo |
| **KDF de respaldo** | PBKDF2-SHA256 con 600 000 iteraciones (se usa si no carga Argon2id) |
| **Sal** | 16 bytes aleatorios (`crypto.getRandomValues`) |
| **Cifrado** | AES-256-GCM (WebCrypto del navegador) |
| **Nonce** | 12 bytes aleatorios, único por entrada |
| **AAD** | El nombre del servicio, ligado al sello de autenticidad |
| **MD5** | `SparkMD5`, solo para la comparación (rápido e inseguro a propósito) |

---

## 🚀 Cómo ejecutarlo

### Opción A · Online (la más fácil)
Abre **https://perdido13.github.io/boveda-password/** en tu navegador.

### Opción B · Descargarlo y abrirlo en tu computadora

1. **Descarga el proyecto**
   - Con Git:
     ```bash
     git clone https://github.com/Perdido13/boveda-password.git
     cd boveda-password
     ```
   - O sin Git: en la página del repositorio pulsa **Code → Download ZIP** y descomprime.
   - O descarga solo el archivo [`index.html`](https://github.com/Perdido13/boveda-password/blob/main/index.html) (botón **Download raw file**).
2. **Abre `index.html`** con doble clic (se abrirá en tu navegador predeterminado).

### Opción C · Con un servidor local (recomendado si algo no carga)
Algunos navegadores restringen funciones criptográficas en ciertos contextos al abrir archivos directamente. Si ves errores, sirve la carpeta localmente:

```bash
# Con Python 3
python -m http.server 8000

# o con Node.js
npx serve .
```

Luego abre **http://localhost:8000** en el navegador.

---

## 🛠️ Requisitos

| Requisito | ¿Obligatorio? | Detalle |
|---|---|---|
| **Navegador moderno** | ✅ Sí | Chrome, Edge, Firefox, Safari o Brave en versiones recientes (necesita WebCrypto y WebAssembly) |
| **JavaScript activado** | ✅ Sí | Todo el funcionamiento es JavaScript |
| **Conexión a internet** | ✅ Sí | Las librerías se cargan desde CDN (ver abajo). Sin internet la página se ve sin estilos y la Bóveda no funciona correctamente |
| Servidor / backend | ❌ No | Todo se ejecuta en tu navegador |
| Node, Python, npm, etc. | ❌ No | Solo hacen falta si quieres usar un servidor local (Opción C) |
| Git | ❌ No | Solo si quieres clonar el repositorio |

### 📡 Librerías externas (cargadas por internet)

| Librería | Para qué sirve | Fuente |
|---|---|---|
| Tailwind CSS | Estilos de la app "Pruebas Interactivas" | `gstatic.com` |
| SparkMD5 `3.0.2` | Calcular hashes MD5 | `cdnjs.cloudflare.com` |
| hash-wasm `4.11.0` | Argon2id real en el navegador | `cdn.jsdelivr.net` |

> 💡 Si Argon2id no llega a cargar, la Bóveda usa **PBKDF2** automáticamente como respaldo y lo indica en pantalla.
>
> ¿Quieres usarlo **sin internet**? Descarga esos 3 archivos `.js`, guárdalos junto a `index.html` y cambia las rutas de las etiquetas `<script>` del `<head>` por rutas locales.

---

## 🔐 Privacidad y alcance

- **Todo ocurre en tu navegador.** No se envía ninguna contraseña ni dato a ningún servidor.
- La bóveda **no guarda nada de forma permanente**: vive en memoria, así que al recargar o cerrar la pestaña se reinicia.
- ⚠️ Es un **proyecto educativo / demostrativo**. No lo uses para guardar tus contraseñas reales; para eso usa un gestor de contraseñas auditado (Bitwarden, KeePassXC, 1Password, etc.).

---

## ✏️ Cómo personalizarlo

El archivo está comentado y dividido en secciones (búscalas con `Ctrl + F`):

| Sección | Contenido |
|---|---|
| `[A]` | Librerías externas del `<head>` |
| `[B]` | Estilos y **tema de colores** |
| `[C]` | Barra superior (cambio de app) |
| `[D]` | HTML de Pruebas Interactivas |
| `[E]` | HTML de la Bóveda |
| `[F]` | JavaScript de ambas apps |

Cambios comunes:

- **Colores:** variables `--primary`, `--acc`, etc. en la sección "TEMA".
- **Las 8 pruebas:** constante `CASOS_PRUEBA` (y su explicación en `EX`).
- **Escenas de la historia:** constante `ESC`.
- **Parámetros de Argon2id:** constante `P = { t, m, p }`.
- **Diccionario del atacante:** constante `DICC`.

---

## 📁 Estructura del repositorio

```
boveda-password/
├── index.html   ← toda la aplicación (HTML + CSS + JS)
└── README.md    ← este archivo
```

---

## 👤 Autor

**Perdido13** · [github.com/Perdido13](https://github.com/Perdido13)

Universidad de Guadalajara · CUGDL

## 📄 Licencia

Proyecto con fines académicos. Agrega aquí la licencia que prefieras (por ejemplo, MIT).
