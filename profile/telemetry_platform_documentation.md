# Documentación Técnica - Sistema de Telemetría y Gestión de Alarmas para Equipos de Extracción de Petróleo y Gas

## Índice
1. [Descripción General](#descripción-general)
2. [Arquitectura del Sistema](#arquitectura-del-sistema)
   - [Broker MQTT](#broker-mqtt)
   - [Procesamiento en AWS Lambda](#procesamiento-en-aws-lambda)
   - [Microservicios](#microservicios)
   - [Base de Datos](#base-de-datos)
3. [Componentes del Sistema](#componentes-del-sistema)
   - [Backend Principal](#backend-principal)
   - [Frontend](#frontend)
4. [Funcionalidades Clave](#funcionalidades-clave)
   - [Métricas Históricas y en Tiempo Real](#métricas-históricas-y-en-tiempo-real)
   - [Gestión de Alarmas](#gestión-de-alarmas)
5. [Tecnologías Utilizadas](#tecnologías-utilizadas)
6. [Próximos Pasos y Mejoras](#próximos-pasos-y-mejoras)

---

## Descripción General

Este sistema ofrece un servicio de telemetría que monitorea equipos ubicados en locaciones de extracción de petróleo y gas. La aplicación centraliza dos funciones principales:
1. **Generación de métricas históricas y en vivo**: los equipos envían datos en tiempo real que luego se procesan para brindar métricas precisas y trazabilidad.
2. **Gestión de alarmas**: permite abrir o cerrar alarmas basadas en reglas configuradas y notificar a los contactos asignados.

Los datos de telemetría son enviados mediante protocolo MQTT desde los dispositivos, los cuales están conectados a un *broker* que recibe la información y la redirige para su procesamiento y almacenamiento.

## Arquitectura del Sistema

### Broker MQTT
- **Función**: Recibe datos en tiempo real desde los equipos en campo y los redirige a un sistema de AWS para su procesamiento.
- **Ubicación**: Cuenta de AWS dedicada al servicio.
- **Flujo de datos**: Los datos del *broker* se dirigen a *lambdas* específicas para cada modelo de dispositivo que realiza la parseo y procesamiento inicial.

### Procesamiento en AWS Lambda
Cada modelo de dispositivo cuenta con una *Lambda* específica que realiza las siguientes tareas:
1. **Parseo de datos**: Convierte los datos crudos en un formato procesable.
2. **Ingesta de datos**: Envía los datos a la base de datos principal en TimescaleDB.
3. **Evaluación de reglas**: Llama al microservicio de alarmas para evaluar si las métricas recibidas infringen alguna regla.
4. **Gestión de alarmas**: Si una regla es infringida, abre o cierra alarmas en el sistema y solicita notificaciones a los contactos asignados.

### Microservicios
El sistema sigue una arquitectura basada en microservicios para realizar operaciones específicas:
- **Microservicio de Alarmas**: Evaluación de reglas y gestión de alarmas (apertura y cierre).
- **Microservicio de Asignaciones**: Determina los contactos que deben recibir notificaciones en caso de incidentes.
- **Microservicio de Notificaciones**: Envía las notificaciones a los contactos vía correo o mensaje según sea configurado.

Estos microservicios están desplegados en AWS AppRunner y se actualizan automáticamente a través de GitHub Actions.

### Base de Datos
- **TimescaleDB**: Base de datos principal en PostgreSQL, utilizada para almacenar métricas históricas y en vivo. Actualmente, esta base presenta problemas de diseño que afectan su rendimiento, por lo que es un área a mejorar.
- **DynamoDB**: Base de datos secundaria en AWS, cuyo uso se está reduciendo y se prevé su descontinuación.
- **Redis**: Base de datos de caché en Redis para mejorar la velocidad de acceso a ciertos datos. Actualmente, sólo está integrada en algunos proyectos, pero se planea extender su uso.

## Componentes del Sistema

### Backend Principal
- **Tecnologías**: NestJS, Sequelize, Typescript, Express.
- **Alojamiento**: AWS ECS.
- **Funcionalidad**: Es el monolito principal que gestiona la lógica de negocio y el sistema de autorización basado en roles.
- **Objetivo a futuro**: Migración de funcionalidades a microservicios específicos para aligerar la carga del backend principal.

### Frontend
- **Tecnologías**: React con Typescript y NextJS.
- **Funcionalidad**: Interfaz de usuario para monitoreo de métricas, visualización de alarmas, y gestión de contactos asignados a las alarmas.

## Funcionalidades Clave

### Métricas Históricas y en Tiempo Real
- **Descripción**: Los equipos envían datos a través de MQTT que son almacenados en TimescaleDB. La aplicación permite visualizar métricas de rendimiento y registros históricos.
- **Objetivo**: Proveer de información en tiempo real para la toma de decisiones y el análisis histórico de rendimiento.

### Gestión de Alarmas
- **Evaluación de Reglas**: Cada equipo tiene reglas definidas que, si son infringidas, activan una alarma. Las reglas pueden variar entre equipos.
- **Notificación**: Cuando una regla se infringe, el sistema busca contactos asignados y les envía una notificación.
- **Cierre de Alarma**: Si las condiciones vuelven a la normalidad, la alarma se cierra automáticamente.

## Tecnologías Utilizadas

- **Backend**: NestJS, Sequelize, Express, Typescript.
- **Frontend**: React, NextJS, Typescript.
- **Base de Datos**: TimescaleDB (PostgreSQL), DynamoDB (AWS), Redis (Cache).
- **Protocolos**: MQTT (para la comunicación entre dispositivos).
- **Autenticación**: Auth0.
- **AWS Services**:
  - ECS (Backend Principal)
  - ECR y AppRunner (Microservicios)
  - Lambdas (Procesamiento de datos de dispositivos)
  - GitHub Actions (Automatización de despliegues)

## Próximos Pasos y Mejoras

1. **Optimización de la Base de Datos**: Rediseñar la estructura de TimescaleDB para mejorar el rendimiento y solucionar problemas actuales de relaciones y conceptualización.
2. **Migración a Microservicios**: Despliegue progresivo de funcionalidades del monolito principal a microservicios específicos para mejorar la escalabilidad.
3. **Integración Completa de Redis**: Expansión de la integración de Redis a todos los proyectos para mejorar la velocidad de acceso y el rendimiento general.
4. **Migración de DynamoDB**: Reducir el uso de DynamoDB y descontinuarlo gradualmente.
5. **Automatización de Despliegue de Lambdas**: Integración completa de Lambdas en GitHub con despliegue automatizado en AWS para mejorar el proceso de desarrollo y actualización.

---

**Última Actualización:** Octubre 2024
