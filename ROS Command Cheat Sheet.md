# ROS Command Cheat Sheet

All commands learned from book "A gentle Intro to ROS" will be put here 


---
## Creating Packages: 

* Create ‘workspace’ directory : mkdir foldername
* Create a subdirectory “src” to contain source file 
* Create a package by command:
```
catkin_create_pkg package-name [depend1] [depend2] [depend3]...
(catkin_create_pkg first_Package std_msgs roscpp)
```
to generate two configuration file, mainfest file: **package.xml** and **CMakelists.txt** for Cmake
* Create souce file .cpp in the same dir to package.xml (or build a subdirctory)

There is an example of a "hello_world.cpp"
```
 // This i s a ROS version of the standard " hello , world"
 // program .
// This header defines the standard ROS class
#include <ros / ros.h>
int main( int argc , char ∗∗ argv ) {
// Initilize the ROS system. Call this once at the begining of the program
// the last param is the defualt name of the mode name, the  string can't have space between them.
ros::init ( argc , argv , " hello_ros ") ;

// Establish this program as a ROS node .
 ros : : NodeHandle nh ;

// Send some output as a log message .
ROS_INFO_STREAM("Hello , ␣ROS! ") ;
}
```

---
## Compile the package
(ROS build system : catkin)
### Decleare the dependencies by editing in the CMakeLists.txt and package.xml
* For CMake file
the default version in CMakeLists.txt was
```
find_package(catkin REQUIRED)
```
Dependencies on other catkin package can add a COMPONENT
```
find_package(catkin REQUIRED COMPONENTS package-names)
```
for c++ package it is, we need the dependency of one c++ package called **roscpp**
```
find_package(catkin REQUIRED COMPONENTS roscpp)
```
* For the mainfest file package.xml
we need roscpp for both build and run
```
<build_depend>roscpp</build_depend>
<run_depend>roscpp</run_depend>
```
### Declearing executable 

Add those in CmakeLists.txt
```
add_executable(executable-name source-files)  
(add_executable(hello hello.cpp) )
target_link_libraries(executable-name ${catkin_LIBRARIES})
(target_link_libraries(hello ${catkin_LIBRARIES}) )
```
### Building the workpace
```
catkin_make
```
### Sourcing 

execute a script called setup.bash once per terminal
```
source devel/setup.bash
```
if forgeting to run the set.bah. It's likely to complain that:
```
[rospack] Error: stack/package package-name not found
```
### Executing 
```
rosrun packagename nodename
(rosrun agitr hello)
```

---
## Publishing message
### Including messages types header

#include every message **type** used in the program
```
#include<package_name/type_name.h>
```
### Creating a publishing object

```
ros::NodeHandle node_handle;
ros::Publisher pub = node_handle.advertise<message_type>(
topic_name, queue_size);
```
* message type: 
should be same as header defined at begining, like geometry_msgs:Twist
* topic_name
"turtle1/cmd_vel", have to use the quote makrs and the topic name has to be same tpoic name  as subsbriber
* message queue size:
in msot cases, a reasonable large number
### Creating and filling the messgae object
create messge by 
```
package_name:: type_name object_name;
(geometry_msgs::Twist msg;)
msg.linear.x=...;
msg.linear.y=...;
```
### Publishing message 
* Publishing object messgae type must match the defined message type
```
publish_object.publish(msg);
```
### Publishing loop
```
while(ros::ok()){
  foo();
}
```
when return false for ros::ok
* when rosnode kill 
* ctrl + C (not immediately shut down, not recommanded)
* ros::shotdown

### Controlling the publishing rate
```
ros::Rate rate(2);
```
unit in Hz, and define it before the whole loop. Add rate.sleep() inside the loop and put it at the end of the loop.

### Declearing message type dependencies in CMakelists.txt
* For CMake file
for every type of message used in the program 
declear the message type package dependency in CMake file. Just like declear the roscpp depedency,use
```
find_package(catkin REQUIRED COMPONENT roscpp geometry_msgs)
```
Note: modify the exsiting find_package line, rather than creating a new one
* For package.xml
add element for new dependency
```
<>build_depend>geometry_msgs</build_depend>
<run_depend>geometry_msgs</run_depend>
```

---
## Quick recall for showing package,node,topic and message
In order to learn more about data being transmitted in ROS
* using -h to help for checking what comment can be used, such as 
rospack -h
### rsopack
```
rospack dependl <package_name>
```

### rosnode
Display informateion about ROS Nodes
```
rosndoe info <node_name>
rosnode kill <node_name> , kill -a; kill --all
rosnode list
```
### rostopic 
Display information about ROS topic
```
rostopic bw <topic_name>// bandwith
rostopic delay <topic_name>
rostopic echo <topic_name>
rodtopic find <msg_type>
rostopic hz <topic_name>
rostopic info <topic_name>
rostopic list
rostopic type // display message type of a topic
```
### rosmsg
Display the fileds in ROS message type
```
rosmsg show <message_type>
rosmsg list
rosmsg package <package_name>
```
### rqt_graph
rqt_graph creates a dynamic graph of what's going on in the system
```
rosrun rqt_graph rqt_graph
```
rqt_plot displays a scrolling time plot of the data published on topics.
```
 rosrun rqt_plot rqt_plot
 ```
