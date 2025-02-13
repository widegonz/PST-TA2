```bash
cd
mkdir -p ~/movilRobot_ws/src
cd ~/movilRobot_ws/src
```

```bash
ros2 pkg create <nombre_del_paquete> --build-type ament_python --dependencies rclpy 
```
```bash
ros2 pkg create percepcion --build-type ament_python --dependencies rclpy
ros2 pkg create planeacion --build-type ament_python --dependencies rclpy 
ros2 pkg create control --build-type ament_python --dependencies rclpy 
```

```bash
cd ~/movilRobot_ws/
code .
```
```bash
<?xml version="1.0"?>
<package format="3">
  <name>percepcion</name>
  <version>0.0.0</version>
  <description>TODO: Package description</description>
  <maintainer email="israel@todo.todo">israel</maintainer>
  <license>TODO: License declaration</license>

  <depend>rclpy</depend>

  <test_depend>ament_copyright</test_depend>
  <test_depend>ament_flake8</test_depend>
  <test_depend>ament_pep257</test_depend>
  <test_depend>python3-pytest</test_depend>

  <export>
    <build_type>ament_python</build_type>
  </export>
</package>
```

```bash
percepcion/      📂  # Paquete de ROS 2
├── percepcion/  📂  # Carpeta con los scripts Python
│   ├── __init__.py  # Archivo requerido para definir el módulo Python
├── resource/        📂  # Archivos adicionales del paquete
├── test/            📂  # Pruebas del paquete
├── package.xml      📄  # Información del paquete (nombre, dependencias)
├── setup.cfg        📄  # Configuración del paquete en Python
├── setup.py         📄  # Configuración adicional
```

```bash
from setuptools import find_packages, setup

package_name = 'percepcion'

setup(
    name=package_name,
    version='0.0.0',
    packages=find_packages(exclude=['test']),
    data_files=[
        ('share/ament_index/resource_index/packages',
            ['resource/' + package_name]),
        ('share/' + package_name, ['package.xml']),
    ],
    install_requires=['setuptools'],
    zip_safe=True,
    maintainer='israel',
    maintainer_email='israel@todo.todo',
    description='TODO: Package description',
    license='TODO: License declaration',
    tests_require=['pytest'],
    entry_points={
        'console_scripts': [
        ],
    },
)
```

```bash
'nombre_de_ejecutable = paquete.modulo:funcion'
```
Instalar colcon build
```bash
sudo apt install python3-colcon-common-extensions
```
Compilar un workspace

```bash
cd movilRobot_ws/
colcon buil
```

```bash
cd movilRobot_ws/
colcon build --symlink-install
```
### Creacion del Publisher
Creamos un workspace llamado `moveTurtle_ws`

```bash
cd
mkdir -p ~/moveTurtle_ws/src
cd ~/moveTurtle_ws/src
```

Creamos un paquete de python para almacenar los nodos
```bash
ros2 pkg create movimiento --build-type ament_python --dependencies rclpy
```

Podemos crear un nodo desde el terminal tambien 
```bash
cd movimiento/movimiento
touch simple_publisher.py
```

```bash
import rclpy
from rclpy.node import Node
from geometry_msgs.msg import Twist

class MoverTortuga(Node):
    def __init__(self):
        super().__init__('mover_tortuga')
        
        self.publisher_ = self.create_publisher(Twist, '/turtle1/cmd_vel', 10)
        self.timer = self.create_timer(1.0, self.mover)

    def mover(self):
        msg = Twist()
        msg.linear.x = 2.0  # Velocidad hacia adelante
        msg.angular.z = 1.0  # Giro
        self.publisher_.publish(msg)
        self.get_logger().info('Moviendo la tortuga')

def main(args=None):
    rclpy.init(args=args)
    node = MoverTortuga()

    rclpy.spin(node)

    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

En package.xml se debe agregar la dependencia de los mensajes `geometry_msgs`
```bash
  <depend>rclpy</depend>
  <depend>geometry_msgs</depend>
```

En setup.py debemos agregar el ejecutable
```bash
        'console_scripts': [
            'mover_tortuga = movimiento.simple_publisher:main',
        ],
