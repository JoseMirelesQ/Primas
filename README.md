# Primas

##Análisis Univariado


##Análisis Bivariado

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


##Árbol de decisión

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
