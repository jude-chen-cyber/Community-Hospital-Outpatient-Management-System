## 社区医院门诊管理系统

一个基于 Web 的社区医院门诊管理平台，支持患者线上预约、现场登记、就诊状态流转、线上/线下缴费、医生排班管理及多维度数据查询与统计。

✨ 功能特性

· 患者服务：在线预约、查看预约记录、取消预约
· 前台工作：现场登记、预约核销签到、就诊状态管理、缴费结算、患者信息查询、收费报表
· 医务管理：诊室管理、医生排班、员工管理、患者与就诊记录查询
· 数据统计：收入统计、就诊量分析、多维度报表生成
· 权限控制：基于角色的访问控制（患者、前台、管理员）

🛠 技术栈

· 后端：Python Flask + PyMySQL + JWT 认证
· 前端：Vue 3 + Element Plus
· 数据库：MySQL 8.0（生产推荐） / SQLite（开发演示）
· 脚本工具：PowerShell（Windows 一键启动）

📁 目录结构

```
├── backend/          # Flask 后端 (REST API + JWT)
├── frontend/         # Vue3 + Element Plus 前端
├── database/         # MySQL 建表、初始化及测试数据脚本
├── docs/             # 设计文档
│   ├── 系统分析与设计.md
│   └── 数据库设计.md
├── PLAN.md           # 实现计划与需求对照
└── start.ps1         # Windows 一键启动脚本
```

⚙️ 环境要求

· MySQL 8.0（用于正式部署）
· Python 3（运行 Flask 后端）
· Node.js + npm（运行 Vue 前端）
· Windows PowerShell 5.1+（运行一键启动脚本）

🚀 快速开始

方法一：一键启动（推荐，Windows + MySQL）

在项目根目录打开 PowerShell，执行：

```powershell
.\start.ps1 -MySqlUser root -MySqlHost 127.0.0.1 -MySqlPort 3306
```

常用参数：

· -MySqlPassword <密码>：免交互输入（密码会明文写入 .env，请谨慎使用）
· -MySqlExePath <路径>：指定 mysql.exe 路径（当其不在 PATH 时）
· -IncludeTestData：导入测试数据（database/test_data.sql）
· -AutoSeed：启动后端时自动生成演示数据（开发演示用）

提示：若因执行策略导致脚本无法运行，可使用：

```powershell
powershell -ExecutionPolicy Bypass -File .\start.ps1 ...
```

首次运行会自动创建 Python 虚拟环境、安装前后端依赖并初始化数据库，请耐心等待。

停止服务：关闭脚本启动的两个 PowerShell 窗口（或分别在其中按 Ctrl+C）。

方法二：手动启动（MySQL）

1. 初始化数据库：
   ```sql
   source database/schema.sql;
   source database/init_data.sql;
   -- 可选：source database/test_data.sql;
   ```
2. 配置后端：
   复制 backend/.env.example 为 backend/.env，修改数据库连接等信息：
   ```ini
   DATABASE_URL=mysql+pymysql://root:password@127.0.0.1:3306/community_hospital?charset=utf8mb4
   JWT_SECRET_KEY=change-me
   ```
3. 启动后端：
   ```bash
   cd backend
   pip install -r requirements.txt
   python run.py
   ```
4. 启动前端：
   ```bash
   cd frontend
   npm install
   npm run dev
   ```

方法三：快速演示（SQLite，无需 MySQL）

1. 启动后端（自动建表并填充种子数据）：
   ```bash
   cd backend
   pip install -r requirements.txt
   python run.py
   ```
   默认启用 AUTO_SEED=1，如需关闭请在 backend/.env 中设置 AUTO_SEED=0。
2. 启动前端：
   ```bash
   cd frontend
   npm install
   npm run dev
   ```

🌐 访问地址

· 前端界面：http://localhost:5173
· 后端 API：http://localhost:5000（前端已通过代理转发 /api 请求）

系统会根据登录账号的角色自动跳转到对应工作台：

· 患者 → 患者预约页面
· 前台 → 前台工作台
· 管理员 → 管理后台

👤 默认账号

角色 账号 密码 说明
管理员 admin admin123 管理后台，拥有全部权限
前台 reception reception123 前台工作台，处理挂号、缴费等
患者 patient1 patient123 患者端，可预约、查看记录（也支持自行注册）

📋 功能模块与入口

患者端

· 预约挂号（/patient/appointment）：选择科室与时间提交预约，查看与管理预约记录。

前台端

登录后进入“前台工作台”：

· 挂号登记：现场录入患者信息，直接创建就诊记录。
· 预约处理：确认/取消预约，并为到院患者执行“到院签到”。
· 就诊/缴费：管理就诊状态（候诊→就诊中→待缴费），执行缴费结算。
· 患者信息：根据姓名、电话、身份证查询患者。
· 收费报表：按条件筛选账单，支持金额汇总。

管理员端（医务科）

登录后进入“管理后台”：

· 诊室管理：增删改查诊室信息，设置所属科室与启用状态。
· 排班管理：管理医生的出诊排班（日期、时段、诊室、容量）。
· 员工管理：管理系统内员工（医生、护士、前台、管理员）。
· 统计报表：查看收入、就诊量统计（支持按科室、医生、日期分组）。
· 账单查询：全量账单筛选与分页查看，支持本页合计。
· 收入明细：查看每一笔收入记录的详细信息。
· 患者查询：查看所有患者档案。
· 就诊查询：筛选查看就诊记录，可查看/编辑对应病历。

🧪 演示流程

1. 患者预约：
   · 使用患者账号登录，进入“预约挂号”，提交一个新预约，记录 appt_id。
2. 前台核销：
   · 前台登录，进入“预约处理”，找到该预约，点击“到院签到”（系统生成 visit_id 并分配诊室/医生）。
3. 状态流转：
   · 在“就诊/缴费”页面，将该就诊状态依次改为“就诊中”→“待缴费”。
4. 缴费完成：
   · 对“待缴费”的就诊点击“缴费结算”，输入金额完成支付，状态变为“已离院”，系统生成收入记录。

📄 文档

· 系统分析与设计
· 数据库设计
· 实现计划与需求对照

---

项目主要用于数据库课程设计与演示。前后端分离，提供 RESTful API，支持角色化权限管理。