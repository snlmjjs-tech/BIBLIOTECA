# Préstamos con Firebase

El botón rojo **BOOK** abre el formulario de préstamo. Los préstamos se guardan en Firestore (colección `prestamos`).
Mientras `FIREBASE_CONFIG` en `index.html` esté vacío, la página guarda solo en el navegador (modo de prueba).

## 1. Crear el proyecto
1. https://console.firebase.google.com → **Agregar proyecto**.
2. **Compilación → Firestore Database → Crear base de datos** (modo producción).
3. **Configuración del proyecto → Tus apps → Web (`</>`)** → copia el objeto `firebaseConfig`.
4. Pégalo en `index.html`, en `var FIREBASE_CONFIG={...}`.

## 2. Reglas de seguridad (Firestore → Reglas)
La página es pública, así que solo permitimos **crear** préstamos; la lectura es solo desde la consola de Firebase.

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /prestamos/{id} {
      allow create: if request.resource.data.nombre is string
                    && request.resource.data.titulo is string;
      allow read, update, delete: if false;
    }
    match /mail/{id} {
      allow create: if request.resource.data.keys().hasOnly(['to','message']);
      allow read, update, delete: if false;
    }
  }
}
```

## 3. Comprobante por correo (opcional)
Firebase no envía correos por sí solo. Instala la extensión **Trigger Email from Firestore**
(Extensiones → Explorar) con la colección de correos `mail` y tu servidor SMTP (requiere plan Blaze).
Cuando alguien escribe su correo, la página crea un documento en `mail` y la extensión envía el comprobante.
Sin extensión, o si la persona no tiene correo, se imprime el comprobante con **Imprimir / Guardar PDF**.

## Campos guardados en `prestamos`
`id, registro, clasificacion, fecha, hora, nombre, curso, titulo, autor, devolucion, correo, devuelto, ts`
