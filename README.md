# Proyecto-Final-TecDB1
# Entregable Final - Tecnología de Base de Datos I

* **Asignatura:** Tecnología de Base de Datos I
* **Estudiante:** Luis Rodrigo Aguilar Justiniano
* **Fecha de Entrega:** 29 de Septiembre

## Descripción del Repositorio
Este repositorio contiene la solución completa para la migración de la base de datos `employees` desde MariaDB hacia PostgreSQL 18 utilizando Docker, `pgloader` y consultas de verificación de integridad.

## Contenido
* **`INFORME.md`**: Informe detallado con los comandos ejecutados, salidas de procesos, logs de migración, creación de vistas y resultados de las consultas de verificación.
* **`migracion.load`**: Archivo de configuración utilizado por `pgloader` para la transferencia y adaptación de esquemas, tablas y tipos de datos.
* **`employees_backup.dump`**: Archivo de respaldo (backup) de la base de datos PostgreSQL ya migrada y verificada.
