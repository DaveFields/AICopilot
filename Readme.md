# Kit **AICopilot** , _ADAS_ con software

## Idea y compromisos

Se pretende crear un prototipo que represente de manera escalada las opciones que el **Edge Computing** y la **Inteligencia Artificial** pueden brindar al parque móvil mundial.

Los compromisos son:

1. Dotar al conductor de un entorno de datos enriquecido con la inclusión de **Realidad Aumentada**.

2. Dotar de un sistema en **tiempo real** e **independiente** del vehículo.

3. Dotar al vehículo de **escalabilidad** y **mejora continua** con la inclusión de un sistema abierto, y por tanto actualizable.

4. Resumir el conjunto en un kit **cómodo** y poco invasivo, además de competitivo en lo **económico**.

5. Obtener una prueba final, una conducción real en diferentes entornos que de muestra de su utilidad.

## Prototipo

Suposiciones base del hardware:

1. El vehículo cuenta con una fuente de corriente.

2. Contamos con el ya obligatorio sistema de OBD2, aunque esto no sera necesario,su inexistencia limitará alguna de las funcionalidades extra.

Resolveremos los compromisos teniendo en cuenta y explicando sus requerimientos no implícitos. Un "copiloto" o un asistente a la conducción se define :

> Dispositivos auxiliares electrónicos en los vehículos de motor para apoyo al conductor en determinadas situaciones de manejo. Conllevan a menudo aspectos de seguridad, dado que la mayoría de accidentes están provocados por error humano.

Dado que se pretende asistir a las personas, evitando errores, buscamos mejorar las capacidades humanas o ampliarlas. Para ello contamos con una limitación: **debe ser un elemento no invasivo para el conductor**, este enriquecimiento no debe en ningún caso interferir en los sentidos del piloto. Esto deja 2 opciones:

- _Elementos acústicos_: Dado que determinar peligros y geoposicionarlos según una señal acústica se antoja complicado, además de que reduciría el confort general del vehículo pudiendo llegar a ser molesto y limitando algunas funciones lúdicas en el interior del vehículo.

- _Elementos visuales_: La opción óptima es proyectar esta información en el parabrisas o a modo de HUD, ambas opciones están lejos de este proyecto he incluso de la realidad misma del sector automovilístico actual. Por ello y dado que es una aproximación tangencial a la solución, para este trabajo se opta por mostrar en una pantalla lo que se pretende que un día sea el parabrisas de un vehículo del futuro, de tal forma que los vehículos antiguos solo tendrán que cambiar su parabrisas para contar con un servicio ADAS sofisticado, lo que nos dota de la **adaptibilidad** actual y futura.

Es en este punto donde hablamos de Realidad Aumentada, lo cual nos obliga a reflejar la realidad actual de la carretera y mediante procesado de imagen dotarla de elementos visuales que nos adviertan y centren la atención visual en los peligros de la carretera. De ello incluimos dos elementos en el proyecto:

1. Unidad de computación para el procesado de imágenes. Donde la fuente de alimentación para el sistema estará limitada a la corriente del vehículo.

2. Cámara para la obtención de las imágenes a procesar.

