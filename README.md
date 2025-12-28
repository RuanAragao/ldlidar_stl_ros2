- [cn](#操作指南)
- [en](#Instructions)
- [pt-br](#Instruções)

# 操作指南

> 此 SDK 仅适用于深圳乐动机器人有限公司销售的激光雷达产品，产品型号为:
>
> - LDROBOT LiDAR LD06
> - LDROBOT LiDAR LD19
> - LDROBOT LiDAR STL-27L

## 0. 获取雷达的 ROS2 功能包

```bash
cd ~

mkdir -p ldlidar_ros2_ws/src

cd ldlidar_ros2_ws/src

git clone  https://github.com/ldrobotSensorTeam/ldlidar_stl_ros2.git
```

## 1. 系统设置

- 第一步，通过板载串口或者 USB 转串口模块(例如,cp2102 模块)的方式使雷达连接到你的系统主板.
- 第二步，设置雷达在系统中挂载的串口设备-x 权限(以/dev/ttyUSB0 为例)
  - 实际使用时，根据雷达在你的系统中的实际挂载情况来设置，可以使用`ls -l /dev`命令查看.

```bash
cd ~/ldlidar_ros2_ws

sudo chmod 777 /dev/ttyUSB0
```

- 第三步，修改`launch/`目录下雷达产品型号对应的 lanuch 文件中的`port_name`值，以`ld06.launch.py` 和 `/dev/ttyUSB0`为例，如下所示.

```py
#!/usr/bin/env python3
from launch import LaunchDescription
from launch_ros.actions import Node

'''
Parameter Description:
---
- Set laser scan directon:
  1. Set counterclockwise, example: {'laser_scan_dir': True}
  2. Set clockwise,        example: {'laser_scan_dir': False}
- Angle crop setting, Mask data within the set angle range:
  1. Enable angle crop fuction:
    1.1. enable angle crop,  example: {'enable_angle_crop_func': True}
    1.2. disable angle crop, example: {'enable_angle_crop_func': False}
  2. Angle cropping interval setting:
  - The distance and intensity data within the set angle range will be set to 0.
  - angle >= 'angle_crop_min' and angle <= 'angle_crop_max' which is [angle_crop_min, angle_crop_max], unit is degress.
    example:
      {'angle_crop_min': 135.0}
      {'angle_crop_max': 225.0}
      which is [135.0, 225.0], angle unit is degress.
'''

def generate_launch_description():
  # LDROBOT LiDAR publisher node
  ldlidar_node = Node(
      package='ldlidar_stl_ros2',
      executable='ldlidar_stl_ros2_node',
      name='LD06',
      output='screen',
      parameters=[
        {'product_name': 'LDLiDAR_LD06'},
        {'topic_name': 'scan'},
        {'frame_id': 'base_laser'},
        {'port_name': '/dev/ttyUSB0'},
        {'port_baudrate': 230400},
        {'laser_scan_dir': True},
        {'enable_angle_crop_func': False},
        {'angle_crop_min': 135.0},
        {'angle_crop_max': 225.0}
      ]
  )

  # base_link to base_laser tf node
  base_link_to_laser_tf_node = Node(
    package='tf2_ros',
    executable='static_transform_publisher',
    name='base_link_to_base_laser_ld06',
    arguments=['0','0','0.18','0','0','0','base_link','base_laser']
  )


  # Define LaunchDescription variable
  ld = LaunchDescription()

  ld.add_action(ldlidar_node)
  ld.add_action(base_link_to_laser_tf_node)

  return ld
```

## 2. 编译方法

使用 colcon 编译.

```bash
cd ~/ldlidar_ros2_ws

colcon build
```

## 3. 运行方法

### 3.1. 设置功能包环境变量

- 编译完成后需要将编译生成的相关文件加入环境变量，便于 ROS 环境可以识别， 执行命令如下所示， 该命令是临时给终端加入环境变量，意味着您如果重新打开新的终端，也需要重新执行如下命令.

  ```bash
  cd ~/ldlidar_ros2_ws

  source install/setup.bash
  ```

- 为了重新打开终端后，永久不用执行上述添加环境变量的命令，可以进行如下操作.

  ```bash
  echo source ~/ldlidar_ros2_ws/install/setup.bash >> ~/.bashrc

  source ~/.bashrc
  ```

### 3.2. 启动激光雷达节点

- 产品型号为 LDROBOT LiDAR LD06

  - 启动 ld06 lidar node:

  ```bash
  ros2 launch ldlidar_stl_ros2 ld06.launch.py
  ```

  - 启动 ld06 lidar node 并显示激光数据在 Rviz2 上:

  ```bash
  ros2 launch ldlidar_stl_ros2 viewer_ld06.launch.py
  ```

- 产品型号为 LDROBOT LiDAR LD19
  - 启动 ld19 lidar node:
  ```bash
  ros2 launch ldlidar_stl_ros2 ld19.launch.py
  ```
  - 启动 ld19 lidar node 并显示激光数据在 Rviz2 上:
  ```bash
  ros2 launch ldlidar_stl_ros2 viewer_ld19.launch.py
  ```
- 产品型号为 LDROBOT LiDAR STL-27L
  - 启动 stl27l lidar node:
  ```bash
  ros2 launch ldlidar_stl_ros2 stl27l.launch.py
  ```
  - 启动 stl27l lidar node 并显示激光数据在 Rviz2 上:
  ```bash
  ros2 launch ldlidar_stl_ros2 viewer_stl27l.launch.py
  ```

## 4. 测试

> 代码支持 ubuntu20.04 ROS2 foxy 版本及以上测试，使用 rviz2 可视化。

- 新打开一个终端 (Ctrl + Alt + T),运行命令:`rviz2`,并通过 Rviz2 工具打开 readme 文件所在目录的 rviz2 文件夹下面的 ldlidar.rviz 文件

```bash
rviz2
```

# Instructions

> This SDK is only applicable to the LiDAR products sold by Shenzhen LDROBOT Co., LTD. The product models are :
>
> - LDROBOT LiDAR LD06
> - LDROBOT LiDAR LD19
> - LDROBOT LiDAR STL-27L

## step 0: get LiDAR ROS2 Package

```bash
cd ~

mkdir -p ldlidar_ros2_ws/src

cd ldlidar_ros2_ws/src

git clone  https://github.com/ldrobotSensorTeam/ldlidar_stl_ros2.git
```

## step 1: system setup

- Connect the LiDAR to your system motherboard via an onboard serial port or usB-to-serial module (for example, CP2102 module).

- Set the -x permission for the serial port device mounted by the radar in the system (for example, /dev/ttyUSB0)

  - In actual use, the LiDAR can be set according to the actual mounted status of your system, you can use 'ls -l /dev' command to view.

```bash
cd ~/ldlidar_ros2_ws

sudo chmod 777 /dev/ttyUSB0
```

- Modify the `port_name` value in the Lanuch file corresponding to the radar product model under `launch/`, using `ld06.launch.py` and `/dev/ttyUSB0` as an example, as shown below.

```py
#!/usr/bin/env python3
from launch import LaunchDescription
from launch_ros.actions import Node

'''
Parameter Description:
---
- Set laser scan directon:
  1. Set counterclockwise, example: {'laser_scan_dir': True}
  2. Set clockwise,        example: {'laser_scan_dir': False}
- Angle crop setting, Mask data within the set angle range:
  1. Enable angle crop fuction:
    1.1. enable angle crop,  example: {'enable_angle_crop_func': True}
    1.2. disable angle crop, example: {'enable_angle_crop_func': False}
  2. Angle cropping interval setting:
  - The distance and intensity data within the set angle range will be set to 0.
  - angle >= 'angle_crop_min' and angle <= 'angle_crop_max' which is [angle_crop_min, angle_crop_max], unit is degress.
    example:
      {'angle_crop_min': 135.0}
      {'angle_crop_max': 225.0}
      which is [135.0, 225.0], angle unit is degress.
'''

def generate_launch_description():
  # LDROBOT LiDAR publisher node
  ldlidar_node = Node(
      package='ldlidar_stl_ros2',
      executable='ldlidar_stl_ros2_node',
      name='LD06',
      output='screen',
      parameters=[
        {'product_name': 'LDLiDAR_LD06'},
        {'topic_name': 'scan'},
        {'frame_id': 'base_laser'},
        {'port_name': '/dev/ttyUSB0'},
        {'port_baudrate': 230400},
        {'laser_scan_dir': True},
        {'enable_angle_crop_func': False},
        {'angle_crop_min': 135.0},
        {'angle_crop_max': 225.0}
      ]
  )

  # base_link to base_laser tf node
  base_link_to_laser_tf_node = Node(
    package='tf2_ros',
    executable='static_transform_publisher',
    name='base_link_to_base_laser_ld06',
    arguments=['0','0','0.18','0','0','0','base_link','base_laser']
  )


  # Define LaunchDescription variable
  ld = LaunchDescription()

  ld.add_action(ldlidar_node)
  ld.add_action(base_link_to_laser_tf_node)

  return ld
```

## step 2: build

Run the following command.

```bash
cd ~/ldlidar_ros2_ws

colcon build
```

## step 3: run

### step3.1: package environment variable settings

- After the compilation is completed, you need to add the relevant files generated by the compilation to the environment variables, so that the ROS environment can recognize them. The execution command is as follows. This command is to temporarily add environment variables to the terminal, which means that if you reopen a new terminal, you also need to re-execute it. The following command.

  ```bash
  cd ~/ldlidar_ros2_ws

  source install/setup.bash
  ```

- In order to never need to execute the above command to add environment variables after reopening the terminal, you can do the following.

  ```bash
  echo source ~/ldlidar_ros2_ws/install/setup.bash >> ~/.bashrc

  source ~/.bashrc
  ```

### step3.2: start LiDAR node

- The product is LDROBOT LiDAR LD06

  - start ld06 lidar node:

  ```bash
  ros2 launch ldlidar_stl_ros2 ld06.launch.py
  ```

  - start ld06 lidar node and show on the Rviz2:

  ```bash
  ros2 launch ldlidar_stl_ros2 viewer_ld06.launch.py
  ```

- The product is LDROBOT LiDAR LD19
  - start ld19 lidar node:
  ```bash
  ros2 launch ldlidar_stl_ros2 ld19.launch.py
  ```
  - start ld19 lidar node and show on the Rviz2:
  ```bash
  ros2 launch ldlidar_stl_ros2 viewer_ld19.launch.py
  ```
- The product is LDROBOT LiDAR STL-27L
  - start stl27l lidar node:
  ```bash
  ros2 launch ldlidar_stl_ros2 stl27l.launch.py
  ```
  - start stl27l lidar node and show on the Rviz2:
  ```bash
  ros2 launch ldlidar_stl_ros2 viewer_stl27l.launch.py
  ```

## step 4: test

> The code supports ubuntu 20.04 ros2 foxy version and above, using rviz2 visualization.

- new a terminal (Ctrl + Alt + T) and use Rviz2 tool(run command: `rviz2`) ,open the `ldlidar.rviz` file below the rviz2 folder of the readme file directory

```bash
rviz2
```

# Instruções

> Este SDK é aplicável somente aos produtos LiDAR vendidos pela Shenzhen LDROBOT Co., LTD. Os modelos de produto são:
>
> - LDROBOT LiDAR LD06
> - LDROBOT LiDAR LD19
> - LDROBOT LiDAR STL-27L

## Passo 0: Obtenha o pacote ROS2 do LiDAR

```bash
cd ~
mkdir -p ldlidar_ros2_ws/src
cd ldlidar_ros2_ws/src
git clone https://github.com/ldrobotSensorTeam/ldlidar_stl_ros2.git
```

## Passo 1: Configuração do sistema

- Conecte o LiDAR à placa-mãe do seu sistema através de uma porta serial integrada ou um módulo USB-serial (por exemplo, o módulo CP2102).

- Defina a permissão -x para o dispositivo de porta serial montado pelo radar no sistema (por exemplo, /dev/ttyUSB0).

- Na prática, o LiDAR pode ser configurado de acordo com o status de montagem do seu sistema. Você pode usar o comando 'ls -l /dev' para verificar.

```bash
cd ~/ldlidar_ros2_ws
sudo chmod 777 /dev/ttyUSB0
```

- Modifique o valor de `port_name` no arquivo Launch correspondente ao modelo do produto de radar em `launch/`, usando `ld06.launch.py` e `/dev/ttyUSB0` como exemplo, conforme mostrado abaixo.

```py
#!/usr/bin/env python3
from launch import LaunchDescription
from launch_ros.actions import Node

'''
Descrição dos Parâmetros:
---
- Definir a direção da varredura do laser:
  1. Definir no sentido anti-horário, exemplo: {'laser_scan_dir': True}
  2. Definir no sentido horário, exemplo: {'laser_scan_dir': False}
- Configuração de recorte do ângulo, mascarar dados dentro do intervalo de ângulo definido:
  1. Habilitar a função de recorte do ângulo:
    1.1. Habilitar o recorte do ângulo, exemplo: {'enable_angle_crop_func': True}
    1.2. Desativar recorte de ângulo, exemplo: {'enable_angle_crop_func': False}
  2. Configuração do intervalo de recorte de ângulo:
  - Os dados de distância e intensidade dentro do intervalo de ângulo definido serão definidos como 0.
  - ângulo >= 'angle_crop_min' e ângulo <= 'angle_crop_max', que é [angle_crop_min, angle_crop_max], unidade em graus.

exemplo:
{'angle_crop_min': 135.0}
{'angle_crop_max': 225.0}
que é [135.0, 225.0], unidade de ângulo em graus.
'''

def generate_launch_description():
  # Nó publicador do LiDAR LDROBOT
  ldlidar_node = Node(
    package='ldlidar_stl_ros2',
    executable='ldlidar_stl_ros2_node',
    name='LD06',
    output='screen',
    parameters=[
      {'product_name': 'LDLiDAR_LD06'},
      {'topic_name': 'scan'},
      {'frame_id': 'base_laser'},
      {'port_name': '/dev/ttyUSB0'},
      {'port_baudrate': 230400},
      {'laser_scan_dir': True},
      {'enable_angle_crop_func': False},
      {'angle_crop_min': 135.0},
      {'angle_crop_max': 225.0}
    ]
  )

  # link base para o nó base_laser tf
  base_link_to_laser_tf_node = Node(
    package='tf2_ros',
    executable='static_transform_publisher',
    name='base_link_to_base_laser_ld06',
    arguments=['0','0','0.18','0','0','0','base_link','base_laser']
  )

  # Define a variável LaunchDescription
  ld = LaunchDescription()
  ld.add_action(ldlidar_node)
  ld.add_action(base_link_to_laser_tf_node)
  return ld
```

## passo 2: construir

Execute o seguinte comando.

```bash
cd ~/ldlidar_ros2_ws

colcon build
```

## Etapa 3: executar

### Etapa 3.1: configuração das variáveis ​​de ambiente do pacote

- Após a conclusão da compilação, você precisa adicionar os arquivos relevantes gerados pela compilação às variáveis ​​de ambiente, para que o ambiente ROS possa reconhecê-los. O comando de execução é o seguinte. Este comando adiciona temporariamente as variáveis ​​de ambiente ao terminal, o que significa que, se você abrir um novo terminal, também precisará executá-lo novamente. O comando a seguir.

```bash

cd ~/ldlidar_ros2_ws

source install/setup.bash

```

- Para não precisar executar o comando acima para adicionar variáveis ​​de ambiente após abrir o terminal novamente, você pode fazer o seguinte.

```bash

echo source ~/ldlidar_ros2_ws/install/setup.bash >> ~/.bashrc

source ~/.bashrc

```

### passo 3.2: iniciar o nó LiDAR

- O produto é o LDROBOT LiDAR LD06

- iniciar o nó LiDAR LD06:

```bash
ros2 launch ldlidar_stl_ros2 ld06.launch.py
```

- iniciar o nó LiDAR LD06 e exibi-lo no Rviz2:

```bash
ros2 launch ldlidar_stl_ros2 viewer_ld06.launch.py
```

- O produto é o LDROBOT LiDAR LD19

- iniciar o nó LiDAR LD19:

```bash
ros2 launch ldlidar_stl_ros2 ld19.launch.py
```

- iniciar o ld19 Para iniciar o nó LiDAR e exibi-lo no Rviz2:

```bash
ros2 launch ldlidar_stl_ros2 viewer_ld19.launch.py
```

- O produto é o LiDAR STL-27L da LDROBOT

- Iniciar o nó LiDAR stl27l:

```bash
ros2 launch ldlidar_stl_ros2 stl27l.launch.py
```

- Iniciar o nó LiDAR stl27l e exibi-lo no Rviz2:

```bash
ros2 launch ldlidar_stl_ros2 viewer_stl27l.launch.py
```

## Etapa 4: Teste

> O código é compatível com o Ubuntu 20.04 e versões superiores do ROS2 Foxy, utilizando a visualização do Rviz2.

- Abra um novo terminal (Ctrl + Alt + T) e use a ferramenta Rviz2 (execute o comando: `rviz2`), abra o arquivo `ldlidar.rviz` localizado na pasta `rviz2` dentro do diretório do arquivo readme.

```bash
rviz2
```
