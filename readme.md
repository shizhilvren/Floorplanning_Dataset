# Data Source
This project collect some dataset for EDA Floorplanning. Thanks to the authors for providing the test dataset.

Below table list all date source
| Name         | Folder            | website                                               | comment                                                                                                     |
| ------------ | ----------------- | ----------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| GSRC         | GSRC              | [GSRC](http://vlsicad.eecs.umich.edu/BK/GSRCbench/)   | format at [link](https://vlsicad.ucsd.edu/GSRC/bookshelf/Slots/Fundamental/HGraph/HGraph1.1.html)           |
| MCNC         | MCNC              | [MCNC](http://vlsicad.eecs.umich.edu/BK/MCNCbench/)   |                                                                                                             |
| IBM02/IBMHB+ | IBM02 IBMHB+      | [link](http://vlsicad.eecs.umich.edu/BK/ISPD06bench/) | format at [link](http://vlsicad.eecs.umich.edu/BK/ISPD06bench/BookshelfFormat.txt)                          |
| ami49_x      | MCNC/SOFT/ami49_x | None                                                  | This case copy from MCNC, only use in PeF: Poisson’s Equation-Based Large-Scale Fixed-Outline Floorplanning |
| test         | test              | None                                                  | this is a small case for debug                                                                              |

# 数据说明

## 数据概述

因为原版的数据说明缺失因此该份数据说明的很多都为猜测，如有错误请谅解。
一个测试用例包含三个部分 .blocks .nets .pl 三类文件
其中"#"开头都表示注释在处理中可以不管

## 软硬区分

所有数据都分为两部分，硬块与软块

## blocks文件

该文件描述电路中的所有电路快
第一行为文件头 格式固定  

```dart
UCSC blocks 1.0
```

下面三行

```dart
NumSoftRectangularBlocks : xx
NumHardRectilinearBlocks : xxx
NumTerminals : xxxx
```

| 名称                     | 类型    | 含义                                   |
| ------------------------ | ------- | -------------------------------------- |
| NumSoftRectangularBlocks | 文本    | 形状变化电路快数量xx个                 |
| NumHardRectilinearBlocks | 文本    | 形状固定的电路快xxx个                  |
| NumTerminals             | 文本    | 表示终点块有xxxx个，不可移动且没有形状 |
| xx,xxx,xxxx              | int变量 | 表示xx个，其他同理                     |

后面伴随xx+xxx+xxxx个电路快 数量符合上三行所说
有两种形式

```dart
nodeName softrectangular area minAspectRatio maxAspectRatio
nodeName hardrectilinear vertexNumber vertex1, vertex2, ..., vertexN
nodeName terminal
```

| 名称                           | 类型                    | 含义                                                               |
| ------------------------------ | ----------------------- | ------------------------------------------------------------------ |
| nodeName                       | 字符变量                | 表示电路快的名字                                                   |
| softrectangular                | 文本                    | 形状可变的电路快                                                   |
| hardrectilinear                | 文本                    | 形状固定的电路快                                                   |
| area                           | double变量              | 表示电路快nodeName的面积                                           |
| minAspectRatio                 | double变量              | 表示电路快nodeName的最小纵横比                                     |
| maxAspectRatio                 | double变量              | 表示电路快nodeName的最大纵横比                                     |
| vertexNumber                   | int变量                 | 表示多边形电路快的顶点数量后面接vertexNumber个顶点，按同一方向排列 |
| vertex1, vertex2, ..., vertexN | (double变量,double变量) | 按直角坐标系表示x,y变量                                            |
| terminal                       | 字符变量                | 表示该电路快固定                                                   |

## net文件

该文件描述电路中的所有网络
第一行为文件头 格式固定 可能存在其他格式 一般都存在文件头

```dart
UCSC blocks 1.0
```

下面两行

```dartx
NumNets : xxx
NumPins : xx
```

| 名称    | 类型    | 含义                |
| ------- | ------- | ------------------- |
| NumNets | 文本    | 表示网络数量有xxx个 |
| NumPins | 文本    | 表示引脚有xx个      |
| xx,xxx  | int变量 | 表示xx个，其他同理  |

下面共有NumNets组数据每组数据格式如下

```dart
NetDegree : N
nodeName1 B [: %xx %yy]
nodeName2 B [: %xx %yy]
...
...
nodeNameN B [: %xx %yy]
```

| 名称                   | 类型       | 含义                                                                                                     |
| ---------------------- | ---------- | -------------------------------------------------------------------------------------------------------- |
| NetDegree              | 文本       | 网络的度，下面会有N行引脚                                                                                |
| B                      | 文本       | 无意义，固定格式                                                                                         |
| xx,yy                  | double变量 | 表示引脚相对中心的偏移量，百分比偏移量，范围为[-50%,50%],%xx中%为固定文本,若该值缺失则默认在中间即 %0 %0 |
| nodeName1,...nodeNameN | 文本       | N个引脚的名字                                                                                            |
| N                      | int变量    | 表示xx个，其他同理                                                                                       |

## pl文件

该文件描述电路中的所有电路块的位置
第一行为文件头 格式固定  

```dart
UCSC blocks 1.0
```

下面有NumSoftRectangularBlocks+NumHardRectilinearBlocks+NumTerminals行
表示上述电路块的位置

```dart
nodeName x y [DIMS = (ww, hh)] [ : Orientations (default none)]
```

| 名称         | 类型                              | 含义                                        |
| ------------ | --------------------------------- | ------------------------------------------- |
| nodeName     | 文本                              | 电路块的名字                                |
| x,y          | double变量                        | 按横纵坐标表示电路块位置                    |
| ww,hh        | double变量                        | 电路块的宽与高只有再电路快可变的情况下存在  |
| Orientations | string 变量有 N E S W FN FE FS FW | 分别表示顺时针旋转0，90，180，270和镜像旋转 |

## 注释

### 关于IBMHB+系列

0. 基本文件格式相同
1. .aux文件说明文件所包含的文件
2. .nodes为固定块格式，（基本格式：名称 宽 高）一般不使用
3. .scl文件为行高说明文件，与IBMHB+数据来源有关，这里只是用布局面积。
4. .wts为空
5. 详细参见 http://vlsicad.eecs.umich.edu/BK/ISPD06bench/
