# ROS2 에 대한 개념과 공부에 대한 정리 Repo
---

# 1. ROS 1 과 ROS 2 의 차이점 비교

## 아키텍쳐 비교
![Relation](./picture/ros_diffrence.png)

| 비교 | ROS 1 | ROS 2 |
| :--------: | :--------: | :--------: | 
|`Application layer` | 항상 ROS Master가 필요[ roscore 실행해야됨] | DDS 기반 미들웨어안에서만 동작[roscore 안해도됨] | 
|`Middleware Layer`| 사용자 정의 위주의 중앙 메커니즘| 추상 미들웨어 인터페이스 | 
|`지원 OS`|리눅스만 지원| 리눅스/윈도우/맥/ 기타 RTOS 등..| 
|`지원 언어`|Python2 & C++ 11| Python3 & C++ 14,17| 
|`다중 Node(프로그램)의 지원 여부`|한 프로세스에 한 노드만 지원| 한 프로세스에 다중 노드 지원| 
|`roslaunch 의 동작 메커니즘`|Xml 기반의 제한된 구조의 실행(은근 빡침)| 복잡한 실행을 Python으로 간편하게|

## ROS 2 의 명령어 특징
 - ros2 node list
   - 실행 중인 ROS 2 환경에 node 목록 호출
 - ros2 node info `/실행중인 node`
   - 실행 중인 Node 의 정보 확인
### Node info
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

# 2. ROS 1 과 ROS 2 는 통신이 가능한가?

ROS1 과 ROS2 두 메타운영체제 간의 통신을 위해서는 별도의 세팅이 필요로 되어진다.

    ros1_bridge node

`ros1_bridge node` 는 ROS1 과 ROS2간의 통신을 위해 만들어진 노드이다.  
사용방법은 ROS 1 에서 Publish 되고있는 msg가 있을때, ROS 2 에서 해당 메세지를 받고 싶을 경우 `ros2 run ros1_bridge '브릿지 명'` 을 통해 받도록 한다.
__※ 이때, 사용자는 ROS1 과 ROS2가 동일한 ROS_MASTER_URI 를 사용하고 있어야 한다.__

# 3. 'ROS 2' 는 'ROS 1' 의 한계를 극복하기위해 만들어 진다

ROS2가 나오기 전 필자가 두대 이상의 로봇을 한 공간에 시뮬레이션을 굴리려고 하면 여간 골치아픈 작업이 아닐 수가 없었다.

`Gazebo`에 두대 이상의 시뮬레이션을 넣고싶어 ROS 1 으로 환경을 구축할 때, 이용 해야했던 xml,yaml,launch 구조는 일반적인 방법으로  ROS Developer 들이 하기에는 매우 적합하지 못했다. `<group>` 태그 안에서 `tf_prefix` 를 생성해 별도의 link,joint 그룹을 생성하고 동작에 대한 별도의 node 를 형성 해 실행하는데 있어 그리고 두개 이상의 rviz를 moveit 으로 병행해 Python으로 동작시키는것은 상당히 번거롭다.


![Relation](./picture/1.png)

    두대의 6축 로봇을 `Gazebo` 안에서 두개의 Rviz로 제어하는 모습

![Relation](./picture/2.png)
   
    두대 이상의 로봇은 보다 복잡한 tf map을 그린다.
![Relation](./picture/3.png)

    두대 이상의 로봇의 node는 group 내에서 더 복잡하게 얽혀있다.

이러한 형태의 번거로운 구조를 ROS2에서는 보다 간편하고 쉽게 풀어나가는 모습을 보여준다.
