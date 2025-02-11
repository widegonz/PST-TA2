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
