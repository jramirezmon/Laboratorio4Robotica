# Laboratorio 4 Robótica: Cinemática directa
## Universidad Nacional de Colombia
## Integrantes: Allan Giordy Serrato Gutierrez y Juan Fernando Ramirez Montes

## Objetivos

Para este laboratorio se busca establecer una comunicación entre el robot y el computador, llegando a enviar posiciones específicas para cada junta con el fin de alcanzar la posición deseada, para cumplir con este bjetivo global se tienen las siguientes metas puntuales.

- Crear todos los Joint Controllers con ROS para manipular servomotores Dynamixel AX-12 del robot Phantom X Pincher.
- Manipular los tópicos de estado y comando para todos los Joint Controllers del robot Phantom X Pincher.
- Manipular los servicios para todos los Joint Controllers del robot Phantom X Pincher.
- Generar distinatas poses con el Phantom X Pincher usando ROS+Dynamixel por medio de un script de MATLAB o Python.

A continuación se presentan el video, el desarrollo desde el código y el análisis del trabajo realizado con el robot en cuestión.


### Video Práctica

https://user-images.githubusercontent.com/51063831/195430627-579bb834-b6b5-4665-8b26-7a8b4e25e530.mp4


### Definición Parámetros DH

Para este laboratorio se hizo uso de un calibrador pie de rey con el fin de obtener las dimensiones del robot y de esta manera establecer los valores para cada eslabón. De dicho trabajo se obtuvo la siguiente tabla:

