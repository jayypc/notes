# PowerDesigner

> 先将现实世界中的客观对象抽象为实体(Entity)和联系(Relationship),它并不依赖于具体的计算机系统或某个DBMS系统，这种模型就是我们所说的**概念数据模型**CDM
>
> 然后再将CDM转换为计算机上某个DBMS所支持的数据模型，这样的模型就是**物理数据模型**,即PDM。

- 左侧结构：

  - Workspace
    - 模型（assetmanage）
      - 默认图（asset）
      - 图中包含表（tables）
      - 图中包含关系（references）

- 增加表注释

  双击表进入设置，点击标签页Columns，单击customize columns and filter（自定义列和过滤器），勾选Commont

- Columns的各个属性

  - Name是PowerDesigner的展示内容
  - Code是具体的表属性
  - Comment是属性注释
  - Data Type是数据类型
  - Length是数据长度（但有些类型如Date是没有长度的）
  - P是主键选项
  - M是非空项选项
  - F是外键选项


- 新建物理数据对象模型

  Physical Data Model > Physical Diagram

- 业务流程图

  Business Process Model > Business Process Diagram

- 业务层次分析图

  Business Process Model > Process Hierarchy Diagram