# Architectural Decision Records (ADRs)

Este documento contiene el registro de las decisiones de arquitectura significativas tomadas para el proyecto. Cada registro describe el contexto, la decisión, las consecuencias y las alternativas consideradas.

---

## Índice

* [ADR-001: Estrategia de Interfaz de Usuario Híbrida (Móvil/Web)](#adr-001-estrategia-de-interfaz-de-usuario-híbrida-móvilweb)
* [ADR-002: Incorporación del Servicio de Analítica y Machine Learning](#adr-002-incorporación-del-servicio-de-analítica-y-machine-learning-pythontensorflow)
* [ADR-003: Arquitectura Basada en API REST (Node.js) con Repositorios Modulares](#adr-003-arquitectura-basada-en-api-rest-nodejs-con-repositorios-modulares)

---

## ADR-001: Estrategia de Interfaz de Usuario Híbrida (Móvil/Web)

* **Fecha:** 25 de Octubre, 2025
* **Estado:** Aprobado
* **Decidido por:** Equipo de Arquitectura
* **Consultado:** Product Owner, Diseñadores UX/UI
* **Informado:** Stakeholders del Proyecto

### Contexto
El proyecto debe servir a dos grupos de usuarios con necesidades y entornos operativos muy distintos, como se evidencia en el Diagrama de Contexto (C1) y Contenedores (C2):

1.  **Usuarios de campo (Agricultores, Transportistas):** Necesitan interfaces simples, rápidas y accesibles en entornos rurales (posible baja conectividad) para tareas de registro de cosecha, inventario básico y reportes de rutas.
2.  **Usuarios de gestión (Centros de Acopio, Compradores, ONG):** Requieren acceso a dashboards complejos, analítica avanzada, reportes de rentabilidad y sostenibilidad.

### Métricas de Decisión
* **Adopción Rural:** Alto índice de uso por agricultores.
* **Funcionalidad Analítica:** Capacidad de renderizar dashboards complejos.
* **Costo de Desarrollo:** Balancear el esfuerzo de mantenimiento de múltiples interfaces.

### Decisión
Hemos decidido implementar una **estrategia de interfaz de usuario híbrida**:

* **Aplicación Móvil (Flutter):** Dirigida a Agricultores y Transportistas. Permite acceso rápido a funciones esenciales (Registro, Rutas, Notificaciones) y facilita estrategias de uso *offline-first* o en entornos con poca señal.
* **Aplicación Web (Angular - SPA):** Dirigida a Operadores de Acopio, Compradores y ONG. Ideal para visualización de métricas detalladas, dashboards interactivos, reportes de sostenibilidad y gestión de inventarios a gran escala.

### Plan de Implementación
1.  **Fase 1:** Desarrollar los modelos de datos compartidos por ambas interfaces a través de la API.
2.  **Fase 2:** Implementación del Core de la Aplicación Móvil (Registro y Rutas).
3.  **Fase 3:** Implementación del Dashboard Web (Dashboard y Analítica).

### Consecuencias

**Positivas:**
* **Máxima Adopción:** La App Móvil mejora la accesibilidad en zonas rurales, clave para el objetivo de aumento de ganancia del agricultor.
* **Funcionalidad Óptima:** La Web SPA permite dashboards complejos para el análisis de desperdicio y rentabilidad.
* **Rendimiento:** Flutter permite un desarrollo eficiente para múltiples plataformas móviles (Android/iOS) con una sola base de código.

**Negativas:**
* **Overhead de Desarrollo:** Se mantiene y actualiza doble base de código de frontend (Flutter y Angular).
* **Complejidad de Integración:** Se debe garantizar que ambas interfaces consuman la API de forma consistente y manejen los estados de manera sincronizada.

### Alternativas Consideradas

**Alternativa 1: Solo Aplicación Web Responsiva (SPA)**
* *Razón de Descarte:* La experiencia en dispositivos móviles para tareas de campo (registro de cosecha, rutas) en condiciones de baja conectividad se vería comprometida, impactando directamente la adopción rural.

**Alternativa 2: Solo Aplicación Móvil Nativa (iOS/Android)**
* *Razón de Descarte:* Es inviable para los dashboards analíticos complejos y el manejo de grandes volúmenes de datos que requieren los usuarios de gestión en pantallas de escritorio.

### Validación
* **Criterio de éxito:** 80% de los agricultores activos utilizan la App Móvil para el registro de cosecha.

---

## ADR-002: Incorporación del Servicio de Analítica y Machine Learning (Python/TensorFlow)

* **Fecha:** 25 de Octubre, 2025
* **Estado:** Aprobado
* **Decidido por:** Equipo de I+D y Arquitectura
* **Consultado:** Científico de Datos, Gestor de Logística
* **Informado:** Product Owner

### Contexto
El Objetivo Principal es reducir las pérdidas post-cosecha en un 15% y optimizar la logística. Esto requiere predicciones y recomendaciones dinámicas basadas en datos (clima, rutas, tiempos de tránsito, historial de pérdidas), como se detalla en el diagrama de Componentes (C3).

### Métricas de Decisión
* **Impacto en Desperdicio:** Capacidad de generar alertas con ≥ 70% de precisión.
* **Eficiencia Logística:** Reducción de tiempos de ruta ≥ 10%.
* **Flexibilidad Tecnológica:** Uso de un stack estándar para Ciencia de Datos.

### Decisión
Se decide implementar un **Servicio de Analítica y ML como un componente de backend separado**, utilizando **Python y TensorFlow**. Este servicio es responsable de:

* Consumir datos históricos de la Base de Datos (MongoDB).
* Integrar datos en tiempo real de Servicios Externos (Meteorológico y Logística/Mapas).
* Ejecutar modelos de Machine Learning para predecir el riesgo de pérdida post-cosecha y generar rutas óptimas.
* Ofrecer estas predicciones y rutas al Gestor de Logística a través de una API interna.

### Plan de Implementación
1.  **Fase 1:** Implementación de la ingesta y preparación de datos (ETL) desde MongoDB y APIs externas.
2.  **Fase 2:** Desarrollo e integración del modelo de Predicción de Pérdidas.
3.  **Fase 3:** Desarrollo e integración del modelo de Optimización de Rutas.

### Consecuencias

**Positivas:**
* **Cumplimiento del Objetivo Clave:** Habilita el análisis predictivo, esencial para la reducción del 15% del desperdicio.
* **Mejora de la Rentabilidad:** La optimización de rutas reduce costos operativos.
* **Aislamiento Tecnológico:** El uso de Python/TensorFlow está aislado del backend principal (Node.js), permitiendo escalar los recursos de cómputo independientemente.

**Negativas:**
* **Dependencia Externa:** Alta dependencia de la disponibilidad y calidad de los datos del Servicio Meteorológico y del Sistema de Logística y Mapas.
* **Operaciones ML (ML Ops)::** Añade una capa de complejidad operativa (despliegue, monitoreo, versionado y reentrenamiento de modelos).

### Alternativas Consideradas

**Alternativa 1: Soluciones de ML ofrecidas por Proveedores Cloud (SaaS)**
* *Razón de Descarte:* La necesidad de mantener el *core* de la analítica bajo control de la plataforma para proteger la propiedad intelectual de los modelos predictivos específicos de LATAM y evitar vendor lock-in costoso a largo plazo.

**Alternativa 2: Implementar modelos de ML sencillos directamente en el backend (Node.js/Django)**
* *Razón de Descarte:* El rendimiento de Node.js no es óptimo para el cálculo matricial pesado y el entrenamiento de modelos de Machine Learning a gran escala, lo que comprometería la precisión y velocidad.

### Validación
* **Criterio de éxito:** El modelo de predicción de pérdidas se integra en el Dashboard con un score de precisión reportado y validado.

---

## ADR-003: Arquitectura Basada en API REST (Node.js) con Repositorios Modulares

* **Fecha:** 25 de Octubre, 2025
* **Estado:** Aprobado
* **Decidido por:** Equipo de Desarrollo Backend
* **Consultado:** Arquitecto de Software
* **Informado:** Product Owner

### Contexto
El sistema necesita exponer sus funcionalidades (Logística, Inventario, Seguridad) a las aplicaciones cliente (Móvil y Web) de manera escalable, segura y desacoplada. Se requiere un manejo robusto de la lógica de negocio y una separación clara de las responsabilidades, como se detalla en los diagramas C2 y C3.

### Métricas de Decisión
* **Escalabilidad:** Capacidad de manejar picos de tráfico de usuarios concurrentes.
* **Mantenibilidad:** Separación clara de la lógica de negocio de la infraestructura.
* **Velocidad de Desarrollo:** Stack conocido y productivo (Node.js).

### Decisión
Se implementará una arquitectura de backend centrada en una **API REST principal (usando Node.js/Express.js)** que delega la lógica de negocio a Componentes Modulares:

* **Controladores de API:** Manejan la recepción de solicitudes HTTP y delegación.
* **Gestores (Services):** Componentes que encapsulan la lógica de negocio pura (ej. Gestor de Cosechas e Inventario, Gestor de Logística).
* **Repositorios de Datos (Mongoose):** Interactúan únicamente con la Base de Datos (MongoDB), abstrayendo las consultas.

### Plan de Implementación
1.  **Fase 1:** Implementación del Módulo de Seguridad (JWT) y los Controladores de API para Auth.
2.  **Fase 2:** Desarrollo del Repositorio de Datos y el Gestor de Cosechas e Inventario.
3.  **Fase 3:** Desarrollo del Gestor de Logística e integración con el Cliente de Servicios externos.

### Consecuencias

**Positivas:**
* **Desacoplamiento:** La separación de la lógica en Gestores modulares facilita el desarrollo, el testing unitario independiente y la reutilización de código.
* **Escalabilidad:** Node.js es altamente eficiente para I/O (operaciones de red y bases de datos), ideal para una API REST con alto tráfico.
* **Seguridad:** El manejo centralizado de tokens en el Módulo de Seguridad asegura la autenticación transversal.

**Negativas:**
* **Complejidad de Mapeo:** Se requiere un mapeo riguroso de objetos (DTOs) entre los Controladores, los Gestores y el Repositorio para evitar fugas de datos.
* **Acoplamiento Interno:** Una falla crítica en un Gestor compartido podría impactar a toda la API si no se manejan los errores adecuadamente (Graceful degradation).

### Alternativas Consideradas

**Alternativa 1: API Monolítica con Capas Tradicionales (MVC acoplado)**
* *Razón de Descarte:* Dificulta el crecimiento de la plataforma y la introducción de nuevos módulos de negocio (ej. integración financiera o IoT). El enfoque modular facilita la evolución futura a una arquitectura de microservicios si es necesario.

**Alternativa 2: Uso de otro Lenguaje Backend (Java/Spring o Go)**
* *Razón de Descarte:* Node.js ofrece un ecosistema rápido y unificado con JavaScript/TypeScript (compartiendo lenguaje con Angular/Web) y es altamente eficiente para APIs de alta concurrencia con menor curva de aprendizaje para el equipo actual.

### Validación
* **Criterio de éxito 1:** El Gestor de Logística puede ser refactorizado o desplegado de forma independiente si es necesario en el futuro.
* **Criterio de éxito 2:** La API soporta 1000+ peticiones por segundo en el endpoint de registro de datos de cosecha bajo pruebas de carga.

# Plataforma de Gestión Agrícola y Logística

![Estado del Proyecto](https://img.shields.io/badge/Estado-En_Desarrollo-orange)
![Versión](https://img.shields.io/badge/Versión-1.0.0-blue)

Este repositorio contiene el código fuente de la plataforma integral para la gestión de cosechas, optimización logística y reducción de desperdicios agrícolas. El sistema implementa una arquitectura modular con servicios especializados.

## 📋 Estructura del Proyecto

El proyecto está organizado como un monorepo (o repositorio múltiple) con los siguientes componentes principales:

* **`backend-api`**: API REST principal (Node.js/Express) - Gestión de usuarios, inventario y orquestación.
* **`ml-service`**: Servicio de Analítica (Python/TensorFlow) - Predicción de demanda y rutas.
* **`web-client`**: Dashboard administrativo (Angular).
* **`mobile-app`**: Aplicación para agricultores y transportistas (Flutter).

---

## 🛠️ Prerrequisitos

Antes de ejecutar el proyecto, asegúrate de tener instalado:

* **Node.js** (v18.x o superior) y **npm**.
* **Python** (v3.9 o superior) y **pip**.
* **Flutter SDK** (v3.x o superior).
* **MongoDB** (Corriendo localmente en puerto 27017 o una instancia en Atlas).
* **Docker & Docker Compose** (Opcional, para ejecución rápida).

---

## 🚀 Ejecución Rápida con Docker (Recomendado)

Si tienes Docker instalado, puedes levantar todos los servicios de backend y bases de datos con un solo comando.

1.  Crea un archivo `.env` en la raíz basado en el `.env.example`.
2.  Ejecuta:

```bash
docker-compose up --build

Markdown

# Plataforma de Gestión Agrícola y Logística

![Estado del Proyecto](https://img.shields.io/badge/Estado-En_Desarrollo-orange)
![Versión](https://img.shields.io/badge/Versión-1.0.0-blue)

Este repositorio contiene el código fuente de la plataforma integral para la gestión de cosechas, optimización logística y reducción de desperdicios agrícolas. El sistema implementa una arquitectura modular con servicios especializados.

## 📋 Estructura del Proyecto

El proyecto está organizado como un monorepo (o repositorio múltiple) con los siguientes componentes principales:

* **`backend-api`**: API REST principal (Node.js/Express) - Gestión de usuarios, inventario y orquestación.
* **`ml-service`**: Servicio de Analítica (Python/TensorFlow) - Predicción de demanda y rutas.
* **`web-client`**: Dashboard administrativo (Angular).
* **`mobile-app`**: Aplicación para agricultores y transportistas (Flutter).

---

## 🛠️ Prerrequisitos

Antes de ejecutar el proyecto, asegúrate de tener instalado:

* **Node.js** (v18.x o superior) y **npm**.
* **Python** (v3.9 o superior) y **pip**.
* **Flutter SDK** (v3.x o superior).
* **MongoDB** (Corriendo localmente en puerto 27017 o una instancia en Atlas).
* **Docker & Docker Compose** (Opcional, para ejecución rápida).

---

## 🚀 Ejecución Rápida con Docker (Recomendado)

Si tienes Docker instalado, puedes levantar todos los servicios de backend y bases de datos con un solo comando.

1.  Crea un archivo `.env` en la raíz basado en el `.env.example`.
2.  Ejecuta:

```bash
docker-compose up --build
Esto levantará:

MongoDB (localhost:27017)

Backend API (localhost:3000)

ML Service (localhost:5000)

Nota: Los clientes Web y Móvil deben ejecutarse manualmente (ver abajo).

⚙️ Configuración y Ejecución Manual
Si prefieres ejecutar cada servicio individualmente, sigue estos pasos:

1. Base de Datos (MongoDB)
Asegúrate de tener una instancia de MongoDB corriendo.

Bash

# Ejemplo si usas Docker localmente
docker run -d -p 27017:27017 --name mi-mongo mongo:latest
2. Backend API (Node.js)
Bash

cd backend-api

# Instalar dependencias
npm install

# Configurar variables de entorno
cp .env.example .env
# (Edita el .env para apuntar a tu BD local y al puerto del servicio ML)

# Ejecutar en modo desarrollo
npm run dev
El servidor estará disponible en http://localhost:3000

3. Servicio de Analítica y ML (Python)
Bash

cd ml-service

# Crear entorno virtual (Recomendado)
python -m venv venv
source venv/bin/activate  # En Windows: venv\Scripts\activate

# Instalar requerimientos
pip install -r requirements.txt

# Ejecutar servicio
python app.py
El servicio estará disponible en http://localhost:5000

4. Cliente Web (Angular)
Bash

cd web-client

# Instalar dependencias
npm install

# Ejecutar servidor de desarrollo
ng serve
Abre tu navegador en http://localhost:4200

5. Aplicación Móvil (Flutter)
Asegúrate de tener un emulador (Android/iOS) abierto o un dispositivo conectado.

Bash

cd mobile-app

# Obtener paquetes
flutter pub get

# Verificar configuración
flutter doctor

# Ejecutar app
flutter run
🔐 Variables de Entorno (.env)
A continuación, un ejemplo de las variables necesarias en el backend-api:

Properties

# Configuración del Servidor
PORT=3000
NODE_ENV=development

# Base de Datos
MONGO_URI=mongodb://localhost:27017/agri_platform_db

# Seguridad
JWT_SECRET=tu_secreto_super_seguro_cambiar_en_prod
JWT_EXPIRE=30d

# Integración con Servicios Externos
ML_SERVICE_URL=http://localhost:5000
MAPS_API_KEY=tu_google_maps_key
WEATHER_API_KEY=tu_weather_api_key
🧪 Ejecución de Tests
Para asegurar la integridad del código:

Backend:

Bash

cd backend-api
npm test
ML Service:

Bash

cd ml-service
pytest
🤝 Contribución
Haz un Fork del repositorio.

Crea una rama para tu feature (git checkout -b feature/NuevaFuncionalidad).

Haz Commit de tus cambios (git commit -m 'Add: Nueva funcionalidad X').

Haz Push a la rama (git push origin feature/NuevaFuncionalidad).

Abre un Pull Request.

📄 Licencia
Este proyecto está bajo la Licencia MIT - ver el archivo LICENSE.md para más detalles.