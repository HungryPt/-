---

title: Kurento自定义模块
tags: WebRTC
copyright: true
comments: true
description: 
date: 2019-05-06
thumbnail: /pic/pexels-photo-1252869.jpeg
disqusId: Kurento自定义模块

---

---

自定义Kurento模块，Kurento分别支持两种方式：一种基于Opencv，另一种基于GStreamer 

---
<!-- more -->

### **Kurento两种模块**

- 基于OpenCV的模块。如果开发一个提供计算机视觉或增强现实功能的过滤器，建议使用此类模块。
- 基于GStreamer的模块。这种模块为使用GStreamer框架进行媒体处理提供了通用入口点。这些模块功能更强大，但    也  更难开发。

  开发过滤器的出发点是创建过滤器结构。可以使用该`kurento-module-scaffold`工具。此工具随`kurento-media-server-dev`包一起分发。要安装此工具，请运行以下命令：

  ```
  sudo apt-get install kurento-media-server-dev

  ```
  1.OpenCV模块：
 ```
    kurento-module-scaffold.sh <module_name> <output_directory> opencv_filter

 ```
  2.Gstreamer模块：
 ```
    kurento-module-scaffold.sh <module_name> <output_directory>
 ```
 该工具生成文件夹树，所有需要的CmakeLists.txt文件以及Kurento模块描述符文件（.kmd）的示例文件。这些文件包含模块的描述，构造函数，方法，属性，事件以及开发人员定义的复杂类型。

一旦KMD文件完成，会产生相应的服务端代码。该工具 kurento-module-creator为服务器端生成相应的代码。从根目录运行：

```
  cd build
  cmake ..
```
### **OpenCV模块**
  生成四个文件`src/server/implementation/`：
  ```
  ModuleNameImpl.cpp
  ModuleNameImpl.hpp
  ModuleNameOpenCVImpl.cpp
  ModuleNameOpenCVImpl.hpp
  ```
  前两个文件不要修改。最后两个文件将包含模块的逻辑。

  该文件ModuleNameOpenCVImpl.cpp包含处理方法和参数的函数（必须实现逻辑）。此外，此文件包含一个名为的函数process。每个新帧都会调用此函数，因此必须在其中实现过滤器的逻辑。

  ### **GStreamer模块**

  在GStreamer模块情况下，在src/文件夹中有两个目录：

  该gst-plugins/文件夹包含GStreamer元素的实现（kurento-module-scaffold生成虚拟过滤器）。

  在server/objects/文件夹中你有两个文件：
  ```
  ModuleNameImpl.cpp
  ModuleNameImpl.hpp
  ```
在文件中，ModuleNameImpl.cpp您必须调用GStreamer元素的方法。模块逻辑将在GStreamer元素中实现。

### **生成服务端安装包**
 ```
  sudo debuild -us -uc
  cd ~/kms_module_directory/
  sudo dpkg -i customfilter_0.0.1~rc1_amd64.deb
 ```

 ### **生成Java客户端**

 对于Java，必须从构建目录执行命令，该命令会生成客户端代码的Jar包。。要在Maven项目中使用该模块，您必须将依赖项添加到文件中：`cmake .. -DGENERATE_JAVA_CLIENT_PROJECT=TRUEmake java_installpom.xml`
```
<dependency>
  <groupId>org.kurento.module</groupId>
  <artifactId>modulename</artifactId>
  <version>moduleversion</version>
</dependency>
```