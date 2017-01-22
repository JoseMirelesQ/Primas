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


##

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
