--- 
title: 'Geocomputación con R'
author: 'Robin Lovelace, Jakub Nowosad, Jannes Muenchow'
date: '2021-11-30'
site: bookdown::bookdown_site
output: bookdown::bs4_book
documentclass: krantz
monofont: "Source Code Pro"
monofontoptions: "Scale=0.7"
bibliography:
  - geocompr.bib
  - packages.bib
biblio-style: apalike
link-citations: yes
colorlinks: yes
graphics: yes
description: "Geocomputación con R is for people who want to analyze, visualize and model geographic data with open source software. It is based on R, a statistical programming language that has powerful data processing, visualization, and geospatial capabilities. The book equips you with the knowledge and skills to tackle a wide range of issues manifested in geographic data, including those with scientific, societal, and environmental implications. This book will interest people from many backgrounds, especially Geographic Information Systems (GIS) users interested in applying their domain-specific knowledge in a powerful open source language for data science, and R users interested in extending their skills to handle spatial data."
github-repo: "Robinlovelace/geocompr"
cover-image: "images/cover.png"
url: https://geocompr.github.io/es/
---



# Bienvenido {-}

Esta es la versión web de *Geocomputación con R*, un libro sobre análisis, visualización y modelado de datos geográficos.

<a href="https://www.routledge.com/9781138304512"><img src="images/cover.png" width="250" height="375" alt="The geocompr book cover" align="right" style="margin: 0 1em 0 1em" /></a>
  