![Tabla](https://user-images.githubusercontent.com/51063831/195765822-5eb767d6-6079-4dbd-868b-b4821d1e8d06.jpeg)

Usando estos parámetros se construyo el módelo del robot usando el Toolbox de PeterCoke en matlab para poder gráficar las distintas posiciones deseadas para el PhantomX.

### Comparación Poses MATLAB vs Práctica 
A continuación se muestran las comparaciones entre las poses gráficadas en MATLAB y las obtenidas al enviar las mismas coordenadas al PhantomX usando el script de python y los servicios de ROS.

En lás gráficas de MATLAB solo se gráficaron los ejes de rotacion, por lo que la última coordenda correspondiente al rotor que cierra o abre el griper no se tienen en cuenta en dichas gráficas.

#### Posición Home [0, 0, 0, 0, 0]°

![home](https://user-images.githubusercontent.com/51063831/195764673-d1845a78-2625-490e-a8af-169bb778d4d7.jpeg)

![Home](https://user-images.githubusercontent.com/62154397/195899708-13748b57-2e74-4be2-b9e6-67db037a3505.jpeg)


#### Posición 2 [-20, 20, -20, 20, 0]°

![conf2](https://user-images.githubusercontent.com/51063831/195764694-a8a7229e-23fb-4482-825c-9aa0c286d00d.jpeg)

![Conf21](https://user-images.githubusercontent.com/62154397/195900229-4d3b70c0-50f8-40e6-8cb8-f4447cd74cf0.jpeg)


#### Posición 3 [30, -30, 30, -30, 0]°

![conf3](https://user-images.githubusercontent.com/51063831/195764698-32bce2f7-1a44-4e1e-8079-cabe699936cd.jpeg)

![Conf32](https://user-images.githubusercontent.com/62154397/195900551-aa66d781-ed11-4423-acbe-8ea043d6631c.jpeg)


#### Posición 4 [-90, 15, -55, 17, 0]°
![conf4](https://user-images.githubusercontent.com/51063831/195764703-3406a615-55e3-4f62-b81e-c549d80c6957.jpeg)

![Conf42](https://user-images.githubusercontent.com/62154397/195901274-2aad1610-5001-4066-887c-17c5c63004c2.jpeg)


#### Posición 5 [-90, 45, -55, 45, 10]°

![conf5](https://user-images.githubusercontent.com/51063831/195764710-0eab6a20-37df-4250-bda4-81d9e10d48e5.jpeg)
  
![Conf53](https://user-images.githubusercontent.com/62154397/195901492-6b361e1b-6ef8-40dc-9644-3c2e3cbd5b72.jpeg)


## Script de Python
Para realizar la práctica mostrada en el video, se uso un script de python dentro de una arquitectura ROS basada en el repositorio [dynamixel_one_motor](https://github.com/fegonzalez7/dynamixel_one_motor) que implementaba el [dynamixelworkbench](https://github.com/ROBOTIS-GIT/dynamixel-workbench) para controlar los servomotores Dynamixel AX-12 del PhantomX. 

A continuación se explican las distintas partes del script utilizado para enviar las instrucciones al robot utilizado.

### Importación de librerias
```console
import rospy
import numpy as np
from std_msgs.msg import String
from sensor_msgs.msg import JointState
from trajectory_msgs.msg import JointTrajectory, JointTrajectoryPoint
 ```    
 
### Función deg(degrees)
```console
def deg(degrees):
    angle=degrees*np.pi/180
    return angle
```
Se creo esta función para poder pasar de grados ° a radianes, ya que el servicio del dynamixel requiere que las coordenadas de los distintos motores se escuentren en radianes.

### Definición de posturas
```console
#postura deseada.
home=[0,0,0,0,0]
rest=[0,deg(-110),deg(90),deg(5),0]
pos1=[deg(-20),deg(20),deg(-20),deg(20),0]
pos2=[deg(30),deg(-30),deg(30),deg(-30),0]
pos3=[deg(-90),deg(15),deg(-55),deg(17),0]
pos4=[deg(-90),deg(45),deg(-55),deg(45),deg(10)]
#array de posturas.
postura=[home,pos1,pos2,pos3,pos4,rest]
```
Se definen las distintas posturas a obtener del PhantomX como arrays de los distintos ángulos para los 5 ejes de rotación del robot en radianes. Los primeros 4 ejes corresponden a los definidos el el modelo DH hecho previamente, y el último es el ángulo del rotor que abre y cierra el gripper del robot.

### Función joint_publisher()

```console
def joint_publisher():
    pub = rospy.Publisher('/joint_trajectory', JointTrajectory, queue_size=0)
    rospy.init_node('joint_publisher', anonymous=False)
    
    while not rospy.is_shutdown():
        #control de mov. con teclas.
        key=input()
        if key == 'a':
            postura = pos1
            key = ' '
        elif key == 'w':
            postura = pos2
            key = ' '
        elif key == 's':
            postura = pos3
            key = ' '
        elif key == 'd':
            postura = pos4
            key = ' '
        elif key == 'q':
            postura = home
            key = ' '
        elif key == 'e':
            postura = rest
            key = ' '

        state = JointTrajectory()
        state.header.stamp = rospy.Time.now()
        state.joint_names = ["joint_1","joint_2","joint_3","joint_4","joint_5"]
        point = JointTrajectoryPoint()
        point.positions = postura  
        point.time_from_start = rospy.Duration(0.5)
        state.points.append(point)
        pub.publish(state)
        print('published command')
        rospy.sleep(1)
```
#### Nodo Publisher 
Esta función es el cuerpo del código. Las primeras dos lineas de esta función crean un nodo publicador dentro de la arquitectura ROS y lo inicializan.

#### Bucle infinito
A continuación se crea un while que se ejecutara mientras se corrá el script.

#### Input Handler
Dentro del while se encuentra una estructura de condicionales que permiten cambiar la postura a publicar basado en el input de teclado dado por el usuario. Pudiendo usar las tedlas 'w' 'a' 's' 'd' 'q' y 'e' para navegar entre las distintas poses propuestas.

#### Modificar Trayectoria y Publicar
Las últimas lineas de código se encargan de modificar la propiedad de joint_trayctory de la arquitectura y publicar dicha información para enviarla al PhantomX. 

Primero se le asignan los nombres a los diferentes motores que se quieren controlar (los nombres se designan en el archivo .yaml que se explica más adelante). En este caso se desean controlar los 5 servomotores del robot.

Luego se le asignan las posiciones a cada joint definió por medio del array escogido en el condicional anterior para alcanzar la postura deseada.

Por último se da un tiempo de duración para la publicación del nodo, y se le da la orden de publicar la información que se le acaba de dar.

### Handler de Errores
```console
if __name__ == '__main__':
    try:
        joint_publisher()
    except rospy.ROSInterruptException:
        pass
```

Por último se pone un event handler para el código de forma que pare cuando halla un error en la ejecución del catkin dentro de ROS.

## Archivo .yaml
Para poder controlar los cinco servomotores con el catkin utilizado, se debe crear un archivo **.yaml** que contenga los IDs de los 5 servomotores, ya que estos IDs son los que utilizará el paquete dynamixel_one_motor para saber que motor controlar. Los IDs de cada motor se pueden visualizar dentro del software DynamixelWizard por medio de una conexión rápida con el pincher por medio de su interfaz.

A continuación se muestra el código dentro del archivo basic.yaml creado dentro del catkin.

```console
# You can find control table of Dynamixel on emanual (http://emanual.robotis.com/#control-table)
# Control table item has to be set Camel_Case and not included whitespace
# You are supposed to set at least Dynamixel ID
joint_1:
  ID: 1
  Return_Delay_Time: 0
  # CW_Angle_Limit: 0
  # CCW_Angle_Limit: 2047
  # Moving_Speed: 100
  
joint_2:
 ID: 2
 Return_Delay_Time: 0
 # CW_Angle_Limit: 0
 # CCW_Angle_Limit: 2047
 #Moving_Speed: 512
  
joint_3:
 ID: 3
 Return_Delay_Time: 0
 # CW_Angle_Limit: 0
 # CCW_Angle_Limit: 2047
 #Moving_Speed: 512
  
joint_4:
 ID: 4
 Return_Delay_Time: 0
 # CW_Angle_Limit: 0
 # CCW_Angle_Limit: 2047
 #Moving_Speed: 512
  
joint_5:
 ID: 5
 Return_Delay_Time: 0
 # CW_Angle_Limit: 0
 # CCW_Angle_Limit: 2047
 # Moving_Speed: 512
```
Este archivo -yaml debe ser creado en el path **catkin1_ws/src/dynamixel_one_motor/config**.


