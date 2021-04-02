自动化运维基石CMDB实战
---

### ITSM、ITIL和CMDB

#### ITSM

```text
没有流程的自动化运维就是耍流氓

ITSM就是IT Service Management，即IT服务管理

服务是向客户提供价值的一种手段，使客户不用承担特定的成本和风险就可以获得所期望的结果

系统稳定，业务连续性保障

工资就是服务价值的体现，而不是技术本身

以流程为导向，以客户为中心的IT管理模式
```

##### 三要素

```text
People，Process，Technology
```

#### ITIL

```text
ITIL即IT基础架构库（Information Technology Infrastructure Library）

ITIL不是软件，不是硬件

ITIL是一个可以直接使用的标准，是ITSM的最佳实践，已于2005年12月15日被ISO接受为国际标准ISO20000
```

##### 目标

```text
将IT管理工作标准化，模式化。减少人为误操作带来的隐患
通过服务目录，服务报告，告诉业务部门，我们可以做什么，做了什么
通过系列流程，知识库减轻对英雄式工程师的依赖。把经验积累下来
通过对流程的管控，减少成本，降低风险，提供客户满意度
```

##### IT Service CMM能力成熟度模型


```text
1. 初始级 Initial
被动响应，没有文档记录，几乎没有过程是经过定义的
各项目经验无法重用，依赖于个人的努力和英雄主义

2. 可重复级 Repeatable
建立了基本的服务管理过程。
所有项目有默认的规则，但未文档化，系统化。
产品或服务无清晰的目标和策略。

3. 定义级 Defined
已将IT服务过程文档化，标准化，并综合成标准服务过程
根据客户需求调整服务产品和服务战略
适当的工具和信息报告

4. 优化级 Optimizing
持续改进的IT服务体系
IT与业务指标建立关系
IT与业务协作改进流程
```

##### ITIL V3将ITIL理论分为五部分

```text
服务战略 Service Strategy
服务设计 Service Design
服务转换 Service Transition
服务运营 Service Operation
持续服务改进 Continual Service Improvement
```

#### CMDB

```text
CMDB的概念来自于ITIL中的服务资产与配置管理

CMDB即Configuration Management Database即配置管理数据库

CMDB包含了每一个配置项（Configuration Item，简称CI）

每一个CI都有不同的属性：例如服务器有CPU、内存、品牌

CI和CI直接有关系：例如服务器放置在机柜上

CI有状态： 例如服务器的状态运行中，已停止，维修中

CMDB分层设计

业务层，物理层
```

##### 开源的CMDB工具

```text
iTOP
蓝鲸配置平台
OneCMDB
CMDBuild
```

##### iTOP

下载地址: https://github.com/Combodo/iTop

iTOP安装采用Docker容器方式


##### CMDB三步工作法

```text
1. 资产建模
   设计标结果，设计关系
   
2. 数据录入
   手工录入
   EXCEL导入
   自动采集
   
3. 数据消费
   搜索
   查询
   展示
```

```
参考书目「CMDB分布构建指南」

iTOP基本的资产录入，导出

iTOP通过XML文件新增自定义字段
```

#### CMDB设计

##### CMDB失败案例

```text
数据录入太多，管理难度增加，变更管理困难
与CI等工具关联太复杂，管理难度增加，容易失败
```

```text
一个没有做过运维的研发，很难知道运维需要什么
研发能力越强，CMDB越容易失败
做CMDB不是比谁数据结构学的好
实施CMDB不是看谁的CI多，属性多
```

```text
关系型数据库 - 非关系型数据库 - 图数据库
好的产品与研发技术能力并没有多少关系
```

##### CMDB简单设计

```text
资源提供者
资源消费者
MongoDB祖先数组模式
资产分为资源树模型和应用树模型
```

##### 腾讯蓝鲸CMDB平台

GitHub：https://github.com/tencent/bk-cmdb
