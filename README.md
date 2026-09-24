# Veterinaria Huellitas

El análisis del negocio, el diagrama ER completo y la propuesta de solución están documentados en [`docs/parcial/analisis_veterinaria.md`](docs/parcial/analisis_veterinaria.md). Este README cubre solo la parte técnica del proyecto.

## Requisitos

- Docker Desktop
- WSL2 (si estás en Windows)
- Laravel Sail

## Instalación

```bash
# Clonar el repositorio y cambiar a la rama del parcial
git clone https://github.com/manuela-aguirre/ParcialSGE-1.git
cd ParcialSGE-1
git checkout parcial

# Copiar variables de entorno (ya vienen configuradas para MySQL de Sail)
cp .env.example .env

# Instalar dependencias de PHP usando Docker (no requiere PHP local).
# Solo es necesario si no existe la carpeta vendor/
docker run --rm -u "$(id -u):$(id -g)" -v "$(pwd):/app" -w /app \
  composer:latest composer install --ignore-platform-reqs

# Levantar los contenedores
./vendor/bin/sail up -d

# Generar la clave de la aplicación
./vendor/bin/sail artisan key:generate

# Ejecutar migraciones y sembrar datos de prueba
./vendor/bin/sail artisan migrate:fresh --seed
```

> Si ya tienes PHP y Composer instalados localmente, basta con `composer install` en lugar del comando con Docker.

La aplicación queda disponible en [http://localhost](http://localhost).

## Entidades implementadas

Del diseño completo (8 entidades, ver documento de análisis), esta implementación cubre 3, según el alcance definido para el parcial:

| Entidad | Descripción |
|---|---|
| `Client` | Dueños de mascotas |
| `Pet` | Mascotas registradas, asociadas a un cliente |
| `Product` | Catálogo de productos (medicamentos, alimentos, accesorios) |

**Relación implementada — Client → Pet (1:N)**

```php
// Client.php
public function pets() {
    return $this->hasMany(Pet::class);
}

// Pet.php
public function client() {
    return $this->belongsTo(Client::class);
}
```

## Seeders

`ClientSeeder` pobla la tabla `clients` con 5 registros de prueba y está registrado en `DatabaseSeeder`, por lo que `migrate:fresh --seed` ya lo ejecuta. También puede correrse solo:

```bash
./vendor/bin/sail artisan db:seed --class=ClientSeeder
```

Para verificar:

```bash
./vendor/bin/sail mysql
# USE laravel;
# SHOW TABLES;
# SELECT * FROM clients;
```

## Comandos útiles

```bash
# Consola interactiva
./vendor/bin/sail artisan tinker

# Acceso directo a MySQL
./vendor/bin/sail mysql

# Reconstruir base de datos desde cero
./vendor/bin/sail artisan migrate:fresh --seed

# Estado de las migraciones
./vendor/bin/sail artisan migrate:status
```

## Estructura de la base de datos

```
clients
├── id
├── name
├── phone
├── email
├── address
└── timestamps

pets
├── id
├── name
├── species
├── breed
├── client_id (FK → clients.id)
└── timestamps

products
├── id
├── name
├── description
├── price
├── stock
└── timestamps
```

## Documentación del parcial

| Documento | Contenido |
|---|---|
| [`docs/parcial/analisis_veterinaria.md`](docs/parcial/analisis_veterinaria.md) | Análisis del negocio, diagrama ER completo, diccionario de datos y propuesta de solución |
| [`docs/parcial/capturas/`](docs/parcial/capturas/) | Capturas de pantalla: tablas en MySQL, migraciones, modelos, seeder ejecutado y registro insertado |