La decisión de ambos elementos depende de las propias limitaciones del vehículo, así como de las obligaciones que adquirimos. Queremos que en el futuro exista una mejora continua del sistema, por ello necesitamos un elemento lo más parecido a un ordenador de abordo, se han investigado varios y se ha optado por [Nvidia Jetson Nano](https://www.nvidia.com/es-es/autonomous-machines/embedded-systems/jetson-nano/) que cumple el requisito del costo económico, además de estar optimizada para el procesado de imágenes por su característica de GPU, especializada en cálculos geométricos, lo que le otorga una ventaja de procesamiento de redes neuronales 22 veces superior al popular modelo Raspberry Pi 3B +.

 La Nvidia Jetson Nano cuenta con conexiones 4xUSB y 2xCSI. Dadas las capacidades de computo se podrían procesar las imágenes de varias cámaras simultáneamente pudiéndose encontrar un equilibrio en la canalización de _fps_ y rendimiento. Para las pruebas que se pretenden tener al final de este proyecto se usará una [Camara Raspberry Pi 2](https://www.raspberrypi.com/products/camera-module-v2/) que mejora la velocidad y resolución de las webcam tradicionales también probadas y que vienen limitadas por la conexión USB.

## Software

Determinados los elementos hardware, se estudiaron las posibilidades para el software. La placa Jetson Nano integra y engloba el esfuerzo de la compañía Nvidia en el ámbito de Edge Computing, la asistencia a la conducción está entre una de las soluciones que pretende impulsar este paradigma. Siguiendo las recomendaciones del fabricante se realiza la instalación a partir del sdk [Jetpack 4.6](https://developer.nvidia.com/embedded/jetpack) que proporciona el gestor de arranque, el _kernel de Linux 4.9_, los _firmwares_ necesarios, los controladores de NVIDIA, un sistema de archivos de muestra basado en _Ubuntu 18.04_, lo cual dota al vehículo de la escalabilidad y mejora continua que buscamos, con un sistema de código abierto.

### Procesado de imagenes

Con esta base sondeamos las soluciones, dadas las posibilidades actuales se piensa en el mundo de la inteligencia artificial como solución óptima en el procesado y comprensión del entorno del vehículo, existen multitud de implementaciones de código libre basados en diferentes modelos de redes neuronales convolucionales como : [YOLO](https://arxiv.org/abs/2004.10934), [R-CNN](https://arxiv.org/abs/1703.06870), [SSD](https://arxiv.org/abs/1512.02325)... La mayoría surgen de desarrollos donde se intentan desde hace tiempo buscar algoritmos capaces de aprender a través del entrenamiento. Nuestro caso de estudio nos ha llevado a plantearnos si es necesario obtener y profundizar en estos sistemas. Existen herramientas que permiten la abstracción dentro del campo de la IA como son: [Tensorflow](https://www.tensorflow.org/?hl=es-419), [Torch.ai](https://www.torch.ai/) o aún más de alto nivel como [Keras](https://keras.io/) o [PyTorch](https://pytorch.org/). No obstante, los resultados de modelos ya preentrenados con datos ingentes etiquetados y en plataformas en la nube con altas capacidades computacionales parecen y se prueban como suficientes. Si profundizamos en el mundo de la IA descubrimos que cualquier procesado matemático vectorial o de tensores es mejor procesado por una GPU que con una CPU. Con estos objetivos y enfocado a la aceleración de los proyectos con unidades de procesamiento gráfico en paralelo, Nvidia desarrolla [CUDA](https://developer.nvidia.com/cuda-zone).

Siguiendo con el uso de tecnologías _OpenSource_ necesitamos librerías que nos permitan transmitir y recoger datos de la cámara, en este sentido existe una popular librería llamada [Gstreamer](https://gstreamer.freedesktop.org/). Esta es la base que usa la propia Nvidia para dar soporte y optimizar las canalizaciones de las fuentes de datos en el _Edge Computing_ con [DeepStream](https://developer.nvidia.com/deepstream-sdk).

> DeepStream es un camino para desarrollar canalizaciones de análisis de video altamente optimizadas que incluyen decodificación de video, procesamiento de transmisión múltiple y transformaciones de imágenes aceleradas en tiempo real, fundamentales para aplicaciones de análisis de video de alto rendimiento.

![DeepStream](https://dli-lms.s3.amazonaws.com/data/s-iv-02-v2/images/DS_reference_ds_app_912.png)

Dentro de este SDK encontramos modelos preentrenados y suficientes para este trabajo, dado que están entrenados para detectar : vehículos, personas, señales de tráfico y bicicletas. Todo esto nos dota de un marco _OpenSource_ capaz de mejorar en el futuro, además de una base muy solida empujada por una gran comunidad. En las pruebas realizadas hasta llegar a esta pila de productos se han probado populares redes como [Darknet](https://pjreddie.com/darknet/yolo/) apoyadas por las canalizaciones de _Gstreamer_, obteniéndose velocidades de 10fps a una resolución de 720p. No es hasta probar la estructura de _DeepStream_ que se consiguen velocidades de 28_fps_ a 2814p, lo cual es una clara mejora, es por ello que se opta por una versión sólida y optimizada.

Una vez tenemos el entorno, dotamos al conductor de un marco de visión donde la IA se encarga de marcar cada uno de los objetos de interés que hemos definido. Estos objetos estarán presentes en el campo visual del conductor, destacándolos sin quitar visibilidad y permitiéndole tener una perspectiva más completa, no tan dependiente del factor visual humano que es incapaz de procesar cada elemento del campo visual al mismo tiempo. Una vez enriquecido el campo visual con el marcado de los elementos imprescindibles, dotaremos en este mismo marco de otros elementos claves en la conducción, como pueda ser la velocidad. Esta información extra es dependiente de la lectura de sistemas **OBD** incluidos de serie en el vehículo. Para esta lectura tomamos un lector del puerto serie incluído y enviaremos estos datos a nuestra placa. Inicialmente se tiene en mente el uso de _Bluetooth_, que aunque de serie _Jetson_ no incluye un receptor, existen módulos acoplables. Se pretende dotar de este tipo de solución dada su flexibilidad para futuras interacciones con otros elementos como [V2X](https://es.wikipedia.org/wiki/Sistema_de_comunicaci%C3%B3n_vehicular) que permitan dotar de más novedosos sistemas de prevención de accidentes así como acercar a todos los vehículos a las _SmartCities_.

### Enriquecimiento

La detección de los elementos, en este caso 4 clases definidas como :

- Coche
- Persona
- Señal
- Bicicleta

Sumado a las nociones sobre el vehículo permiten dotar de un sistema de colores que altere su condición según la distancia al objeto, así como la propia velocidad del vehículo. Esto permite, utilizando un código de colores popular y viario, identificar lo _Verde_ como no peligroso, lo _Amarillo_ como un potencial peligro y lo _Rojo_ como peligroso. Enriqueciendo aún más el entorno visual del conductor y abstrayéndolo de cálculos involuntarios, centrándose solo en tomar las medidas oportunas ante esas advertencias.

## Kit y Disposicion

Como se ha indicado hasta ahora el conjunto de los elementos de este kit consta:

1. _Jetson Nano_
2. Cámara
3. Pantalla
4. Lector _OBD_

Su disposición dado que se pretende substituir con este kit los obsoletos ordenadores de abordo, y a falta de parabrisas que permitan proyectar **AR**, dispondremos de la pantalla en el compartimento central del vehículo, solución similar a la que usan agentes de la autoridad desde hace tiempo y más actualmente los modelos _Tesla_. Se pretende de cara a la comodidad y dado que cada modelo cuenta con un panelado frontal de salpicadero distinto, usar elementos comunes para la colocación de los elementos. Queremos que este proyecto se adapte a las necesidades y gusto de cada usuario, y dado que la placa carece de carcasa protectora, se pretende incitar y de esta manera potenciar el uso de la impresión _3D_, para adaptar y colocar tanto cámara como placa. Dado que es imposible crear un modelo valido para los distintos vehículos, sugerimos el uso de la parte trasera del retrovisor central como referencia donde alojar mediante un acople ambos elementos, dotando así a la cámara de una perspectiva centrada del vehículo y maximizando la cantidad de entorno, no vehículo propio, que procesa.

![Tesla](https://www.heise.de/select/ct/2019/18/1566823811925084/contentimages/display-totale_bg_SO.jpg)

El lector OBD deberá acoplarse donde tenga cada vehículo su puerto, los sistemas de alimentación trataran de ser lo mas disimulados posibles, sin obviar que la alimentación deberá ir desde la fuente del vehículo hasta el retrovisor central, asi como la conexión HDMI de la pantalla.

## Futuro y ambiciones

Con este kit se pretende crear una base que permita acercar a todos los vehículos a tecnologías que puedan mejorar la seguridad vial, centrándose principalmente en sistemas que limiten los errores humanos. Se cree además que aunque se hacen esfuerzos en que los nuevos vehículos sean más seguros, capaces de interactuar con el entorno de la mano de las **Smartcities**, este proceso es largo y requiere de un rejuvenecimiento de un parque móvil que actualmente en España se situá en [13,11 años](https://www.elconfidencial.com/motor/industria/2021-08-20/edad-media-parque-movil-espanol_3239410/), la distancia entre las tecnologías de los vehículos se antoja demasiado alta para que las mejorías actuales tengan una repercusión en el ahora, limitando además las posibilidades de tecnologías ya mencionadas como _V2X_, donde un bajo porcentaje de los vehículos actuales podría interactuar. No se pretende obviar con esto que la situación socioeconomica de la última década no ayuda, sino que se pretende hacer énfasis en que el esfuerzo debe de ser bidireccional, si los datos dicen que cada año los vehículos se alejan más tecnlogicamente unos de otros, soluciones que intenten paliar esta distancia intentando ser asequibles, deben de ser las que den respuesta a esta necesidad en el mercado.

### Posibilidades

Hemos hablado de incluir: placa, cámara , _OBD_, pantalla. Expongamos ahora como pueden/podrían dotar de más posibilidades futuras:

- El procesamiento de las imágenes podría seguir mejorando de manera continua, con modelos con más datos de entrenamiento, refinados para distintas situaciones que se adapten mejor a las características de cada vehículo, incluyendo:
  - Mayor número de clases detectables.
  - El uso de la [**segmentación de semántica**](https://gl.wikipedia.org/wiki/Segmentaci%C3%B3n_sem%C3%A1ntica) como mejor representación visual.
  - Detección y **marcado de carril**.
  - Nuevas **alertas** a partir de detección o interpretación de escenas/situaciones:
    - Obras o cortes viales.
    - Atascos.
    - Accidentes.
  - Detección y lectura de velocidades viales.
  - Estimadores de distancia.

Además el procesamiento de esta placa, como ya se dijo, puede incluir más cámaras que asistan por ejemplo:

- En adelantamientos, con los temidos ángulos muertos.
- Mejora en la detección y protección de bicicletas y vehículos de movilidad personal.
- Asistencia de aparcamiento.
- Control de la carga en vehículos de transporte.

Incluir funcionalidades:

- **GPS** con **AR**
- **V2X**
- Alertas de estado de vehículo a traves de **OBD**.
- Alerta a emergencias tras detección de colisión.

La inclusión de una herramienta computacional en el vehículo lo dota de la posibilidad de añadir sensórica y mejoras, grandes o pequeñas ya sea a la conducción o al confort. La inclusión de una herramienta escalable de código abierto, da lugar a la posibilidad de que surja una comunidad que enriquezca más alla de lo aqui expuesto con nuevas necesidades y soluciones, como ha sido la semilla de este proyecto.
