
自动组网P2P软件wiretinc完整产品/开发文档

一、项目概述

本项目是一套跨平台自动组网、高安全性、极简配置、强大NAT穿透能力的P2P网络软件。
它支持Mesh自动组网、灵活路由、多端自动管理，且全程端到端加密。
核心亮点：极简配置、自动路由、NAT穿透、用户统一管理、自动下发配置、多平台支持。

⸻

二、核心功能

基础功能
	•	自动节点发现与组网（Mesh/混合模式）
	•	高效点对点加密通信（现代算法：Curve25519/ChaCha20）
	•	高穿透率NAT处理（UDP打洞、STUN、UPnP、TURN兜底）
	•	节点/路由/流量实时可视化
	•	节点状态实时监控、自动自愈
	•	流量统计与日志记录
	•	跨平台支持（Windows、macOS、Linux、Android、iOS）
	•	API/CLI/图形界面管理

用户管理增强
	•	用户可通过邮箱、微信、手机注册
	•	用户名+密码登录
	•	多设备：同一用户下任意设备可自动组网
	•	服务端集中管理：启用/禁用、设置用户有效期
	•	有效期控制：用户到期/禁用立即失去组网能力
	•	配置下发：客户端登录后自动上传系统信息，服务端自动生成并下发配置，客户端自动应用
	•	管理员后台：支持用户、设备、配置、日志等全流程可视化管理

⸻

三、用户画像与使用场景
	•	企业IT：自动组建专用安全虚拟网
	•	家庭/极客：多设备、多地互联
	•	IoT/智能终端：远程批量管理
	•	SaaS平台：提供自动组网能力的二次开发接口

⸻

四、非功能性需求
	•	性能：节点数≥200，单链路带宽≥1Gbps，组网延迟<30ms
	•	安全：端到端加密，防重放、防中间人、密码与Token加密存储
	•	兼容性：全主流平台
	•	易用性：零配置体验、自动运维
	•	稳定性：断线自愈、可监控

⸻

五、页面/交互流程

客户端
	•	欢迎页：注册/登录（邮箱/微信/电话任选其一）
	•	登录页：用户名+密码
	•	设备管理页：展示当前用户所有设备状态
	•	网络状态页：自动组网、节点状态、流量等
	•	日志页：历史事件与错误提示

管理后台
	•	用户管理：注册用户、状态、有效期、禁用/启用
	•	设备管理：查看、踢下线、配置下发
	•	日志/统计：用户/设备操作历史
	•	配置管理：批量下发、历史配置归档

⸻

六、数据流/系统流程
	1.	用户注册（邮箱/微信/手机号，验证码/授权，设置密码）
	2.	客户端登录（用户名+密码），服务端校验返回Token
	3.	客户端自动上传本机信息
	4.	服务端根据信息下发定制化配置文件
	5.	客户端自动应用配置，加入组网
	6.	管理员可随时修改用户状态/有效期，强制下线等

⸻

七、数据库结构

users

字段	类型	说明
id	string/UUID	用户唯一标识
username	string	用户名
password_hash	string	密码哈希（加密存储）
email	string	邮箱
phone	string	电话
wx_openid	string	微信OpenID
status	int	1=启用 0=禁用
expire_time	datetime	账号有效期
create_time	datetime	创建时间
last_login	datetime	最近登录

devices

字段	类型	说明
id	string/UUID	设备唯一标识
user_id	string	所属用户ID
device_name	string	设备名
platform	string	平台/系统
sys_info	string	系统详细信息
last_online	datetime	最近在线时间
status	int	1=在线 0=离线

sessions

字段	类型	说明
id	string/UUID	会话ID
user_id	string	用户ID
device_id	string	设备ID
token	string	登录令牌
login_time	datetime	登录时间
expire_time	datetime	有效期
status	int	1=有效 0=失效

nodes/links/logs
	•	nodes：节点信息（与设备表可合并或映射）
	•	links：路由拓扑
	•	logs：所有日志、事件、告警

⸻

八、API接口（部分示例）

# 用户注册与登录
POST   /api/users/register          # 参数：邮箱/微信/电话+验证码+密码
POST   /api/users/login            # 参数：用户名+密码，返回Token
GET    /api/users/me               # 当前用户信息（需Token）

# 设备相关
POST   /api/users/devices/register # 客户端上报系统信息注册新设备
GET    /api/users/devices          # 获取本用户所有设备

# 配置下发
GET    /api/users/config           # 获取下发配置，参数：系统信息等

# 管理接口（管理员权限）
POST   /api/admin/users/enable     # 启用/禁用用户
POST   /api/admin/users/expire     # 设置有效期
GET    /api/admin/users            # 用户列表/状态
GET    /api/admin/devices          # 设备列表/状态
POST   /api/admin/devices/kick     # 踢设备下线
POST   /api/admin/config/push      # 批量下发配置

# 节点/P2P核心API同前述（/api/nodes, /api/topology等）


⸻

九、技术方案与架构
	•	后端服务：Rust/Go（高性能跨平台），RESTful API
	•	P2P/组网核心：Rust/Go/C++实现，Mesh+NAT打洞
	•	数据库：SQLite（嵌入式）或PostgreSQL（服务端）
	•	前端UI：Tauri+React/Vue（跨平台桌面），可拓展Web管理
	•	移动端/小程序：后续可拓展
	•	配置推送：根据客户端信息，动态生成/分发
	•	部署：Docker镜像/一键脚本/二进制包
	•	安全：全加密传输，敏感数据加密存储

⸻

十、开发规范
	•	变量/常量/文件/类名：统一风格（snake_case/大写常量/驼峰类名）
	•	所有函数、API均需注释功能、参数、返回、异常
	•	提交前代码需无调试残留
	•	API、文档、配置全在/docs目录
	•	提交需带自动化测试用例
	•	密码/Token严禁明文存储
	•	配置/日志/敏感数据全UTF-8编码

⸻

十一、初始化/部署脚本（setup_env.sh）

#!/bin/bash
set -e

apt-get update
apt-get install -y git curl build-essential pkg-config libssl-dev libsodium-dev sqlite3

# 安装 Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source $HOME/.cargo/env

# 安装 Node.js 与 Yarn
curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
apt-get install -y nodejs
npm install -g yarn

# 克隆代码库（需替换成实际地址）
git clone https://github.com/your-org/p2pnet.git

# 安装前端依赖
cd p2pnet/ui
yarn install

echo "全部依赖已就绪"


⸻

十二、典型用例
	•	用户A注册（邮箱/微信/手机），添加多台设备，自动组网
	•	管理员禁用用户B，B所有设备立刻断开组网
	•	用户C登录新设备，服务端识别并自动下发适配配置
	•	用户D过期，自动失去组网权限

⸻

十三、交付与管理
	•	所有源代码、脚本、配置、文档存储在统一仓库
	•	每个主要模块独立分支，自动测试通过后合并
	•	每次上线/部署前做自动化测试
	•	文档、接口、配置持续同步维护

