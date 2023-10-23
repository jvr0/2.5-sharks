# w2pandas

## 1_general_cleaning
### Inicio de la limpieza
Lo primero, por seguridad, realizamos una copia del df para poder realizar cambios sin preocuparnos de la versión original de los datos.

Iniciamos la limpieza comprobando los valores nulos. En una primera observación encontramos 17.020 registros donde todos los valores resultan nulos. Decidimos eliminar estas filas debido a su falta de interés. 

A continuación filtramos por filas que tengan menos de 1 o 2 valores. Observamos que son las mismas que aquellas que tienen menos de 10 valores por lo tanto decidimos eliminarlas debido a la falta de utilidad.

Tras eliminar las columnas con menos de 10 valores efectivos nos queda un Data Frame de 6302 registros con los que podemos trabajar y que podrían tener interés.

### Limpieza de columnas nulas (aproximación general)
A continuación se procederá a abordar el data frame columna por columna, revisando los valores y tratando de despejar los valores nulos.

Tras revisar las variables de las columnas empezamos la limpieza por las dos últimas columnas. Vemos que los únicos datos que ofrecen estas columnas son: [nan, 'Teramo', 'stopped here', 'change filename']. Revisaremos las veces que se repiten los datos para comprobar su utilidad, aunque a primera vista parecen columnas inservibles.

Vemos que los datos únicamente aparecen una vez cada uno en los 6.000 registros. Por tanto decidimos dropear las columnas.

## 2_date_cleaning
### Limpieza de fecha

Para este df decidimos crear un data frame propio con los datos de interes. Así trabajamos con un panel que únicamente contine las siguientes columnas ["Case Number", "Date", "Year", "Case Number.1", "Case Number2."].

Aplicamos la función sacar_fecha para crear 6 nuevas columnas a través de "Case Number.1" y "Case Number.2". Estas nuevas ["Y_casen1", "M_casen1", "D_casen1", "Y_casen2", "M_casen2", "D_casen2"] columnas contendrán de forma individual el año, mes y día correspondiente a las columnas aplicadas.

A continuación aplicamos nuevas funciones personalizadas para obtener el año, mes y día de la columna ["Date"].

En este momento contamos con nueve nuevas columnas en las que hemos presentado los datos obtenidos del df. Revisamos los datos creados y corregimos de forma manual 3 registros. Eliminamos otros 16 registros debido a la incapacidad de presentar una fecha fiable.

A continuación cruzamos las columnas creadas para unificar la fecha. Aplicamos unas serie de funciones que comparan ["Year", "Y_casen1", "Y_casen2"] y lo mismo con el mes y el año. En estas funciones buscamos similitudes entre los registros obtenidos para mantener el dato más fiable en las columnas ["Final_year", "Final_month", "Final_day"]. Finalmente exportamos el df para reunificarlo con los datos limpiados en el primer checkpoint.

## 3_index_type_cleaning
reunificamos el proyecto importando los datos 1_shark_limpio y 2_shark_fechas. Concatenamos y eliminamos las columnas sobrantes. Además, ahora que la columna "Case number" ya no tiene sentido la vamos a reconvertir en el índice para los ataques. Sin embargo antes vamos a eliminar filas que tras la exploración no se hayan podido corregir

Limpiamos el df para una mejor presentación eliminando columnas y reordenando otras. A continuación enfrentaremos la limpieza columna por columna.

### Limpieza columna "Type"
Buscamos unificar los valores mal escritos o que significan lo mismo y ocuparnos de los posibles NaN.

Tras una primera corrección revisamos las dos filas categorizadas como questionable. Se decide eliminar dichas filas debido a su falta de interés para la base de datos. No se puede confirmar la presencia de un tiburon en el ataque y no se presentan mayores daños que desperfectos en una tabla de surf.

Observamos que en la columna solamente hay 4 valores nulos. Sin embargo se decide eliminar las filas debido a la presencia de nulos en otras columnas clave como "Species".