**Nota**: La primera edición del libro en inglés ha sido publicada por CRC Press en la [Serie R] (https://www.routledge.com/Chapman--HallCRC-The-R-Series/book-series/CRCTHERSER). 
Puedes comprar el libro en [CRC Press](https://www.routledge.com/9781138304512), o en [Amazon](https://www.amazon.com/Geocomputation-R-Robin-Lovelace-dp-0367670577/dp/0367670577/) y acceder a la primera edición archivada en la plataforma para libros en abierto [bookdown.org](https://bookdown.org/robinlovelace/geocompr/spatial-class.html). 

Inspirado en [**bookdown**](https://github.com/rstudio/bookdown) y en el movimiento del software libre y de código abierto para el sector geoespacial ([FOSS4G](https://foss4g.org/)), este libro es de código abierto. 
Esto garantiza que su contenido sea reproducible y accesible al público en todo el mundo.

La versión online del libro está alojada en [geocompr.robinlovelace.net](https://geocompr.robinlovelace.net) y se mantiene actualizada gracias a [GitHub Actions](https://github.com/Robinlovelace/geocompr/actions), que proporciona información sobre su "estado de construcción" de la siguiente manera:

[![Actions](https://github.com/Robinlovelace/geocompr/workflows/Render/badge.svg)](https://github.com/Robinlovelace/geocompr/actions)

Esta versión del libro fue elaborada en GH Actions el 2021-11-30.

## ¿Cómo contribuir? {-}

**bookdown** hace que editar un libro sea tan fácil como editar una wiki, siempre que tengas una cuenta en ([sign-up at github.com](https://github.com/join)). 
Una vez iniciada tu sesión en GitHub, haz clic en el icono "Editar esta página" ('Edit this page' en inglés) en el panel derecho del sitio web del libro. 
Esto te llevará a una versión editable del archivo original de [R Markdown](http://rmarkdown.rstudio.com/) que ha generado la página en la que te encuentras.


<!--[![](figures/editme.png)](https://github.com/Robinlovelace/geocompr/edit/main/index.Rmd)-->

Para plantear un problema sobre el contenido del libro (por ejemplo, que el código no se ejecute) o hacer una solicitud de funcionalidad, consulte el [rastreador de problemas](https://github.com/Robinlovelace/geocompr/issues).

Los responsables del mantenimiento y los colaboradores deben seguir el [CÓDIGO DE CONDUCTA](https://github.com/Robinlovelace/geocompr/blob/main/CODE_OF_CONDUCT.md) de este repositorio.

## Reproducibilidad {-}

Para reproducir el código del libro, se necesita una versión reciente de R y que los paquetes estén actualizados. Estos pueden ser instalados con el siguiente comando (que requiere del paquete remotes):

La forma más rápida de reproducir los contenidos del libro si eres principiante en tratar datos geográficos en R puede ser en el navegador web, gracias a [Binder](https://mybinder.org/).

Al hacer clic en el siguiente enlace se abrirá una nueva ventana con RStudio Server en su navegador web, lo que te permitirá abrir archivos de capítulos y ejecutar trozos de código para comprobar que el código es reproducible.

[![Binder](http://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/robinlovelace/geocompr/main?urlpath=rstudio)

Si ves algo como la imagen de abajo, enhorabuena, ha funcionado y puedes empezar a explorar Geocomputación con R en un entorno en la nube (siendo consciente de las normas de uso de [mybinder.org](https://mybinder.readthedocs.io/en/latest/about/user-guidelines.html)):
  
<!-- ![](https://user-images.githubusercontent.com/1825120/134802314-6dd368c7-f5eb-4cd7-b8ff-428dfa93954c.png) -->


<div class="figure" style="text-align: center">
<img src="https://user-images.githubusercontent.com/1825120/134802314-6dd368c7-f5eb-4cd7-b8ff-428dfa93954c.png" alt="Screenshot of reproducible code contained in Geocomputación con R running in RStudio Server on a browser served by Binder" width="100%" />
<p class="caption">(\#fig:index-2-4)Screenshot of reproducible code contained in Geocomputación con R running in RStudio Server on a browser served by Binder</p>
</div>


Para reproducir el código del libro en tu propio ordenador, necesitarás una versión reciente de [R](https://cran.r-project.org/) con paquetes actualizados.
Estos pueden ser instalados usando la librería [**remotes**](https://github.com/r-lib/remotes).


```r
install.packages("remotes")
remotes::install_github("geocompr/geocompkg")
remotes::install_github("nowosad/spData")
remotes::install_github("nowosad/spDataLarge")

# Durante el desarrollo de la segunda edición tal vez necesites versiones de desarrollo de
# otros paquetes para construir el libro, p.ej.:
remotes::install_github("rspatial/terra")
remotes::install_github("mtennekes/tmap")
```

Después de instalar las dependencias del libro, deberías ser capaz de reproducir los fragmentos de código de cada uno de los capítulos del libro.
Si clonas el repo del libro y accedes a la carpeta `geocompr`, deberías poder reproducir el contenido con el siguiente comando:


```r
bookdown::serve_book()
```



Echa un vistazo al [repositorio de GitHub](https://github.com/robinlovelace/geocompr#reproducing-the-book) para los detalles sobre la reproducción del libro.

## Apoya el proyecto  {-}

Si encuentras el libro útil, por favor, apóyalo mediante:

- Hablando de él en persona
- Comunicando sobre el libro en medios digitales, por ejemplo, a través del [hashtag #geocompr](https://twitter.com/hashtag/geocompr) en Twitter (véase nuestro [Libro de visitas](https://geocompr.github.io/guestbook/)) o haciéndonos saber sobre [cursos](https://github.com/geocompr/geocompr.github.io/edit/source/content/guestbook/index.md) en los que se utiliza el libro
- [Citándolo](https://github.com/Robinlovelace/geocompr/raw/main/CITATION.bib) o [enlazándolo](https://geocompr.robinlovelace.net/)
- [Poniendo estrellas](https://help.github.com/articles/about-stars/) en el [repositorio GitHub de geocompr](https://github.com/robinlovelace/geocompr)
- Haciendo reseñas, por ejemplo, en Amazon o [Goodreads](https://www.goodreads.com/book/show/42780859-geocomputation-with-r)
- Haciendo preguntas o sugerencias sobre el contenido a través de [GitHub](https://github.com/Robinlovelace/geocompr/issues/372) o Twitter.
- [Comprando](https://www.amazon.com/Geocomputation-R-Robin-Lovelace-dp-0367670577/dp/0367670577) un ejemplar en papel

Puedes encontrar más detalles en [github.com/Robinlovelace/geocompr](https://github.com/Robinlovelace/geocompr#geocomputation-with-r).

<a href="https://www.netlify.com"><img src="https://www.netlify.com/img/global/badges/netlify-color-accent.svg"/></a>

<a rel="license" href="http://creativecommons.org/licenses/by-nc-nd/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-nd/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-nd/4.0/">Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International License</a>.



# Prólogo (1a Edición) {-}

Hacer "espacial" en R siempre ha sido una cuestión muy amplia, buscando proporcionar e integrar herramientas de geografía, geoinformática, geocomputación y estadística espacial para cualquier persona interesada en participar: participar en la formulación de preguntas interesantes, contribuir con preguntas fructíferas para la investigación, y escribir y mejorar el código. 
Es decir, hacer "espacial" en R siempre ha incluido código abierto, datos abiertos y reproducibilidad.

Hacer "espacial" en R también ha buscado estar abierto a la interacción con muchas ramas del análisis de datos espaciales aplicados, y también implementar nuevos avances en la representación de datos y métodos de análisis para exponerlos al escrutinio interdisciplinario. 
Como demuestra este libro, a menudo existen flujos de trabajo alternativos para obtener resultados similares a partir de datos similares, y podemos aprender de las comparaciones con la forma en que otros crean y entienden sus flujos de trabajo.
Esto incluye aprender de comunidades similares en torno a los SIG de código abierto y lenguajes complementarios como Python, Java, etc.

La amplia variedad de capacidades espaciales de R nunca habría evolucionado sin personas dispuestas a compartir lo que están creando o adaptando.
Esto puede incluir material didáctico, software, prácticas de investigación (investigación reproducible, datos abiertos) y combinaciones de todos ellos. 
Los usuarios de R también se han beneficiado enormemente de las bibliotecas geográficas de código abierto como GDAL, GEOS y PROJ.

Este libro es un claro ejemplo de que, si eres curioso y estás dispuesto a participar, puedes encontrar cosas que es necesario hacer y que se ajustan a tus aptitudes.
Con los avances en la representación de datos y las alternativas de flujo de trabajo, y el número cada vez mayor de nuevos usuarios que a menudo no están expuestos a la línea de comandos cuantitativa aplicada, un libro de este tipo es realmente necesario.
A pesar del esfuerzo que supone, los autores se han apoyado mutuamente para sacar adelante la publicación.

Por lo tanto, este libro fresco está listo para funcionar; sus autores lo han probado durante muchos seminarios y talleres, por lo que los lectores e instructores podrán beneficiarse de saber que los contenidos han sido y siguen siendo probados por personas como ellos.
Comprométete con los autores y con la comunidad R-spatial, observa el valor de tener más opciones en la construcción de tus flujos de trabajo y, lo más importante, disfruta aplicando lo que aprendes aquí en cosas que te interesan.

Roger Bivand

Bergen, September 2018

# Prefacio {-}

## A quién va dirigido este libro {-}

Este libro está dirigido a personas que quieren analizar, visualizar y modelar datos geográficos con software de código abierto.
Se basa en R, un lenguaje de programación estadístico que tiene potentes capacidades de procesamiento de datos, de visualización y geoespaciales.
El libro cubre una extensa variedad de temas y puede ser de interés para un amplio abanico de personas de campos muy distintos, especialmente:

- Personas que han aprendido a realizar análisis espaciales con un Sistema de Información Geográfica (SIG) de escritorio, como [QGIS](http://qgis.org/en/site/), [ArcGIS](http://desktop.arcgis.com/en/arcmap/), [GRASS](https://grass.osgeo.org/) o [SAGA](http://www.saga-gis.org/en/index.html), que quieran acceder a un potente lenguaje de programación de (geo)estadística y de visualización y a las ventajas de un entorno de línea de comandos [@sherman_desktop_2008]:

  > Con la llegada del software SIG "moderno", la mayoría de la gente quiere apuntar y hacer clic en su camino por la vida. Eso está bien, pero hay una enorme cantidad de flexibilidad y poder esperándote en las líneas de comandos.

- Estudiantes graduados e investigadores de campos especializados en datos geográficos, como la geografía, la teledetección, la planificación, los SIG y la ciencia de los datos geográficos
- Académicos y estudiantes que trabajan con datos geográficos --- en campos como la Geología, la Ciencia Regional, la Biología y la Ecología, las Ciencias Agrícolas, la Arqueología, la Epidemiología, la Modelización del Transporte, y la Ciencia de los Datos en sentido amplio --- los cuales requieren la potencia y la flexibilidad de R para su investigación
- Investigadores y analistas aplicados en organizaciones públicas, privadas o del tercer sector que necesitan la reproducibilidad, la velocidad y la flexibilidad de un lenguaje de línea de comandos como R en aplicaciones que tratan datos espaciales tan diversos como la planificación urbana y del transporte, la logística, el geomarketing (análisis de localización de tiendas) y la planificación de emergencias

El libro está diseñado para usuarios de R de nivel intermedio-avanzado interesados en la geocomputación y para principiantes en R que tengan experiencia previa con datos geográficos.
Si eres nuevo tanto en R como trabajando con datos geográficos, no te desanimes: proporcionamos enlaces a materiales adicionales y describimos la naturaleza de los datos espaciales desde una perspectiva de principiante en el capítulo \@ref(spatial-class) y en los enlaces que se proporcionan a continuación.

## Cómo leer este libro {-}

El libro está dividido en tres partes:

1. Parte I: Fundamentos, destinado a ponerte al día con los datos geográficos en R.
2. Parte II: Extensiones, las cuales cubren técnicas avanzadas.
3. Parte III: Aplicaciones, para los problemas del mundo real.

Los capítulos se vuelven progresivamente más difíciles, por lo que recomendamos leer el libro en orden.
Uno de los principales obstáculos para el análisis geográfico en R es su pronunciada curva de aprendizaje.
Los capítulos de la primera parte pretenden abordar esta cuestión proporcionando código reproducible en conjuntos de datos sencillos que deberían facilitar el proceso de iniciación.

Un aspecto importante del libro desde el punto de vista de la enseñanza/aprendizaje son los **ejercicios** al final de cada capítulo.
Al completarlos, desarrollarás tus habilidades y obtendrás la confianza necesaria para abordar distintos problemas geoespaciales.
Las soluciones a los ejercicios, así como varios ejemplos ampliados, se encuentran en la web de apoyo del libro, en [geocompr.github.io](https://geocompr.github.io/).

Los lectores impacientes pueden sumergirse directamente en los ejemplos prácticos, los cuales comienzan en el capítulo \@ref(spatial-class).
Sin embargo, recomendamos leer primero el amplio contexto de *Geocomputación con R* en el capítulo \@ref(intro).
Si eres nuevo en R, también te recomendamos que aprendas más sobre el lenguaje antes de intentar ejecutar los bloques de código proporcionados en cada capítulo (a menos que estés leyendo el libro para entender los conceptos).
Afortunadamente para los principiantes de R, la comunidad de apoyo ha desarrollado una gran cantidad de recursos que pueden ayudar.
Nosotros particularmente recomendamos tres tutoriales:  [R para Ciencia de Datos](http://r4ds.had.co.nz/) [@grolemund_r_2016] y [Programación eficiente con R](https://csgillespie.github.io/efficientR/) [@gillespie_efficient_2016], especialmente [Capítulo 2](https://csgillespie.github.io/efficientR/set-up.html#r-version) (sobre la instalación y configuración de R/RStudio) y [Capítulo 10](https://csgillespie.github.io/efficientR/learning.html) (sobre aprender a aprender), y  [Una introducción a R](http://colinfay.me/intro-to-r/) [@venables_introduction_2017].

## ¿Por qué R? {-}

Aunque R tiene una curva de aprendizaje pronunciada, el método de línea de comandos que se defiende en este libro puede ser rápidamente rentable.
Como aprenderás en los capítulos siguientes, R es una herramienta eficaz para abordar una gran variedad de retos relacionados con los datos geográficos.
Esperamos que, con la práctica, R se convierta en el programa elegido en tu caja de herramientas geoespaciales para muchas aplicaciones.
Escribir y ejecutar comandos en la línea de comandos es, en muchos casos, más rápido que apuntar y hacer clic en la interfaz gráfica de usuario (GUI) de un SIG de escritorio.
Para algunas aplicaciones, como la estadística espacial y el modelado, R puede ser la *única* forma realista de llevar a cabo el trabajo.

Como se indica en la Sección \@ref(why-use-r-for-geocomputation), hay muchas razones para usar R para la geocomputación:
R se adapta bien al uso interactivo que requieren muchos flujos de trabajo de análisis de datos geográficos en comparación con otros lenguajes.
R destaca en los campos de rápido crecimiento de la Ciencia de Datos (que incluye la carpintería de datos, las técnicas de aprendizaje estadístico y la visualización de datos) y el Big Data (a través de interfaces eficientes con bases de datos y sistemas de computación distribuidos).
Además, R permite un flujo de trabajo reproducible: compartir las secuencias de comandos subyacentes a tu análisis permitirá que otros se basen en tu trabajo.
Para garantizar la reproducibilidad en este libro, hemos puesto a disposición su código fuente en [github.com/Robinlovelace/geocompr](https://github.com/Robinlovelace/geocompr#geocomputation-with-r).
Allí encontrarás archivos en la carpeta `code/` que generan figuras:
Cuando el código que genera una figura no se proporciona en el texto principal del libro, el nombre del archivo que la generó se proporciona en el pie de foto (véase, por ejemplo, el pie de foto de la figura \@ref(fig:zones)).
Otros lenguajes como Python, Java y C++ pueden utilizarse para la geocomputación y existen excelentes recursos para aprender geocomputación *sin R*, como se discute en la sección \@ref(software-for-geocomputation).
Ninguno de ellos proporciona la combinación única de ecosistema de paquetes, capacidades estadísticas, opciones de visualización y potentes IDEs que ofrece la comunidad R.
Además, al enseñar a utilizar un lenguaje (R) en profundidad, este libro te proporcionará los conceptos y la confianza necesarios para realizar geocomputación en otros lenguajes.

## Impacto en el mundo real {-}

*Geocomputación con R* proporcionará los conocimientos y las habilidades necesarias para abordar una amplia variedad de cuestiones, incluidas aquellas con implicaciones científicas, sociales y medioambientales, que se manifiestan en los datos geográficos.
Como se describe en la sección \@ref(qué-es-la-geocomputación), la geocomputación no sólo consiste en utilizar ordenadores para procesar datos geográficos:
también se trata del impacto en el mundo real.
Si estás interesado en un contexto más amplio y las motivaciones que hay detrás de este libro, sigue leyendo; se tratan en el capítulo \@ref(intro).

## Agradecimientos {-}



Muchas gracias a todos los que han contribuido directa e indirectamente a través del código de alojamiento y colaboración a través de GitHub, incluyendo las siguientes personas que contribuyeron directamente a través de solicitudes de extracción (pull requests): prosoitos, florisvdh, katygregg, rsbivand, KiranmayiV, zmbc, erstearns, MikeJohnPage, eyesofbambi, nickbearman, tyluRp, marcosci, giocomai, KHwong12, LaurieLBaker, MarHer90, mdsumner, pat-s, gisma, ateucher, annakrystalli, DarrellCarvalho, kant, gavinsimpson, Henrik-P, Himanshuteli, yutannihilation, jbixon13, yvkschaefer, katiejolly, layik, mpaulacaldas, mtennekes, mvl22, ganes1410, richfitz, wdearden, yihui, chihinl, cshancock, gregor-d, jasongrahn, p-kono, pokyah, schuetzingit, sdesabbata, tim-salabim, tszberkowitz.
Un agradecimiento especial a Marco Sciaini, que no sólo creó la imagen de la portada, sino que también publicó el código que la generó (véase `code/frontcover.R` en el repositorio de GitHub del libro). 
Docenas de personas más contribuyeron en línea, planteando y comentando cuestiones, y proporcionando comentarios a través de las redes sociales.

El hashtag `#geocompr` seguirá vivo!

Nos gustaría dar las gracias a John Kimmel, de CRC Press, que ha trabajado con nosotros durante dos años para convertir nuestras ideas iniciales de un plan de libro en la producción final a través de cuatro rondas de revisión.
Los revisores merecen una mención especial: sus detallados comentarios y su experiencia mejoraron sustancialmente la estructura y el contenido del libro.

Damos las gracias a Patrick Schratz y Alexander Brenning, de la Universidad de Jena, por sus fructíferas discusiones y aportaciones a los capítulos \@ref(spatial-cv) y \@ref(eco).
Damos las gracias a Emmanuel Blondel, de la Organización de las Naciones Unidas para la Agricultura y la Alimentación, por su aportación experta a la sección sobre servicios web;
Michael Sumner, por su aportación crítica en muchas áreas del libro, especialmente en la discusión de los algoritmos del capítulo 10;
Tim Appelhans y David Cooley, por sus contribuciones clave al capítulo sobre visualización (capítulo 8);
y Katy Gregg, que corrigió todos los capítulos y mejoró enormemente la legibilidad del libro.

Podrían mencionarse innumerables personas que han contribuido de innumerables maneras.
El último agradecimiento es para todos los desarrolladores de software que hacen posible Geocomputación con R.
Edzer Pebesma (que creó el paquete **sf**), Robert Hijmans (que creó **raster**) y Roger Bivand (que sentó las bases de gran parte del software espacial de R) han hecho posible la computación geográfica de alto rendimiento en R.
