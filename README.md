# Proyecto-Limpieza-de-datos
## Descripción del Proyecto
Este proyecto demuestra un flujo completo de Limpieza de Datos (Data Cleansing) y Análisis Estadístico utilizando SQL Server. El dataset original, obtenido de fuentes externas y diseñado inicialmente para MySQL, fue migrado y optimizado para T-SQL (Microsoft SQL Server), aplicando técnicas avanzadas de manipulación de strings, manejo de fechas y lógica de negocio.

## Tecnoligias usadas
* Motor de Base de Datos: Microsoft SQL Server 2025
* Lenguaje: T-SQL (Transact-SQL)
* Técnicas: Stored Procedures, CTEs, Window Functions (RANK), Data Casting y Normalización.

## Proceso de Limpieza y Transformación (ETL)
* El objetivo fue convertir un dataset crudo y ruidoso en una tabla optimizada para la toma de decisiones. Las etapas incluyeron:
* Estandarización de Esquema: Renombramiento masivo de columnas para cumplir con estándares de nomenclatura profesional mediante sp_rename.
* Integridad de Datos: Identificación y eliminación de registros duplicados utilizando tablas temporales y filtrado por ID_Empleado.
* Refactorización de Strings: * Eliminación de espacios redundantes (LTRIM/RTRIM).
* Limpieza de caracteres especiales y corrección de espacios intermedios.
* Traducción y normalización de categorías (ej. de 'Hombre/Mujer' a 'Male/Female').
* Optimización de Tipos de Datos: Conversión de columnas VARCHAR a DECIMAL, INT y DATE para habilitar cálculos matemáticos y cronológicos.
* Parsing Complejo de Fechas: Implementación de lógica CASE y CONVERT para manejar múltiples formatos de fecha en una sola columna (ej. 'DD/MM/YYYY' vs 'YYYY-MM-DD').
* Automatización: Creación de Stored Procedures para la visualización dinámica y control de versiones de los datos limpios.

## Análisis de Datos y Business Intelligence
* Una vez procesada la información, se ejecutaron consultas de nivel intermedio y avanzado para extraer insights clave:
* Análisis de Planilla: Cálculo de gasto total por área y comparativa de salarios promedio entre modalidades Full-time vs Part-time.
* Gestión de Talento (Retention): Identificación de tasas de deserción por departamento y antigüedad promedio de la plantilla activa.
* Demografía y Segmentación: Agrupación de empleados por rangos generacionales (Juniors, Seniors, Veteranos) mediante el cálculo dinámico de edades.
* Analítica Avanzada: Uso de Funciones de Ventana (RANK, PARTITION BY) para determinar el posicionamiento salarial relativo de cada empleado dentro de su respectiva área.

### 1. Cálculo de Rangos de Edad
Para responder a la pregunta de negocio **"¿Cuál es el rango de edad de los empleados?"**, se utilizaron funciones de diferencia de fechas y lógica condicional para agrupar a los colaboradores en cuatro niveles generacionales.

#### **Criterios de Clasificación:**
* **Juniors:** 0 a 20 años.
* **Semisenior:** 21 a 30 años.
* **Senior:** 31 a 50 años.
* **Veteranos:** Más de 50 años.

#### **Implementación Técnica (T-SQL):**
A diferencia de la versión original en MySQL, aquí se optimizó el uso de `DATEDIFF` y `CASE` para garantizar la precisión de los cálculos en el entorno de SQL Server.

```sql
/* Determinación de la distribución de la plantilla activa 
por categorías generacionales.
*/

SELECT 
    CASE
        WHEN DATEDIFF(YEAR, Birth_Date, GETDATE()) BETWEEN 0 AND 20 THEN 'Juniors'
        WHEN DATEDIFF(YEAR, Birth_Date, GETDATE()) BETWEEN 21 AND 30 THEN 'Semisenior'
        WHEN DATEDIFF(YEAR, Birth_Date, GETDATE()) BETWEEN 31 AND 50 THEN 'Senior'
        ELSE 'Veteranos'
    END AS Categoria_Edad,
    COUNT(id_empleado) AS Q_empleados
FROM limpiezalista
WHERE Finish_date IS NULL -- Filtrado de empleados activos
GROUP BY 
    CASE
        WHEN DATEDIFF(YEAR, Birth_Date, GETDATE()) BETWEEN 0 AND 20 THEN 'Juniors'
        WHEN DATEDIFF(YEAR, Birth_Date, GETDATE()) BETWEEN 21 AND 30 THEN 'Semisenior'
        WHEN DATEDIFF(YEAR, Birth_Date, GETDATE()) BETWEEN 31 AND 50 THEN 'Senior'
        ELSE 'Veteranos'
    END;


## Analítica Avanzada: Rankings y Comparativas Salariales

En esta fase, se aplicaron **Window Functions** (Funciones de Ventana) para realizar análisis comparativos profundos sin perder el detalle individual de cada registro. Este enfoque es fundamental para auditorías salariales y equidad interna.

### 1. Ranking Salarial por Departamento
Para identificar a los empleados con mayor remuneración dentro de sus respectivas áreas, se utilizó la función `RANK()`. 

* **Lógica:** Se particionan los datos por `area` y se ordenan de forma descendente por `salary`. Esto permite ver el lugar exacto que ocupa cada colaborador en su "escala salarial local".

```sql
/* Determinación del escalafón salarial por área
*/

SELECT 
    last_name, 
    area, 
    salary,
    RANK() OVER(
        PARTITION BY area 
        ORDER BY salary DESC
    ) AS ranking
FROM limpiezalista;
