# Hello Bullet CMake

A minimal example showing how to compile link and run a C++ example using Bullet Physics.

## Local Bullet Installation ##

Bullet can be install locally, instead of system-wide. This allows to customize setting, using a double precision build for example.

```
git clone https://github.com/bulletphysics/bullet3
cd bullet3
mkdir build_cmake
cd build_cmake
```

On Windows using Visual Studio you can use
```
cmake -DUSE_DOUBLE_PRECISION=ON -DCMAKE_DEBUG_POSTFIX="" -DINSTALL_LIBS=ON -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX:PATH=local_install -G "Visual Studio 16 2019" ..
```
or if you have Python installed in c:\python37 you can use
```
cmake -DCMAKE_DEBUG_POSTFIX="" -DINSTALL_LIBS=ON -DBUILD_PYBULLET=ON -DUSE_DOUBLE_PRECISION=ON -DCMAKE_BUILD_TYPE=Release -DPYTHON_INCLUDE_DIR=c:\python37\include -DPYTHON_LIBRARY=c:\python37\libs\python37.lib -DPYTHON_DEBUG_LIBRARY=c:\python37\libs\python37.lib -DCMAKE_INSTALL_PREFIX:PATH=local_install -G "Visual Studio 16 2019" ..
```

Then open Visual Studio, compile the INSTALL target in both Debug and Release build configuration.

After that, open a terminal window and create the hello_bullet_cmake target

```
git clone https://github.com/erwincoumans/hello_bullet_cmake
cd hello_bullet_cmake
cmake -DBullet_DIR=D:\dev\bullet3\build_cmake .
start .
```
Now open the solution file and compile either in Debug or Release build configuration.

On Linux or Mac you can install Bullet in a local directory using:

```
git clone https://github.com/bulletphysics/bullet3
cd bullet3
mkdir build_cmake
cd build_cmake
cmake -DCMAKE_INSTALL_PREFIX:PATH=local_install -DUSE_DOUBLE_PRECISION=ON -DCMAKE_DEBUG_POSTFIX="" -DINSTALL_LIBS=ON -DCMAKE_BUILD_TYPE=Release ..
make -j
make install
pwd
```

Then you can build the hello_bullet_cmake example
(replace /home/users/test/bullet3/ by the output of the pwd command)

```
git clone https://github.com/erwincoumans/hello_bullet_cmake
cd hello_bullet_cmake
cmake -DBullet_DIR=/home/users/test/bullet3/build_cmake .
make
```


## System Wide Bullet Installation ##

For example on Linux:
```
sudo apt-get install libbullet-dev
```

Then
```
git clone https://github.com/erwincoumans/hello_bullet_cmake
cd hello_bullet_cmake
cmake .
make
```
