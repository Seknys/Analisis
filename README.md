# Analisis
## Web Scraping (Tema libre) a Neo4j (Base NoSQL)
- Importar las librerías (neo4j) necesarias para establecer la conexión con la base de datos y tambien las librerías de BeautifulSoup para realizar la extracción de datos mediante Web Scraping
```
from neo4j import GraphDatabase
import logging
from neo4j.exceptions import ServiceUnavailable
from bs4 import BeautifulSoup
import requests

```

-	Crear una clase y establecer funciones que permiten habilitar la conexión y cerrar esta
```
class App:

    def __init__(self, uri, user, password):
        self.driver = GraphDatabase.driver(uri, auth=(user, password))

    def close(self):
        # Don't forget to close the driver connection when you are finished with it
        self.driver.close()
```

-	Se crea una función que permite establecer la relación entre los datos que ya deben existir en la base de datos 
```
def create_friendship(self, person1_name, person2_name,total,Salud,Ataque,Defensa,
                          Velocidad_atck,Velocidad_defen,Velocidad):
        with self.driver.session() as session:
            result = session.write_transaction(
                self._create_and_return_friendship, person1_name, person2_name,total,Salud,
                Ataque,Defensa,Velocidad_atck,Velocidad_defen,Velocidad)
            for row in result:
                print("El pokemon {p1} es tipo {p2}".format(p1=row['p1'], p2=row['p2']))
```

-	Establecer la función principal, que permite recopilar la información en la base de datos, definiendo sus relaciones. Para esto la función tiene que recibir parámetros para que estos puedan ser enviados. (Estos parámetros son los extraídos mediante web scraping y cada uno es almacenado en una variable)
```
@staticmethod #Importacion de los datos
    def _create_and_return_friendship(tx, person1_name, person2_name, person3_name, person4_name, 
                                      person5_name, person6_name, person7_name, person8_name, 
                                      person9_name):
```

-	Query que crea los datos con los parámetros enviados anteriormente, ademas de establecer una relación entre todos estos. Debido a que la base de datos es una NoSQL de nodos ( estas relaciones son las uniones de nodos)
```
        query = ( 
            "CREATE (p1:Pokemon { name: $person1_name }) "
            "CREATE (p2:Pokemon { name: $person2_name }) "
            "CREATE (p3:Pokemon { name: $person3_name }) "
            "CREATE (p4:Pokemon { name: $person4_name }) "
            "CREATE (p5:Pokemon { name: $person5_name }) "
            "CREATE (p6:Pokemon { name: $person6_name }) "
            "CREATE (p7:Pokemon { name: $person7_name }) "
            "CREATE (p8:Pokemon { name: $person8_name }) "
            "CREATE (p9:Pokemon { name: $person9_name }) "
            "CREATE (p1)-[:TIPO]->(p2) "
            "CREATE (p1)-[:SALUD]->(p4) "
            "CREATE (p1)-[:ATAQUE]->(p5) "
            "CREATE (p1)-[:DEFENSA]->(p6) "
            "CREATE (p1)-[:VELOCIDAD_ATAQUE]->(p7) "
            "CREATE (p1)-[:VELOCIDAD_DEFENSA]->(p8) "
            "CREATE (p1)-[:VELOCIDAD]->(p9) "
            "CREATE (p1)-[:TOTAL]->(p3) "

            "RETURN p1, p2,p3,p4,p5,p6,p7,p8,p9"
        )
        result = tx.run(query, person1_name=person1_name, person2_name=person2_name, person3_name=person3_name, 
                        person4_name=person4_name, person5_name=person5_name, person6_name=person6_name, 
                        person7_name=person7_name, person8_name=person8_name, person9_name=person9_name)
```

-	Se crea un try and catch para capturer cualquier error que se pueda generar con el query enviado 
```
  try:
            return [{"p1": row["p1"]["name"], "p2": row["p2"]["name"],"p3": row["p3"]["name"], 
                     "p4": row["p4"]["name"],"p5": row["p5"]["name"], "p6": row["p6"]["name"],
                     "p7": row["p7"]["name"], "p8": row["p8"]["name"], "p9": row["p9"]["name"]}
                    for row in result]
        # Capture any errors along with the query and data for traceability
        except ServiceUnavailable as exception:
            logging.error("{query} raised an error: \n {exception}".format(
                query=query, exception=exception))
            raise
```