## 4_geography

Ahora que tenemos nuestro propio índice y la columna Type corrediga pasamos a enfrentarnos a las columnas Country y Area. El objetivo es unificar lo máximo posible a través de la corrección de erratas y nulos.

Vemos que varias filas que tienen nulos en su categoría Country también tienen en Area y Location. Decidimos eliminarlas debido a la falta de concreción geográfica del ataque.

Aplicamos una función utilizando las librerias pycountry y difflib para unificar las categorias de ["Country", "Area", Location"]. Vemos que la función tiene gran éxito corrigiendo las columnas Area y Location, unificando casi 150 y 330 categorias, respectivamente.

A continuación veremos los nulos de estas columnas. Vemos que hay demasiados registros cómo para analizarlos a mano. Trataremos de arreglar la columna Area a través de la localización. A continuación revisamos las columnas donde "Area" sea null y "Location" contenga un elemento. La longitud continua siendo demasiado grande como para poder determinar el Area a través del Location. Por tanto decidimos asignar el valor "Unknown" a los elementos nulos.

## 5_person
### Limpieza columna "Activity"

Aplicamos una función para unificar los tipos de actividad del ataque. Con la función utilizada simplificamos la columna en 10 categorías. Previamente a la aplicación de la función contamos con 500 nulos que al no tener información sobre la actividad introducimos en la categoría "Otros", la cual resulta ser la más amplia. De cerca le siguen las categorias de pesca y surf con valores muy similares.

### Limpieza columna "Name"

A continuación limpiarémos la columna name. Revisando a través de value_counts() detectamos varios valores como male or boy, los cuales nos sirven para verificar la columna de género. Se procede a la utilización y limpieza de estos valores. A continuación, al ser útiles en la columna de nombre los sustituirémos por Unknown, esto serña tras la verificación del género.

Ahora que tenemos los nombres agrupados procedemos a utilizar las categorias creadas "M" y "F" para verificar y rellenar la columna género. Vemos 190 registros en los que Name "M"/"F" y Sex son diferentes. Vamos a priorizar el registro dado en la columna Sex, sin embargo tb vemos algunos valores NaN que rellenaremos con el valor obtenido de la exploración previa.

### Limpieza columna "Sex"

Al ya haber concatenado la columna Sex con los registros obtenidos de la columna Name en este paso únicamente rellenaremos los NaN Sex con valores Unknown. También limpiaremos las categorias desconocidas.

### Limpieza columna "Age"

Para limpiar la columna age se decide recuperar las edades de los strings y convertir la columna a tipo int. Tanto los valores nulos cómo los no deseados se convierten a 0. A la hora de realizar un análisis estadístico de la columna será necesario tener en cuenta que los valores 0 no deben influenciar los resultados.

### Limpieza columna "Injury"

En un primer vistazo a los datos aportados por la columna "Injury" observamos un considerable número de registros catalogados como "FATAL". Decidimos utilizar estos para contrastar los datos aportados por la siguiente columna ["Fatal (Y/N)"].

Observamos que hay una diferencia entre el número de registros "FATAL" y el número de coincidencias con la columna fatal(Y/N). A continuación cambiaremos el nombre de la columna fatal para una mayor comodidad. También decidimos que allí donde se haya indicado que la herida fue fatal situaremos el mismo registro en la columna "Deadly".

Vemos que en la columna Injury tenemos 26 nulos. Categorizaremos estos registros como "Unknown". Para finar el tratamiento de estos datos aplicaremos formulas para corregir facilitar la lectura.

### Limpieza columna "Deadly"

A continuación limpiamos los valores incongruentes de la columna Deadly categorizandolos como "Unknown". Al ya haber contrastado esta columna con los registros de ["Injury"] no es necesario más tratamiento.

### Limpieza columna "href"

Para la limpieza de las columnas "href" y "href formula" hemos comprobado que únicamente existe un valor nulo en la columna "href formula". También se ha comprobado que aunque aparentemente parezcan contener la misma información existen 60 registros donde estas columnas son diferentes. Debido a la inexistencia de nulos en la columna "href" se decide confiar en esta y eliminar "href formula", ya que no tendría sentido tener dos columnas quee tuvieran la misma información.

## 6_reorganization

Aprovechamos este checkpoint del tratamiento del data frame para reorganizar las columnas y sus nombres. Tras este proceso contamos con el siguiente data frame:
["Case Number", "Year", "Month", "Day", "Season", "Time", "Daytime", "Country", "Area", "Location", "Type", "Activity", "Injury", "Deadly", "Species", "Size", "Name", "Sex", "Age", "Source", "pdf", "href", "original order"]

## 7_time
### Limpieza columna "Time"

Para la limpieza de la columna ["Time"], la cual representa el momento del ataque, creamos varias funciones que simplifican los datos y los restringen a las posibles 24 horas del día. Utilizamos el formato 24 horas, dando un valor único e integro a cada hora del día. Además, para mayor simplificación decidimos que si el ataque sucedió antes o exactamente a la media hora se introducirá el número exacto, si sucedio despues se declarará que fue en la hora siguiente. 
19:45 == 20:00
15:25 == 15:00
15:30 == 15:00

### Creación columna "Daytime"

Al obtener las horas del ataque pasamos las siguientes condiciones para establecer la hora del ataque:
6 <= hora < 12: return "Morning"
12 <= hora < 14: return "Noon"
14 <= hora < 20: return "Afternoon"
else: return "Night"

### Creación columna "Season"

Para la creación de la columna ["Season"] utilizamos los datos obtenidos en la exploración de la fecha para establecer la estación en la que se produjo el ataque. 

## 8_Species
### Limpieza columna "Species"

Para la limpieza de esta columna se buscará encontrar en las strings fragmentos de los 20 tipos de tiburones más comunes. En caso de no encontrarse se categorizará como Other Species o Unknown Shark. Previamente a la transformación de los datos utilizaremos los registros obtenidos para obtener el tamaño del tiburón, dato que almacenaremos en una nueva columna para su posterior utilización.

### Creación columna "Size"

Para la creación de la columna Size hemos separado los números encontrados en la columna Species y los introducimos a una lista como valores enteros. Continuación Se devuelve la media fila por fila, esto es debido a que se detecto que en un gran número de ocasiones los datos introducidos eran un rango de tamaño (Size 10 to 15m). A continuación se eliminan los valores atípicos, considerando a estos los resultados superiores a 100m. Por último se redondea el tamaño a tan solo dos dígitos para facilitar la lectura de los datos.

Además se ha decidido no tratar los valores NaN de la columna Size debido a que la introducción de otro tipo de dato o el uso de la media o algún estadistico para el relleno podría alterar los valores.

### Limpieza columna "Source"

Para la limpieza de la columna Source se ha decidido rellenar los valores nulos con la categoría "Unknown". Además, buscando legibilidad, y teniendo en cuenta que se trata de nombres, siglas e iniciales se ha decidido poner la primera letra de cada palabra en mayuscula.

## 9_final

En este apartado vamos a realizar las últimas comprobaciones (valores nulos y casos atípicos). Además reiniciaremos el índice personal creado en la columna "Case Number" y prepararemos el data frame para una facil lectura.

Vemos que solo quedan valores nulos en la columna "Size". Recordamos que esto es una decisión tomada intencionalmente.

En la descripción general de los se detecta que el año máximo es 2176. A continuación corregimos el df para evitar la presencia de años por delante del actual.

Mientras se solucionaba el problema se detecto que únicamente había un año por encima de los valores posibles. Se decide cambiarlo a mano, encontrando el año del ataque en la columna "pdf".

Finalmente, y tras las últimas comprobaciones, declaramos la base de datos como limpia. Se ha eliminado la presencia de valores nulos, excepto los intencionadamente colocados en la columna Size y no se detectan más valores atípicos en la visualización.