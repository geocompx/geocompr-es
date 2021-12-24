# Operaciones de datos de atributos {#attr}

## Prerrequisitos {-}

- Este capítulo requiere que se instalen y adjunten los siguientes paquetes: 


```r
library(sf)      # paquete de datos vectoriales introducido en el capítulo 2
library(terra)   # paquete de datos raster introducido en el capítulo 2
library(dplyr)   # paquete tidyverse para la manipulación de marcos de datos
```

- También depende de **spData**, que carga los conjuntos de datos utilizados en los ejemplos de código de este capítulo:


```r
library(spData)  # paquete de datos espaciales introducido en el capítulo 2
```

## Introducción

Los datos de atributos son la información no espacial asociada a los datos geográficos (geométricos).
Un ejemplo sencillo es el de una parada de autobús: su posición suele estar representada por coordenadas de latitud y longitud (datos geométricos), además de su nombre.
La parada [Elephant & Castle / New Kent Road](https://www.openstreetmap.org/relation/6610626) de Londres, por ejemplo, tiene unas coordenadas de -0,098 grados de longitud y 51,495 grados de latitud que pueden representarse como `POINT (-0,098 51,495)` en la representación `sfc` descrita en el capítulo \@ref(spatial-class).
Los atributos, como el nombre *atributo*\index{attribute} de la función POINT (por utilizar la terminología de Simple Features) son el tema de este capítulo.




Otro ejemplo es el valor de elevación (atributo) de una celda específica de la cuadrícula en los datos raster.
A diferencia del modelo de datos vectoriales, el modelo de datos raster almacena la coordenada de la celda de la cuadrícula de forma indirecta, lo cual significa que la distinción entre atributo e información espacial es menos clara.
Para ilustrar este punto, piensa en un píxel en la 3ª fila y la 4ª columna de una matriz raster.

Su ubicación espacial está definida por su índice en la matriz: se mueve desde el origen cuatro celdas en la dirección x (normalmente este y derecha en los mapas) y tres celdas en la dirección y (normalmente sur y abajo).
La *resolución* del raster define la distancia para cada paso en x e y que se especifica en un *cabezal*.
La cabecera es un componente vital de los conjuntos de datos raster que especifica cómo se relacionan los píxeles con las coordenadas geográficas (véase también el capítulo \@ref(spatial-operations)).

Esto muestra cómo manipular objetos geográficos basados en atributos como los nombres de las paradas de autobús en un conjunto de datos vectoriales y las elevaciones de los píxeles en un conjunto de datos rasterizados.
En el caso de los datos vectoriales, esto implica técnicas como crear subconjuntos y o agregaciones (véanse las secciones \@ref(subconjunto de atributos vectoriales) y \@ref(agregación de atributos vectoriales)).

Las secciones \@ref(vector-attribute-joining) y \@ref(vec-attr-creation) demuestran cómo unir datos en objetos de características simples utilizando un ID compartido y cómo crear nuevas variables, respectivamente.
Cada una de estas operaciones tiene un equivalente espacial:
el operador `[` en R básico, por ejemplo, funciona igualmente para subconjuntar objetos basados en su atributo y objetos espaciales; también se pueden unir atributos en dos conjuntos de datos geográficos utilizando uniones espaciales.
Esto es una buena noticia: las habilidades desarrolladas en este capítulo son transferibles.
El capítulo \@ref(spatial-operations) extiende los métodos presentados aquí al mundo espacial.

Después de una inmersión profunda en varios tipos de operaciones de atributos *vectoriales* en la siguiente sección, las operaciones de datos de atributos *raster* se cubren en la Sección \ref(manipulando-objetos-raster), que demuestra cómo crear capas raster que contienen atributos continuos y categóricos y cómo extraer valores de celdas de una o más capas (subconjunto raster). 
La sección \@ref(summarizing-raster-objects) proporciona una visión general de las operaciones ráster "globales" que pueden utilizarse para resumir conjuntos de datos raster completos.

## Manipulación de atributos vectoriales

Los conjuntos de datos vectoriales geográficos están bien soportados en R gracias a la clase `sf`, que extiende la clase `data.frame` de R.
Al igual que los marcos de datos, los objetos `sf` tienen una columna por variable de atributo (como 'nombre') y una fila por observación o *característica* (por ejemplo, por estación de autobuses).
Los objetos `sf` se diferencian de los marcos de datos básicos porque tienen una columna de `geometría` de la clase `sfc` que puede contener una serie de entidades geográficas (uno o 'múltiples' puntos, líneas y polígonos) por fila.
Esto se describió en el capítulo \@ref(spatial-class), donde se demostró cómo *los métodos genéricos* como `plot()` y `summary()` funcionan con los objetos `sf`.
**sf** también proporciona genéricos que permiten que los objetos `sf` se comporten como marcos de datos normales, como se muestra al imprimir los métodos de la clase:


```r
methods(class = "sf") # métodos para objetos sf, se muestran los 12 primeros
```


```r
#>  [1] aggregate             cbind                 coerce               
#>  [4] initialize            merge                 plot                 
#>  [7] print                 rbind                 [                    
#> [10] [[<-                  $<-                   show                 
```



Muchos de ellos (`aggregate()`, `cbind()`, `merge()`, `rbind()` y `[`) sirven para manejar marcos de datos.
Por ejemplo, `rbind()` une dos marcos de datos, uno "sobre" el otro.
`$<-` crea nuevas columnas. 
Una característica clave de los objetos `sf` es que almacenan datos espaciales y no espaciales de la misma manera, como columnas en un `data.frame`.

\BeginKnitrBlock{rmdnote}<div class="rmdnote">La columna de geometría de los objetos `sf` suele llamarse `geometría` o `geom`, pero puede utilizarse cualquier nombre.
El siguiente comando, por ejemplo, crea una columna de geometría llamada `g`:
  
`st_sf(data.frame(n = world$name_long), g = world$geom)`

Esto permite que las geometrías importadas de las bases de datos espaciales tengan varios nombres, como `wkb_geometry` y `the_geom`.</div>\EndKnitrBlock{rmdnote}

Los objetos `sf` también pueden extender las clases `tidyverse` para marcos de datos, `tibble` y `tbl`.
\index{tidyverse (package)}.
Por lo tanto, **sf** permite dar rienda suelta a toda la potencia de las capacidades de análisis de datos de R en los datos geográficos, tanto si se utiliza la base de R como las funciones de tidyverse para el análisis de datos.
\index{tibble}
(Ver [`Rdatatable/data.table#2273`](https://github.com/Rdatatable/data.table/issues/2273) para ver la compatibilidad entre los objetos `sf` y el paquete `data.table`).
Antes de utilizar estas capacidades, merece la pena repasar cómo descubrir las propiedades básicas de los objetos de datos vectoriales.
Empecemos por utilizar las funciones básicas de R para conocer el conjunto de datos `world` del paquete **spData**:


```r
class(world) # es un objeto sf y un marco de datos (ordenado)
#> [1] "sf"         "tbl_df"     "tbl"        "data.frame"
dim(world)   # es un objeto bidimensional, con 177 filas y 11 columnas
#> [1] 177  11
```

`world` contiene diez columnas no geográficas (y una columna que contiene una lista de geometrías) con casi 200 filas que representan los países del mundo.
La función `st_drop_geometry()` mantiene sólo los datos de los atributos de un objeto `sf`, es decir, elimina su geometría:


```r
world_df = st_drop_geometry(world)
class(world_df)
#> [1] "tbl_df"     "tbl"        "data.frame"
ncol(world_df)
#> [1] 10
```

La eliminación de la columna de geometría antes de trabajar con los datos de atributos puede ser útil; los procesos de manipulación de datos pueden ejecutarse más rápido cuando trabajan sólo con los datos de atributos y las columnas de geometría no siempre son necesarias.
En la mayoría de los casos, sin embargo, tiene sentido mantener la columna de geometría, lo que explica que la columna sea "pegajosa" (permanece después de la mayoría de las operaciones de atributos a menos que se elimine específicamente).
Las operaciones de datos no espaciales sobre objetos `sf` sólo cambian la geometría de un objeto cuando es apropiado (por ejemplo, disolviendo los bordes entre polígonos adyacentes tras una agregación).
Convertirse en un experto en la manipulación de datos de atributos geográficos significa convertirse en un experto en la manipulación de marcos de datos.

Para muchas aplicaciones, el paquete tidyverse \index{tidyverse (package)} **dplyr** ofrece un enfoque eficaz para trabajar con marcos de datos.
La compatibilidad con Tidyverse es una ventaja de **sf** frente a su predecesor **sp**, pero hay que evitar algunos inconvenientes (véase la viñeta complementaria `tidyverse-pitfalls` en [geocompr.github.io](https://geocompr.github.io/geocompkg/articles/tidyverse-pitfalls.html) para más detalles).

### Subconjuntos de atributos vectoriales

Los métodos de subconjunto de R base incluyen el operador `[` y la función `subset()`.
Las funciones clave para manejar y crear subconjuntos de **dplyr** son `filter()` y `slice()` para crear subconjuntos de filas, y `select()` para crear subconjuntos de columnas.
Ambos planteamientos conservan los componentes espaciales de los datos de atributos en los objetos `sf`, mientras que si se utiliza el operador `$` o la función **dplyr** `pull()` para devolver una única columna de atributos como vector se perderán los datos de atributos, tal y como veremos.
\index{attribute!subsetting}
Esta sección se centra en el subconjunto de marcos de datos `sf`; para más detalles sobre el subconjunto de vectores y marcos de datos no geográficos recomendamos leer la sección [2.7](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Index-vectors) de An Introduction to R [@rcoreteam_introduction_2021] y el Capítulo [4](https://adv-r.hadley.nz/subsetting.html) de Advanced R Programming [@wickham_advanced_2019], respectivamente.

El operador `[` puede dividir tanto filas como columnas. 
Los índices colocados dentro de los corchetes situados directamente después del nombre de un objeto de marco de datos especifican los elementos que se quieren conservar.
El comando `object[i, j]` significa 'devolver las filas representadas por `i` y las columnas representadas por `j`, donde `i` y `j` suelen contener enteros o `TRUE` y `FALSE` (los índices también pueden ser caracteres, indicando los nombres de las filas o las columnas).
`object[5, 1:3]`, por ejemplo, significa `devolver datos que contengan la 5ª fila y las columnas 1 a 3`: el resultado debería ser un marco de datos con sólo 1 fila y 3 columnas, y una cuarta columna de geometría si es un objeto `sf`.
Si se deja `i` o `j` vacía se devuelven todas las filas o columnas, por lo que `world[1:5, ]` devuelve las cinco primeras filas y las 11 columnas que componen el marco de datos.
Los ejemplos que hay a continuación demuestran la creación de subconjunto con R base.
Adivina el número de filas y columnas de los marcos de datos `sf` devueltos por cada comando y comprueba los resultados en tu propio ordenador (consulta el final del capítulo para ver más ejercicios):


```r
world[1:6, ]    # Subconjunto de las filas 1 a 6 
world[, 1:3]    # Subconjunto de las columnas 1 a 3
world[1:6, 1:3] # Subconjunto de las filas 1 a 6 y las columnas 1 a 3
world[, c("name_long", "pop")] # Columnas por nombre
world[, c(T, T, F, F, F, F, F, T, T, F, F)] # Selección de columnas por índices lógicos
world[, 888] # Índice representando una columna no existenete
```



Una demostración de la utilidad de utilizar vectores `lógicos` para el subconjunto se muestra en el fragmento de código siguiente.
Esto crea un nuevo objeto, `small_countries`, que contiene las naciones cuya superficie es inferior a 10.000 km^2^:


```r
i_small = world$area_km2 < 10000
summary(i_small) # un vector lógico
#>    Mode   FALSE    TRUE 
#> logical     170       7
small_countries = world[i_small, ]
```

El objeto `i_small` (abreviatura de "índice" que representa a los países pequeños) es un vector lógico que se puede utilizar para agrupar los siete países más pequeños del `mundo` por su superficie.
Un comando más conciso, que omita el objeto intermediario (`i_small`), genera el mismo resultado:


```r
small_countries = world[world$area_km2 < 10000, ]
```

La función base de R `subset()` proporciona otra forma de conseguir el mismo resultado:


```r
small_countries = subset(world, area_km2 < 10000)
```

Las funciones de R base son maduras, estables y ampliamente utilizadas, lo que las convierte en una opción sólida, especialmente en contextos en los que la reproducibilidad y la fiabilidad son fundamentales.
Las funciones de **dplyr** permiten flujos de trabajo "ordenados" que algunas personas (incluidos los autores de este libro) encuentran intuitivos y productivos para el análisis interactivo de datos, especialmente cuando se combinan con editores de código como RStudio que permiten [autocompletar](https://support.rstudio.com/hc/en-us/articles/205273297-Code-Completion-in-the-RStudio-IDE) los nombres de las columnas.
A continuación se muestran las funciones clave para el subconjunto de marcos de datos (incluidos los marcos de datos `sf`) con las funciones **dplyr**.
<!-- La frase que sigue parece no ser cierta según el punto de referencia que se indica a continuación. -->
<!-- `dplyr` también es más rápido que R base para algunas operaciones, debido a su backend C++\index{C++}. -->
<!-- ¿Algo sobre dbplyr? Nunca he visto a nadie usarlo regularmente para datos espaciales 'en el campo' así que omitiremos la parte de la integración con dbs por ahora (RL 2021-10) -->
<!-- Las principales funciones de **dplyr** para crear subgrupos son `select()`, `slice()`, `filter()` y `pull()`. -->



`select()` selecciona las columnas por nombre o posición.
Por ejemplo, podrías seleccionar sólo dos columnas, `name_long` y `pop`, con el siguiente comando:


```r
world1 = dplyr::select(world, name_long, pop)
names(world1)
#> [1] "name_long" "pop"       "geom"
```

Nota: al igual que con el comando equivalente en R base (`world[, c("name_long", "pop")]`), la columna `geom` permanece.
`select()` también permite seleccionar un rango de columnas con la ayuda del operador `:`: 


```r
# Selecciona todas las columnas entre name_long y pop (incluidas)
world2 = dplyr::select(world, name_long:pop)
```

Puedes eliminar columnas específicas con el operador `-`:


```r
# Muestra todas las columnas excepto subregion y area_km2 
world3 = dplyr::select(world, -subregion, -area_km2)
```

Crear subconjuntos y renombrar columnas al mismo tiempo con la sintaxis `nuevo_nombre = antiguo_nombre`:


```r
world4 = dplyr::select(world, name_long, population = pop)
```

Cabe destacar que el comando anterior es más conciso que el equivalente en R base, el cual requiere dos líneas de código:


```r
world5 = world[, c("name_long", "pop")] # subagrupar las columnas por nombre
names(world5)[names(world5) == "pop"] = "population" # renombrar la columna manualmente
```

`select()` también funciona con "funciones de ayuda" para operaciones más avanzadas, como `contains()`, `starts_with()` y `num_range()` (véase la página de ayuda con `?select` para más detalles).

Most **dplyr** verbs return a data frame, but you can extract a single column as a vector with `pull()`.
<!-- Note: I have commented out the statement below because it is not true for `sf` objects, it's a bit confusing that the behaviour differs between data frames and `sf` objects. -->
<!-- The subsetting operator in base R (see `?[`), by contrast, tries to return objects in the lowest possible dimension. -->
<!-- This means selecting a single column returns a vector in base R as demonstrated in code chunk below which returns a numeric vector representing the population of countries in the `world`: -->
You can get the same result in base R with the list subsetting operators `$` and `[[`, the three following commands return the same numeric vector:


```r
pull(world, pop)
world$pop
world[["pop"]]
```

<!-- Commenting out the following because it's confusing and covered better in other places (RL, 2021-10) -->
<!-- To turn off this behavior, set the `drop` argument to `FALSE`,  -->





`slice()` is the row-equivalent of `select()`.
The following code chunk, for example, selects rows 1 to 6:


```r
slice(world, 1:6)
```

`filter()` is **dplyr**'s equivalent of base R's `subset()` function.
It keeps only rows matching given criteria, e.g., only countries with and area below a certain threshold, or with a high average of life expectancy, as shown in the following examples:


```r
world7 = filter(world ,area_km2 < 10000) # countries with a small area
world7 = filter(world, lifeExp > 82)      # with high life expectancy
```

The standard set of comparison operators can be used in the `filter()` function, as illustrated in Table \@ref(tab:operators): 




Table: (\#tab:operators)Comparison operators that return Booleans (TRUE/FALSE).

|Symbol                        |Name                            |
|:-----------------------------|:-------------------------------|
|`==`                          |Equal to                        |
|`!=`                          |Not equal to                    |
|`>`, `<`                      |Greater/Less than               |
|`>=`, `<=`                    |Greater/Less than or equal      |
|`&`, <code>&#124;</code>, `!` |Logical operators: And, Or, Not |

### Chaining commands with pipes

Key to workflows using **dplyr** functions is the ['pipe'](http://r4ds.had.co.nz/pipes.html) operator `%>%` (and since R `4.1.0` the native pipe `|>`), which takes its name from the Unix pipe `|` [@grolemund_r_2016].
Pipes enable expressive code: the output of a previous function becomes the first argument of the next function, enabling *chaining*.
This is illustrated below, in which only countries from Asia are filtered from the `world` dataset, next the object is subset by columns (`name_long` and `continent`) and the first five rows (result not shown).


```r
world7 = world %>%
  filter(continent == "Asia") %>%
  dplyr::select(name_long, continent) %>%
  slice(1:5)
```

The above chunk shows how the pipe operator allows commands to be written in a clear order:
the above run from top to bottom (line-by-line) and left to right.
The alternative to `%>%` is nested function calls, which is harder to read:


```r
world8 = slice(
  dplyr::select(
    filter(world, continent == "Asia"),
    name_long, continent),
  1:5)
```

### Vector attribute aggregation

\index{attribute!aggregation}
\index{aggregation}
Aggregation involves summarizing data with one or more 'grouping variables', typically from columns in the data frame to be aggregated (geographic aggregation is covered in the next chapter).
An example of attribute aggregation is calculating the number of people per continent based on country-level data (one row per country).
The `world` dataset contains the necessary ingredients: the columns `pop` and `continent`, the population and the grouping variable, respectively.
The aim is to find the `sum()` of country populations for each continent, resulting in a smaller data frame (aggregation is a form of data reduction and can be a useful early step when working with large datasets).
This can be done with the base R function `aggregate()` as follows:


```r
world_agg1 = aggregate(pop ~ continent, FUN = sum, data = world, na.rm = TRUE)
class(world_agg1)
#> [1] "data.frame"
```

The result is a non-spatial data frame with six rows, one per continent, and two columns reporting the name and population of each continent (see Table \@ref(tab:continents) with results for the top 3 most populous continents).

`aggregate()` is a [generic function](https://adv-r.hadley.nz/s3.html#s3-methods) which means that it behaves differently depending on its inputs. 
**sf** provides the method `aggregate.sf()` which is activated automatically when `x` is an `sf` object and a `by` argument is provided:


```r
world_agg2 = aggregate(world["pop"], list(world$continent), FUN = sum, na.rm = TRUE)
class(world_agg2)
#> [1] "sf"         "data.frame"
nrow(world_agg2)
#> [1] 8
```

The resulting `world_agg2` object is a spatial object containing 8 features representing the continents of the world (and the open ocean).
`group_by() %>% summarize()` is the **dplyr** equivalent of `aggregate()`, with the variable name provided in the `group_by()` function specifying the grouping variable and information on what is to be summarized passed to the `summarize()` function, as shown below:


```r
world_agg3 = world %>%
  group_by(continent) %>%
  summarize(pop = sum(pop, na.rm = TRUE))
```

The approach may seem more complex but it has benefits: flexibility, readability, and control over the new column names.
This flexibility is illustrated in the command below, which calculates not only the population but also the area and number of countries in each continent:


```r
world_agg4  = world %>% 
  group_by(continent) %>%
  summarize(pop = sum(pop, na.rm = TRUE), `area (sqkm)` = sum(area_km2), n = n())
```

In the previous code chunk `pop`, `area (sqkm)` and `n` are column names in the result, and `sum()` and `n()` were the aggregating functions.
These aggregating functions return `sf` objects with rows representing continents and geometries containing the multiple polygons representing each land mass and associated islands (this works thanks to the geometric operation 'union', as explained in Section \@ref(geometry-unions)).

Let's combine what we have learned so far about **dplyr** functions, by chaining multiple commands to summarize attribute data about countries worldwide by continent.
The following command calculates population density (with `mutate()`), arranges continents by the number countries they contain (with `dplyr::arrange()`), and keeps only the 3 most populous continents (with `top_n()`), the result of which is presented in Table \@ref(tab:continents)):


```r
world_agg5 = world %>% 
  st_drop_geometry() %>%                      # drop the geometry for speed
  dplyr::select(pop, continent, area_km2) %>% # subset the columns of interest  
  group_by(continent) %>%                     # group by continent and summarize:
  summarize(Pop = sum(pop, na.rm = TRUE), Area = sum(area_km2), N = n()) %>%
  mutate(Density = round(Pop / Area)) %>%     # calculate population density
  top_n(n = 3, wt = Pop) %>%                  # keep only the top 3
  arrange(desc(N))                            # arrange in order of n. countries
```


Table: (\#tab:continents)The top 3 most populous continents ordered by population density (people per square km).

|continent |        Pop|     Area|  N| Density|
|:---------|----------:|--------:|--:|-------:|
|Africa    | 1154946633| 29946198| 51|      39|
|Asia      | 4311408059| 31252459| 47|     138|
|Europe    |  669036256| 23065219| 39|      29|

\BeginKnitrBlock{rmdnote}<div class="rmdnote">More details are provided in the help pages (which can be accessed via `?summarize` and `vignette(package = "dplyr")` and Chapter 5 of [R for Data Science](http://r4ds.had.co.nz/transform.html#grouped-summaries-with-summarize). </div>\EndKnitrBlock{rmdnote}

###  Vector attribute joining

Combining data from different sources is a common task in data preparation. 
Joins do this by combining tables based on a shared 'key' variable.
**dplyr** has multiple join functions including `left_join()` and `inner_join()` --- see `vignette("two-table")` for a full list.
These function names follow conventions used in the database language [SQL](http://r4ds.had.co.nz/relational-data.html) [@grolemund_r_2016, Chapter 13]; using them to join non-spatial datasets to `sf` objects is the focus of this section.
**dplyr** join functions work the same on data frames and `sf` objects, the only important difference being the `geometry` list column.
The result of data joins can be either an `sf` or `data.frame` object.
The most common type of attribute join on spatial data takes an `sf` object as the first argument and adds columns to it from a `data.frame` specified as the second argument.
\index{join}
\index{attribute!join}

To demonstrate joins, we will combine data on coffee production with the `world` dataset.
The coffee data is in a data frame called `coffee_data` from the **spData** package (see `?coffee_data` for details).
It has 3 columns:
`name_long` names major coffee-producing nations and `coffee_production_2016` and `coffee_production_2017` contain estimated values for coffee production in units of 60-kg bags in each year.
A 'left join', which preserves the first dataset, merges `world` with `coffee_data`:


```r
world_coffee = left_join(world, coffee_data)
#> Joining, by = "name_long"
class(world_coffee)
#> [1] "sf"         "tbl_df"     "tbl"        "data.frame"
```

Because the input datasets share a 'key variable' (`name_long`) the join worked without using the `by` argument (see `?left_join` for details).
The result is an `sf` object identical to the original `world` object but with two new variables (with column indices 11 and 12) on coffee production.
This can be plotted as a map, as illustrated in Figure \@ref(fig:coffeemap), generated with the `plot()` function below:


```r
names(world_coffee)
#>  [1] "iso_a2"                 "name_long"              "continent"             
#>  [4] "region_un"              "subregion"              "type"                  
#>  [7] "area_km2"               "pop"                    "lifeExp"               
#> [10] "gdpPercap"              "geom"                   "coffee_production_2016"
#> [13] "coffee_production_2017"
plot(world_coffee["coffee_production_2017"])
```

<div class="figure" style="text-align: center">
<img src="03-atributos_files/figure-html/coffeemap-1.png" alt="World coffee production (thousand 60-kg bags) by country, 2017. Source: International Coffee Organization." width="100%" />
<p class="caption">(\#fig:coffeemap)World coffee production (thousand 60-kg bags) by country, 2017. Source: International Coffee Organization.</p>
</div>

For joining to work, a 'key variable' must be supplied in both datasets.
By default **dplyr** uses all variables with matching names.
In this case, both `world_coffee` and `world` objects contained a variable called `name_long`, explaining the message `Joining, by = "name_long"`.
In the majority of cases where variable names are not the same, you have two options:

1. Rename the key variable in one of the objects so they match.
2. Use the `by` argument to specify the joining variables.

The latter approach is demonstrated below on a renamed version of `coffee_data`:


```r
coffee_renamed = rename(coffee_data, nm = name_long)
world_coffee2 = left_join(world, coffee_renamed, by = c(name_long = "nm"))
```



Note that the name in the original object is kept, meaning that `world_coffee` and the new object `world_coffee2` are identical.
Another feature of the result is that it has the same number of rows as the original dataset.
Although there are only 47 rows of data in `coffee_data`, all 177 country records are kept intact in `world_coffee` and `world_coffee2`:
rows in the original dataset with no match are assigned `NA` values for the new coffee production variables.
What if we only want to keep countries that have a match in the key variable?
In that case an inner join can be used:


```r
world_coffee_inner = inner_join(world, coffee_data)
#> Joining, by = "name_long"
nrow(world_coffee_inner)
#> [1] 45
```

Note that the result of `inner_join()` has only 45 rows compared with 47 in `coffee_data`.
What happened to the remaining rows?
We can identify the rows that did not match using the `setdiff()` function as follows:


```r
setdiff(coffee_data$name_long, world$name_long)
#> [1] "Congo, Dem. Rep. of" "Others"
```

The result shows that `Others` accounts for one row not present in the `world` dataset and that the name of the `Democratic Republic of the Congo` accounts for the other:
it has been abbreviated, causing the join to miss it.
The following command uses a string matching (regex) function from the **stringr** package to confirm what `Congo, Dem. Rep. of` should be:


```r
(drc = stringr::str_subset(world$name_long, "Dem*.+Congo"))
#> [1] "Democratic Republic of the Congo"
```





To fix this issue, we will create a new version of `coffee_data` and update the name.
`inner_join()`ing the updated data frame returns a result with all 46 coffee-producing nations:


```r
coffee_data$name_long[grepl("Congo,", coffee_data$name_long)] = drc
world_coffee_match = inner_join(world, coffee_data)
#> Joining, by = "name_long"
nrow(world_coffee_match)
#> [1] 46
```

It is also possible to join in the other direction: starting with a non-spatial dataset and adding variables from a simple features object.
This is demonstrated below, which starts with the `coffee_data` object and adds variables from the original `world` dataset.
In contrast with the previous joins, the result is *not* another simple feature object, but a data frame in the form of a **tidyverse** tibble:
the output of a join tends to match its first argument:


```r
coffee_world = left_join(coffee_data, world)
#> Joining, by = "name_long"
class(coffee_world)
#> [1] "tbl_df"     "tbl"        "data.frame"
```

\BeginKnitrBlock{rmdnote}<div class="rmdnote">In most cases, the geometry column is only useful in an `sf` object.
The geometry column can only be used for creating maps and spatial operations if R 'knows' it is a spatial object, defined by a spatial package such as **sf**.
Fortunately, non-spatial data frames with a geometry list column (like `coffee_world`) can be coerced into an `sf` object as follows: `st_as_sf(coffee_world)`. </div>\EndKnitrBlock{rmdnote}

This section covers the majority of joining use cases.
For more information, we recommend @grolemund_r_2016, the [join vignette](https://geocompr.github.io/geocompkg/articles/join.html) in the **geocompkg** package that accompanies this book, and documentation of the **data.table** package.^[
**data.table** is a high-performance data processing package.
Its application to geographic data is covered in a blog post hosted at r-spatial.org/r/2017/11/13/perp-performance.html.
]
Another type of join is a spatial join, covered in the next chapter (Section \@ref(spatial-joining)).

### Creating attributes and removing spatial information {#vec-attr-creation}

Often, we would like to create a new column based on already existing columns.
For example, we want to calculate population density for each country.
For this we need to divide a population column, here `pop`, by an area column, here `area_km2` with unit area in square kilometers.
Using base R, we can type:


```r
world_new = world # do not overwrite our original data
world_new$pop_dens = world_new$pop / world_new$area_km2
```

Alternatively, we can use one of **dplyr** functions - `mutate()` or `transmute()`.
`mutate()` adds new columns at the penultimate position in the `sf` object (the last one is reserved for the geometry):


```r
world %>% 
  mutate(pop_dens = pop / area_km2)
```

The difference between `mutate()` and `transmute()` is that the latter drops all other existing columns (except for the sticky geometry column):


```r
world %>% 
  transmute(pop_dens = pop / area_km2)
```

`unite()` from the **tidyr** package (which provides many useful functions for reshaping datasets, including `pivot_longer()`) pastes together existing columns.
For example, we want to combine the `continent` and `region_un` columns into a new column named `con_reg`.
Additionally, we can define a separator (here: a colon `:`) which defines how the values of the input columns should be joined, and if the original columns should be removed (here: `TRUE`):


```r
world_unite = world %>%
  unite("con_reg", continent:region_un, sep = ":", remove = TRUE)
```

The `separate()` function does the opposite of `unite()`: it splits one column into multiple columns using either a regular expression or character positions.
This function also comes from the **tidyr** package.


```r
world_separate = world_unite %>% 
  separate(con_reg, c("continent", "region_un"), sep = ":")
```



The **dplyr** function `rename()` and the base R function `setNames()` are useful for renaming columns.
The first replaces an old name with a new one.
The following command, for example, renames the lengthy `name_long` column to simply `name`:


```r
world %>% 
  rename(name = name_long)
```

`setNames()` changes all column names at once, and requires a character vector with a name matching each column.
This is illustrated below, which outputs the same `world` object, but with very short names: 




```r
new_names = c("i", "n", "c", "r", "s", "t", "a", "p", "l", "gP", "geom")
world %>% 
  setNames(new_names)
```

It is important to note that attribute data operations preserve the geometry of the simple features.
As mentioned at the outset of the chapter, it can be useful to remove the geometry.
To do this, you have to explicitly remove it.
Hence, an approach such as `select(world, -geom)` will be unsuccessful and you should instead use `st_drop_geometry()`.^[
`st_geometry(world_st) = NULL` also works to remove the geometry from `world`, but overwrites the original object.
]


```r
world_data = world %>% st_drop_geometry()
class(world_data)
#> [1] "tbl_df"     "tbl"        "data.frame"
```

## Manipulating raster objects
<!--jn-->

In contrast to the vector data model underlying simple features (which represents points, lines and polygons as discrete entities in space), raster data represent continuous surfaces.
This section shows how raster objects work by creating them *from scratch*, building on Section \@ref(an-introduction-to-terra).
Because of their unique structure, subsetting and other operations on raster datasets work in a different way, as demonstrated in Section \@ref(raster-subsetting).
\index{raster!manipulation}

The following code recreates the raster dataset used in Section \@ref(raster-classes), the result of which is illustrated in Figure \@ref(fig:cont-raster).
This demonstrates how the `rast()` function works to create an example raster named `elev` (representing elevations).


```r
elev = rast(nrows = 6, ncols = 6, resolution = 0.5, 
            xmin = -1.5, xmax = 1.5, ymin = -1.5, ymax = 1.5,
            vals = 1:36)
```

The result is a raster object with 6 rows and 6 columns (specified by the `nrow` and `ncol` arguments), and a minimum and maximum spatial extent in x and y direction (`xmin`, `xmax`, `ymin`, `ymax`).
The `vals` argument sets the values that each cell contains: numeric data ranging from 1 to 36 in this case.
Raster objects can also contain categorical values of class `logical` or `factor` variables in R.
The following code creates a raster representing grain sizes (Figure \@ref(fig:cont-raster)):


```r
grain_order = c("clay", "silt", "sand")
grain_char = sample(grain_order, 36, replace = TRUE)
grain_fact = factor(grain_char, levels = grain_order)
grain = rast(nrows = 6, ncols = 6, resolution = 0.5, 
             xmin = -1.5, xmax = 1.5, ymin = -1.5, ymax = 1.5,
             vals = grain_fact)
```



The raster object stores the corresponding look-up table or "Raster Attribute Table" (RAT) as a list of data frames, which can be viewed with `cats(grain)` (see `?cats()` for more information).
Each element of this list is a layer of the raster.
It is also possible to use the function `levels()` for retrieving and adding new or replacing existing factor levels:


```r
levels(grain)[[1]] = c(levels(grain)[[1]], wetness = c("wet", "moist", "dry"))
levels(grain)
#> [[1]]
#> [1] "clay"  "silt"  "sand"  "wet"   "moist" "dry"
```

<div class="figure" style="text-align: center">
<img src="03-atributos_files/figure-html/cont-raster-1.png" alt="Raster datasets with numeric (left) and categorical values (right)." width="100%" />
<p class="caption">(\#fig:cont-raster)Raster datasets with numeric (left) and categorical values (right).</p>
</div>

\BeginKnitrBlock{rmdnote}<div class="rmdnote">Categorical raster objects can also store information about the colors associated with each value using a color table.
The color table is a data frame with three (red, green, blue) or four (alpha) columns, where each row relates to one value.
Color tables in **terra** can be viewed or set with the `coltab()` function (see `?coltab`).
Importantly, saving a raster object with a color table to a file (e.g., GeoTIFF) will also save the color information.</div>\EndKnitrBlock{rmdnote}

### Raster subsetting

Raster subsetting is done with the base R operator `[`, which accepts a variety of inputs:
\index{raster!subsetting}

- Row-column indexing
- Cell IDs
- Coordinates (see Section \@ref(spatial-raster-subsetting))
- Another spatial object (see Section \@ref(spatial-raster-subsetting))

Here, we only show the first two options since these can be considered non-spatial operations.
If we need a spatial object to subset another or the output is a spatial object, we refer to this as spatial subsetting.
Therefore, the latter two options will be shown in the next chapter (see Section \@ref(spatial-raster-subsetting)).

The first two subsetting options are demonstrated in the commands below ---
both return the value of the top left pixel in the raster object `elev` (results not shown):


```r
# row 1, column 1
elev[1, 1]
# cell ID 1
elev[1]
```

Subsetting of multi-layered raster objects will return the cell value(s) for each layer.
For example, `c(elev, grain)[1]` returns a data frame with one row and two columns --- one for each layer.
To extract all values or complete rows, you can also use `values()`.

Cell values can be modified by overwriting existing values in conjunction with a subsetting operation.
The following code chunk, for example, sets the upper left cell of `elev` to 0 (results not shown):


```r
elev[1, 1] = 0
elev[]
```

Leaving the square brackets empty is a shortcut version of `values()` for retrieving all values of a raster.
Multiple cells can also be modified in this way:


```r
elev[1, c(1, 2)] = 0
```

Replacing values of multilayered rasters can be done with a matrix with as many columns as layers and rows as replaceable cells (results not shown):


```r
two_layers = c(grain, elev) 
two_layers[1] = cbind(c(0), c(4))
two_layers[]
```

### Summarizing raster objects

**terra** contains functions for extracting descriptive statistics\index{statistics} for entire rasters.
Printing a raster object to the console by typing its name returns minimum and maximum values of a raster.
`summary()` provides common descriptive statistics\index{statistics} -- minimum, maximum, quartiles and number of `NA`s for continuous rasters and a number of cells of each class for categorical rasters.
Further summary operations such as the standard deviation (see below) or custom summary statistics can be calculated with `global()`. 
\index{raster!summarizing}


```r
global(elev, sd)
```

\BeginKnitrBlock{rmdnote}<div class="rmdnote">If you provide the `summary()` and `global()` functions with a multi-layered raster object, they will summarize each layer separately, as can be illustrated by running: `summary(c(elev, grain))`.</div>\EndKnitrBlock{rmdnote}

Additionally, the `freq()` function allows to get the frequency table of categorical values.

Raster value statistics can be visualized in a variety of ways.
Specific functions such as `boxplot()`, `density()`, `hist()` and `pairs()` work also with raster objects, as demonstrated in the histogram created with the command below (not shown):


```r
hist(elev)
```

In case the desired visualization function does not work with raster objects, one can extract the raster data to be plotted with the help of `values()` (Section \@ref(raster-subsetting)).
\index{raster!values}

Descriptive raster statistics belong to the so-called global raster operations.
These and other typical raster processing operations are part of the map algebra scheme, which are covered in the next chapter (Section \@ref(map-algebra)).

<div class="rmdnote">
<p>Some function names clash between packages (e.g., a function with the name <code>extract()</code> exist in both <strong>terra</strong> and <strong>tidyr</strong> packages). In addition to not loading packages by referring to functions verbosely (e.g., <code>tidyr::extract()</code>), another way to prevent function names clashes is by unloading the offending package with <code>detach()</code>. The following command, for example, unloads the <strong>terra</strong> package (this can also be done in the <em>package</em> tab which resides by default in the right-bottom pane in RStudio): <code>detach("package:terra", unload = TRUE, force = TRUE)</code>. The <code>force</code> argument makes sure that the package will be detached even if other packages depend on it. This, however, may lead to a restricted usability of packages depending on the detached package, and is therefore not recommended.</p>
</div>

## Exercises


Para estos ejercicios utilizaremos los conjuntos de datos `us_states` y `us_states_df` del paquete **spData**.
Antes de realizar estos ejercicios, deberás haber añadido este paquete y los otros utilizados en el capítulo de operaciones con atributos (**sf**, **dplyr**, **terra**) con comandos como `library(spData)`.



`us_states` es un objeto espacial (de clase `sf`), que contiene la geometría y algunos atributos (incluyendo el nombre, la región, el área y la población) de los estados contiguos de Estados Unidos.
El objeto `us_states_df` es un marco de datos (de la clase `data.frame`) que contiene el nombre y variables adicionales (incluyendo la renta media y el nivel de pobreza, para los años 2010 y 2015) de los estados de EE.UU., incluyendo Alaska, Hawaii y Puerto Rico.
Los datos proceden de la Oficina del Censo de los Estados Unidos, y están documentados en "us_states" y "us_states_df".

E1. Crea un nuevo objeto llamado `us_states_name` que contenga sólo la columna `NAME` del objeto `us_states` utilizando la sintaxis de R base (`[`) o tidyverse (`select()`).
¿Cuál es la clase del nuevo objeto y qué lo hace geográfico?



E2. Selecciona las columnas del objeto `us_states` que contienen datos de población.
Obtén el mismo resultado utilizando un comando diferente (bonus: intenta encontrar tres formas de obtener el mismo resultado).
Sugerencia: intenta utilizar funciones de ayuda, como `contains` o `starts_with` de **dplyr** (ver `?contains`).

E3. Encuentra todos los estados con las siguientes características (bonus: encuéntralos *y* grafícalos):

- Pertenecen a la región del Medio Oeste.
- Pertenecen a la región Oeste, tienen una superficie inferior a 250.000 km^2^ *y* en 2015 una población superior a 5.000.000 de habitantes (pista: puede que tengas que utilizar la función `units::set_units()` o `as.numeric()`).
- Pertenecen a la región Sur, tienen una superficie superior a 150.000 km^2^ o una población total en 2015 superior a 7.000.000 de residentes.

E4. ¿Cuál fue la población total en 2015 en el conjunto de datos `us_states`?
¿Cuál fue la población total mínima y máxima en 2015?

E5. ¿Cuántos estados hay en cada región?

E6. ¿Cuál fue la población total mínima y máxima en 2015 en cada región?
¿Cuál fue la población total en 2015 en cada región?

E7. Añade las variables de `us_states_df` a `us_states`, y crea un nuevo objeto llamado `us_states_stats`.
¿Qué función has utilizado y por qué?
¿Qué variable es la clave en ambos conjuntos de datos?
¿Cuál es la clase del nuevo objeto?

E8. `us_states_df` tiene dos filas más que `us_states`.
¿Cómo puedes encontrarlas? (pista: intenta utilizar la función `dplyr::anti_join()`)

E9. ¿Cuál era la densidad de población en 2015 en cada estado?
¿Cuál era la densidad de población en cada estado en 2010?

E10. ¿Cuánto ha cambiado la densidad de población entre 2010 y 2015 en cada estado?
Calcula el cambio en porcentajes y crea un mapa que lo muestre.

E11. Cambia los nombres de las columnas en `us_states` a minúsculas. (Sugerencia: las funciones - `tolower()` y `colnames()` pueden ayudar).

E12. Usando `us_states` y `us_states_df` crea un nuevo objeto llamado `us_states_sel`.
El nuevo objeto debe tener sólo dos variables - `median_income_15` y `geometry`.
Cambia el nombre de la columna `median_income_15` por `Income`.

E13. Calcula la variación del número de residentes que viven por debajo del nivel de pobreza entre 2010 y 2015 para cada estado. (Sugerencia: Consulta ?us_states_df para ver la documentación sobre las columnas relacionadas con el nivel de pobreza).
Bonus: Calcula el cambio en el *porcentaje* de residentes que viven por debajo del nivel de pobreza en cada estado.

E14. ¿Cuál fue el número mínimo, medio y máximo de personas que viven por debajo del umbral de la pobreza en 2015 en cada región?
Bonus: ¿Cuál es la región con el mayor aumento de personas que viven por debajo del umbral de la pobreza?

E15. Crea un raster desde cero con nueve filas y columnas y una resolución de 0,5 grados decimales (WGS84).
Rellénalo con números aleatorios.
Extrae los valores de las cuatro celdas de las esquinas. 

E16. ¿Cuál es la clase más común de nuestro ejemplo de raster `grain`? (pista: `modal()`)

E17. Traza el histograma y el boxplot del raster `data(dem, package = "spDataLarge")`. 
