# Introducción

## ¿Qué es ActiveRecord en KumbiaPHP?  
¡Bienvenidos al emocionante mundo de ActiveRecord para KumbiaPHP! 🌟 Imagina poder manejar tus bases de datos con la
facilidad de jugar con bloques de construcción. ActiveRecord es una implementación del patrón de diseño Active Record
que nació en el ecosistema de KumbiaPHP, pero que ahora puede correr libremente en cualquier aplicación PHP o framework.
Con ActiveRecord, puedes mapear objetos PHP directamente a tablas de bases de datos, haciendo que las operaciones CRUD
(Crear, Leer, Actualizar, Eliminar) sean tan fáciles como un paseo por el parque. Olvídate de escribir consultas SQL
tediosas y céntrate en lo que realmente importa: ¡tu lógica de negocio!

## Ventajas y funcionalidades
- **Abstracción de datos**: Olvídate de las complicadas consultas SQL. El modelo traduce todas tus operaciones a SQL
  automáticamente.
- **Simplicidad**: Con métodos predefinidos, las operaciones CRUD están al alcance de tu mano, sin complicaciones.
- **Validaciones y callbacks**: Activa validaciones y acciones en momentos específicos del ciclo de vida de tus modelos
  con solo un par de líneas de código.

## Requisitos previos
- **PHP**: Asegúrate de tener PHP 7.4 o superior instalado.
- **Base de datos**: Compatible con MySQL, PostgreSQL, SQLite y otros sistemas de bases de datos soportados por PDO.

## Instalación y configuración inicial
- **Instalación**: Tienes dos caminos para instalar ActiveRecord y ambos son sencillos.
  - **GitHub**: Clona el repositorio directamente desde [ActiveRecord en GitHub](https://github.com/KumbiaPHP/ActiveRecord/).
  - **Composer**: Añade el paquete a tu archivo `composer.json` o utiliza el comando `composer require kumbia/activerecord`.

- **Configuración**:
  - **Conexión a la base de datos**: Define las credenciales de tu base de datos en el archivo de configuración de tu
    aplicación para establecer una conexión sólida.
  - **Carga del ORM**: Asegúrate de que ActiveRecord esté correctamente cargado en el flujo de tu aplicación, como un
    invitado VIP en una fiesta exclusiva.
  - **Modelo Base**: Crea un modelo base que extienda de `ActiveRecord` para comenzar a definir tus modelos específicos
    y desatar todo el poder de tu ORM.