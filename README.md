# ros_prebuild_shared_library_tutorials

* ポイントとしては，`file`コマンドを利用し，その`DESTINATION`がcmakeによる生成物であることが確定するところ
* `glob`を利用するとその生成物リストが確定しない?
* 生成物リストを調べているときにはシンボリックリンクの先を見ているのでシンボリックリンクのみのパスを指定するとlibがnot foundという表示になってしまうので注意

```
find_library(libhello_path hello PATHS lib/)
add_library(hello_lib_dummy_target EXCLUDE_FROM_ALL ${libhello_path})
set_target_properties(hello_lib_dummy_target PROPERTIES LINKER_LANGUAGE CXX)
file(COPY ${libhello_path} DESTINATION ${CATKIN_DEVEL_PREFIX}/lib)
# NOTE: for symbolic linked real file
get_filename_component(libhello_realpath ${libhello_path} REALPATH)
file(COPY ${libhello_realpath} DESTINATION ${CATKIN_DEVEL_PREFIX}/lib)
```

----

## FYI
### other way?
* [export a prebuilt library in catkin \- ROS Answers: Open Source Q&A Forum]( https://answers.ros.org/question/97606/export-a-prebuilt-library-in-catkin/ )
  * [prosilica\_gige\_sdk/CMakeLists\.txt at hydro\-devel · ros\-drivers/prosilica\_gige\_sdk]( https://github.com/ros-drivers/prosilica_gige_sdk/blob/hydro-devel/CMakeLists.txt )
    * この方法でexportしたshared_libraryをlinkすると絶対パスでlinkされてしまう
    * (この理由はcatkinが絶対パスの.soでexportしてしまうため)

lib build command
```
cd lib-src
g++ -shared -fPIC -o ../lib/libhello.so.1.0.0 hello.cpp
```

lib symbolic link command
```
cd lib
ln -s libhello.so.1.0.0 libhello.so
```

init command
```
catkin_create_pkg ros_prebuild_shared_library_tutorials std_msgs roscpp
```
