# graduation-process-management
### 2021.02.16
#### task - 教师更新描述信息
patch /api/teachers/info

#### task - 手动添加删除教师/学生
post /api/director/teachers

手动添加学生，使用导入表格相同接口，将学生对象存数组  
post /api/director/students

delete /api/director/teachers/{tid}  
delete /api/director/students/{sid}  

#### task - 创建选导师任务
director，创建任务，下拉选择，选导师任务。加载选导师组件。    
所有导师带学生数，必须与学生总数匹配，否则无法创建选导师任务。

前端，提供2处可修改教师带学生数。   
1.在教师列表(教师维护组件中)，将每个教师所带学生数显式为输入框，某处显式总数与当前学生总数的比较，提供提交按钮，

2.选导师任务组件。vuex要缓存一些数据。  
提供数据：教师列表，学生总数  
教师列表：仅显式姓名与带学生数，带学生数是输入框，先判断vuex是否有teachers，有获取长度，没有则请求：get /api/common/teachers   
学生总数：先判断vuex中是否有students，没有则请求：get /api/common/students/count  
   
2处提交更新请求：patch /api/director/teachers/quantity  

比较相等后，允许创建任务。后端补全任务类型，创建者信息  
post /api/director/tasks/choice

### 业务流程
系统以选择导师，收集整理学生/导师提交的文件为主，发通知为辅(QQ群发通知更便捷，无需学生经常登录系统查看消息)。  
所有模板文档，传qq群更方便吧？  

毕设主要功能过程
- 导入导师，导入学生
- 导师添加毕设方向等简介，可为空
- 启动学生选导师任务，注意并发请求。这个任务与普通任务不同，必须有单独选项，前端有单独组件，无法抽象
- 录入毕设题目。导师与学生单独讨论拟定论文题目(导师建自己的毕设QQ群等)
- 专业讨论题目内容是否冲突是否合适，导师再次修改确定毕设题目内容。学生无修改题目功能。以上应该11月中旬前结束
- 答辩分组，如何分？导入表格，秘书手动选择本组学生？
- 下发毕设开题任务，可能是通知等等，也可能没有此任务，下发分组表格，也可能qq群发
- 任务，全体，下发开题答辩报告模板
- 任务，全体，下发开题答辩记录模板
- 任务，学生，提交开题报告，同组导师可查看
- 任务，教师，开题答辩后，提交对本组每个学生的答辩意见，非文档，文本记录即可
- 任务，学生，提交开题答辩记录。学生查看开题答辩意见，复制评委教师意见，给出回答，提交文档。开题答辩记录文档，要作为毕业答辩考察依据。设置文件命名规则，可打包下载，主任
- 任务，全体，下发期中检查报告模板
- 任务，学生，提交期中检查报告。4月中旬
- 任务，学生，上传查重版毕设，word版，5月末。文件命名规则，打包下载
- 任务，教师，毕业答辩后，提交答辩意见，同上
- 任务，学生，提交毕业答辩记录，同上。文件命名规则，打包下载

### 功能

功能，适合于按角色划分

需要制定，导入教师的表格，直接给权限列，没填算默认值；导入学生表格，前端直接权限赋值。学生分组表格？

功能实现，统一上传文件根目录，分任务模板目录，任务目录。目录下按，ID-任务名称，创建目录。创建任务时，业务逻辑层先处理保存数据操作，在保存文件，异常可使数据同时回滚

##### 学生
学生资料要求提供手机号，修改密码

提交各种文档报告

##### 导师

可重置自己学生密码；查看/修改自己学生信息，毕设信息；

给出本组学生的开题/毕业答辩意见

查看所有学生任务的完成情况，即查询当前未完成学生。如果有自己带的学生，导师负责联系催促

##### 主任

包含以上所有功能，也许给出本组学生答辩意见等等

可修改普通导师权限为主任

发布任务，提供任务级别，是否有上一级任务节点，仅支持2级即可

