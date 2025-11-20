El objetivo de este proyecto es crear varias aplicaciones/herramientas relacionadas con videos, subtitulos, que permitan el trabajo con videos y audiovisuales, y soluciones a problemas frecuentes en relación a estos.

### Herramientas Scripts

- 1. [Traductor de Subtitulos usando traductores de navegador como gTranslator y Bing](./subtitles.show.html). Para que funcione, es necesario correr un servidor local como `php -s localhost:8000` o `python3 -m http.server 8000`
- 2. [Interactive Video Navigator.html](./Interactive_Video_Navigator.html). Este visualizador permite visualizar videos de diferentes fuentes, como youtube o local en el ordenador, con hasta 3 subtitulos en diferentes idiomas y un índice de los apartados/capítulos de una forma visual agradable.
- 3. Subtitulos - Sincronización (pendiente)
 
### Tareas

- Desplegar estos scripts en Github Deploy para no necesitar servidor local. 


 ### Pendiente para mejorar el 1. Traductor de subtitulos

> elimina el botón o cuadro que muestra Smart que no es necesario ponerlo ahí y ocupa espacio innecesario y pon el texto ((pending)) en una columna extra al final. Pon el mensaje de la acción que se está ejecutando, en el centro de la pantalla para que sea visible con un spinner. 

<details>
> Usando el mismo input definido en los test, usa este output: 1 00:02:15,360 --> 00:02:21,280 Hallo.   2 00:02:21,280 --> 00:02:23,040 Ah, okay. Ich dachte schon, ich habe   3 00:02:23,040 --> 00:02:27,040 kein Tun. Ich auch gerade gedacht.   4 00:02:27,040 --> 00:02:46,630 M.   5 00:03:15,040 --> 00:05:39,990 Die Stummschaltung ist aktiviert. 6 00:05:40,000 --> 00:05:42,880 Hat er recht an der Stelle,   7 00:05:42,880 --> 00:05:44,000 ne?   8 00:05:44,000 --> 00:05:45,560 Mhm. ist ja auch kein Problem, aber 9 00:05:45,560 --> 00:05:47,720 diesen ganzen Workflow zu verstehen   10 00:05:47,720 --> 00:05:49,520 machst du nicht in zwe Monaten einfach   11 00:05:49,520 --> 00:05:51,440 so nebenbeiri   12 00:05:51,440 --> 00:05:53,479 und sowas.   13 00:05:53,479 --> 00:05:54,319 Mhm. y mira a ver sí funciona el algoritmo. Actualiza el output con este. También deseo que al pulsar en "iniciar..." que se minimice el head a una sola línea solo con este botón y los demás se quiten para que haya más área de visualización de los subtitulos. Además, deseo que en el título de la aplicación que aparece en el tab se ponga el por ciento de avance. Cuando llegue al final, deseo que la aplicación usando toda la información actual ya traducida, que automáticamente exporte en formato .srt lo mismo y se descargue. También que se agregue un botón de exportar que permita hacerlo cuando se desee. Adicionalmente creo que se puede mejorar para que se haga automáticamente al actualizarse la página por el traductor, poniendo algo así como ((pending)) y cuando se detecte que se cambien las líneas que se están mostrando por ((pendiente)) o simplemente, que no aparezca (mejor solución) más en la parte que se visualiza ((pending)), entonces, automáticamente proceder a pasar a la próxima página sin tener que esperar los segundos programados que hace que sea más lento el proceso  y menos inteligente.
</details>
