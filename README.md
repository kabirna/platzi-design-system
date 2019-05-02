# Sistemas de diseño para desarrolladores

Un sistema de diseño es un conjunto de reglas para un producto que puede mutar.

## Instalando herramientas

Vamos a empezar instalando NodeJS y Express

En distribuciones basadas en ArchLinux:

```bash
sudo pacman -Sy nodejs
```

```bash
sudo npm install express-generator -g
```

Ahora nos creamos una carpeta con el nombre de nuestro proyecto y nos deplazamos a ella en la terminal con el comando `cd` y seguimos los siguientes pasos:

1. Creamos la base del proyecto con:

    ```bash
    express --view=pug NombreProyecto
    ```

2. Entamos a la carpeta recién generada

    ```bash
    cd NombreProyecto
    ```

3. Actualizamos las dependencias de NodeJS

    ```bash
    npm install
    ```

4. Iniciamos el servidor

    ```bash
    npm start
    ```

5. Ahora nuestro servidor estará corriendo en el puerto 3000, vamos a nuestro navegador y escribimos:

    <localhost:3000>

6. Si nos vamos dentro de nuestra estructura de archivos veremos que en la carpeta `public` tendremos las carpetas donde iran las imagenes, scripts y hojas de estilo. Dentro de la carpeta `views` encontraremos los archivos relativos a la pagina, entre ellos un `index.pug`, si lo abrimos veremos su estructura:

    ```html
    extends layout

    block content
    h1= title
    p Welcome to #{title}

    ```

    Podemos cambiar los valores de la etiqueta `h1` o la etiqueta `p` para probar.

## Automatización (preprocesadores)

Vamos a instalar `gulp` de manera global con `npm`

```bash
sudo npm install gulp gulp-cli -g
```

¿Qué es gulp?
Gulp es una herramienta para poder automatizar procesos por medio de pipelines, o sea flujos , digamos que le decimos primero haz esto y luego haz esto con tan solo un comando, justo lo que necesitamos.

Dentro de nuestro proyecto vamos a instalar otras dos dependencias:

```bash
npm install gulp-sass -D
```

¿Qué es SCSS?
Como CSS requiere de muchos caracteres pues SCSS lo hace más fácil donde utilizamos el poder de la programación para definir variables y construir de manera modular CSS, esto nos mantiene óptimos y sobre todo podemos asegurar la consistencia de nuestro sistema. Comenzamos a instalar las funciones que necesitas para potenciar tu SCSS.

Ahora vamos a crear un archivo en la raiz de nuestro proyecto llamado `gulpfile.js` y dentro pondremos el siguiente codigo:

```js
var gulp = require('gulp');

gulp.task('hello', function() {
    console.log('Hello!!!!');
});
```

Si nos vamos a la terminar y escribimos:

```bash
gulp hello
```

Nos saldra como respuesta:

```bash
[16:41:42] Starting 'hello'...
Hello!!!!
```

Ahora vamos a utilizar **gulp** para convertir nuestros archivos **.scss** a **CSS**, para esto en nuestro archivo `gulpfile.js` agregamos lo siguiente:

```js
var gulp = require('gulp');
var sass = require('gulp-sass');

gulp.task('hello', function() {
    console.log('Hello!!!!');
});

gulp.task('sass', function(){
    return gulp.src('scss/**/*.scss')
        .pipe(sass()) //Convert Sass to CSS with gulp-sass
        .pipe(gulp.dest('public/stylesheets'))
});
```

Nos vamos a la terminal y lo ejecutamos:

```bash
gulp sass
```

Esto nos generará el resultante en CSS dentro de la ruta especificada (public/stylesheets).

## Mas de automatización

Ahora vamos a minificar el codigo de nuestro CSS, esto nos sirve para compactar nuestro archivo CSS y sea mas rapida su lectura por el navegador.

```bash
npm install gulp-minify-css gulp-concat -D
```

Y agregaremos otra funcion en nuestro archivo `gulpfile.js`

```js
//Minificar el código
var minifyCSS = require('gulp-minify-css');
var concat = require('gulp-concat');

gulp.task('style_min', function () {
    return gulp.src('scss/**/*.scss')
        .pipe(sass())
        .pipe(minifyCSS())
        .pipe(concat('style_main.min.css'))
        .pipe(gulp.dest('public/stylesheets'))
});
```

Lo ejecutamos en la terminal con:

```bash
gulp style_min
```

Esto nos generará un codigo minificado con el nombre `style_main.min.js`.

Para no tener que estar ejecutando constantemente este script en consola cada que hagamos cambios podemos dejar ejecutando un **watcher** para que lo haga en cuanto detecte que hicimos algun cambio, para esto debemos agregar otra función:

```js
//Observar cambios en los archivos SASS
gulp.task('watch', function () {
    gulp.watch('scss/**/*.scss', gulp.series('style_min'));
});
```

Lo ejecutamos en la terminal con:

```bash
gulp watch
```

Ahora cada que hagamos un cambio en nuestros archivos .scss lo actualizara al archivo resultante .css

## SMACSS: Aquitectura de CSS escalable y modular

Existen diferentes paradigmas y vamos a revisar algunos de ellos pues depende mucho de las necesidades de cada proyecto.

