# Documentación de procedimientos.

# Consultas 
* 1.Cree un procedimiento almacenado que permita insertar un nuevo usuario retornando un mensaje que indique si la inserción fue satisfactoria.

DELIMITER $$

CREATE PROCEDURE insertar_usuario(
    IN nombre VARCHAR(40),
    IN email VARCHAR(100),
    IN contrasena  VARCHAR(100),
    OUT mensaje VARCHAR(100)
)
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    SET mensaje =  'Hubo un error al insertar el usuario';

    INSERT INTO usuarios (nombre, email, contrasena)
    VALUES (nombre, email, contrasena);
SET  mensaje =  'la insercion ha sido exitosa';
END $$

DELIMITER ;

CALL insertar_usuario('osman ortiz', 'osmanortizrolon@gmail.com', 'osman12', @mensaje);

SELECT @mensaje;

* 2.Cree un procedimiento almacenado que permita eliminar un usuario retornando un mensaje que indique si la inserción fue satisfactoria.
DELIMITER $$

CREATE PROCEDURE eliminar_usuario(
    IN u_usuario_id INT,
    OUT mensaje VARCHAR(100)
)
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    SET mensaje =  'Hubo un error al eliminar el usuario';

    DELETE FROM usuarios WHERE  usuario_id =  u_usuario_id;
IF ROW_COUNT() > 0 THEN
               SET  mensaje = CONCAT ('El usuario con ID ', u_usuario_id,' ha sido eliminado exitosamente.');
ELSE 
         SET mensaje = 'No se encontró ningun usuario con ese ID proporcionado.';
    END IF;
END $$

DELIMITER ;

CALL eliminar_usuario(23, @mensaje);

* 3.Cree un procedimiento almacenado que permita editar un usuario retornando un mensaje que indique si la inserción fue satisfactoria.

DELIMITER $$

CREATE PROCEDURE actualizar_usuario(
    IN  u_usuario_id INT,
    IN  nuevo_nombre VARCHAR(100),
    IN  nuevo_email VARCHAR(100),
    IN  nueva_contrasena VARCHAR(70),
    OUT mensaje VARCHAR(100)
)
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    SET mensaje =  'Hubo un error al actualizar el usuario';

    UPDATE  usuarios SET nombre = nuevo_nombre, email = nuevo_email, contrasena = nueva_contrasena
WHERE  usuario_id =  u_usuario_id;
IF ROW_COUNT() > 0 THEN
               SET  mensaje = CONCAT ('El usuario con ID ', u_usuario_id,' ha sido actualizado exitosamente.');
ELSE 
         SET mensaje = 'No se encontró ningun usuario con ese ID proporcionado.';
    END IF;
END $$

DELIMITER ;

CALL actualizar_usuario(33,'Osman rolon','OsmanOrtizrolon@gmail.com','osman09',@mensaje); 

SELECT @mensaje;

* 4.Cree un procedimiento almacenado que permita buscar un usuario por su nombre.
DELIMITER $$

CREATE PROCEDURE buscar_usuario( 
 IN nombre VARCHAR(100) ) 
BEGIN
 SELECT  usuario_id, nombre, email, fecha_creacion     FROM usuarios      WHERE nombre = nombre;
END$$
DELIMITER ;

CALL buscar_usuario('Osman rolon');

* 5.Realice una consulta que permita iniciar una conversación.
DELIMITER $$

CREATE PROCEDURE iniciar_conversacion(
    IN nombre_conversacion VARCHAR(100),
    OUT mensaje VARCHAR(100)
)
BEGIN
    INSERT INTO conversaciones(nombre_conversacion, fecha_creacion) VALUES  (nombre_conversacion,NOW());
SET mensaje =  CONCAT('conversacion" ',nombre_conversacion,' "creada con exito con el ID ', LAST_INSERT_ID());

END $$

DELIMITER ;


CALL iniciar_conversacion('Conversacion nueva', @mensaje);

select @mensaje;
* 6.Realice un procedimiento almacenado que permita agregar un nuevo participante a la conversación y valide si hay capacidad disponible. La cantidad maxima por cada conversación son 5 usuarios. 

DELIMITER $$

CREATE PROCEDURE agregar_participante(
    IN  p_conversacion_id INT,
    IN  p_usuario_id INT,
    OUT mensaje VARCHAR(100)
)
BEGIN
    DECLARE  cantidad_participantes INT;
SELECT  COUNT(*) INTO cantidad_participantes FROM  participantes WHERE conversacion_id = p_conversacion_id;
IF  cantidad_participantes <5 THEN
INSERT INTO  participantes (usuario_id, conversacion_id)
VALUES  (p_usuario_id,p_conversacion_id);
SET mensaje = 'se agrego con exito el participante.';
ELSE 
SET mensaje =  'No se pudo agregar al participante por limite de cupo';
END IF;

END $$

DELIMITER ;

CALL agregar_participante(1,33,@mensaje);

SELECT @mensaje;
