# Collada URDF

This contains packages for converting from collada files to URDF.
See the ROS wiki for API documentation and tutorials.

* [`collada_urdf`](http://wiki.ros.org/collada_urdf)
* [`collada_parser`](http://wiki.ros.org/collada_parser)

This was originally part of the [`ros/robot_model`](https://github.com/ros/robot_model) repository.
It has been moved to this repo as described by [`ros/robot_model#195`](https://github.com/ros/robot_model/issues/195)

## Linking issue with Raspbian

If you are using a Raspberry Pi with Raspbian you might encounter a linking problem with Assimp that looks like:
```bash
~/ros_catkin_ws/devel_isolated/collada_urdf/lib/libcollada_urdf.so: undefined reference to `typeinfo for Assimp::IOSystem'
collect2: error: ld returned 1 exit status
```

A work-around consists of compiling and installing Assimp latest version:
```bash
mkdir -p $HOME/libraries/assimp-3.3.1/build_release
cd $HOME/libraries/assimp-3.3.1/
wget https://github.com/assimp/assimp/archive/v3.3.1.zip
unzip v3.3.1.zip
rm v3.3.1.zip
mv v3.3.1 src
cd build_release
cmake ../src -DCMAKE_BUILD_TYPE=Release -DASSIMP_BUILD_TESTS=False
make -j3

sudo make -j3 install
```

Make sure you reconfigure the catkin workspace (or just delete the build files) so that the newest Assimp version is found before compiling again.

