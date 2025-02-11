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