```
Asegurarnos de guardar los cambios

Compilar el espacio de trabajo
```bash
cd ~/moveTurtle_ws
colcon build
source install/setup.bash
```
Probar el algoritmo

En un terminal
```bash
ros2 run turtlesim turtlesim_node
```

```bash
ros2 run movimiento mover_tortuga
```
### Creacion del Suscriber

```bash
cd ~/moveTurtle_ws/src/movimiento/movimiento
touch simple_suscriber.py
```
Dentro agregamos lo siguiente:

```bash
import rclpy
from rclpy.node import Node
from turtlesim.msg import Pose  # Mensaje que envía la posición de la tortuga

class SubscriptorTortuga(Node):
    def __init__(self):
        super().__init__('subscriptor_tortuga')
        
        # Crear el subscriber
        self.subscription = self.create_subscription(
            Pose,                      # Tipo de mensaje
            '/turtle1/pose',           # Tópico al que se suscribe
            self.pose_callback,        # Función de callback
            10                         # Tamaño de la cola
        )
        self.subscription  # Evita que el garbage collector elimine la suscripción

    def pose_callback(self, msg):
        # Muestra la posición en la terminal
        self.get_logger().info(f'Posición: x={msg.x:.2f}, y={msg.y:.2f}, theta={msg.theta:.2f}')

def main(args=None):
    rclpy.init(args=args)  # Inicializa ROS 2
    node = SubscriptorTortuga()  # Crea el nodo
    rclpy.spin(node)  # Mantiene el nodo en ejecución
    node.destroy_node()  # Elimina el nodo al finalizar
    rclpy.shutdown()  # Apaga ROS 2

if __name__ == '__main__':
    main()
```

En package.xml se debe agregar la dependencia de los mensajes `geometry_msgs`
```bash
  <depend>rclpy</depend>
  <depend>geometry_msgs</depend>
```

En setup.py debemos agregar el ejecutable
```bash
        'console_scripts': [
            'mover_tortuga = movimiento.simple_publisher:main',
            'escuchar_pose = movimiento.simple_suscriber:main',
        ],
```
Asegurarnos de guardar los cambios

Compilar el espacio de trabajo
```bash
cd ~/moveTurtle_ws
colcon build
source install/setup.bash
```
Probar el algoritmo

En un terminal
```bash
ros2 run turtlesim turtlesim_node
```
En otro terminal mover la tortuga:
```bash
ros2 run movimiento mover_tortuga
```

En un 3er terminal agregar el Suscriber
```bash
ros2 run movimiento escuchar_pose
```
### Publisher y Suscribers
```bash
import rclpy
from rclpy.node import Node
from geometry_msgs.msg import Twist
from turtlesim.msg import Pose
import math
import time

class SquareTrajectoryNode(Node):
    def __init__(self):
        super().__init__('square_trajectory')

        # Publicador para controlar el robot
        self.publisher_ = self.create_publisher(Twist, '/turtle1/cmd_vel', 10)
        # Suscriptor para recibir posición de la tortuga
        self.pose_subscriber_ = self.create_subscription(Pose, '/turtle1/pose', self.pose_callback, 10)

        # Variables de posición
        self.current_x = None
        self.current_y = None

        # Variables de control de movimiento
        self.start_x = None
        self.start_y = None
        self.state = 0  # 0 = avanzar, 1 = girar
        self.side_count = 0  # Contador de lados recorridos

        # Longitud del lado del cuadrado (ajustable)
        self.side_length = 5.0

        # Timer de control de movimiento
        self.timer = self.create_timer(0.1, self.move_turtle)

    def pose_callback(self, msg):
        """ Actualiza la posición de la tortuga """
        self.current_x = msg.x
        self.current_y = msg.y

    def move_turtle(self):
        """ Controla el movimiento de la tortuga para hacer un cuadrado """
        if self.current_x is None or self.current_y is None:
            return  # Esperar a recibir datos de la posición

        msg = Twist()

        if self.state == 0:  # Mover hacia adelante
            if self.start_x is None or self.start_y is None:
                self.start_x = self.current_x
                self.start_y = self.current_y

            # Calcular distancia recorrida
            distance = math.sqrt((self.current_x - self.start_x)**2 + (self.current_y - self.start_y)**2)
            if distance < self.side_length:  
                msg.linear.x = 1.5  # Avanzar
            else:
                msg.linear.x = 0.0  # Detener avance
                self.state = 1  # Cambiar a estado de giro

        elif self.state == 1:  # Girar 90 grados (con temporizador)
            self.get_logger().info("Girando 90 grados...")
            msg.angular.z = 1.555  # Velocidad de giro
            self.publisher_.publish(msg)
            time.sleep(2.0)  # Esperar ~2.0 segundos para completar el giro
            msg.angular.z = 0.0  # Detener giro
            self.state = 0  # Volver a avanzar
            self.start_x = None  # Resetear coordenadas
            self.start_y = None
            self.side_count = (self.side_count + 1) % 4  # Contar lados y reiniciar después de 4

        self.publisher_.publish(msg)

