# Uaemex-Electonicos-JP-JJ-R

Aplicación web para la tienda de componentes electrónicos de la Facultad de Ingeniería de la UAEMéx. El proyecto está construido sobre Next.js 15 con TypeScript y Tailwind CSS, e integra una base de datos PostgreSQL gestionada mediante Prisma para almacenar el catálogo, ofertas y pedidos.

##  Stack principal

- **Framework:** Next.js 15 (App Router) + React 19
- **Lenguaje:** TypeScript
- **Estilos:** Tailwind CSS 4, Radix UI, shadcn/ui
- **Gráficas:** Recharts
- **ORM / Base de datos:** Prisma + PostgreSQL
- **Herramientas adicionales:** Prisma Studio, tsx

##  Estructura relevante

```
├── app/                  # Rutas y páginas (app router)
│   └── api/              # Endpoints REST para productos y pedidos
├── components/           # Componentes de UI y páginas clientes
├── hooks/                # Hooks personalizados (p.ej. useProducts)
├── lib/
│   └── prisma.ts         # Cliente Prisma reutilizable
├── prisma/
│   ├── schema.prisma     # Modelo de datos (Category, Product, Order, OrderItem)
│   └── seed.ts           # Seed de catálogo/ofertas/pedido de ejemplo
└── public/               # Assets e imágenes de productos
```

##  Despliegue Local

Existen **dos formas** de ejecutar el proyecto localmente:

### Opción 1: Docker Compose (Recomendado) 

La forma más rápida y sencilla. Docker se encarga de todo: base de datos, aplicación y Prisma Studio.

#### Prerrequisitos