SMACSS o Scalable and Modular Architecture for CSS (Arquitectura en CSS Escalable y Modular).

SMACSS se trata de una manera de construcción para los estilos de tu sistema. Es un paradigma basado en las siguientes categorías:

1. Base

    Etiquetas basicas de HTML como:

    > html, body, form, input, a

2. Layout

    Etiquetas pertenecientes a elementos del layout:

    > header, article, footer

3. Module

    Etiquetas pertenencientes a los elementos al interior del layout:

    > header, #article, #footer

4. State

    Se refiere a los estados de los elementos:

    > class="is-collapsed is-error is hidden"

5. Theme

    Crear archivos especificos por tema:

    ```css
    /* In module-name.css*/
    .mod {
        border:1px solid;
    }

    /* In theme.css */
    .mod {
        border-color: blue;
    }
    ```

## OCSS: Orientación de objetos en CSS

OCSS o Object-Oriented CSS (Orientación de objetos en CSS).

Este se trata de un paradigma basado en la creación de objetos, que son básicamente lo que también se llama modulo o componente en otros paradigmas.

Este paradigma puede no incluir la estructura base del html.

## BEM

BEM es un paradigma que anticipa modificadores de un componente.
En esta metodología categorizamos o dividimos las clases de la siguiente manera:

Block (Bloque)
Los bloques consideran contenedores de los componentes que nos sirven para englobarlos tipo objetos.

> header container menu chechbox input

Elements (Elementos)
Los elementos que van dentro de estos contenedores

> menu-item list-item checkbox-caption header-title

Modifiers (modificadores)
Son clases de lo más geniales, nos permiten modificar el comportamiento o estilo de un componente.

> disabled highlighted checked fixed size-big color-yellow

## Naming

Modificadores
Son clases que modifican el comportamiento o clase de un elemento.

Utilidades
Son clases que nos ayudan a generar ciertas propiedades de uso general.

## Tokens (variables)

Nuestro objetivo es sistematizar nuestros procesos entonces nuestro sistema tiene que ser un ente configurable. En este sentido, los tokens son configuraciones que afectan a todo nuestro sistema tales como:

Colores
Tipografía
Reglas de espaciado
Brakepoints
Definiremos nuestros colores basándonos en algún paradigma o en los guidelines y UI kit del producto, para definir estas variables es importante que si trabajamos con un diseñador, este también este presente y pueda verificar la viabilidad de lo que estamos haciendo.

Vamos a crear las reglas necesarias para fundamentar nuestro sistema de columnas y podremos hacer otras extras que nos permitan generar un nuevo sistema de interacción mobile.

## Espaciado

Ya que tenemos elementos dentro de nuestro sistema, ahora tenemos que poner a prueba nuestros tokens de espaciado para ello crearemos un organismo para ver cuál es el efecto de nuestras reglas cuando los átomos conviven en un ecosistema de elementos.

El organismo más común de toda plataforma es el formulario que nos ayuda a recopilar información del usuario. Para esto tomaremos elementos como:

Label
Input
Alert
Btn
Modals

## Grillas

Llegó el momento que nos pongamos rudos y definamos cómo se va a comportar nuestro sistema. Para esto necesitamos crear un sistema de columnas.

Antes de hacerlo vamos a cuestionar si usaremos un layout al que tengamos que seguir, pues esto es bastante importante para la reglamentación de nuestro grid.
Utilizaremos el sistema de doce columnas como lo hace Bootstrap, basados en un layout.
Una vez definido tenemos cómo funcionan nuestras columnas, el departamento de diseño tienen que estimar cómo es que sus componentes se van a comportar y para ello este sistema les ayudara cuando estén diseñando.
Una parte importante es que el documento de diseño como programación, siga las mismas reglas.
Dentro de nuestro wrapper, donde definiremos el contenido construiremos los grid, podemos basarnos en librerías de grids que ya existen en internet, solo tenemos que modificar los estilos para que tengan sentido dentro de nuestro sistema.
Hay tres cosas importantes qué definir dentro del grid de nuestro sistema:
Nomenclatura de columnas
Nomenclatura de los brakepoints
Obtener los tokens de nuestro sistema para esta definición.

## Documentación

Hay que tener en cuenta que somos parte de un proceso, un proceso que empieza desde la idea de resolver un problema, se crea una herramienta y se le da al usuario. Es súper importante documentar cada elemento del sistema para que el siguiente en la construcción de esta gran fabrica de elementos le pueda ser más fácil y eficiente la construcción. También te permite anticiparte a los momentos que puede tener el que se dedique a conectar este componente y brindarle las herramientas necesarias es nuestro trabajo. Algunos puntos importantes que tienes que definir en tu documentación dentro del sistema ## Titulo El nombre del componente que viene desde el diseño del componente ## Descripción La descripción de por qué estamos haciendo este componente y que resuelve ## Implementación Descripción sobre cómo puedes implementar este componente en tu proyecto ## Estados Estados que tiene con los modificadores o utilidades y si pudiera no funcionar en otros contextos o reglas que no se hayan definido. ## Bitacora Donde defines que cambios a tenido el componente durante la vida del sistema