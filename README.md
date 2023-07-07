指向引用的指针是非法的，因为引用只是一个别名，不存在真实的内存地址。
int a;
int *p = &a;
int & *pr = a;

指针的引用是合法的

int a;
int *p = &a;
int * &pr = p;

返回自身对象的引用允许链式操作，比如，return *this

++num;
num.someFunction()

private成员不可继承
public 继承 继承类可以访问基类的public 与protected（维持访问权限不变）

protected继承 继承类可以访问基类的public 与protected（继承为子类的protected）

private继承 继承类可以访问基类的public 与protected（继承为子类的private）

C++中的接口是通过抽象基类和纯虚函数来定义的，主要用到了orvrride 关键字，去重写父类虚函数

依赖包
find_package

指定头文件目录
include_directories

添加可执行文件
add_executable

将可执行文件链接到目标库
target_link_libraries




CMake 基本语法
注释：这一点与Python很像都是使用“#”作为注释符
变量：在CMake中变量的定义有些特别，一般用set命令作为显式定义和变量的赋值。
如果在if语句中使用到变量时可以直接使用变量名，如果不是在if语句中使用的话则需要使用${}来引用变量。

调用命令：命令不分大小写，参数使用空格分隔，如果参数中出现空格则需要用双引号包含起来
package.xml
格式1
格式1标准说明是 REP-0127
格式2
格式2标准说明是 REP-0140
格式1和格式2有区别的标签：
格式1
<run_depend> (多个)：运行时需要依赖的其它package，如动态链接库、可执行文件、Python模块、脚本文件等。
<test_depend> (多个)：进行单元测试时的依赖项，如前两个标签中已出现过，则不必再出现
格式2
<build_export_depend>：用于帮助使用本package的其它包传递依赖声明
<buildtool_export_depend>：构建工具的依赖传递声明
<exec_depend>：相当于格式1的run_depend标签
<depend>: 相当于build_depend + buildtool_depend + exec_depend，避免重复写三个声明。不建议对系统依赖使用，因为会强行依赖development级package。
<doc_depend>：构建文档所需要的依赖，当前没什么用，以后可能会有需要
<test_depend>：进行单元测试时的依赖项，此标签中的package可以在其它标签中出现
在package.xml中主要包含以下信息：
描述信息（比如package的功能描述、维护者等）
依赖信息
元信息（比如作者、网页）
package信息（比如版本）
每个package.xml文件必须包含的标签包括：
<package>: 最高级tag，属性：format，用于指定格式
<name> ：package名称
<version> ：当前版本
<description>：package的基本描述
<maintainer>(至少一个)：维护者
<license>(至少一个)：协议
<buildtool_depend>(至少一个)：一般情况下只需要指定catkin作为编译工具，在需要交叉编译的情况下需要增加目标机器的编译工具。
可选标签包括：
<package>
<url> (多个)：指定package的网址
<author> (多个)：作者
<build_depend> (多个)：编译时需要依赖的其它package，适用于静态库
<conflict> (多个)：声明本package与其它ROS package有冲突，不能同时部署
<replace> (多个)：声明本package是其它package的替换版本
<export>: 用于添加额外的信息，比如需要嵌入的其它package的插件，或者一些说明信息。
上述是对package.xml的基本说明，对于用户而言，如果通过catkin_create_package命令生成的包，往往已经自动生成了需要的全部标签，只需要在依赖有所变化的情况下更改（格式1）：
CMakeLists.txt
catkin基于CMake实现，CMakeList的语句与基本CMake一致，常用的包括：

cmake_minimum_required()：CMake版本
project()：工程名
find_package()：加载外部变量
加载catkin中的变量，同时添加angles、roscpp、std_msgs等的依赖 find_package(catkin REQUIRED COMPONENTS angles roscpp std_msgs)
加载Boost中的变量 find_package(Boost REQUIRED COMPONENTS thread)
include_directories()：添加头文件路径
添加./include, ${catkin_INCLUDE_DIRS}为头文件路径，如果有其它外部路径也在这里添加 include_directories(include ${catkin_INCLUDE_DIRS})
Boost_INCLUDE_DIRS环境变量在上面find_package中加载 include_directories(${Boost_INCLUDE_DIRS})
add_library()：生成库
生成库文件，可传入多个源文件 add_library(your_library libsrc1.cpp libsrc2.cpp libsrc_etc.cpp )
add_executable()：生成可执行文件
生成可执行文件，可以传入多个源文件 add_executable(my_node src0.cpp src1.cpp src2.cpp )
add_subdirectory()：一般情况下，我们的项目各个子项目都在一个总的项目根目录下，但有的时候，我们需要使用外部的文件夹，怎么办呢？add_subdirectory命令，可以将指定的文件夹加到build任务列表中。
add_dependencies()：添加依赖项，在使用ROS的message、service、action时注意添加，如下：
添加对其它package消息的依赖，前提是已经通过find_package()引入了这个package
  add_dependencies(my_target ${catkin_EXPORTED_TARGETS}) # 添加对本package消息的依赖
  add_dependencies(my_target ${${PROJECT_NAME}_EXPORTED_TARGETS})       
1.
2.
target_link_libraries()：链接库
为可执行文件或库添加链接库 target_link_libraries(my_node ${catkin_LIBRARIES}
ROS基本库 ${Boost_LIBRARIES} # Boost库 )
install()：安装
默认情况catkin会为每个package在./devel中建立目录，存放目标文件
目标文件可以直接通过rosrun和roslaunch访问
如需制定其它安装位置，则需通过install()命令
  install(TARGETS my_node0 my_node1 ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION} LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION} RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION} )
1.
除此之外，catkin还为ROS设计了一些独有语句，包括：
add_message_files(), add_service_files(), add_action_files()
用于添加自定义的message、service和action文件。基本语法为：
以message为例，service、action类似
 add_message_files( # 目录名 DIRECTORY msg # 文件名 FILES YourFirstMessage.msg YourSecondMessage.msg YourThirdMessage.msg)
1.
generate_messages()
用于生成所有定义的message、service、action文件，需要添加本文件需要依赖的message文件包。
一般情况下需要依赖std_msgs，如果用到了其它类型的msg，也要在这声明。如下：
generate_messages(DEPENDENCIES std_msgs)
catkin_package()
catkin_package()是catkin提供的CMake宏，用于为catkin提供构建、生成pkg-config和CMake文件所需要的信息。
必须在声明add_library()或add_executable()前调用
有5个可选参数
INCLUDE_DIRS - 声明给其它package的include路径
LIBRARIES - 声明给其它package的库
CATKIN_DEPENDS - 本包依赖的catkin package
DEPENDS - 本包依赖的非catkin package
CFG_EXTRAS - 其它配置参数
catkin_package( INCLUDE_DIRS include LIBRARIES ${PROJECT_NAME} CATKIN_DEPENDS roscpp nodelet DEPENDS eigen opencv)