def main(args=None):
    rclpy.init(args=args)
    node = SquareTrajectoryNode()

    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        pass

    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

### Servicio y Cliente
Cuando trabajamos con servicios, una buena practica de programacion es manejar los servicios por defecto, sin embargo, en caso de querer agregar servicios personalizados, debemos crear un paquete de C++ para poder escribir estos servicios.

Creamos un paquete de C++
```bash
cd ~/moveTurtle_ws/src
ros2 pkg create --build-type ament_cmake tutorial_interfaces
```

Accedemos al paquete y creamos una carpeta llamada `srv`
```bash
cd ~/moveTurtle_ws/src/tutorial_interfaces
mkdir srv
```
Creamos el archivo del servicio

```bash
cd srv
touch AddThreeInts.srv
```

Dentro del archivo que generamos, escribimos lo siguiente:
```bash
int64 a
int64 b
int64 c
---
int64 sum
```
Este es tu servicio personalizado que solicita tres enteros llamados `a`, `b`, y `c`, y responde con un entero llamado `sum`.

Para convertir las interfaces que has definido en código específico de un lenguaje (como C++ y Python) para que puedan ser utilizadas en esos lenguajes, añade las siguientes líneas a CMakeLists.txt:
```bash
find_package(rosidl_default_generators REQUIRED)

rosidl_generate_interfaces(${PROJECT_NAME}
  "srv/AddThreeInts.srv"
)
```

Debemos modificar el package.xml tambien con la siguiente informacion:
```bash
<buildtool_depend>rosidl_default_generators</buildtool_depend>
<exec_depend>rosidl_default_runtime</exec_depend>
<member_of_group>rosidl_interface_packages</member_of_group>
```

Para confirmar que el servicio se agrego correctamente necesitamos compilar el paquete con `colcon build` y usar el siguiente codigo:

```bash
source install/setup.bash
ros2 interface show tutorial_interfaces/srv/AddThreeInts
```

Deberia mostrarse en el terminal lo siguiente:
```bash
int64 a
int64 b
int64 c
---
int64 sum
```
Con esto ya tenemos lo necesario para usar ese servicio en los nodos

Agregamos 2 nodos para la creacion del servicio y el cliente en el paquete movimiento
```bash
cd ~/moveTurtle_ws/src/movimiento/movimiento
touch servicio.py
touch cliente.py
```

Dentro del nodo servicio.py va esta informacion:
```bash
from tutorial_interfaces.srv import AddThreeInts                                                           

import rclpy
from rclpy.node import Node


class MinimalService(Node):

    def __init__(self):
        super().__init__('minimal_service')
        self.srv = self.create_service(AddThreeInts, 'add_three_ints', self.add_three_ints_callback)       

    def add_three_ints_callback(self, request, response):
        response.sum = request.a + request.b + request.c                                                   
        self.get_logger().info('Incoming request\na: %d b: %d c: %d' % (request.a, request.b, request.c))  

        return response

def main(args=None):
    rclpy.init(args=args)

    minimal_service = MinimalService()

    rclpy.spin(minimal_service)

    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

Dentro del nodo cliente.py agregamos este codigo:
```bash
from tutorial_interfaces.srv import AddThreeInts
import sys
import rclpy
from rclpy.node import Node
from functools import partial

