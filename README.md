# ROS2 에 대한 개념과 공부에 대한 정리 Repo
---


## 1. ROS 1 과 ROS 2 의 차이점 비교

### 아키텍쳐 비교
![Relation](./picture/ros_diffrence.png)

| 비교 | ROS 1 | ROS 2 |
| :--------: | :--------: | :--------: | 
|`Application layer` | 항상 ROS Master가 필요[ roscore 실행해야됨] | DDS 기반 미들웨어안에서만 동작[roscore 안해도됨] | 
|`Middleware Layer`| 사용자 정의 위주의 중앙 메커니즘| 추상 미들웨어 인터페이스 | 
|`지원 OS`|리눅스만 지원| 리눅스/윈도우/맥/ 기타 RTOS 등..| 
|`지원 언어`|Python2 & C++ 11| Python3 & C++ 14,17| 
|`다중 Node(프로그램)의 지원 여부`|한 프로세스에 한 노드만 지원| 한 프로세스에 다중 노드 지원| 
|`roslaunch 의 동작 메커니즘`|Xml 기반의 제한된 구조의 실행(은근 빡침)| 복잡한 실행을 Python으로 간편하게|

### ROS 2 의 명령어 특징
 - ros2 node list
   - 실행 중인 ROS 2 환경에 node 목록 호출
 - ros2 node info `/실행중인 node`
   - 실행 중인 Node 의 정보 확인
#### Node info
 - Subscribers : 메세지 발행자 목록 [ 토픽 통신 : 단방향 메세지 통신 ]
 - Publishers : 메세지 구독자 목록  [ 토픽 통신 : 단방향 메세지 통신 ]
 - Service Servers : 서비스 서버 정보 [ 양방향 통신 : req / res ]
   - DescribeParameters
   - GetParameterTypes
   - ListParameters
   - SetParametersAtomically
 - Service Clients : 서비스 클라이언트 [ 양방향 통신 : req / res ]
 - Action Servers : 액션 서버 [ 양방향 통신 : goal / result / feedback ] 
 - Action Clients : 액션 클라이언트 [ 양방향 통신 : goal / result / feedback ]


        이와 같이 ROS 1 에서 사용했던 명령어와 동일하나 앞에 ` 2 ` 를 붙여 실행하는 것이 특징

## 2. ROS 1 과 ROS 2 는 통신이 가능한가?

ROS1 시절 부터 수많은 패키지 들이 개발되어왔다. 이러한 ROS 1 전용 툴을 ROS 2 에서 재개발 하는것이 아닌 재사용을 하기위하여, ROS 2에서는 Bridge node를 활용해 ROS 1에서 사용 되어온 패키지를 재사용 해 재개발의 번거로움을 극복하였다.

ROS1 과 ROS2 두 메타운영체제 간의 통신을 위해서는 별도의 세팅이 필요로 되어진다.

    ros1_bridge node

`ros1_bridge node` 는 ROS1 과 ROS2간의 통신을 위해 만들어진 노드이다.  
사용방법은 ROS 1 에서 Publish 되고있는 msg가 있을때, ROS 2 에서 해당 메세지를 받고 싶을 경우 `ros2 run ros1_bridge '브릿지 명'` 을 통해 받도록 한다.

__※ 이때, 사용자는 ROS1 과 ROS2가 동일한 ROS_MASTER_URI 를 사용하고 있어야 한다.__

## 3. 'ROS 2' 는 'ROS 1' 의 한계를 극복하기위해 만들어 진다

ROS2가 나오기 전 필자가 두대 이상의 로봇을 한 공간에 시뮬레이션을 굴리려고 하면 여간 골치아픈 작업이 아닐 수가 없었다.

`Gazebo`에 두대 이상의 시뮬레이션을 넣고싶어 ROS 1 으로 환경을 구축할 때, 이용 해야했던 xml,yaml,launch 구조는 일반적인 방법으로  ROS Developer 들이 하기에는 매우 적합하지 못했다. `<group>` 태그 안에서 `tf_prefix` 를 생성해 별도의 link,joint 그룹을 생성하고 동작에 대한 별도의 node 를 형성 해 실행하는데 있어 그리고 두개 이상의 rviz를 moveit 으로 병행해 Python으로 동작시키는것은 상당히 번거롭다.


![Relation](./picture/1.png)

    두대의 6축 로봇을 `Gazebo` 안에서 두개의 Rviz로 제어하는 모습

![Relation](./picture/2.png)
   
    두대 이상의 로봇은 보다 복잡한 tf map을 그린다.
![Relation](./picture/3.png)

    두대 이상의 로봇의 node는 group 내에서 더 복잡하게 얽혀있다.

이러한 형태의 번거로운 구조를 ROS2에서는 보다 간편하고 쉽게 풀어나가는 모습을 보여준다.

# 4. ROS 2 와 그 이전의 개념 탑재하기
ROS 1 시절부터 배워왔던 개념을 다시 정립하고자 해당 내용을 보충하고자 한다. 

#### ROS 에서 pkg 란 무엇인가?

__ROS에서 패키지란 ?__ 
cpp 파일, python 파일, configuration 파일, compilation 파일, launch 파일, and parameters 파일을 종합적으로 묶어 놓은 도구를 일컫는다. 패키지에 대한 추상적 개념부터 특징에 대해 말하자면 다음과 같다.

    1. 모든 ROS 프로그램은 패키지로 구성된다.
    2. 당신이 만드는 모든 ROS 프로그램은 패키지로 구성되야 한다.
    3. 패키지는 ROS 프로그램의 주요 시스템이다.

패키지는 직접 `catkin_create_pkg` 명령어를 통해 직접 만들 수 도 있으며, 또는 직접 패키지를 설치하여 관리 할 수 있다.

설치된 패키지들은 `/opt/ros/groovy/share/` 의 경로에서 관리되며 생성된 패키지가 없을 경우 사용이 불가하다. 

    대표적인 pkg 중 하나인 'MoveIt! Setup Assistant'

![Relation](./picture/4.png)

패키지를 설치하면 다음과 같은 디렉터리 구조를 지닌다.

    launch 폴더 : launch files을 보관하는 장소
    src 폴더 : Source files (cpp, python)을 보관하는 장소
    CMakeLists.txt : CMake 빌드환경에 대한 기술
    package.xml : 패키지 정보가 담긴 xml 구조의 파일

해당 디렉토리 이외에도 개발자들 사이에서 control 폴더, config 폴더[configuration],urdf 폴더 등으로 구조를 지니고 있으며, 큰 단위의 로봇 프로젝트를 진행 할 때

1. 로봇이름_description : 로봇 모델에 대한 상세 기술
2. 로봇이름_controller : 로봇 동작을 위한 상세 기술
3. 로봇이름_gazebo : gazebo 내 모델에 대한 기술
4. 로봇이름_hw_interface : 로봇 자체의 hw interface를 위한 pkg
5. 로봇이름_drvier : 4번과 동일
6. 로봇이름_moveit_config : moveit으로 생성한 pkg

등등의 형태로 로봇 패키지를 관리하는 작명센스를 보이고있다. 이와 관련된 내용은 프로젝트 진행자 마다 다르므로 건너뛰기로 한다. 필자는 독일에 kuka, ur로봇, 국내 indy series, robotis의 manipulator 에 대한 프로젝트 진행자들이 짠 구조에 대한 내용을 참조함을 이해 바란다.

