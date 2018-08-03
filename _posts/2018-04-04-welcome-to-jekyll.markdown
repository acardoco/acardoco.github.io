---
layout: post
title:  "Redes de neuronas convolucionales"
date:   2018-04-04 17:26:13 +0200
categories: computer vision and deep learning
---
{:center-image: style="margin: 0 auto; display: block;"}

Actualmente existe un gran foco en investigar nuevas técnicas y tipos de *R-CNN* que permitan detectar objetos con una alta precisión y en un tiempo muy reducido: sin ir más lejos, el primer tipo de *R-CNN* fue publicado hace cuatro años por unos investigadores en la universidad de Berkeley y el último hace menos de un año por *Facebook*.

**Redes de neuronas convolucionales y arquitecturas**
==================

En estos últimos años las **redes de neuronas convolucionales** (redes convolucionales o simplemente *convnets*) se han convertido en el *status quo* del ***Deep Learning*** (aprendizaje profundo) y la visión por computador debido a la cantidad de nuevas librerías de inteligencia artificial que se están abriendo al público y al aumento de la capacidad de cómputo para problemas en este ámbito.

Partiendo del concepto de red de neuronas artificiales, una *convnet* (red de neuronas convolucionales) es un tipo de red que se centra en sacar patrones a partir de imágenes para obtener conclusiones de las mismas (características de un objeto, de qué tipo es, si está realizando una acción, etc). Para ello se emplea una operación llamada **convolución**, la cual se aplica a dos matrices para obtener una tercera que comparte características de ambas.

Las imágenes son traducidas a vectores o matrices para ser más fáciles de manipular por unos **filtros** (también llamados *kernels* o campos de recepción locales) que actúan como “lupas” buscando patrones o características (*features*). Estas características se almacenarán en mapas de características (*feature maps*), por lo que al añadir mapas de este tipo más **características** sabrá reconocer la red. (NOTA: una característica puede ser una curva, una línea recta en determinada posición, una combinación de formas, etc).

Por lo tanto, en cada **mapa de características** habrá *n* neuronas donde, para cada una de ellas, se le aplicará una función de activación (*FA*) al producto entre su *kernel* y la entrada más el bias. La **función de activación** corresponde al mismo concepto de *FA* de una red neuronal, y en este caso se suele usar una llamada *ReLU* (*Rectified Linear Unit*) o variantes de la misma (*ELU, Leaky RELU, PReLU*, etc).

