

# Overview Wek 1-11 IDBF


# Week 1




## Week 11. 一些相关的周边事情

### 目标

- 认识DDL, Views
- 认识不同的数据库类型
- 认识结构化和非结构化数据 Structured / Unstructured Data
- 理解：大数据会更好吗
- 完成一些考试复习

### DDL Data Define Language
DDL: 创建表  
VIEW: 数据的一部分显示之类的

### 不同类型的 Databases

- Transactional Processing Databases, 1st Era.
- Data Warehouses, 2nd Era
- Cloud Based Databases, MySQL..
- Whats Big Data: Massive data sets
- Object Oriented Databases

### Structured / Unstructured Data

1. Data vs Information  
   数据是原始的 信息可能是特殊查询出来的 更有利于分析看

2. Structured Data
   结构化数据 存储在关系表中的字段中。关系型数据库即是。
   关系型数据库只有几种类型: 整数 小数 日期 文字 等

3. Unstructured Data  
   没有数据模型，难以查询。例：视频文件 音频文件 各种文件

4. Unstructured Data 分析  
   例如图片识别 声音识别

5. Complex Data Manage
   在例如java的语言中管理








# Week 10. E-ERD的概念 类型 关系

### 目标: E-ERD, Enhanced Entity Relationship Diagrams

- 超类型 子类型 Supertype and Subtypes
- 通用化 和 特定化 Generalisation and Specialisation
- 不需计算的约束 全/部分 特定化 Completeness Constraints : Total and Partial Specialisation
- 解体规则 Disjoint Rules : Overlap and Disjoint Rule
- 子类型标记 Subtype discriminators
- 变换关系 Translation to Relations

### E-ERD
他可以更复杂 更细致 更多

#### Supertypes and Subtypes.
子/超类型 是EERD相对最重要的额外概念

- 标记方法
- 数据库实现方法

### Generalisation and Specialisation

前者 定义更多父累 BottomUp，后者 定义更多子类 TopDown

### Completeness Constraints

Total Spec: 必须符合(其任意子类)  
Partial Spec: 可以符合 也可以不

### Disjointness Constraint

解体约束 让实例的父类型数据 成为子类型的

可能的规则:
1. Disjoint Rule 只能同时1个
2. Overlap Rule 可以同时多个

### Constraints in E-ERD Vs Cardinality and Modality of the ERD

图 展示 EERD的记号vs ERD

### 子类型标记 Subtype discriminators

如何存在数据库 如何图像标记

### Translation to Relations

一系列方法 将EERD变换成 实际数据库






# Week 9 范式, 键约束 强弱实体

### 目标:
- 依赖的类型 Type of dependencies
- 时间依赖的数据 Modeling time dependent data.
- 约束和引用集成 Constraints and Referential Integrity
- ERDs: Defining Entities, Strong and Weak Entities, Associative Entities

### 2NF
消除部分依赖 Partial Dependencies

Payments: EmployeeNum, EName -> remove EName

> 部分依赖:
> A -> B && RealSubSet(A) -> B

### 3NF
消除间接依赖 Transitive Dependencies

Staff: StaffName, RoomNum, RoomDepartment -> remove RoomDepartment
> 间接依赖:
> A -> B (B !-> A), B -> C


### BCNF

每一个属性都完全依赖键

This is where each attribute is dependent on the key, the whole key and nothing but the key (So help me Codd). These relations are fully normalised or in BCNF.

### 时间依赖的数据

记时间戳

### Referential Integrity (RI) Constraints
... keys 约束
### Strong vs Weak Entities

A Strong entity
• Exists independently of other types of entities
• Has its own unique identifier
• Identifier underlined with single-line
A Weak entity
• Dependent on a strong entity ...cannot exist on its own
• Does not have a unique identifier (only a partial identifier)
• Partial identifier underlined with double-line
• Entity box has double line

### Associative Entities

These entities are created when
• the relationship is many to many
• and the relationship has attributes!!!








# Week8 标准化, 范式

### 目标: 

- 标准化 Normalisation
- Synonyms, Homonyms 同义词，同音异意词
- 0NF, 1NF, 2NF, 3NF, BCNF
- Follow examples

### Normalisation

标准化在 Conceptual Level / ERD / Relational Model 之前

### Synonyms, Homonyms

前者: CustID, CustNum.
后者: Address -> UserAddress, CarAddress...

### UNF or 0NF

### 1NF

每个字段 只包含一个值，原子性

### 2NF 消除部分依赖
防止: 某字段依赖部分主键

### 3NF 消除过渡依赖
防止: 某字段依赖其他非主键字段

### BCNF 让更特殊的字段成为主键字段 重新定义主键集






# Week7

### 目标
- 数据库设计过程 概览
- ERD to Relational Model
- 理解 数据库Keys: Primary Key, Candidate Keys | Single Valued Key, Composite Key
- 鉴别 关系的程度: Unary, Binary, Ternary


### Degree of a Relationship
The degree of a relationship is determined by the number of entity types that participate in a relationship.

There can be
- Unary – degree 1 or recursive relationship
- Binary – degree 2 (the most common)
- Ternary – degree 3
- There can be more but very uncommon



每种都可以 One2One, One2Many, Many2Many






# Week 6

好像是考试 没有



# Week 5

目标:
- 为何用 DBMS 
- 组织性建模数据
- 商业规则
- Conceptual Level
- ERD. Entity Relationship Diagrams

### Cardinality and Modality

Cardinality refers to the maximum number of times a specific value exists in the related entity.

Modality refers to the minimum number of times a specific value exists in the related entity.

外部是 Cardinality





# Week4

目标:
- 子查询: CompareOperators, IN, ALL, EXISTS
- 高级 DML


# Week 3

目标:
- 日期
- BETWEEN, IN 
- 理解聚合函数
- GROUP BY, HAVING
- 理解复杂JOIN 联表



# Week 2

目标:
- 别名 和 计算 SQL
- LIKE子句
- 操作符优先级
- 基本DML
- 了解聚合函数





# Week1

目标:
- 了解 关系型数据库
- SELECT, FROM, WHERE, ORDER BY
- DISTINCT
- eqjoin, innerjoin