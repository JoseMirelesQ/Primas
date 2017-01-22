# Primas

##Análisis Univariado


##Análisis Multivariado

### Edad y Sexo vs Prima

```R
xrange<-range(primas$Edad)
yrange<-range(primas$Prima)
plot(xrange, yrange, type="n", xlab="Edad", ylab="Prima" ) 

M <- subset(primas, Sexo=="M")
F <- subset(primas, Sexo=="F")

points(F$Edad, F$Prima, lty=8, col=8, pch=19)

points(M$Edad, M$Prima, lty=5, col=5, pch=18)

legend(xrange[1], yrange[2], c("F","M"),cex=.7, col=c(8,5), pch=c(19,18), lty=c(8,5), title="Sexo")
```
![plot of edad y sexo](images/edadsexo.png) 

La anterior gráfica muestra una clara diferencia entre la prima cobrada a mujeres y la cobrada a hombres, donde estos últimos tienen que pagar menos por el seguro de su auto. 

En cambio la edad pareciera ser una variable poco importante al momento de calcular las primas, pues bien puede haber mujeres jóvenes que gastan lo mismo en su seguro de auto que algunas mujeres adultas; lo mismo se percibe en los hombres.


###Árbol de decisión

```R
library(rpart)
library(rpart.plot)

primas2<-primas

primas2$Fecha<-as.Date(primas2$Fecha,"%d/%m/%Y")
clases <- cut(primas2$Fecha, "year", labels=c(2014,2015,2016,2017))
primas2$Fecha <- as.factor(clases)

Arbol<-rpart(Prima  ~ .,data=primas2,parms=list(split="information"))

rpart.plot(Arbol, type=1, extra=100)
```
![plot of arbol de decisión](images/arbol1.png)

El árbol de decisión, en una primera instancia, sólo segmenta una vez: en hombre y mujer.

La raíz del árbol contiene el 100% de la población e indica que en promedio los clientes pagan $1250 por su seguro de auto.

Al segmentar obtenemos dos nodos, cada uno con el 50% de la población:
* **Nodo 1:** Hombres, en promedio pagan una prima de $1000.
* **Nodo 2:** Mujeres, en promedio pagan una prima de $1500.

De nuevo concluimos que las primas son más elevadas para las mujeres, estas son en promedio 50% más caras que las de los hombres.



##Distribución de la variable *Prima*

Con los resultados anteriores nos hemos dado cuenta que la variable *Sexo* influye bastante al momento de calcular una prima, pues en promedio las mujeres pagan más: ¿será que se modela diferente o ambos sexo seguirán la misma distibución salvo un recargo?

Para responder la anterior pregunta analizaremos qué ocurre con la variable *Prima* dado que el cliente es hombre (*Sexo=="M"*) y, por otro lado, dado que es mujer (*Sexo=="F"*):

```R
summary(primas[primas$Sexo=="M","Prima"])
``` 
Min.  | 1st Qu.| Median |  Mean | 3rd Qu. |  Max. 
------|--------|--------|--------|--------|-------
672.2 | 935.2  | 1004.0 | 1000.0 | 1067.0 | 1275.0 


```R
summary(primas[primas$Sexo=="F","Prima"])
```
Min. | 1st Qu.| Median | Mean | 3rd Qu.| Max. 
-----|--------|--------|------|--------|-------
1045 | 1398   | 1504   | 1500 | 1595   | 1958 



Si analizamos la proporción de las medidas de tendencia de las primas de mujeres y hombres nos daremos cuenta, una vez más, que las mujeres pagan aproximadamente 50% más que los hombres.

```R
summary(primas[primas$Sexo=="F","Prima"])/summary(primas[primas$Sexo=="M","Prima"])
```
  Min.  | 1st Qu.| Median | Mean   | 3rd Qu.|   Max.
--------|--------|--------|--------|--------|-------
1.554597|1.494867|1.498008|1.500000|1.494845|1.535686 



Lo mismo ocurre con las distribuciones que se ajustan a los datos. Gracias a la librería `rriskDistributions` y su función `fit.cont()`, podemos encontrar que la variable *Prima* tanto en hombres como en mujeres se ajusta, entre otras, a una distribución Normal o a una distribución Logistica basándonos en la prueba de *Kolmogórov-Smirnov*:

```R
library(rriskDistributions)

#Ajustar distribución en variable "Prima"

#Condición: Sexo=="M" (Hombres)
ajuste<-fit.cont(primas[primas$Sexo=="M","Prima"])

#Condición: Sexo=="F" (Mujeres)
ajuste<-fit.cont(primas[primas$Sexo=="F","Prima"])
```


Dist.   | Hombres|        | Mujeres  |       |
--------|--------|--------|----------|--------
        |Mean    | SD     | Mean     | SD
Normal  |1000    |99.89995|1500      |149.8499 
        |location|  scale |location  |scale
Logistic|1000.7258|56.9268|1500.69773|85.52088

Con esto comprobamos que dada una distribución *X* que describe la prima cobrada a los hombres para su seguro de autos, la variable que determina lo mismo para las mujeres es *(1.5)X*. Por lo tanto supondremos que, en un principio, la variable *Prima* se modela de igual forma para hombres que para mujeres pero al final ***a las mujeres se les cobra un recargo del 50%*** de la prima calculada.

Dicho esto, con el fin de buscar más variables significativas al momento de calcular la prima, modificaremos un poco la tabla de datos aplicando la misma transformación de la variable *Fecha* que el aplicado anteriormente en el Árbol de decisión, cambiando el valor de la prima de cada mujer por ese mismo valor sobre 1.5 y, finalmente, eliminando la variable *Sexo*:

```R
primas2<-primas

#Cambiar el formato de Fecha de dd/mm/yyyy a sólo yyyy
primas2$Fecha<-as.Date(primas2$Fecha,"%d/%m/%Y")
clases <- cut(primas2$Fecha, "year", labels=c(2014,2015,2016,2017))
primas2$Fecha <- as.factor(clases)

#Prima de mujeres = prima de mujeres / 1.5
primas2[primas2$Sexo=="F",]$Prima<-primas2[primas2$Sexo=="F",]$Prima/1.5

#Eliminar la variable "Sexo"
primas2<-primas2[,2:6]

#Así es como se ve nuestra tabla ahora:
head(primas2)
```
![segmentacion](images/primas2.png)
