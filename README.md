# Proyecto-Limpieza-de-datos
## Descripción del Proyecto
Este proyecto demuestra un flujo completo de Limpieza de Datos (Data Cleansing) y Análisis Estadístico utilizando SQL Server. El dataset original, obtenido de fuentes externas y diseñado inicialmente para MySQL, fue migrado y optimizado para T-SQL (Microsoft SQL Server), aplicando técnicas avanzadas de manipulación de strings, manejo de fechas y lógica de negocio.

## Tecnoligias usadas
Motor de Base de Datos: Microsoft SQL Server 2025
Lenguaje: T-SQL (Transact-SQL)
### Técnicas: Stored Procedures, CTEs, Window Functions (RANK), Data Casting y Normalización.

## Proceso de Limpieza y Transformación (ETL)
### El objetivo fue convertir un dataset crudo y ruidoso en una tabla optimizada para la toma de decisiones. Las etapas incluyeron:
### Estandarización de Esquema: Renombramiento masivo de columnas para cumplir con estándares de nomenclatura profesional mediante sp_rename.
### Integridad de Datos: Identificación y eliminación de registros duplicados utilizando tablas temporales y filtrado por ID_Empleado.
### Refactorización de Strings: * Eliminación de espacios redundantes (LTRIM/RTRIM).
### Limpieza de caracteres especiales y corrección de espacios intermedios.
### Traducción y normalización de categorías (ej. de 'Hombre/Mujer' a 'Male/Female').
### Optimización de Tipos de Datos: Conversión de columnas VARCHAR a DECIMAL, INT y DATE para habilitar cálculos matemáticos y cronológicos.
### Parsing Complejo de Fechas: Implementación de lógica CASE y CONVERT para manejar múltiples formatos de fecha en una sola columna (ej. 'DD/MM/YYYY' vs 'YYYY-MM-DD').
### Automatización: Creación de Stored Procedures para la visualización dinámica y control de versiones de los datos limpios.

## Análisis de Datos y Business Intelligence
### Una vez procesada la información, se ejecutaron consultas de nivel intermedio y avanzado para extraer insights clave:
### Análisis de Planilla: Cálculo de gasto total por área y comparativa de salarios promedio entre modalidades Full-time vs Part-time.
### Gestión de Talento (Retention): Identificación de tasas de deserción por departamento y antigüedad promedio de la plantilla activa.
### Demografía y Segmentación: Agrupación de empleados por rangos generacionales (Juniors, Seniors, Veteranos) mediante el cálculo dinámico de edades.
### Analítica Avanzada: Uso de Funciones de Ventana (RANK, PARTITION BY) para determinar el posicionamiento salarial relativo de cada empleado dentro de su respectiva área.