### rosed
rosed will let us drectly edit the files in package without access the path by path
Usage:
```
rosed [package_name] [filename]
rosed [package_name] [Tab][Tab]
```
Specify editor, the default editor is Vim, it could be change by
```
export EDITOR='atom -w'
```
---
## Creating msg and srv file
Details see: http://wiki.ros.org/ROS/Tutorials/CreatingMsgAndSrv
```
rosmsg -h
rossrv -h
```
### Create own message type
Create a msg folder, and cd to this folder, create .msg file like:
```
string first_name
string last_name
uint8 age
uint32 score
```
To make turn msg file into source code. 
In *package.xml*, add
```
<build_depend>message_generation</build_depend>
<exec_depend>message_runtime</exec_depend>
```
In *CMakeLists.txt*,add
```
find_package ( ...
std_msgs
message_generation
...)

catkin_package ( ...
CATKIN_DEPENDS message_runtime
...)

add_message_files(
  FILES
  Num.msg
)

generate_messages(
  DEPENDENCIES
  std_msgs
)
```

### Create srv file
Same as msg, in *CMakeLists.txt*, add additional service dependencies
```
add_service_files(
  FILES
  AddTwoInts.srv
)
```
---
## Subscurbing message

### Writing  a callback function 
```
void function_name(const package_name::type_name &msg) {
                     foo();
}
```
### Creating a subscriber object
```
ros::NodeHandle node_handle;
ros::Sbuscriber sub=node_handle.subscribe(topic_name,queue_szie,pointer_to_call_back_function_name);
```
**pointer_to_call_back_function_name** here is **&function_name** where funcion name is the name of the callback function introduced above. 
### Giving ROS control
To execute call back function , we have to use
```
ros::spin();
ros::spinOnce();
```
ros::spinOnce is usually written inside a loop, and if dont have repetitive work, use ros::spin()

---
## Create Launch file
roslaunch is just to run myltiple nodes together at once rather than using rosrun multiple times. It is a XML document written in a .launch file. The tempalte of .launch file is like: 
More details can be find in the book**Introduction to ROS** Chapter 6
Basic Syntax:
```
<launch> ...</launch>
```
### Adding nodes, **Note** *type* here is the executable name in Cmakefile *add_executable*; *name* here will overwrite the name
in source file ros::init; *respawn* will reopen the node if it(or all nodes)is/are terninated. On the contrast, *required* is true means when the node is terminated, all other nodes will close; *launch-prefix="xterm -e"* all open a new terminal for this node
```
<node 
pkg="package_name" 
type="executable_name" 
name="node_name" 
respawn="true"
required="true"
launch-prefix="xterm -e"
\>
```
### Remapping names
Remap a name (could be topic name) to another name. A example for this is, let say if a node subscribe a topic "topic1" and we do something about the values of this topic and make it as a new "topic2"(same message type), so we can rename the "topic1" in this node to "topic2" so that the node will subscribe the modified message value.

Remap name in command line
```
rosrun turtlesim turtlesim_node turtle1/pose:=anglepose
```
Or in .launch file
```
<node 
 pkg="..." type="..." name="...">
 <remap from="turtle1/pose" to="anglepose" />
 </node>
 ```
### Add other launch files
```
<include file="$(find package_name)/launch_file_name"/>

<inlcude file="$(find turtlebot_gazebo)/turtlebot_world.launch">
<arg name="world_file" value="$(find turtlebot_gazebo)/worlds/corridor.world" />
</include>
```
### Declarign arguments
*"default"* can be override in command line. Hoever, *"value"* can't be changed, so if arg is set value="...", it can't modified in command line
```
<arg name="arg_name" default="0" />
<arg name="OpenOrClose" value "1" />
```
when launch the launch file, use code below only if when the arg is set default or no assigned value
```
roslaunch pkg_name launch_file_name arg_name:=arg_vale
```
This arg vale can be used in source and launch file by 
```
$(arg arg_name)
```

### Creating groups
*group* element is a convenient way to origanize nodes in large launch files, it can be defined as:
```
<group ns="namespace_name"/>
...
...
</group>
```
Or add conditions
```
<group if ="0 or 1" /> 
...
</group>

<group if=$(arg arg_name) />
...
</group>
```
---

## Google stytle C++

### code analysis using cpplint
Lint tools are a form of static code analysis (like cppcheck). Google's cpplint tool is a python script that identifies potential source code issues that are in conflict with the Google C++ style guide. This tool can be run before a version control commit, after you've written a new class, or following refactoring. Any issue found by cpplint will have a confidence score, 1-5, with 5 most certain. Note: cpp-boilerplate has a few errors right now.

 install and run from terminal:
    wget https://raw.githubusercontent.com/google/styleguide/gh-pages/cpplint/cpplint.py (Links to an external site.)
    Change permission to executable: chmod +x cpplint.py
    cd <repository>
    Run cpplint

Example run with cpp-boilerplate (cpplint.py is in the parent directory):

cd cpp-boilerplate
../cpplint.py --extensions=h,hpp,cpp $( find . -name *.h -or -name *.hpp -or -name *.cpp | grep -vE -e "^./build/" -e "^./vendor/" )

This command runs cpplint.py and tells the script to examine files with extension .h, .hpp, or .cpp. The script expects a list of files so the bash command first finds all files in the directory and sub-directories that have the extension .h, .hpp, or .cpp. It then excludes any file found in the build or vendor sub-directories.

---

