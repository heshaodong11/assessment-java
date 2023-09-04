1.系统架构
本程序以低耦合高内聚作为第一原则，使用微服务架构，每个微服务对应一种功能，通过openFeign进行微服务之间的调用。大致架构如下：

![image](https://github.com/heshaodong11/assessment-java/assets/110575976/183140e8-a07c-44ea-8680-74705b15bb71)

Assessment-common微服务用来保存所有微服务的公共依赖和公共工具类，assessment-gateway通过nacos负责网络负载和路由转发，前端通过gateway转发给对应的微服务。每个需要操作数据库的微服务负责一个数据库，这样对于数据库如果后续数据过大，可以分别保存在不同的服务器上，减小服务器硬盘压力。
文件上传如果通过前端上传给服务器，每个文件所携带的数据量是很大的，一张图片可能几百kB，一个word文件可能会超过1MB，如果所有的文件上传操作都通过前端上传到后端，对于服务器网络将造成很大压力，因此，我们选择通过assessment-third-party返回给前端oss，前端在通过oss签名直接上传到阿里云oss，减小网络压力。
Assessment-chat首先通过redis保存每个聊天文档的id，每个id对应与保存在mongoDB中的聊天文档。Assessment-user使用reids保存每个用户登录和注册所使用的key，mongoDB保存每个用户信息。
2.功能流程图
2.1首页功能（展示每张表对应的最佳用户的头像和当前用户的完成进度）

![image](https://github.com/heshaodong11/assessment-java/assets/110575976/5f87c782-4687-4723-9e2a-6f3d699a45f1)

2.2 指标详情页功能（展示当前指标表下的所有用户信息）

![image](https://github.com/heshaodong11/assessment-java/assets/110575976/d4a09075-0e02-445b-95cf-3bd1f7e1f037)

2.3 指标填写页（如果检查数据和提交数据都分别上传一次所有指标数据，将会造成网络负载，所以使用rabbitMQ延迟队列，30分钟后检测用户是否进行数据提交，没有提交则删除已经保存的指标数据）（注：如果java后台使用multiFile进行文件接收，可能收到.bat .vbs .php等病毒文件，这需要对java后端进行接收文件判断，即使判断之后，也可能面临，修改.txt文件为.bat文件的攻击，所以在没有网络安全基础时，优先让前端进行文件解析）

![image](https://github.com/heshaodong11/assessment-java/assets/110575976/4c0fffab-afcc-4af6-a2e8-016db7638c98)


2.4 指标图表（echarts绘制指标相关图表，并添加保存按钮，用来发送到用户聊天中）

2.5 用户聊天信息（redis保存聊天文档的索引项uuid，mongoDB保存用户聊天文档）

![image](https://github.com/heshaodong11/assessment-java/assets/110575976/d5663fdd-3fe0-4179-b0f4-5b4fc3c8c0e9)


2.6 用户个人信息（第三方登录使用第三方个人信息，手机号注册使用随机保存在阿里OSS中的用户头像，用户修改头像阿里OSS签名直传）

![image](https://github.com/heshaodong11/assessment-java/assets/110575976/48c5894e-880e-4833-90f8-148fbd01c2a8)

3.软件版本

3.1后端

Springboot 2.3.7RELEASE

Springcloud Hoxton.SR12

Maven 3.8.8

Nacos 端口号8848

elasticsearch 7.13.0 端口号 9200 （注：spring集成的6.x版本的es不能使用ELasticsearchRestTemplate）

Kibana 7.13.0

Redis 端口号6379

rabbitMQ 3.12.1 端口号5672 网络端口号15672

3.2前端

Uniapp vue2 echarts
