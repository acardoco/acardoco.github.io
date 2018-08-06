---
layout: post
title:  "Polígonos arrastrables en GoogleMaps"
date:   2018-04-04 17:26:13 +0200
categories: android and MapView
---
{:center-image: style="margin: 0 auto; display: block;"}
En *GoogleMaps* se pueden crear figuras o formas, como marcadores, polígonos, caminos ... No obstante, no aparece la opción de editar los vértices de un polígono o cualquier otra forma que se desee dibujar en el mapa. Por ello, se va a describir resumidamente una solución a esta problemática para el caso de un polígono estándar;

Para conseguir cambiar la forma de un **polígono** se tiene que implementar un mecanismo que permita, a partir de la figura, mover los vértices y actualizarlos en la vista de la interfaz. Como se ha visto, ninguna de las APIs de mapas para Android permite arrastrar los vértices de un polígono creado, por lo que se tiene que hacer unos ajustes para conseguirlo, los cuales se detallan a continuación:

1. Primero hay que preparar el *MapView* con los parámetros que se deseen desde la función ***OnMapReady()***: se dibuja el polígono con las coordenadas de los vértices deseados, se fija la cámara en él, se establece la vista deseada, etc. 

2. A continuación, se dibuja un polígono inicial de partida (como se muestra en la figura de abajo), el cual tendrá **marcadores** (objetos *Java* que poseen la latitud y longitud) por cada vértice que la figura posea.
![Polígono inicial.](/figures/interfaz_editar.png "Polígono inicial."){: center-image}{: height="500px" width="350px"}

3. Posteriormente, se crea un ***Listener*** especial para cada marcador, llamado *OnMarkerDragListener()*, que permite estar al tanto en caso de arrastrar uno de ellos. Esta opción no aparece con figuras más complejas que un marcador simple.

4. Para conseguir el efecto de **arrastrar los vértices** del polígono se tiene que implementar la función del *Listener* llamada *OnMarkerDrag()*, que permite ejecutar sentencias mientras se desplaza el marcador por la pantalla. En la siguiente figura se muestra cómo quedaría el polígono arrastrado:

![Polígono final.](/figures/interfaz_editar_arrastrar.png "Polígono final."){: center-image}{: height="500px" width="350px"}

Mientras el usuario arrastra el vértice y no lo suelte, la función anterior actualiza el *status* del marcador cada pocos **milisegundos**, aspecto que se aprovecha para borrar el polígono actual y luego crear uno nuevo con la posición del vértice actual; en el instante tiempo donde se encuentra actualmente el marcador se dibuja uno nuevo partiendo del anterior, de tal manera que el nuevo polígono dibujado queda con las coordenadas sin inmutar de los vértices no seleccionados más el vértice que se está desplazando.

Existen otras funciones como ***OnMarkerDragStart()***, que actúa solo cuando el usuario inicia empieza a arrastrar el marcador (es parecida a la función *OnClick()*), y ***OnMarkerDragEnd()***. Esta última actúa cuando el usuario levanta el dedo de su selección y es útil para, por ejemplo, persistir los cambios de la nueva posición del vértice arrastrado.
