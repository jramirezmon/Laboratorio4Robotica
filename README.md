# Laboratorio 4 Robótica: Cinemática directa
## Universidad Nacional de Colombia
## Integrantes: Allan Giordy Serrato Gutierrez y Juan Fernando Ramirez Montes

## Objetivos

Para este laboratorio se busca establecer una comunicación entre el robot y el computador, llegando a enviar posiciones específicas para cada junta con el fin de alcanzar la posición deseada, para cumplir con este bjetivo global se tienen las siguientes metas puntuales.

- Crear todos los Joint Controllers con ROS para manipular servomotores Dynamixel AX-12 del robot Phantom X Pincher.
- Manipular los tópicos de estado y comando para todos los Joint Controllers del robot Phantom X Pincher.
- Manipular los servicios para todos los Joint Controllers del robot Phantom X Pincher.
- Conectar el robot Phantom X Pincher con MATLAB usando ROS.

A continuación se presentan los videos, el desarrollo desde el código y el análisis del trabajo realizado con el robot en cuestión.


### Video

https://user-images.githubusercontent.com/51063831/195430627-579bb834-b6b5-4665-8b26-7a8b4e25e530.mp4


### Definición Parámetros DH

Para este laboratorio se hizo uso de un calibrador pie de rey con el fin de obtener las dimensiones del robot y de esta manera establecer los valores para cada eslabón. De dicho trabajo se obtuvo la siguiente tabla:

![Tabla](https://user-images.githubusercontent.com/51063831/195765822-5eb767d6-6079-4dbd-868b-b4821d1e8d06.jpeg)

### Muestra posiciones en Matlab con Peter-Corke

#### Posición Home definida
![home](https://user-images.githubusercontent.com/51063831/195764673-d1845a78-2625-490e-a8af-169bb778d4d7.jpeg)

#### Posición 2
![conf2](https://user-images.githubusercontent.com/51063831/195764694-a8a7229e-23fb-4482-825c-9aa0c286d00d.jpeg)

#### Posición 3
![conf3](https://user-images.githubusercontent.com/51063831/195764698-32bce2f7-1a44-4e1e-8079-cabe699936cd.jpeg)

#### Posición 4
![conf4](https://user-images.githubusercontent.com/51063831/195764703-3406a615-55e3-4f62-b81e-c549d80c6957.jpeg)

#### Posición 5
![conf5](https://user-images.githubusercontent.com/51063831/195764710-0eab6a20-37df-4250-bda4-81d9e10d48e5.jpeg)