- [Docker Desktop](https://www.docker.com/products/docker-desktop) instalado y corriendo
- Git para clonar el repositorio

#### Pasos

1. **Clonar el repositorio**
   ```bash
   git clone https://github.com/[RajeZ0/ProyectoTecnologias.git](https://github.com/RajeZ0/Uaemex-Electonicos-JP-JJ-R)
   cd ProyectoTecnologias-main
   ```

2. **Configurar variables de entorno (opcional)**
   
   Si quieres personalizar la configuración, copia el archivo de ejemplo:
   ```bash
   cp .env.example .env
   ```
   
   Puedes modificar `.env` para cambiar credenciales de la base de datos o habilitar el seed automático:
   ```env
   SEED_DATABASE=true  # Llena la BD con datos de ejemplo al iniciar
   ```

3. **Levantar todos los servicios**
   ```bash
   docker compose up --build -d
   ```
   
   Este comando:
   - Construye las imágenes Docker necesarias
   - Inicia PostgreSQL en el puerto `5432`
   - Inicia la aplicación Next.js en el puerto `8080`
   - Inicia Prisma Studio en el puerto `5555`
   - Ejecuta las migraciones de base de datos
   - Siembra datos de ejemplo si `SEED_DATABASE=true`

4. **Acceder a la aplicación**
   
   - **Tienda web:** http://localhost:8080
   - **Prisma Studio** (administrador de BD): http://localhost:5555

5. **Ver logs en tiempo real**
   ```bash
   # Ver logs de la aplicación
   docker compose logs -f app
   
   # Ver logs de la base de datos
   docker compose logs -f db
   
   # Ver todos los logs
   docker compose logs -f
   ```

6. **Detener los servicios**
   ```bash
   # Detener sin eliminar datos
   docker compose down
   
   # Detener y eliminar la base de datos (reinicio completo)
   docker compose down -v
   ```

#### Comandos útiles con Docker Compose

```bash
# Reiniciar solo la aplicación
docker compose restart app

# Reconstruir después de cambios en el código
docker compose up --build -d

# Ver estado de los contenedores
docker compose ps

# Ejecutar comandos dentro del contenedor de la app
docker compose exec app npx prisma studio
docker compose exec app npm run db:seed
```

---

### Opción 2: Instalación Manual 🛠️

Para desarrollo avanzado o si prefieres tener control total del entorno.

#### Prerrequisitos

- **Node.js 18 o superior** (recomendado: Node 20 o 22)
  - Descargar desde: https://nodejs.org/
  - Verificar: `node --version`
- **npm** (incluido con Node)
  - Verificar: `npm --version`
- **PostgreSQL 16** (o Docker para ejecutar solo la BD)

#### Pasos

1. **Clonar el repositorio**
   ```bash
   git clone https://github.com/RajeZ0/ProyectoTecnologias.git
   cd ProyectoTecnologias
   ```

2. **Instalar dependencias**
   ```bash
   npm install
   ```

3. **Configurar base de datos PostgreSQL**

   **Opción A: Usar Docker solo para PostgreSQL**
   ```bash
   docker run --name uaemex-postgres \
     -e POSTGRES_USER=postgres \
     -e POSTGRES_PASSWORD=postgres \
     -e POSTGRES_DB=uaemex \
     -p 5432:5432 \
     -d postgres:16
   ```

   **Opción B: Instalar PostgreSQL localmente**
   - Descargar desde: https://www.postgresql.org/download/
   - Crear una base de datos llamada `uaemex`

4. **Configurar variables de entorno**
   ```bash
   cp .env.example .env
   ```
   
   Edita `.env` y configura la URL de tu base de datos:
   ```env
   DATABASE_URL="postgresql://postgres:postgres@localhost:5432/uaemex"
   ```

5. **Configurar la base de datos**
   ```bash
   # Generar el cliente de Prisma
   npx prisma generate
   
   # Aplicar migraciones (crear tablas)
   npx prisma migrate deploy
   
   # Sembrar datos de ejemplo
   npm run db:seed
   ```

6. **Iniciar el servidor de desarrollo**
   ```bash
   npm run dev
   ```
   
   La aplicación estará disponible en: http://localhost:8080

7. **Abrir Prisma Studio (opcional)**
   
   En otra terminal:
   ```bash
   npx prisma studio
   ```
   
   Prisma Studio estará disponible en: http://localhost:5555

#### Scripts disponibles

| Script            | Descripción                                                                 |
| ----------------- | ---------------------------------------------------------------------------- |
| `npm run dev`     | Inicia Next.js en modo desarrollo (puerto **8080**)                          |
| `npm run build`   | Genera el build de producción                                               |
| `npm run start`   | Sirve el build generado                                                     |
| `npm run lint`    | Ejecuta `next lint`                                                         |
| `npm run db:seed` | Ejecuta `prisma db seed` y repuebla la base de datos                        |

> **Nota:** `npm run db:seed` elimina y repuebla las tablas antes de insertar los datos de ejemplo.

---

##  Modelo de datos

El archivo `prisma/schema.prisma` define las entidades:

- **Category** → agrupa productos del catálogo.
- **Product** → información del inventario (precio, stock, oferta, imagen, categoría).
- **Order** → pedidos realizados, con número de orden, totales y ventana de envío.
- **OrderItem** → relación detalle producto ↔ pedido.

Los valores monetarios se guardan como `Float` y las fechas (creación y entrega estimada) en `DateTime`.

### Flujo de migraciones

Las migraciones viven en `prisma/migrations`. Para instalar el esquema en cualquier entorno:

```bash
npx prisma migrate deploy
npm run db:seed
```

##  Endpoints REST

| Método | Ruta              | Descripción                                       |
| ------ | ----------------- | ------------------------------------------------- |
| GET    | `/api/products`   | Lista productos y categorías (filtros opcionales) |
| GET    | `/api/orders`     | Obtiene pedidos de un cliente (`?email=`)         |
| POST   | `/api/orders`     | Registra un pedido y genera número de tracking    |

### Parámetros y filtros

- `GET /api/products?offersOnly=true` → solo ofertas
- `GET /api/products?category=slug` → filtra por categoría

`POST /api/orders` espera un payload:

```json
{
  "customerEmail": "cliente@example.com",
  "customerName": "Invitado",
  "items": [
    { "productId": 1, "quantity": 2 },
    { "productId": 7, "quantity": 1 }
  ]
}
```

Responde con la orden creada (total, ventana de entrega, `orderNumber`, etc.).

## 🛒 Flujo de compra

1. El cliente inicia sesión (o compra como "Invitado").
2. Agrega productos al carrito desde cualquier listado.
3. `Finalizar Compra` → `components/cart-dropdown` llama a `POST /api/orders`.
4. Se limpia el carrito y se muestra el número de pedido y la fecha estimada.
5. La página **Mis pedidos** (`/orders`) consume `GET /api/orders` y se actualiza automáticamente.

Puedes verificar la inserción del pedido en **Prisma Studio** (`Order` y `OrderItem`).

##  Datos de ejemplo

El seed crea:

- 5 categorías principales (pasivos, activos, fuentes de energía, instrumentación, accesorios).
- Catálogo completo con precios, ofertas, stock y rutas de imagen.
- Pedido de ejemplo (`ORD-SEED-001`) para `guest@example.com` con artículos reales.

Edita `prisma/seed.ts` si necesitas ajustar productos u ofertas.

##  Verificación manual

1. Inicia la aplicación (con Docker Compose o manualmente)
2. Abre http://localhost:8080
3. Recorre el catálogo y agrega productos al carrito
4. Finaliza una compra
5. Revisa `/orders` para ver tus pedidos
6. Abre Prisma Studio (http://localhost:5555) para confirmar los datos en la base de datos

## ☁️ Despliegue en Render

1. Publica el repositorio en GitHub y crea en Render un servicio de base de datos **PostgreSQL**. Copia la URL completa (incluye usuario, contraseña, host, puerto y base).
2. En Render crea un **Web Service** apuntando a la rama principal del repo y configura las variables:
   - `DATABASE_URL` → URL del Postgres provisionado (añade `?sslmode=require`).
   - `NODE_VERSION` → `20.17.0`.
   - `SEED_DATABASE` → `true` **solo para el primer deploy**, luego elimínala.
3. Ajusta los comandos del servicio:
   - **Build command:** `npm install && npx prisma generate && npm run build`
   - **Start command:** `npm run start:render`
   - **Post-Deploy command** (opcional; úsalo si prefieres ejecutarlo manualmente tras el build inicial): `npx prisma migrate deploy && npm run db:seed`
4. `npm run start:render` aplica `prisma migrate deploy`, ejecuta el seed cuando `SEED_DATABASE=true` y finalmente levanta `next start`.
5. Para refrescar datos después de deploys futuros solo corre de nuevo el Post-Deploy command o ejecuta manualmente `npx prisma migrate deploy` y `npm run db:seed` desde la consola del servicio.


---
