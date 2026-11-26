# Cómo compilar y ejecutar el RPG Manager

El proyecto tiene dos versiones de la misma aplicación:

| Versión | Carpeta | Puerto |
|---|---|---|
| Java (Javalin + Maven) | `rpg_manager_multinivel_javalin/` | http://localhost:7070/personajes |
| Python (Flask) | `rpg_manager_multinivel_flask/` | http://localhost:5000/personajes |

Las dos leen un archivo de configuración que **no se sube a git** (está en `.gitignore`).
Siempre se crea copiando el `.example` de su carpeta. Con `repositorio = memoria` no hace falta
ninguna base de datos. Para usar `mariadb` o `mongo`, primero carga la base (ver `db/LEEME.md`)
y pon tus datos de conexión en el archivo de configuración.

---

## 1. Java (Javalin)

### Requisitos

- JDK 17 o superior (`java -version`)
- Maven 3.9+ (`mvn -v`), o NetBeans / IntelliJ, que ya traen Maven

### Configuración (solo la primera vez)

```bash
cd rpg_manager_multinivel_javalin
cp config.example.properties config.properties
```

Edita `config.properties`: `repositorio=memoria`, `mariadb` o `mongo`.

### Compilar

```bash
cd rpg_manager_multinivel_javalin
mvn compile
```

La primera vez Maven descarga las dependencias del `pom.xml` (Javalin, Jackson, drivers de
MariaDB y MongoDB), así que necesita internet. Si todo va bien termina en `BUILD SUCCESS`.

### Ejecutar

```bash
mvn exec:java
```

Abre http://localhost:7070/personajes. Para detenerlo usa `Ctrl + C`.

Para el recorrido completo (sala A.1, Kaelen):

```bash
mvn compile exec:java -Dexec.mainClass=gestionrpg.RecorridoPersistencia
```

### Desde NetBeans

Abre la carpeta `rpg_manager_multinivel_javalin` como proyecto Maven y usa
**Clean and Build** y luego **Run Project** (ejecuta `gestionrpg.Main`).

---

## 2. Python (Flask)

### Requisitos

- Python 3.10 o superior (`python3 --version`)

### Entorno virtual y dependencias (solo la primera vez)

```bash
cd rpg_manager_multinivel_flask
python3 -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

### Configuración (solo la primera vez)

```bash
cp config.example.ini config.ini
```

Edita `config.ini`: en `[app]`, `repositorio = memoria`, `mariadb` o `mongo`.

### Comprobar que compila

Python no se compila como Java, pero se puede revisar la sintaxis (y la indentación) de todos los archivos:

```bash
python -m compileall -q -x '\.venv' .
```

Si no imprime nada, no hay errores.

### Ejecutar

Siempre desde dentro de `rpg_manager_multinivel_flask/`, con el entorno virtual activo:

```bash
python app.py
```

Abre http://localhost:5000/personajes. Para detenerlo usa `Ctrl + C`.

Para el recorrido completo (sala A.1, Kaelen):

```bash
python recorrido.py            # usa el motor de config.ini
python recorrido.py mariadb    # fuerza un motor solo para esta ejecución
```

---

## Errores comunes

| Mensaje | Causa | Solución |
|---|---|---|
| `No existe config.ini` / no encuentra `config.properties` | No copiaste el `.example` | Paso de configuración de arriba |
| `IndentationError` (Python) | Un bloque pegado quedó con otra sangría | Todo el bloque debe tener la misma sangría que el código que lo rodea |
| `ModuleNotFoundError: rpg_manager_multinivel_flask` | El IDE agregó un `import` con el nombre de la carpeta | Bórralo; los imports son `from modelo...`, `from repositorio...` |
| `class X is already defined` / `illegal start of type` (Java) | El bloque pegado repite la firma o la clase que ya estaba fuera de las marcas | Deja entre `>>>` y `<<<` solo lo que falta; no repitas lo que ya está fuera |
| Código Java dentro de un `.py` | Se copió la pestaña equivocada en el Edificio | Copia el bloque de la pestaña **Python** |
