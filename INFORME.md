# Informe Final: Migración de MariaDB a PostgreSQL (Base de Datos Employees)

* **Asignatura:** Tecnología de Base de Datos I
* **Estudiante:** Luis Rodrigo Aguilar Justiniano
* **Fecha:** 29 de Septiembre de 2026
* **Herramientas Utilizadas:** Docker, Docker Compose, MariaDB, PostgreSQL 18, pgloader, psql, DBeaver[cite: 1].

---

## 1. Migración de Tablas (Punto 1)

Para realizar la migración automatizada de esquemas, tipos de datos, tablas y registros desde MariaDB hacia PostgreSQL, se utilizó la herramienta **pgloader** a través de un contenedor Docker con la configuración establecida en el archivo `migracion.load`.

### 1.1 Salida del Proceso (Logs de pgloader)
```text
2026-09-29T01:01:05.043987Z LOG pgloader version "3.6.7~devel"
2026-09-29T01:01:05.539852Z LOG Migrating from #<MYSQL-CONNECTION mysql://root@host.docker.internal:3306/employees {1007E84F13}>
2026-09-29T01:01:05.539852Z LOG Migrating into #<PGSQL-CONNECTION pgsql://luis@host.docker.internal:5432/employees {1007E85F03}>
2026-09-29T01:02:04.152781Z LOG report summary reset
                       table name     errors         rows         bytes      total time
-----------------------  ---------  ---------  ---------  --------------
         fetch meta data          0         21                     1.024s
            Create Schemas          0          0                     0.000s
          Create SQL Types          0          0                     0.016s
             Create tables          0         12                     0.152s
            Set Table OIDs          0          6                     0.032s
-----------------------  ---------  ---------  ---------  --------------
      employees.salaries          0    2844047    94.2 MB         36.921s
        employees.titles          0     443308    16.9 MB          9.610s
     employees.employees          0     300024    13.2 MB         12.499s
   employees.departments          0          9     0.1 kB          0.028s
      employees.dept_emp          0     331603    10.7 MB          5.044s
  employees.dept_manager          0         24     0.8 kB          0.088s
-----------------------  ---------  ---------  ---------  --------------
 COPY Threads Completion          0          8                    36.889s
          Create Indexes          0          9                    21.432s
Index Build Completion          0          9                    14.920s
           Reset Sequences          0          0                     1.204s
              Primary Keys          0          6                     0.048s
     Create Foreign Keys          0          6                     3.616s
           Create Triggers          0          0                     0.004s
          Install Comments          0          0                     0.000s
-----------------------  ---------  ---------  ---------  --------------
       Total import time          ✓    3919015   134.9 MB       1m18.113s

### 1.2 Conteo de Filas
    Tabla	Filas Migradas	Estado
    departments	9	Éxito
    dept_emp	331,603	Éxito
    dept_manager	24	Éxito
    employees	300,024	Éxito
    salaries	2,844,047	Éxito
    titles	443,308	Éxito


2. Migración de Vistas (Punto 2)
2.1 Creación de la Vista en PostgreSQL

        CREATE OR REPLACE VIEW dept_emp_latest_date AS
        SELECT emp_no, MAX(from_date) AS from_date, MAX(to_date) AS to_date
        FROM dept_emp
        GROUP BY emp_no;

2.2 Prueba de Funcionamiento
    
    SELECT * FROM dept_emp_latest_date LIMIT 5;

            emp_no | from_date  |  to_date   
            --------+------------+------------
            10001 | 1986-06-26 | 9999-01-01
            10002 | 1996-08-03 | 9999-01-01
            10003 | 1995-12-03 | 9999-01-01
            10004 | 1986-12-01 | 9999-01-01
            10005 | 1989-09-12 | 9999-01-01
            (5 rows)

3. Consultas de Verificación (Punto 3)
3.1 Verificación de Integridad Referencial (Registros Huérfanos)

        SELECT COUNT(*) AS empleados_huerfanos 
        FROM dept_emp de 
        LEFT JOIN employees e ON de.emp_no = e.emp_no 
        WHERE e.emp_no IS NULL;


        empleados_huerfanos 
        ---------------------
                        0
        (1 row)

3.2 Suma de Control (Checksum) de Salarios

SELECT SUM(salary) AS total_salarios FROM salaries;

total_salarios   
-------------------
      181480757419
(1 row)