class MinimalClientAsync(Node):
    def __init__(self):
        super().__init__('nodo_async')
        self.cliente = self.create_client(AddThreeInts, 'add_three_ints')

    def send_request(self):
        while not self.cliente.wait_for_service(timeout_sec=1.0):
            self.get_logger().info('Service not available, waiting again...')
        
        req = AddThreeInts.Request()
        req.a = int(sys.argv[1])
        req.b = int(sys.argv[2])
        req.c = int(sys.argv[3])

        future = self.cliente.call_async(req)
        future.add_done_callback(partial(self.callback_service, req))

    def callback_service(self, req, future):
        try:
            response = future.result()
            self.get_logger().info(f'Result of add_three_ints: {req.a} + {req.b} + {req.c} = {response.sum}')
        except Exception as e:
            self.get_logger().error(f'Service call failed: {e}')
        finally:
            rclpy.shutdown()
            
def main(args=None):
    rclpy.init(args=args)
    nodo = MinimalClientAsync()
    nodo.send_request()
    rclpy.spin(nodo)  # Esperar hasta que termine el callback
    nodo.destroy_node()

if __name__ == '__main__':
    main()

```

Se agregan los ejecutables al setup.py
```bash
    'servicio = movimiento.servicio:main',
    'cliente = movimiento.cliente:main',
```

Se debe de agregar una dependencia en el paquete de python
```bash
<exec_depend>tutorial_interfaces</exec_depend>
```

Compilamos el paquete
```bash
colcon build --packages-select movimiento
```

Abrimos 2 terminales y probamos el codigo
```bash
ros2 run movimiento servicio
```

```bash
ros2 run movimiento cliente 2 3 1
```

### Implementando Servicios Existentes
En este caso vamos a implementar el servicio del Turtlesim que es capaz de cambiar el camino que va dejando la totuga cuando se mueve.

```bash
import rclpy
from rclpy.node import Node
from geometry_msgs.msg import Twist
from turtlesim.msg import Pose
from turtlesim.srv import SetPen
from functools import partial

class MoverTortuga(Node):
    def __init__(self):
        super().__init__("turtle_controller")

        self.previous_x = 0

        self.cmd_vel_publisher_ = self.create_publisher(Twist, "/turtle1/cmd_vel", 10)
        self.pose_subscriber_ = self.create_subscription(Pose, "/turtle1/pose", self.pose_callback, 10)
        self.get_logger().info("Controlador Iniciado")

    def pose_callback(self, pose):
        cmd = Twist()
        if pose.x > 9.0 or pose.x < 2.0 or pose.y > 9.0 or pose.y < 2.0:
            cmd.linear.x = 1.0
            cmd.angular.z = 0.9
        else:
            cmd.linear.x = 5.0
            cmd.angular.z = 0.0
        self.cmd_vel_publisher_.publish(cmd)

        #Para que el servicio sea llamado una sola vez
        if pose.x > 5.5 and self.previous_x <= 5.5:
            self.previous_x = pose.x
            self.get_logger().info("Cambiando el color a rojo")
            self.call_set_pen_service(255,0,0,3,0)
        elif pose.x <= 5.5 and self.previous_x > 5.5:
            self.previous_x = pose.x
            self.get_logger().info("Cambiando el color a verde")
            self.call_set_pen_service(0,255,0,3,0)

    def call_set_pen_service(self, r, g, b, width, off):
        #Creamos el cliente
        cliente = self.create_client(SetPen, "/turtle1/set_pen")
        
        #Nos aseguramos de que el servicio esta disponible
        while not cliente.wait_for_service(1.0):
            self.get_logger().warn("Esperando por el servicio...")
        
        #Se crea la solicitud
        request = SetPen.Request()
        request.r = r
        request.b = b
        request.g = g
        request.width = width
        request.off = off

        #Enviamos la solicitud al servicio
        future = cliente.call_async(request)

        #Se crea un callback para manejar la respuesta cuando el servicio la envia
        future.add_done_callback(partial(self.callback_set_pen))


    def callback_set_pen(self, future):
        #Nos aseguramos de manejar los errores que puedan existir al momento de que el servicio responde
        try:
            #Si todo esta bien, obtenemos la respuesta del servicio
            respuesta = future.result()
        except Exception as e:
            self.get_logger().error(f"La llamada al servicio fallo: {e}")

    
def main(args=None):
    rclpy.init(args=args)
    node = MoverTortuga()

    rclpy.spin(node)

    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()

```



