# ROS Command Cheat Sheet

All commands learned from book "A gentle Intro to ROS" will be put here 

## Creating Packages: 

* Create ‘workspace’ directory : mkdir foldername
* Create a subdirectory “src” to contain source file 
* Create a package by command:
```
catkin_create_pkg package-name
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
// the last param is the defualt name of the mode name
ros::init ( argc , argv , " hello_ros ") ;

// Establish this program as a ROS node .
 ros : : NodeHandle nh ;

// Send some output as a log message .
ROS_INFO_STREAM("Hello , ␣ROS! ") ;
}
```
## Compile the package
(ROS build system : catkin)
### decleare the dependencies by editing in the CMakeLists.txt and package.xml
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