-	Credenciales que la proia base de datos nos genera, cuando la creamos por primera vez 
![image](https://user-images.githubusercontent.com/74793607/155898225-9b996462-3e81-4406-8973-e2ba461e2d53.png)

- Para extraer los datos de la página web debemos usar las librerías BeautifulSoup, se debe buscar los nombres de las clases de los datos que querremos recopilar ( En este ejemplo es una tabla )
```
            #Web Scraping#
tablelist=[]
url='https://pokemondb.net/pokedex/all'
req = requests.get(url)
soup = BeautifulSoup(req.text, 'html.parser')
league = soup.find('table',class_ = 'data-table block-wide')
for team in league.find_all('tbody'):
    rows = team.find_all('tr')
```

- Dentro de la tabla html  se crea un for que recorre todos los datos de la tabla, se crean variables para almacenar cada dato recopilado. Cuando existe algún mismo atributo con la misma clase para que las  funciones reconozcan los datos específicos que deseamos al final de los atributos y nombre de la clase se añade un corchete con el numero que el usuario desee
```
    for row in rows: 
        nombre = row.find('td', class_ = 'cell-name').text.strip()
        clase = row.find('td', class_ = 'cell-icon').text.strip()
        total = row.find('td', class_ = 'cell-total').text.strip()
        Salud = row.find_all('td',class_='cell-num')[1].text
        Ataque = row.find_all('td',class_='cell-num')[2].text
        Defensa = row.find_all('td',class_='cell-num')[3].text
        Velocidad_atck = row.find_all('td',class_='cell-num')[4].text
        Velocidad_defen = row.find_all('td',class_='cell-num')[5].text
        Velocidad = row.find_all('td',class_='cell-num')[6].text
```

- Dentro del mismo for se crea una variable con formato tipo json para almacenar los datos y depues estos mismos añadirlos a una lista
```
 teaminLeague = {
            'Nombre': nombre,
            'Tipo': clase,
            'Total': total,
            'Salud ': Salud,
            'Ataque':Ataque,
            'Defensa':Defensa,
            'Velocidad de Ataque': Velocidad_atck,
            'Velocidad de defensa': Velocidad_defen,
            'Velocidad': Velocidad
         }
```

- Dentro del mismo for llamamos a la función principal y le enviamos todas las variables requeridas y que fueron creadas y almacenadas con información anteriormente. Ademas cerramos la conexión una vez finalizada todo este proceso
```
        if __name__ == "__main__": ###########Enviar los datos 
            # Aura queries use an encrypted connection using the "neo4j+s" URI scheme
            app.create_friendship(nombre, clase,total,Salud,Ataque,Defensa,Velocidad_atck,Velocidad_defen,Velocidad)
            app.close()
```

- Se crea un archivo csv para el análisis de los datos en Power bi, para esto se importa la librería pandas y se crea un data frame y se lo transforma a csv
```
import pandas as pd
#################Creacion de un CSV
df= pd.DataFrame(tablelist)
df.to_csv('Tema Libre.csv')
```
- Se comprueba todos los datos subidos a neo4j mediante la propia herramienta que proporciona neo4j.
![image](https://user-images.githubusercontent.com/74793607/155897825-7aa603e3-77fa-41be-b5fd-e2c8a71beb65.png)

## Envio de datos hacia MongoDB

- Importar las librerías necesarias para establecer la conexión con Mongo DB y otras para enviar los datos 
```
from bs4 import BeautifulSoup
import requests
import pandas as pd
import pymongo
from pymongo import MongoClient
from pymongo.errors import ConnectionFailure
```

- Establecer la conexion con mongo db mediante las debidas credenciales
```
cliente = MongoClient('localhost',27017)
```

- Crear una nueva base de datos llamada Scraping y una colección llamada listas
```
db = cliente["Scraping"]
listas = db.listas
```

- Extraemos los datos mediante web Scraping
```

url='https://pokemondb.net/pokedex/all'
req = requests.get(url)
soup = BeautifulSoup(req.text, 'html.parser')
league = soup.find('table',class_ = 'data-table block-wide')
for team in league.find_all('tbody'):
    rows = team.find_all('tr')
    for row in rows: 
        num = row.find('td', class_ = 'cell-num cell-fixed').text.strip()
        nombre = row.find('td', class_ = 'cell-name').text.strip()
        clase = row.find('td', class_ = 'cell-icon').text.strip()
        total = row.find('td', class_ = 'cell-total').text.strip()
        Salud = row.find_all('td',class_='cell-num')[1].text
        Ataque = row.find_all('td',class_='cell-num')[2].text
        Defensa = row.find_all('td',class_='cell-num')[3].text
        Velocidad_atck = row.find_all('td',class_='cell-num')[4].text
        Velocidad_defen = row.find_all('td',class_='cell-num')[5].text
        Velocidad = row.find_all('td',class_='cell-num')[6].text
```

- Dentro del for se crea una variable para poder enviar todos los datos ala base de datos Mongo Db
```
        teaminLeague = [{
            '#': num,
            'Nombre': nombre,
            'Tipo': clase,
            'Total': total,
            'Salud ': Salud,
            'Ataque':Ataque,
            'Defensa':Defensa,
            'Velocidad de Ataque': Velocidad_atck,
            'Velocidad de defensa': Velocidad_defen,
            'Velocidad': Velocidad
         }]
```

-	Enviar los datos a la base de datos (Dentro del for)
```
        listas.insert_many(teaminLeague)
```

- Se comprueba que los datos fueron recopilados exitosamente en la base de datos MongoDB.
- ![image](https://user-images.githubusercontent.com/74793607/155898531-9889e6a7-32a4-44d3-af6d-155e3d8c4c22.png)






## Analisis
-	Para el analisis se utiliza la herrmienta Power  Bi que nos permite realizar graficos dependiendo del tipo de datos que deseemos analizar. Para este ejemplo vamos a analizar las estadisticas de cada pokemon, realizando graficos que relacionen el nombre, tipo y todas las estadisticas. Utilizando diferentes filtros como por categoria, por rango o por tipo se puede obtener muchas conclusiones, desde cual es el atributo mas fuerte de una clase en especifico, hasta una comparacion entre todos los atributos mediante un grafico de barras entre los pokemons escogidos.
![image](https://user-images.githubusercontent.com/74793607/155897802-2d9c6a10-128a-4bea-9938-ea98c202b9e8.png)


- En la siguiente imagen se puede observar como mendiante filtros somos capaces de recolectar informacion como :
El atributo mas alto de los pokemons que tienen un total (sumatoria de todas las estadisticas) entre 200 y 275. 
**Velocidad**
![image](https://user-images.githubusercontent.com/74793607/155898732-29bc0c38-b2cd-4e14-abb3-2bf837dbbaf0.png)
![image](https://user-images.githubusercontent.com/74793607/155898738-273485b8-6b4c-4a65-bd93-2ae7956dbdab.png)
- Otro tipo de analisis que se puede realizar, es cuando deseamos averiguar que pokemon tiene la salud mas alta en comparacion con los de su tipo de una clase especifica. 
**Emboar, Salud = 110**
![image](https://user-images.githubusercontent.com/74793607/155898890-c357ab99-7ae0-44f6-91b0-4be66dafde02.png)



## Web Scraping (Noticias) a PostgreSQL (Base SQL)

- Importar las librerías necesarias para establecer una conexión con la base PostgreSQL y las librerías de BeautifulSoup para extraer los datos de una pagina web
```
##Librerias
from bs4 import BeautifulSoup
import requests
import pandas as pd
import psycopg2
```
- Establecer la conexión con la base de datos con sus resectivas credenciales 
```
###Conexion####
conn = psycopg2.connect(host="localhost", database="webscraping", user="postgres", password="1234", port="5432")
conn.autocommit = True 
cur = conn.cursor()
```
- Mediante una instruccion sql enviamos un query que cree la table Noticias_Scraping con sus respectivos atributos
```
##Query
cur.execute('''CREATE TABLE Noticias_Scraping(N_Id int NOT NULL,\
Titulo text,\
Descripcion text,\
Autor varchar(50),Fuente varchar(50), Fecha varchar(50));''')
conn.commit()
```
- Inicializar variables que se usaran mas adelante
```
i=1
count = 2
tablelist=[]
url='https://www.bbc.com/mundo/topics/c2lej05epw5t'
```
- Con el while se puede definar cuantas paginas se desea extraer , ademas de buscar las debidas etiquetas en el código html ara poder extraer todos los datos deseados. Existen condicionales if que verifican que esa etiqueta tenga texto, caso contrario establecerá su variable como nulo
```
while count <=50 :
    req = requests.get(url)
    soup = BeautifulSoup(req.text, 'html.parser')
    league = soup.find('ol',class_ = 'gs-u-m0 gs-u-p0 lx-stream__feed qa-stream')
    for team in league.find_all('li', class_= 'lx-stream__post-container'):
        num = team.find('a', class_ = 'qa-heading-link lx-stream-post__header-link').text.strip()
        t = team.find('p', class_ = 'lx-stream-related-story--summary qa-story-summary')
        if t is not None:
            nombre= t.text.strip()
        else:
            nombre =None
        t2 = team.find('p', class_ = 'qa-contributor-name lx-stream-post__contributor-name gel-long-primer gs-u-m0')
        if t2 is not None:
            Tipo = t2.text.strip()
        else:
            Tipo = None
        t3 = team.find('p', class_ = 'qa-contributor-role lx-stream-post__contributor-description gel-brevier gs-u-m0')
        if t3 is not None:
            fuente=t3.text.strip()
        else :
            fuente = None
        t4 = team.find('span', class_ = 'gs-u-vh qa-visually-hidden-meta')
        if t4 is not None:
            fecha=t4.text.strip()
        else:
            fecha = None
```
- Se envían los datos mediante una instrucción query con los respectivos valores extraídos en el paso anterior
```
        cur.execute("INSERT INTO Noticias_Scraping VALUES (%s,%s,%s,%s,%s,%s)",(i,num,nombre,Tipo,fuente,fecha))
```
- Se crea un archivo formato json para almacenar los datos y posterior a eso enviar estos datos a una lista para que mediante la librería pandas se pueda crear un data frame y por consecuente un archivo csv. Por ultimo cerramos la conexión con la base de datos
```
        json ={
            "Titulo": num,
            "Descripcion": nombre,
            "Autor": Tipo,
            "Fuente": fuente,
            "Fecha": fecha
        }
        tablelist.append(json)
        i+=1
    subS = 'https://www.bbc.com/mundo/topics/c2lej05epw5t/page/'+ str(count)
    url = subS
    count+=1

df= pd.DataFrame(tablelist)
df.to_csv('Noticias.csv')
   
conn.close()
```
- Verificamos que los datos se hayan enviado correctamente hacia PostreSQL
![image](https://user-images.githubusercontent.com/74793607/155899278-5fc1bbcd-e4b7-4e5b-b46f-cf6add6abf67.png)

## Envio de datos hacia MongoDB
- Importar las librerías para establecer una conexión tanto con PostgreSQL y MongoDB`
```
import psycopg2
import pymongo
from pymongo import MongoClient
from pymongo.errors import ConnectionFailure
```
- Establecer conexión con la base de datos MongoDB con las respectivas credenciales
```
cliente = MongoClient('localhost',27017)
db = cliente["Noticias"]
Noticias = db.noticias
```
- Establecer conexión con la base de datos PostgreSQL con las respectivas credenciales 
```
conn = psycopg2.connect(host="localhost", database="webscraping", user="postgres", password="1234", port="5432")
cur = conn.cursor()
```
- Con una instrucción SQL extraemos todos los datos de la base de datos PostgreSQL
```
Query = "Select * from noticias_scraping "
cur.execute(Query)
```
- Con la función .fetchall() organizamos todos los datos estableciendo sus cabezeras para que estos se puedan exportar de una manera adecuada a la base de datos MongoDB
```
scraping= cur.fetchall()
```
- Con el uso de un for guardamos todos los datos en archivo tipo json con sus respectivas cabeceras.
```
for row in scraping:
    teamingLeague = [{
        "Id" : row[0],
        "Titulo" : row [1],
        "Descripcion" : row[2],
        "Autor" : row[3],
        "Fuente" : row[4],
        "Fecha" : row[5]
    }]
```
- Se envían los datos recopilados a la base de datos mongoDB y por ultimo cerramos la conexión con la base de datos PostgreSQL
```
    Noticias.insert_many(teamingLeague)
conn.close()
```
- Se comprueba que los datos se hayan enviado correctamente hacia MongoDB
![image](https://user-images.githubusercontent.com/74793607/155899480-3751e588-ddbe-494c-80b5-d1754c9e23fb.png)

## Analisis 
- Para el analisis  del dataset extraido mediante webscraping se utiliza herramientas que solo se relacionen con texto, ya que como el tema principal son noticias, es muy dificil poder ponderar estos datos y convertirlos en graficos estadisticos.
![image](https://user-images.githubusercontent.com/74793607/155899661-3e63dad8-e69f-4577-abc2-a9be8d27b290.png)

- En el siguiente ejemplo se puede observar como mediante un filtro de palabras y fuente  se puede visualizar las noticias que cumplan con todos estos filtros que hemnos establecido, Ademas que el grafico inferior se pueden observar las palabras que mas se repiten.
![image](https://user-images.githubusercontent.com/74793607/155899777-6677dce3-e7a8-4a73-9b4a-ef476bb7b708.png)








