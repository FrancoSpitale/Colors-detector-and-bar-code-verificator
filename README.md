Descripción del Programa
Este código es un sistema avanzado de detección y clasificación de colores mediante un sensor TCS3210 y un microcontrolador basado en Arduino. Adicionalmente, utiliza un teclado USB HID para ingresar datos, control de relés para activar dispositivos específicos, y una pantalla LCD I2C para mostrar información al usuario. Se enfoca en la interacción con varios componentes para realizar tareas complejas de control y clasificación de objetos.

Características Principales
Sensor de Color TCS3210:

Detecta colores analizando las frecuencias de luz reflejada (rojo, verde y azul).
Clasifica los colores en base a valores predefinidos de TSL (Tinte, Saturación y Luminancia).
Usa un LED para iluminar el objeto a detectar y sensores para determinar el color.
Interacción con Teclado USB:

Utiliza la biblioteca HIDBoot para procesar entradas de un teclado USB.
Detecta teclas presionadas y las almacena para verificar códigos de identificación.
Control de Relés:

Dependiendo del color o del código ingresado, se activan distintos relés (Rele_Ford, Rele_Gdrive, Rele_Combine) para accionar mecanismos externos.
Pantalla LCD I2C:

Muestra información en tiempo real, como:
Color detectado.
Resultado de la clasificación.
Mensajes de error o confirmación.
Memoria EEPROM:

Almacena información del sensor de color para uso futuro.
Permite guardar configuraciones específicas (por ejemplo, si el sensor de color está habilitado o deshabilitado).
Detección de Color:

Clasifica colores como "rojo", "marrón", "amarillo", etc.
Utiliza un algoritmo de normalización y transformación para determinar el tinte, saturación y luminancia del color detectado.
Estructura del Programa
Clases y Estructuras:

KbdRptParser: Procesa eventos del teclado USB.
TSL: Estructura que almacena los valores de tinte, saturación y luminancia.
Funciones Principales:

detectorColor: Retorna los valores de color detectados por el sensor en formato TSL.
colorRead: Lee la frecuencia del color seleccionado (blanco, rojo, verde, azul) y la convierte en un valor.
setMode: Configura el modo de operación del sensor TCS3210 (apagado, escala 1:1, escala 1:5, escala 1:50).
TCS3210setup: Configura los pines del sensor.
Configuración y Ciclo Principal:

En el setup:
Configura el sensor TCS3210.
Inicializa la pantalla LCD.
Configura la conexión USB para el teclado.
En el loop:
Procesa entradas del teclado.
Lee valores del sensor de color.
Clasifica los colores y acciona relés según los resultados.
Control de Salidas:

Activa relés según el color detectado o el código ingresado.
Cada relé controla un mecanismo específico:
Rele_Ford: Activa dispositivos relacionados con el color rojo.
Rele_Gdrive: Activa dispositivos relacionados con el color marrón.
Rele_Combine: Activa dispositivos relacionados con el color amarillo.
Lógica de Clasificación de Colores
Criterios de Clasificación:

Los colores se clasifican en base a rangos predefinidos de valores RGB.
Los resultados se comparan con valores almacenados en la EEPROM para verificar la precisión.
Acción en Base al Color:

Rojo: Activa el relé Rele_Ford y muestra "SENSOR ROJO".
Marrón: Activa el relé Rele_Gdrive y muestra "SENSOR MARRON".
Amarillo: Activa el relé Rele_Combine y muestra "SENSOR AMARILLO".
Casos de Uso
Clasificación de Objetos por Color:

El sistema puede usarse en una línea de ensamblaje para clasificar objetos según su color.
Verificación por Códigos:

Usa el teclado USB para ingresar códigos de identificación.
Clasifica y almacena los resultados en la EEPROM.
Control de Dispositivos:

Activa diferentes dispositivos (como transportadores, actuadores) en función de los colores detectados o códigos ingresados.