关闭任务；

修改任务信息，时间/标题内容/等等

删除任务，直接删除任务对应的上传目录。即任务的所有文件

添加删除学生。手动，导入的学生可能有变化

修改导师带学生人数。前端，应给出当前学生数，与导师带学生总人数，的比较，后端给统计数据

##### 超级管理员

初始化时最高权限，导入教师表格，初始化教师权限，导入学生

##### 前端

前端左导航分2部分，固定功能，任务列表

固定的管理功能。比如导师查看当前学生，修改学生信息；主任创建任务，修改/删除/关闭任务去已经创建的任务中操作，无权限的看不到编辑按钮

动态任务列表。不同角色用户看见自己的任务列表，动态生成的任务列表，最高2级

### 数据表

所有表，包含ID(mybatis-plus雪花算法自动生成)；update_time，随修改自动修改，default current_timestamp on update current_timestamp；无外键加索引

id,BIGINT(19)

update_time default current_timestamp on update current_timestamp



用户，user

- 账号/密码，学号，员工号. number, varchar(12)
- 姓名.name, varchar(8)
- 角色值，role, int

角色值，固定，非数据表，减少表数

- 1，学生
- 2，导师
- 4，主任
- 9，超级管理员

学生，student

- 用户ID user_id, BIGINT(19)
- 班级. clazz, varchar(16)
- 导师ID. teacher_id, BIGINT(19)
- 小组, group, int
- 课设题目,topic, varchar(45)

教师，teacher

- 用户ID. user_id, BIGINT(19)
- 小组. group, smallint
- 职称. title,  varchar(8)
- 指导学生数.  quantity, TINYINT(2)
- 描述，预指导课设说明要求，提供可选课设题目等。例如，预指导基于Flutter的移动端课设. varchar(45)

是否可合并成一张用户表？

任务，task。不可分割的实现单元

- 标题. title, varchar(45)
- 描述. description, varchar(45)
- 开始/结束时间；(结束时间是否有意义，不提交怎么办？当前按自动关闭设计.  start_time/end_time, DATETIME
- 状态。0未开始，1开始，2结束。status，int
- 任务类型，type，int。任务类型，详细信息查看types.md文档
- 完成角色，任务由那种角色完成。默认1。也有对教师下发任务。target，1学生，2教师。int
- 发布者ID，user_id, BIGINT(19)
- 模板文件名称。单独的模板文件目录，去找同名文件，可覆盖，文件名冲突要有提示。filename，varchar(45)
- 上传文件名称规则，js的字符串数组[]。详细信息查看types.md文档。一旦确定不可修改，否则，如果学生已经上传，文件已经由此命名，修改会很麻烦。可以直接删除任务，创建新任务
- 文件类型后缀，*.doc, *.docx等。直接存字符串。ext_name, varchar(8)

学生任务，student_task，学生与任务中间表

- 学生ID
- 任务ID
- 任务描述。description, varchar(45)
- 文件名称，上传时，前端直接基于任务命名规范拼接文件名称。拼接的文件名称。filename，varchar(45)

教师答辩意见，teacher_suggestion，教师对指定任务(开题/毕业答辩)的意见

- 教师ID
- 学生ID
- 任务ID
- 问题，对指定毕设，suggestion，description, varchar(45)

### Others
就这点功能，不必分模块了吧  
没加swagger，前后端无法联网联调。并行开发，使用md文档，github分享请求/响应数据结构  
component包下为原工具类，按单例组件设计  
entity直接当do层  
修改application.yml/generatorConfig.xml数据源配置  
在Maven视图/plugins/mybatis-generator，运行mybatis-generator:generate生成model/mapper  
每次均创建model覆盖，但仅为新数据表创建mapper  

mybatis-plus全局雪花算法生成ID；注入乐观锁拦截器  
Jackson序列化时忽略空值属性  
redis后期看情况加  
其他自己加  




