# Analisis
## Web Scraping (Tema libre) a Neo4j (Base NoSQL)
- Importar las librerías (neo4j) necesarias para establecer la conexión con la base de datos y tambien las librerías de BeautifulSoup para realizar la extracción de datos mediante Web Scraping

![image](https://user-images.githubusercontent.com/74793607/155894106-dcaf0969-b967-4293-a82a-4a9e00be2162.png)

-	Establecer funciones que permiten establecer la conexión y cerrar esta y se crea una clase
![image](https://user-images.githubusercontent.com/74793607/155894329-d7d71256-4c57-4e6f-bacd-a30daf6e5e8a.png)

-	Se crea una función que permite establecer la relación entre los datos que ya deben existir en la base de datos 
![image](https://user-images.githubusercontent.com/74793607/155894343-55c14f7e-c268-4af6-b6a9-232fbadf8d1d.png)

-	Establecer la función principal, que permite recopilar la información en la base de datos, definiendo sus relaciones. Para esto la función tiene que recibir parámetros para que estos puedan ser enviados. (Estos parámetros son los extraídos mediante web scraping y cada uno es almacenado en una variable)
![image](https://user-images.githubusercontent.com/74793607/155894410-787ee4af-bb7e-4826-836b-5cdb029a132c.png)

-	Query que crea los datos con los parámetros enviados anteriormente, ademas de establecer una relación entre todos estos. Debido a que la base de datos es una NoSQL de nodos ( estas relaciones son las uniones de nodos)
![image](https://user-images.githubusercontent.com/74793607/155894419-c6bbf088-bf73-4aca-81d3-35e2311daa67.png)

-	Se crea un try and catch para capturer cualquier error que se pueda generar con el query enviado 
![image](https://user-images.githubusercontent.com/74793607/155894432-1381b584-6e15-4780-8d35-9441a9bb5ab8.png)

-	Credenciales que la proia base de datos nos genera, cuando la creamos por primera vez 
![image](https://user-images.githubusercontent.com/74793607/155894273-49ed8eb4-83ee-4874-9a6f-ce24f03fbec0.png)
- Para extraer los datos de la página web debemos usar las librerías BeautifulSoup, se debe buscar los nombres de las clases de los datos que querremos recopilar ( En este ejemplo es una tabla )
![image](https://user-images.githubusercontent.com/74793607/155894454-c1d828c9-e99b-4020-a4e0-64088ab3f803.png)

- Dentro de la tabla html  se crea un for que recorre todos los datos de la tabla, se crean variables para almacenar cada dato recopilado. Cuando existe algún mismo atributo con la misma clase para que las  funciones reconozcan los datos específicos que deseamos al final de los atributos y nombre de la clase se añade un corchete con el numero que el usuario desee
![image](https://user-images.githubusercontent.com/74793607/155894475-498975ad-8fea-4f07-9b79-f265a5419539.png)

- Dentro del mismo for se crea una variable con formato tipo json para almacenar los datos y depues estos mismos añadirlos a una lista
![image](https://user-images.githubusercontent.com/74793607/155894500-d78cc22c-ea8d-4bc0-afd6-d70786c4d84a.png)

- Dentro del mismo for llamamos a la función principal y le enviamos todas las variables requeridas y que fueron creadas y almacenadas con información anteriormente. Ademas cerramos la conexión una vez finalizada todo este proceso
![image](https://user-images.githubusercontent.com/74793607/155894514-39f347e4-140e-4c22-8c66-b3569f0b2eab.png)

- Se crea un archivo csv para el análisis de los datos en Power bi, para esto se importa la librería pandas y se crea un data frame y se lo transforma a csv
![image](https://user-images.githubusercontent.com/74793607/155894520-571dfb5b-5431-4882-b7f3-e52885903feb.png)

## Envio de datos hacia MongoDB

- Importar las librerías necesarias para establecer la conexión con Mongo DB y otras para enviar los datos 
![image](https://user-images.githubusercontent.com/74793607/155895003-b0b56153-9d69-4ca5-8d81-2237a996cc45.png)

- Establecer la conexion con mongo db mediante las debidas credenciales
![image](https://user-images.githubusercontent.com/74793607/155895079-e9514a79-3876-4696-84f0-e8870c165801.png)

- Crear una nueva base de datos llamada Scraping y una colección llamada listas
