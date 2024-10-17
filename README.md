---

#———# created by MRL  #———#

---

- Paletrone ROS Installation Guide
    
    팔레트론 구동시 필요한 시스템을 정리하였습니다.
    
    — 설치 리스트 —
    
    ### Ubuntu 18.04 && ROS 1
    
    - Ubuntu, ROS Install
        
        [고군분투 우분투 ROS ](https://www.notion.so/ROS-1d414c59e64e4e69874693e0f7db9048?pvs=21)
        
    - FAC_MAV_Paletrone system
        
        ```bash
        cd catkin_ws/src
        git clone https://github.com/kamgaa/FAC_MAV_paletrone.git
        catkin_make
        ```
        
    
    ### Dynamixel
    
    github 에 업로되어있으므로 git clone 시 설치됩니다. 
    
    *** dynamixel-workbench/dynamixel_workbench_controllers/launch/position_control.launch 에서 scan_range 가 default =4 로 되어있는지 확인합시다. 
    
    ### IMU (microstrain)
    
    https://github.com/LORD-MicroStrain/microstrain_inertial
    
    해당 사이트에서 2.4.1 버전을 가져와야 합니다. 
    
    우선
    
    ```cpp
    sudo apt-get update && sudo apt-get install ros-melodic-microstrain-inertial-driver -y
    sudo apt-get update && sudo apt-get install ros-melodic-microstrain-inertial-rqt -y
    ```
    
    - git clone (home 이나 download 에 해줄것)
        
        ```cpp
        git clone https://github.com/LORD-MicroStrain/microstrain_inertial.git
        ```
        
    - 2.4.1 버전 git checkout
        
        ```cpp
        git checkout tags/2.4.1
        ```
        
    - 2.4.1 버전 git clone(catkin_ws/src/FAC_MAV_paletrone 에 해줄것)
        
        ```bash
        git clone -b 2.4.1 https://github.com/LORD-MicroStrain/microstrain_inertial.git
        ```
        
    
    이후 
    
    ```bash
    cd /catkin_ws/src/FAC_MAV_paletorne/microstrian_inertial
    rosdep install --from-paths ~/catkin_ws/src --ignore-src -r -y
    cm
    sb
    ```
    
    ### T265
    
    - 서버 공개키 등록
        
        ```bash
        sudo apt-key adv --keyserver [keyserver.ubuntu.com](http://keyserver.ubuntu.com/) --recv-key F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE || sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE
        ```
        
    - 레포지토리 등록
        
        ```bash
        sudo add-apt-repository "deb https://librealsense.intel.com/Debian/apt-repo bionic main" -u
        ```
        
    - 라이브러리 설치
        
        ```bash
        sudo apt install librealsense2-udev-rules librealsense2-utils -y
        sudo apt-get install librealsense2-dev librealsense2-dbg
        ```
        
    
    - realsense-viewer 의존성 해결
        
        ```bash
        sudo rm -f /etc/udev/rules.d/99-realsense-libusb.rules
        ```
        
    
    설치가 다 끝났다면 roscd 를 이용해 rs_t265.launch 파일위치로 갑시다.
    
    rs_t265.launch 내부 parameter 중 camera 를 rs_t265로 바꿔줍시다. 안하면 setting_tf에서 문제생김.
    
    ### Arduino
    
    아두이노는 serial 통신을 해야합니다.  
    
    [[ROS] 8. ROS + Arduino 와 통신 Rosserial](https://95mkr.tistory.com/entry/ROS8)
    
    상세히 설명되어있으니 따라갑시다
    
    꼭 /root/Arduino 에 라이브러리를 저장합시다. home이나 다른경로에 설치하면 업로드 실패
    
    이후
    
    ```bash
    sudo apt-get install ros-melodic-rosserial-arduino -y
    sudo apt-get install ros-melodic-rosserial -y
    cd catkin_ws
    catkin_make install
    cd /아두이노 폴더/libraries
    rm -rf ros_lib
    rosrun rosserial_arduino make_libraries.py .
    ```
    
    다 했으면 sbus_t4.ino 파일 업로드 합시다. 
    
    ### USB 이름설정
    
    [https://winterbloooom.github.io/computer science/linux/2022/01/10/symbolic_link.html](https://winterbloooom.github.io/computer%20science/linux/2022/01/10/symbolic_link.html)
    
    위 사이트를 참고하여
    
    ```bash
    cd /etc/udev/rules.d
    sudo gedit 99-tty.rules
    ```
    
    ```bash
    SUBSYSTEM=="tty", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="5740", ATTRS{serial}=="0000__6253.98756", SYMLINK+="ttyIMU"
    SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6014", ATTRS{serial}=="FT6Z5HG3", SYMLINK+="ttyU2D2" // not same as LP V1(FT2N06T2)
    SUBSYSTEM=="tty", ATTRS{idVendor}=="8087", ATTRS{idProduct}=="0b37", SYMLINK+="ttyT265"
    SUBSYSTEM=="tty", ATTRS{idVendor}=="2341", ATTRS{idProduct}=="8036", SYMLINK+="ttyARDUINO"
    
    ```
    
    ---
    
    
    — 설치 후 —
    
    설치가 완료되었다면 해당 workspace 에서 catkin_make 를 해줍시다
    
    ---
    
    - SSH 설정
        
        ```
        sudo apt update
        sudo apt install openssh-server
        sudo systemctl status ssh #active(running)이 보이면 성공
        sudo ufw allow ssh #방화벽 허가
        ip a #192.~~~로끝나는 주소가 해당 와이파이 ssh주소입니다. 
        ```
        
        ssh 에접속할 컴퓨터에서 
        
        ssh -p 192.~~~.~~.~~@computer name 입력후 enter
