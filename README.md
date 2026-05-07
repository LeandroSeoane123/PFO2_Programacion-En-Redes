# Sistema de Gestión de Tareas, API Flask + SQLite

API REST para registrar usuarios y gestionar tareas personales, con autenticación HTTP Basic Auth y contraseñas hasheadas.

---

## Requisitos

- Python 3.10 o superior
- pip

## Instalación

```bash
pip install flask requests
```

## Ejecución del servidor

```bash
python3 servidor.py
```

El servidor queda disponible en `http://127.0.0.1:5001`. La base de datos `tareas.db` se crea automáticamente al iniciar.

## Uso del cliente (consola)

Con el servidor corriendo, en otra terminal:

```bash
python3 cliente.py
```

El cliente muestra un menú interactivo para registrarse, iniciar sesión, y gestionar tareas.

---

## Endpoints

### `POST /registro`

Registra un nuevo usuario.

**Body JSON:**

```json
{ "usuario": "usuario123", "password": "1234" }
```

**Respuestas:**

- `201` — Usuario registrado exitosamente.
- `409` — El usuario ya existe.
- `400` — Faltan campos o no cumplen el largo mínimo.

---

### `POST /login`

Verifica las credenciales de un usuario.

**Body JSON:**

```json
{ "usuario": "usuario123", "password": "1234" }
```

**Respuestas:**

- `200` — Credenciales válidas.
- `401` — Credenciales inválidas.

---

### `GET /tareas` _(requiere autenticación)_

Muestra las tareas del usuario autenticado.

- Desde el **navegador** (con Basic Auth): devuelve una página HTML de bienvenida con la lista de tareas.
- Desde el **cliente / curl**: devuelve JSON.

Si no se envían credenciales, se muestra un mensaje de error indicando que el usuario no está logueado.

**Autenticación:** HTTP Basic Auth (`-u usuario:password` en curl).

**Ejemplo curl:**

```bash
curl http://127.0.0.1:5001/tareas -u usuario123:1234
```

---

### `POST /tareas` _(requiere autenticación)_

Crea una nueva tarea para el usuario autenticado.

**Body JSON:**

```json
{ "descripcion": "tarea mia" }
```

**Respuestas:**

- `201` — Tarea creada, devuelve el `id` asignado.
- `400` — Descripción vacía o ausente.

**Ejemplo curl:**

```bash
curl -X POST http://127.0.0.1:5001/tareas \
     -H "Content-Type: application/json" \
     -u usuario123:1234 \
     -d '{"descripcion": "tarea mia"}'
```

---

### `DELETE /tareas/<id>` _(requiere autenticación)_

Elimina una tarea del usuario autenticado por su `id`.

**Respuestas:**

- `200` — Tarea eliminada.
- `404` — Tarea no encontrada o no pertenece al usuario.

**Ejemplo curl:**

```bash
curl -X DELETE http://127.0.0.1:5001/tareas/1 -u usuario123:1234
```

---

## Prueba rápida con curl

```bash
# 1. Registrar usuario
curl -X POST http://127.0.0.1:5001/registro \
     -H "Content-Type: application/json" \
     -d '{"usuario":"usuario123","password":"1234"}'

# 2. Login
curl -X POST http://127.0.0.1:5001/login \
     -H "Content-Type: application/json" \
     -d '{"usuario":"usuario123","password":"1234"}'

# 3. Crear tarea
curl -X POST http://127.0.0.1:5001/tareas \
     -H "Content-Type: application/json" \
     -u usuario123:1234 \
     -d '{"descripcion":"Mi primera tarea"}'

# 4. Ver tareas
curl http://127.0.0.1:5001/tareas -u usuario123:1234

# 5. Eliminar tarea con id 1
curl -X DELETE http://127.0.0.1:5001/tareas/1 -u usuario123:1234
```

---
## Pruebas:  
### Menú principal:
<img width="576" height="278" alt="image" src="https://github.com/LeandroSeoane123/PFO2_Programacion-En-Redes/blob/main/image/1.jpeg" />

### Registro de usuario:  
<img width="560" height="335" alt="image" src="https://github.com/LeandroSeoane123/PFO2_Programacion-En-Redes/blob/main/image/2.jpeg" />

### Inicio de sesión (usuario ya registrado):  
<img width="581" height="350" alt="image" src="https://github.com/LeandroSeoane123/PFO2_Programacion-En-Redes/blob/main/image/3.jpeg" />

### Inicio de sesión (usuario no registrado):  
<img width="601" height="325" alt="image" src="https://github.com/LeandroSeoane123/PFO2_Programacion-En-Redes/blob/main/image/4.jpeg" />

### Ver mis tareas (no registrado):
<img width="511" height="260" alt="image" src="https://github.com/LeandroSeoane123/PFO2_Programacion-En-Redes/blob/main/image/5.jpeg" />

### Ver mis tareas (registrado):
<img width="624" height="321" alt="image" src="https://github.com/LeandroSeoane123/PFO2_Programacion-En-Redes/blob/main/image/6.jpeg" />

### Cerrar sesión:  
<img width="606" height="284" alt="image" src="https://github.com/LeandroSeoane123/PFO2_Programacion-En-Redes/blob/main/image/7.jpeg" />

### Salir:  
<img width="603" height="228" alt="image" src="https://github.com/LeandroSeoane123/PFO2_Programacion-En-Redes/blob/main/image/8.jpeg" />


---

## Respuestas Conceptuales

### ¿Por qué hashear contraseñas?

Almacenar contraseñas en texto plano es un riesgo grave, porque si la base de datos es comprometida, todas las contraseñas quedan expuestas de inmediato. El hashing convierte la contraseña en una cadena irreversible. Así, aunque alguien acceda a la base de datos, no puede recuperar la contraseña original.

### Ventajas de usar SQLite en este proyecto

- **Sin servidor:** SQLite es una biblioteca embebida, que no requiere instalar ni configurar un motor de base de datos separado.
- **Archivo único:** toda la base de datos es un solo archivo (`tareas.db`), fácil de mover, respaldar o eliminar.
- **Incluido en Python:** el módulo `sqlite3` viene en la biblioteca estándar, sin dependencias adicionales.
- **Ideal para proyectos pequeños:** tiene soporte completo de SQL, transacciones y claves foráneas, más que suficiente para este caso de uso.
