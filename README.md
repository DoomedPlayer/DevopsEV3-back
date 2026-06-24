# ⚙️ Innovatech Chile - Backend Microservicios

Este repositorio contiene la lógica de negocio y la capa de acceso a datos para la plataforma de Innovatech Chile. El backend está estructurado bajo una arquitectura de microservicios independientes que se comunican con una base de datos centralizada, diseñados para ser escalables y tolerantes a fallos en un entorno Cloud nativo.

## 🏗️ Arquitectura de Microservicios
El ecosistema consta de dos microservicios principales:
1. **Microservicio de Ventas (`port: 8082`):** Gestiona la creación y consulta de las órdenes de compra de los clientes.
2. **Microservicio de Despachos (`port: 8081`):** Administra la logística, asignación de patentes de camión, control de intentos de entrega y cierre definitivo de las órdenes (estado entregado).

Ambos servicios están orquestados en AWS EKS. El tráfico es expuesto y distribuido internamente mediante balanceadores de carga nativos de AWS (ELB). Además, cuentan con **Horizontal Pod Autoscaler (HPA)**, configurado para escalar dinámicamente el número de réplicas si el consumo de CPU de los contenedores supera el 50%.

## 🚀 Instalación y Ejecución Local

### Prerrequisitos
* Java Development Kit (JDK) 17+
* Maven 3.8+
* MySQL Server (Local o mediante contenedor Docker)

### Configuración de Base de Datos
1. Iniciar una instancia de MySQL en el puerto `3306`.
2. Crear la base de datos definida en tu archivo `application.properties` (por ejemplo: `db_desarrollo`).
3. El motor de Hibernate (`spring.jpa.hibernate.ddl-auto=update` o `create`) creará automáticamente las tablas `venta` y `despacho` al ejecutar el proyecto, y se pueden inicializar datos de prueba con `data.sql`.

### Pasos de Ejecución
1. Clonar el repositorio:
   ```bash
   git clone https://github.com/DoomedPlayer/DevopsEV3-back.git
2. Compilar el proyecto y descargar dependencias:

    ```bash
    mvn clean install
3. Ejecutar los microservicios (debes levantar cada proyecto por separado):
    ```bash
    mvn spring-boot:run
### 🔄 Integración y Despliegue Continuo (CI/CD)
Este repositorio implementa automatización DevOps completa mediante GitHub Actions. Al recibir un git push en la rama principal:

1. El pipeline compila el código y genera los archivos ejecutables .jar de ambos microservicios.

2. Se construyen las imágenes Docker individuales para Ventas y Despachos.

3. Se realiza la subida (Push) de forma segura hacia un registro privado en Amazon ECR.

4. El pipeline despliega automáticamente las nuevas versiones en el clúster de AWS EKS mediante comandos kubectl, inyectando las credenciales de la base de datos de producción a través de secretos configurados en GitHub (GitHub Secrets), asegurando que ninguna contraseña o variable sensible quede expuesta en el código fuente.
