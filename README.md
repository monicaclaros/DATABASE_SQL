![portada](https://github.com/monicaclaros/DATABASE_SQL/blob/main/images/blockbusterlogo.jpg)

# DATABASE_SQL

# BLOCKBUSTER RETRO CHALLENGE

Para la limpieza de datos de este archivo importado desde .csv, he realizado los siguientes pasos, cada uno me llevó a alguna observación o conclusion.
La exploración y limpieza está trabajada en visual code con pandas sql

He creado una funciona de esquema para tener una escaleta de eventos:

        1. #Importar Librerías
        2. #SqlAlchemy
        3. #Crear conexión a la base de datos
        4.#Revision de columnas.
        5.#Examinar los datos en busca de posibles problemas como los valores nulos
        6.# Revisar tablas desde CSV explorar csv
        7.# Primero Rental: aquí eliminar la columna 'last_uptdate' 
        8.#Para el archivo de pelis, revisamos film
        9. #Explorar el archivo film con datos null
        10.#dejar en blanco release year porque no contiene letras ni num
        11.#removemos last_update
        12.#Las pelis están en ingles, esto lo reflejamos con English
        13.#vaciar original language, si son nulos lo dejamos así
        14.# En inventario es necesario editar las columnas 'last_update'
        15.#exploramos tabla inventario
        16.# Hace falta una base de datos sobre clientes, entonces se crea el dataframe 'client' con los ids de todos los clientes ya 
        existentes.
        17.# Crear la tabla "actor" y cargar los datos desde el archivo "actor.csv"
        18.# Crear la tabla "category" y cargar los datos desde el archivo "category.csv"
        19.# Crear la tabla "film" y cargar los datos desde el archivo "film.csv"
        20.# Crear la tabla "language" y cargar los datos desde el archivo "language.csv"
        21.#Completamos llevando las tablas a sql
        22.#insertamos valores con funciones
Sucesos:
En estos códigos pude crar las tablas desde visual code al workbench de 4 tablas:
"actor" "category" "film" y  "language" ; pero las otras 3 tablas no las pude subir, ma salían errores a razón del orden de creación, es que hay tablas que dependen de otra y esas contradicciones han evitado crearla desde acá para mi caso.
Con esto determiné seguir con códigos de SQL en querys para crear tablas y su contenido otra vez con python

# SQL códigos en workbench
        
        23.#verificamos la conexión
        24.#creamos tablas
SHOW DATABASES;

CREATE TABLE rental (
    rental_id INT PRIMARY KEY,
    rental_date DATETIME,
    inventory_id INT,
    customer_id INT,
    staff_id TINYINT,
    return_date DATETIME,
    last_update TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
CREATE TABLE client (
    customer_id INT PRIMARY KEY,
    name CHAR(20) NOT NULL,
    lastname CHAR(20) NOT NULL,
    phone INT,
    mail VARCHAR(255) NOT NULL
);
CREATE TABLE inventory (
    inventory_id INT PRIMARY KEY,
    film_id INT,
    store_id TINYINT,
    last_update TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    CONSTRAINT fk_inventory_film FOREIGN KEY (film_id) REFERENCES film (film_id),
    CONSTRAINT fk_inventory_store FOREIGN KEY (store_id) REFERENCES store (store_id)
);
SELECT rental_id AS RENTALS, rental_date AS 'RENTAL DATE', rental.inventory_id AS 'INVENTORY ID', customer_id AS 'CUSTOMER ID', 
       return_date AS 'RETURN DATE', staff_id AS 'STAFF ID', title as TITLE, 
       (DATEDIFF(return_date, rental_date) + 1) AS DAYS,
       (rental_rate * (DATEDIFF(return_date, rental_date) + 1)) AS INCOME
       
FROM rental
LEFT JOIN inventory ON inventory.inventory_id = rental.inventory_id
LEFT JOIN films ON inventory.film_id = films.film_id
;

SELECT inventory.inventory_id AS 'INVENTORY ID', film_id AS 'FILM ID', store_id AS 'STORE ID', 
        CASE
        WHEN rental_date IS NOT NULL AND return_date = '' AND rental.inventory_id IS NOT NULL THEN 'NOT AVAILABLE'
        ELSE 'AVAILABLE'
    END AS AVAILABILITY

FROM inventory

LEFT JOIN rental ON rental.inventory_id = inventory.inventory_id
;








