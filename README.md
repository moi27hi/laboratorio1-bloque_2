Integrantes:

Erick Josué Rivera Velásquez

Moisés Abelino Ramírez Rubio     

Situación problematica:

En el ámbito estudiantil y profesional de ingeniería en El Salvador, es frecuente la necesidad de convertir unidades de medida entre sistemas distintos (métrico e imperial) al trabajar con planos, cálculos físicos, recetas, laboratorios y actividades cotidianas. Los estudiantes y profesionales pierden tiempo buscando fórmulas o calculadoras externas cada vez que necesitan convertir kilómetros a millas, grados Celsius a Fahrenheit, o kilogramos a libras, aumentando el margen de error humano en los cálculos.

Preguntas:
1-Explique con sus propias palabras qué es Vue.js y cuál es su función dentro de la
página web desarrollada.

R// Vue.js es un framework que permite construir frontend "interfaces de usuario" de forma sencilla; Su función en un pagina web es permitir que los desarrolladores cuenten con herramientas que faciliten el trabajo y puedan realizarlo de forma mas agil y rapida en lugar de estar construyendo montañas de codigo desde cero, Vue proporciona paquetes, archivos y herramientas que facilitan la fabricacion de la pagina web.

2-Describa qué variables reactivas utilizó en su aplicación y cuál es la función de
cada una dentro del sistema.

R// En el programa algunas de las variables reactivas que usamos son: 
  ->activeCategory: Guarda la categoria activa (longitud, peso o temperatura).
  ->Guarda la unidad de origen seleccionada (km, millas, kg, etc.).
  ->toUnit: Guarda la unidad de destino seleccionada (metro, libras, etc.).
  ->inputValue: Guarda el número que el usuario ingresa.
  ->result: Guarda el resultado numérico de la conversión. 
  -> Entre otras.

3-Explique la diferencia entre las siguientes directivas utilizadas en su proyecto: v-bind y v-model

R// v-bind es una directiva de enlace uniderecciona, es decir va del dato la vista.Se usa para enlazar dinámicamente un atributo HTML con una expresión de JavaScript. En la aplicacion la usamos para asignar la clase active al boton de la categoria seleccionada. Mientras que el v-model en cambio crea un enlace bidireccional (del dato a la vista y de la vista al dato). Se utiliza principalmente en elementos de formulario como input y select. En la aplicación lo usamos para capturar el valor ingresado por el usuario.

4-Mencione al menos un ejemplo de evento utilizado dentro de su aplicación.

R//En la aplicación usamos el evento @click en el boton convertir. Para que cuando el usuario haga clic se ejecute la funcion convert(), que valida el dato ingresado, realiza el cálculo de conversión y guarda la entrada en el historial.

5-Explique para qué utilizó la directiva v-for dentro de su aplicación.

R//Esta directiva se utilizo para generar las pestañas de categorias dinámicamente a partir del objeto categories, tambien para generar las opciones de los selectores de unidad origen y destino y finalmente para renderizar el historial de conversiones. En todos los casos, v-for permite generar elementos HTML de forma dinámica sin repetir código, simplemente iterando sobre los datos disponibles.

6-Describa en qué situación utilizó v-if y qué problema resuelve dentro de su
interfaz.

R// El v-if se uso en dos eventos, primero para mostrar el mensaje de error solo cuando existe, evitando que aparezca un bloque vacio en la interfaz cuando no hay errores. Solo se renderiza cuando errorMsg tiene contenido. Segundo para alternar entre la lista del historial y el mensaje "Sin conversiones aún".Esto resuelve el problema de mostrar contenido vacío: si el historial tiene entradas, se muestra la lista; si no, se muestra un mensaje informativo al usuario.

7-Explique cómo se realiza la validación de datos en su aplicación y por qué es
importante validar la información ingresada por el usuario.

R//La validación se realiza dentro de la función convert() antes de ejecutar cualquier cálculo Se verifican dos condiciones: 

-> Que el campo no esté vacío (inputValue.value === '') 
-> Que el valor sea un número válido (isNaN(val)) 

Adicionalmente, el botón "Convertir" está deshabilitado con v-bind:disabled="inputValue === ''", lo que impide que el usuario intente convertir sin haber ingresado nada. La validación es importante porque protege al programa de errores inesperados, como intentar operar con NaN o valores indefinidos, lo que podría causar resultados incorrectos o fallos en la aplicación. También mejora la experiencia del usuario al comunicarle claramente qué debe corregir.