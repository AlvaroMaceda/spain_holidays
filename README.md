# Vacaciones de España

Este repositorio contiene los días festivos de España en formato csv para poder ser utilizadas en procesos automáticos. El objetivo es poder cargarlas en algún gestor de base de datos y explotarlas desde allí.

Las vacaciones se actualizan de forma manual, de modo que puede que no estén todos los años ni todos los municipios. 

Si detectas alguna incorrección o quieres completar datos puedes enviar una pull request para integrarlo en el repositorio tal y como se indica en la sección "Contribuir en el proyecto"

## Ficheros

Hay dos tipos de fichero:

- Fichero de estructura: contiene las entidades sobre las que se aplican las vacaciones
- Ficheros de vacaciones: contiene las vacaciones de cada año para las entidades del fichero de estructura

### Ficheros de vacaciones

Los ficheros de vacaciones están estructurados por año. las vacaciones de cada año están en un directorio con ese año de nombre.

Cada directorio contiene dos ficheros: YYYY_structure_holidays y YYYY_municipality_holidays. El primero contiene las vacaciones nacionales y de comunidades autónomas (incluyendo regiones e islas) y el segundo la vacaciones de cada municipio y entidad local. 

Los ficheros tienen la misma estructura, y se han dividido para poder cargar únicamente los ficheros con vacaciones de nivel superior al municipio si es necesario.

- code: código del territorio al que pertenecen las vacaciones. El código debe corresponderse con un territorio del fichero de estructura. El código se repite tantas veces como vacaciones tenga ese territorio.
- name: Este campo es opcional y contiene el nombre del municipio. Sirve únicamente para ayudar en la edición del fichero, ya que el nombre ya está en el fichero de estructura
- date:	fecha del día festivo, en formato dd/mm/yyyy
- description: descripción opcional de la festividad

### Fichero de estructura

El fichero de estructura contiene los datos de territorios donde se van a aplicar las vacaciones. El fichero contiene cuatro campos: parent_code, kind, code y name. 

#### kind

Este campo contiene el tipo de territorio. Los valores que puede tomar son:

- ```country```: sólo hay un registro de este tipo (España)
- ```autonomous_community```: se corresponde con las diferentes comunidades autónomas
- ```island```: este tipo de registros se corresponde con las islas que tienen vacaciones propias 
- ```region```: regiones (territorios de nivel inferior a la comunidad autónoma) que tienen vacaciones propias
- ```municipality```: municipios
- ```local_entity```: entidades locales (territorios de nivel inferior al municipio) que tienen vacaciones propias

#### code

La estructura del campo código varía dependiendo del tipo de territorio:

- CT_ESP para españa
- ACnnnnn para las diferentes comunidades autónomas
- IS_xxx para las diferentes islas
- RE_xxxx para las regiones
- ESppnnn para los diferentes municipios, donde pp es el número de provincia y nnn el número de municipio dentro de la provincia (se corresponden con los campos CPRO y CMUN del INE: https://www.ine.es/daco/daco42/codmun/codmunmapa.htm)
- ESppnnnLn para las entidades locales

#### parent_code

Indica el territorio "padre" del territorio en cuestión. Sirve para recorrer la jerarquía de territorios. Por ejemplo, el siguiente registro:

```
parent_code kind          code     name
IS_LAN      municipality  ES35018  San Bartolomé
```

Se corresponde al municipio de San Bartolomé. El campo ```parent_code``` contiene el valor ```IS_LAN``` que corresponde con el registro:

```
parent_code kind          code     name
AC00005     island        IS_LAN   Lanzarote
```
Por lo tanto, San Bartolomé pertenece a la isla de Lanzarote (aparece dicha isla porque tiene vacaciones propias) A su vez, el padre de Lanzarote es ```AC00005``` que corresponde a:

```
parent_code kind          code     name
CT_ESP	autonomous_community	AC00005	Canarias
```

El registro es el correspondiente a la comunidad autónoma de Canarias. A su vez, el padre de este territorio es:

```
parent_code kind          code     name
""          country       CT_ESP   España
```

Que se corresponde con España. Es el territorio de nivel superior y, por lo tanto, parent_code está en blanco.

De modo que la jerarquía sería:

San Bartolomé -> Lanzarote -> Canarias -> España

La jerarquía puede variar: un territorio puede ser una entidad local que a su vez pertenece a un municipio, puede o no pertenecer a una isla o a una región... El proceso general es ir "siguiendo" el campo parent_code hasta llegar a la raíz.


#### name

Nombre del municipio. No contiene el nombre oficial: el objetivo es facilitar la búsqueda de territorios, de modo que en ocasiones está el nombre tanto en el idioma oficial como en castellano sparados por el carácter "/"


## Como saber las vacaciones de un territorio

Calcular las vacaciones de un territorio depende de qué tipo sean. El proceso general sería el siguiente:

1) Obtener las vacaciones del territorio buscando por su código en las vacaciones del año
2) Obtener el territorio "padre" a través del campo ```parent_code``` del territorio
3) Obtener las vacaciones del territorio padre
4) Repetir el proceso hasta llegar al territorio de nivel superior (España)

Por ejemplo, para San Bartolomé (ES35018) habría que incluir:
- Las vacaciones de San Bartolomé
- Las vacaciones de la isla de Lanzarote
- Las vacaciones de Canarias
- Las vacaciones de España

La única excepción es con las entidades locales: en las entidades locales NO hay que incluir las vacaciones del municipio, que es la entidad directamente superior.

## Contribuir al proyecto

Si quieres contribuir a completar las vacaciones o a corregir algún error, simplemente envía una pull request con las vacaciones. En la pull request debes incluir un enlace al documento oficial de donde has sacado la información de las vacaciones (BOE, publicación oficial de comunidad autónoma, etc.)

## Autores

* **Álvaro Maceda** - *Carga inicial* - [PurpleBooth](https://github.com/PurpleBooth)

## Licencia

Estos datos están publicados bajo la [Open Data Commons Open Database License (ODbL) v1.0](https://opendatacommons.org/licenses/odbl/1-0/) con la excepción de que no pueden ser usados por la Generalitat Valenciana ni ninguna entidad, pública o privada, dependiente de  ella.



