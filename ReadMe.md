# Laboratorio 3: ANÁLISIS DE CAPA DE TRANSPORTE Y SOCKETS

## Grupo 7:

David Quiroga 202310820 <br>
Nicolas Gonzalez 202310041 <br>
Samuel Rodríguez Torres 202310140

El propósito de este archivo ReadMe.md es explicar los detalles relacionados con la implementación del patrón de arquitectura publish/subscriber con sockets para poder analizar y ver el funcionamiento de los protocolos de la capa de transporte TCP y UDP. Para esto, se hizo codigo en C que nos permitiriera interactuar con elementos de bajo nivel de la máquina, con la excepción de no poder alterar el kerner propio de la máquina donde se hace el handshake, el control de flujo y el control de congestión.

Adicionalmente, se encuentra una sección del protocolo QUIC, el cual se explica en su respectiva sección y es parte del bono propuesto por el laboratorio, por lo que no tiene apartado en el documento PDF de análisis.

# PUB/SUB
El patrón de arquitectura publish/subscriber es un patrón usado para manejar una comunicación asincrona entre escenarios donde la comunicación se maneja unicamente en 1 dirección. Este patrón nace a raiz de la necesidad de desacoplar responsabilidades en ambas partes e incrementar la escalabilidad y el desempeño del sistema. <br>
Para esto, se necesita de un broker que haga de intermedio en la comunicación; recibe todos los mensajes producidos por los publicadores y sabe rediriglos a los suscriptores correspondientes. <br>
En este orden de ideas, cada protocolo cuenta con la implementación de 3 archivos, 
- 1.Publisher: Correspondiente al publicador que genera los mensajes.
- 2.Broker: Corresponde al broker de mensajeria encargado de distribuir los mensajes a los suscriptores que estén registrados en el tema de cada mensaje. 
- 3.Subscriber: Corresponde al suscriptor de cada tema que espera recibir los mensajes generados solo para su tema distribuido por el broker.

# Librerías
Únicamente se utilizaron librerias nativas de C para hacer el socket, las cuales fueron:

- stdio.h: <br>
Aporta funciones de entrada/salida estándar para imprimir al terminal y construir cadenas.

- stdlib.h <br>
Utilidades generales: En los archivos se usa para tener exit y terminar el programa con un código de estado y error (de stdio.h) para mostrar errores legibles.

- string.h <br>
Manejo de cadenas. Aquí se usan strlen (para saber el tamaño de lo que vamos a enviar) y strcspn (para eliminar el salto de línea \n que deja fgets al leer el tema).

- unistd.h <br>
Funciones POSIX para interactual a bajo nivel con el sistema, como close/read/write para el descriptor del socket.

- arpa/inet.h <br>
Tipos y utilidades para redes: struct sockaddr_in, htons (convertir puerto a “network byte order”) e inet_addr (convertir una IP en texto, p. ej. "127.0.0.1", a formato binario).

## Compilación
```bash
gcc subscriber_tcp.c -o subscriber_tcp
```

### Ejecución:
```
./subscriber_tcp
```

# TCP

## publisher_tcp.c

- [Archivo Documentado](https://github.com/LabsRedes/Laboratorio-3/blob/main/publisher_tcp.c) 


#### Explicación

Este programa implementa un cliente suscriptor TCP que se conecta a un broker para recibir mensajes de un tema específico.  


#### Creación del socket

Primero se crea un socket TCP usando IPv4 a través de <sys/socket.h>.  
El socket se configura con la familia de direcciones `AF_INET` y el tipo `SOCK_STREAM`, que corresponde a una conexión TCP.  
Si la creación falla, el programa muestra un error y termina.


#### Conexión al broker
Se prepara una estructura `sockaddr_in` que contiene la dirección IP y el puerto del broker.  
Luego, el cliente intenta conectarse mediante la función `connect`, lo que inicia el handshake TCP con el broker.  
Si la conexión es rechazada o falla, se muestra el mensaje de error correspondiente.


#### Suscripción a un tema
El usuario ingresa el nombre del tema al que desea suscribirse.  
El programa construye un mensaje en el formato `SUBSCRIBE <tema>` y lo envía al broker a través del socket.  
Si el envío falla, se imprime un error y el programa termina.


#### Recepción de mensajes
Una vez suscrito, el cliente entra en un bucle infinito donde espera mensajes del broker.  
Cada vez que recibe datos, los muestra por pantalla sin modificarlos.  
Si el broker cierra la conexión o ocurre un error, el cliente sale del bucle y finaliza.


#### Cierre de la conexión
Al finalizar la comunicación, el socket se cierra correctamente y el programa termina su ejecución limpiamente.


#### Resumen general
- Crea un socket TCP y se conecta al broker.  
- Envía un mensaje de suscripción con el tema deseado.  
- Permanece recibiendo y mostrando mensajes en tiempo real.  
- Finaliza cuando el broker cierra la conexión o hay un error.  


## subscriber_tcp.c
- [Archivo Documentado](https://github.com/LabsRedes/Laboratorio-3/blob/main/publisher_tcp.c) 

## broker_tcp.c
- [Archivo Documentado](https://github.com/LabsRedes/Laboratorio-3/blob/main/publisher_tcp.c) 



# UDP

# QUIC