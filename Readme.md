#Tutorial Atrapa al goblin multijugador
![Con titulo](http://i.imgur.com/zyxxxRb.png "Juego")
## Juan Nicolás Vargas Moreno
### Systemas y Tecnologias Web
[Enlace al juego](http://codepen.io/innNNN/pen/rxVPxd "Juego codepen")

En este tutorial explicaré como hacer un sencillo juego multijugador utilizando las etiquetas Canvas y JavaScript

Para comenzar a desarrollar nuestro juego debemos crear una etiqueta canvas, para ello utlizamos 

:::js

var canvas = document.createElement("canvas");
var ctx = canvas.getContext("2d");
canvas.width = 512;
canvas.height = 480;
document.body.appendChild(canvas);

En este fragmento creamos un lienzo canvas cuyo contexto será en 2D y definidas sus dimensiones en 512 x 480 pixeles

A continuación vamos a proceder a definir las imagenes que compondrán nuestro juego

:::js

// imagen de fondo
var bgReady = false;
var bgImage = new Image();
bgImage.onload = function () {
	bgReady = true;
};
bgImage.src = "http://uploads.gamedev.net/monthly_04_2014/post-168788-0-87611800-1396469745.png";

:::js

//imagen Heroe 1
var heroReady = false;
var heroImage = new Image();
heroImage.onload = function () {
	heroReady = true;
};
heroImage.src = "http://www.milweevideogames.com/wp-content/uploads/2014/11/hero.png";

:::js

// imagen Heroe 2
var heroReady2 = false;
var heroImage2 = new Image();
heroImage2.onload = function () {
	heroReady2 = true;
};
heroImage2.src = "http://i.imgur.com/OXNUmdy.png";

:::js

// imagen Monstruo
var monsterReady = false;
var monsterImage = new Image();
monsterImage.onload = function () {
	monsterReady = true;
};
monsterImage.src = "http://www.milweevideogames.com/wp-content/uploads/2014/11/monster.png";


Cuando se cargue la imagen nuestro objeto mostrara su atribute Ready = true

El siguiente paso será definir los personajes


:::js

// objetos del juego
var hero = {
	speed: 256 
};

:::js

var hero2 = {
	speed: 256 
};

:::js

var monster = {};
var n_monstruos = 0;
var n_monstruos2 = 0;

Hemos creado dos variables contador para ver quien de los dos jugadores consigue alcanzar más goblins.
También hemos definido la velocidad de los jugadores en 256 pixeles por segundo.
Ahora pasaremos a definir la función para los controles

:::js

// controles teclado
var keysDown = {};

:::js

addEventListener("keydown", function (e) {
	keysDown[e.keyCode] = true;
}, false);

:::js

addEventListener("keyup", function (e) {
	delete keysDown[e.keyCode];
}, false);

Creamos evento de escucha y definimos las funciones keydown y keyup. (Cuando se pulsa un botón)
Ahora vamos a implementar que cuando un jugador atrape a un goblin, vuelvan a aparecer en una posición aleatoria.
El jugador más rapido atrapará al monstruo.

:::js

// Reset del juego al atrapar un monstruo
var reset = function () {
	hero.x = 32 + (Math.random() * (canvas.width-64));
	hero.y = 32 + (Math.random() * (canvas.height - 64));
:::js

  	hero2.x = 32 + (Math.random() * (canvas.width-64));
	hero2.y = 32 + (Math.random() * (canvas.height - 64));
:::js

	monster.x = 32 + (Math.random() * (canvas.width - 64));
	monster.y = 32 + (Math.random() * (canvas.height - 64));
};

Definimos la posicion x e y de cada heroe y monstruo con una funcion Random (Math.random) donde se le pasa el tamaño del lienzo
(canvas.width y canvas.height) Los numeros (32 y 64) son aproximaciones del tamaño de la imagen para que no se tengan en cuenta.


A continuación definiremos las teclas de juego, los numeros 87,83,65,68 corresponden con WASD que serán
nuestras teclas principales de control y 38,40,37,39 las flechas del teclado.

:::js

// Cargar objetos
var update = function (modifier) {
	if (87 in keysDown) { 
		hero2.y -= hero2.speed * modifier;
	}
	if (83 in keysDown) {
		hero2.y += hero2.speed * modifier;
	}
	if (65 in keysDown) { 
		hero2.x -= hero2.speed * modifier;
	}
	if (68 in keysDown) { 
		hero2.x += hero2.speed * modifier;
	}
}
:::js  

 var update2 = function (modifier2) {
	if (38 in keysDown) { // arriba
		hero.y -= hero.speed * modifier2;
	}
	if (40 in keysDown) { // abajo
		hero.y += hero.speed * modifier2;
	}
	if (37 in keysDown) { // izquierda
		hero.x -= hero.speed * modifier2;
	}
	if (39 in keysDown) { // derecha
		hero.x += hero.speed * modifier2;
	}
	
	En esta siguiente funcion veremos si el espacio de algún jugador coincide con el espacio del monstruo. 
	En tal caso significaria que el jugador a cazado al goblin y se le deberá sumar su punto correspondiente luego de llamar a la funcion
	reset que inciará un nuevo juego con posiciones aleatorias
	
	:::js
	
	// Estan en contacto?
	if (
		hero.x <= (monster.x + 32)
		&& monster.x <= (hero.x + 32)
		&& hero.y <= (monster.y + 32)
		&& monster.y <= (hero.y + 32)
	) {
		++n_monstruos;
		reset();
	}
:::js 

  else if (
		hero2.x <= (monster.x + 32)
		&& monster.x <= (hero2.x + 32)
		&& hero2.y <= (monster.y + 32)
		&& monster.y <= (hero2.y + 32)
	) {
		++n_monstruos2;
		reset();
	}
};  

Pasamos ahora a renderizar y definir los objetos para que salgan en pantalla donde si la imagen está cargada mostrará al jugador 
en las posiciones correspondientes.

:::js

var render = function () {
	if (bgReady) {
		ctx.drawImage(bgImage, 0, 0);
	}
	if (heroReady) {
		ctx.drawImage(heroImage, hero.x, hero.y);
	}
	if (heroReady2) {
		ctx.drawImage(heroImage2, hero2.x, hero2.y);
	}
	if (monsterReady) {
		ctx.drawImage(monsterImage, monster.x, monster.y);
	}
	
	
	Mostraremos la puntuación de los jugadores
	
	:::js
	
	ctx.fillStyle = "rgb(250, 250, 250)";
	ctx.font = "24px Helvetica";
	ctx.textAlign = "left";
	ctx.textBaseline = "top";
	ctx.fillText("Azul: " + n_monstruos, 32, 32);

	 :::js
	 
  	ctx.fillStyle = "rgb(250, 250, 250)";
	ctx.font = "24px Helvetica";
	ctx.textAlign = "left";
	ctx.textBaseline = "top";
	ctx.fillText("Rojo: " + n_monstruos2, 290,32);
};


Funcion bucle principal donde esta pidiendo constantemente la entrada de datos (pulsacion de teclas para movimiento)


:::js

var main = function () {
	var now = Date.now();
	var delta = now - then;
:::js

  update2(delta / 1000);
	update(delta / 1000);
	render();
	then = now;
	requestAnimationFrame(main);
};


Y para terminar añadimos el soporte para todos los navegadores


:::js

// Soporte para navegadores
var w = window;
requestAnimationFrame = w.requestAnimationFrame || w.webkitRequestAnimationFrame || w.msRequestAnimationFrame || w.mozRequestAnimationFrame;

:::js

// Let's play this game!
var then = Date.now();
reset();
main();



	

