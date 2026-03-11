---
title: "Unity-Dots"
date: 2023-04-10T13:10:43+08:00

tags:
  - Unity
  - Dots

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202304101313250.jpg
---

# Dots前置

## Dots简介

### DOTS是什么

Data-Oriented Technology Stack(面向数据的技术栈）

1. The C# job system
2. The Burst compiler
3. Unity Mathematics
4. Unity Collections
5. Entities ( Entity-Component System)

- Entities.Graphics ( Hybird Renderer )

- Netcode

- Physics

- Animation (WIP)

- Audio(WIP)

### DOTS可以应用到哪些地方？

- 具有大世界流式加载的游戏

- 具有复杂的大规模模拟的游戏

- 具有多种网络类型的多人联线游戏

- 具有需要客户端模拟预测的网络游戏，如射击游戏

### 为什么我们需要DOTS?

1. CPU与Memory的速度发展不均衡以及带宽限制

   **添加高速的缓存Cache内存层级结构去弥补**

1. 摩尔定律的延续与现代CPU设计。

- 越来越好的工艺

- 越来越多的核

- 分工越来越细的处理单元与存储

- SIMD/SIMT


3.并行编程的发展

- OpenMP

- TBB—-Intel Threading Building Blocks
- CUDA--Compute Unified Divice Architecture 
- OpencL-—Open Computing Language 
- MPI/OpenMPI-—Message Passing Interface

**OOD ==>DOD**

![image-20230410132100824](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202304101321851.png)

## 面向数据设计（ODO）

### 程序设计方法

- Instructional Programming 指令化编程

- Functional Programming 函数化编程

- Procedural Programming 过程化编程

- Object-Oriented Programming/Design面向对象编程/设计(OOP/OOD) •Data-Oriented Design(DoD) 面向数据设计

### OOD==>DOD

面向对象设计＞面向数据设计

DOD本质：面向内存/缓存友好的设计

![image-20230410132731256](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202304101327291.png)

### Cache的3C与3R

- Compulsory misses：首次读取数据时，不可避免的Miss

- Capacity misses：缓存空间不足时，连续使用期问访问数据过多的话，无法保存所有活动的数据

- Conflict misses：发生访问冲突时，由于数据映射到相同的缓存行，导致缓存的抖动

- Rearrange 重新排列（代码、数据）：更改布局以增加数据空间的局部性 
- Reduce 减少（大小、缓存行读取）：更小更智能的格式、压缩
- Reuse重用 (Cache lines)：增加数据的时间（和空间）的局部性

### 面向数据设计需要思考的问题

1. 哪些数据需要捆鄉在一起，是一个概念还是有隐藏的含义？
2. 你的数据布局是由如何设计的，AOS/SOA？支持SIMD?
3. 你的目标平台内存最小访问单位是多少，CPU各级缓存有多大？
4. 你的数据多久需要一次，一帧一次，还是一帧多次，还是几乎不更新？
5. 你如何访问数据，随机\连续的还是stride或其他burst方式？
6. 你是总在修改数据，还是只是读取数据，你正在修改所有的内容，还是只修改一部分？
7. 哪些数据设计对带宽与延迟影响大？

### DOTS面向数据设计原则

- 先设计，后编码

- 为高效使用内存与缓存而设计

- 为Blittable Data设计

- 为普通情况设计

- 拥抱迭代

## Dots中的ECS架构

### Entity-Component-System 实体组件系统

- 实体组件系统（ ECS ）架构

- 遵循组合优于继承的原则

- 面向数据设计

- 弱耦合

- 常被应用在游戏开发上

![image-20230410133751482](https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202304101337524.png)



### DOTS1.0中的ECS

- ECS架构一直在演变

- 应用场景不同，各家实现和工作流有差异

- DOTS1.0中的ECS与老版本变化较大，不要看老资料 •开放心态去接受

### DOTS下的ECS

- Entity：Entity不是一个容器，它只是一个标识符，它用来指示某个对象的存在。系统可以用它来操作，可以通过组件来分配某些属性

- Component：组件是一个数据容器，没有任何逻辑，一组特定的参数关联在一起并定义属性。

- System：系统是负责对数据进行操作的部分。换句话说，系统是对具有特定属性（组件）的特定实体执行的操作。