Los **pesos son todos iguales y están compartidos** por todas las neuronas en cada mapa de características, por lo que solo se aprenderá una característica en cada uno (otro pilar de las *convnets*), ya que este tipo de redes neuronales se adapta muy bien a la traslación invariante de imágenes.
Por ejemplo, si se detecta un oso panda en una imagen da igual que el oso esté inclinado, esté en la esquina superior o inferior de la foto: seguirá siendo una foto de un oso panda (*sexto capítulo del libro online.

A su vez, hay que tener en cuenta que el tamaño de cada mapa varía en función de los siguientes **parámetros**:

* Tamaño de la imágen de entrada o *input*: normalmente viene dado por una altura multiplicada por una anchura (por ejemplo 48 x 34 píxeles), pero pueden existir redes convolucionales de *N* dimensiones.
* Tamaño del *kernel*: tamaño que tiene el campo de recepción local.
* *Stride*: posiciones consecutivas que recorre el *kernel* en la entrada.
* *Zero-Padding*: opción que añade ceros en los bordes de la imágen para ajustar el *kernel* cuando pasa por la frontera de la entrada.


![Arquitectura típica de una *CNN*. Fuente Wikipedia](/figures/convolution.png "Arquitectura típica de una CNN. Fuente Wikipedia")


Por ejemplo, si no hay **Zero-Padding** y existe un *stride* mínimo de 1, la salida para un *feature map* vendrá dado por el tamaño en un eje de la imágen de entrada (por ejemplo la altura) menos el tamaño del *kernel* en ese eje más uno. Esto es bastante importante porque se está indicando el nivel de detalle de las características y cuán rápido se quiere que se entrene la red (a más tamaño de *kernel* y *strides* pequeños más información se tendrá, pero le llevará más tiempo entrenar al clasificador).

Otro concepto importante que posee una red convolucional es el ***pooling***. Esta capa lo que hace es simplificar el tamaño de la red (*subsampling*), haciendo medias u obteniendo el máximo de una región para así reducir el tamaño del vector sin perder mucha información. Normalmente se coloca después de un par de capas convolucionales para ir gradualmente reduciendo el tamaño del vector final.
No obstante, esta no es la única técnica para reducir el tamaño;  por ejemplo, para la segmentación semántica (se clasifican imágenes a nivel de píxel) se suele querer mantener el máximo de información posible, por tanto se aplica una *dilatación convolucional*.

Pero para entender más a fondo cómo funcionan este tipo de redes hay que estudiar el pilar de estas a nivel matemático, que es la operación matemática llamada **convolución**. Así pues, el resultado para la neurona *j,k* será el siguiente: 

![Ecuación de la red convolucional.](/figures/for1.png "Ecuación de la red convolucional."){: center-image}

La letra *sigma* corresponde a la función de activación (generalmente una *RELU*). La letra *b* es el bias y los índices *l* y *m* corresponden a los ejes del *kernel* empleado (en este caso está en dos dimensiones con tamaño *n*). De esta manera se pasa el filtro por toda la porción de entrada que le toque y se almacena el valor en la neurona *j, k*. Como se puede apreciar, se **compartirán los pesos** que haya en el filtro con todas las neuronas de la capa al aplicarles el mismo filtro a todas ellas, cumplíendose el hecho de que por cada mapa de características solo se aprenderá una característica para todas las neuronas.

Aunque a esta operación se le aplica una convolución, realmente lo que se está haciendo es la operación de **correlación cruzada** (salvo que se indique explicitamente que se está dando la vuelta al filtro).
Dado un filtro *H* y una función *F* el resultado de aplicar correlación, denotado como ![correlación.](/figures/expr1.png "correlación."){: height="25px" width="90px"} es:

![Operación de correlación.](/figures/for2.png "Operación de correlación."){: center-image}

Por otro lado, el resultado de aplicar una convolución, ![convolución.](/figures/expr2.png "convolución."){: height="25px" width="90px"}, es el siguiente:

![Convolución.](/figures/for3.png "Convolución."){: center-image}

Como se puede apreciar, la diferencia entre ambas operaciones reside en que en la segunda operación hay una matriz a la que se le cambia de orden los índices. Esto quiere decir que para el caso de la convolución el filtro se da la **vuelta** completamente, consiguiendo así propiedades como la conmutativa o asociativa de una función lineal en filtros simétricos, lo que reduce considerablemente la complejidad computacional de calcular parámetros. 

De ahí que se apliquen algunas veces convolucionales, porque de esta manera se pueden aplicar filtros base muy potentes como el **filtro de Sobel** (un filtro simétrico), que permite marcar los bordes en una imagen usando gradientes a partir de las intensidades de sus píxeles. No obstante, estos filtros deterministas solo se aplican para casos concretos (por ejemplo, encontrar bordes verticales (eje de las x's)): tener una red que aprende y actualiza sus pesos, como es una red convolucional, permite que se puedan encontrar otros bordes, como por ejemplo bordes con una rotación de 80 grados con respecto a un eje. 

![Ejemplo del resultado de hacer una convolución en una imagen con un filtro de Sobel](/figures/sobel_filter.PNG "Ejemplo del resultado de hacer una convolución en una imagen con un filtro de Sobel"){: center-image}

Con este fin, una red convolucional puede crear variantes de los filtros ya existentes (Sobel, Gaussiano, etc) y sacar las caracerísticas que le permitirán clasificar los objetos finales.

De todas formas, el que se aplique al final la operación de convolución o de correlación depende de la propia librería de aprendizaje automático que se esté empleando ya que, para el caso de este proyecto, *Tensorflow* emplea por detrás correlación, por lo que sus redes convolucionales deberían llamarse realmente redes de neuronas de correlación cruzada. Esto sobretodo depende de lo que resulte más rentable a la hora de procesar los resultados, pero la base teórica es esta, la **convolución**.


En las últimas capas de la **arquitectura de la red** (por ejemplo dos capas convolucionales seguidas de un *max-pooling*) se suele incorporar al menos una capa de neuronas completamente conectadas entre sí (perceptrón multicapa o  *fully connected layer* (*FCL*)) que se encargará de, a partir de las características obtenidas, elegir a qué clase pertenece un determinado objeto; aquí es donde se sacan las conclusiones de la red convolucional ya que se traduce en resultados visibles, como es encuadrar una imagen en una clase. 

Lo malo de esta arquitectura es que se tendrá que transformar el vector obtenido de la **extracción de características** al *input* esperado por la *FCL*, por lo que las imágenes que se pasan como conjunto de entrenamiento a la red deben de ser del mismo tamaño (existen variantes a esto como la red completamente convolucional (*fully convolutional network*)) que sirve para obtener objetos a nivel de pixel).

También hay muchos tipos de arquitecturas, de las cuales las más famosas son las siguientes:


* **AlexNet**: de las primeras arquitecturas en salir a la luz y de la que se parte generalmente para comparar.
* **VGG**: tiene varias versiones en función de las capas incluidas. Ver siguiente figura.

![Arquitectura VGG.](/figures/vgg16.png "Arquitectura VGG."){: center-image}

* **GoogleLeNet**: introduce el concepto de **módulo *inception***, que consiste en añadir filtros paralelos concatenados al final de un número de capas para recabar más características, incluyendo filtros 1x1 para no aumentar la complejidad computacional.

![Arquitectura Inception.](/figures/inception_implement.png "Arquitectura Inception."){: center-image}

* **ResNet**: es una red que permite añadir muchas más capas a la red mediante una **capa residual** que mantiene actualizadas a las capas posteriores que vengan; generalmente en las *convnets* si contienen un cierto número de capas pueden empezar a dar peores resultados o a estancarse, pero con esta idea de capa residual se puede llegar a mejorar las precisiones sin temor a incluir más capas.


Existen otras arquitecturas actualmente, algunas muy nuevas o recientes, o que son combinaciones de otras ya existentes (**Inception** = *ResNet + GoogleLeNet*) o que simplemente se les añaden nuevos parámetros para crear nuevas versiones de las mismas.

Por supuesto los usos de este tipo de redes no solo abarcan indicar qué tipo de objeto aparece en una imagen: se pueden usar para la detección de objetos, segmentación semántica, búsqueda de similitudes… hasta se puede emplear junto a una *NLP* (*Neuro Linguistic Programming*) para describir lo que se visualiza en una imagen.

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
