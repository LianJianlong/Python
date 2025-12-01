# Multable-B 项目结构及关键模块样板

## 项目结构
Multable-B/  
├── backend/  
│   ├── app/  
│   │   ├── auth/             # OAuth2 认证与授权  
│   │   ├── users/            # 用户、角色、权限管理  
│   │   ├── projects/         # 项目/部门/表格结构  
│   │   ├── tables/           # 多维表格接口（主键/外键/引用/视图）  
│   │   ├── dashboard/        # 综合看板与可视化  
│   │   ├── tools/            # 项目工具相关  
│   │   ├── knowledge/        # 项目知识库、文件链接  
│   │   ├── ai/               # AI assistant（chatbot/分析）  
│   │   ├── mails/            # 内部邮箱/AI邮件推送  
│   │   └── history/          # 历史操作记录  
│   ├── database/             # 数据库模型（SQLAlchemy/ORM）  
│   ├── config/               # 全局配置（语言、邮件、部署等）  
│   └── main.py               # FastAPI 服务入口  
├── frontend/  
│   ├── src/  
│   │   ├── components/       # 通用组件（表格、项目、知识库、工具、邮箱等）  
│   │   ├── views/            # 页面视图（项目、多维表格、看板、知识库、AI助手等）  
│   │   ├── assets/  
│   │   ├── i18n/             # 多语言支持（默认中文、可选英文）  
│   │   ├── utils/  
│   │   └── main.tsx          # 应用入口  
│   └── public/  
├── docs/  
│   └── design.md             # 技术详解及接口文档  
├── deploy/  
│   └── docker-compose.yaml   # Docker/云部署脚本  
├── tests/                    # 前后端测试  
└── description.md            # 项目需求描述

---

## 核心模块样板举例

**后端 FastAPI 数据模型简要:**

```python
from sqlalchemy import Column, String, Integer, Boolean, ForeignKey, DateTime, Text
from sqlalchemy.orm import declarative_base, relationship

Base = declarative_base()

class Role(Base):
    __tablename__ = "roles"
    id = Column(Integer, primary_key=True)
    name = Column(String, unique=True)  # 超管、管、用户、访客

class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True)
    username = Column(String, unique=True)
    email = Column(String, unique=True)
    role_id = Column(Integer, ForeignKey("roles.id"))
    role = relationship("Role")
    active = Column(Boolean, default=True)

class Project(Base):
    __tablename__ = "projects"
    id = Column(Integer, primary_key=True)
    name = Column(String)
    owner_id = Column(Integer, ForeignKey("users.id"))

class Folder(Base):
    __tablename__ = "folders"
    id = Column(Integer, primary_key=True)
    name = Column(String)
    icon = Column(String)
    project_id = Column(Integer, ForeignKey("projects.id"))

class Table(Base):
    __tablename__ = "tables"
    id = Column(Integer, primary_key=True)
    name = Column(String)
    folder_id = Column(Integer, ForeignKey("folders.id"))
    main_key = Column(String)
    view_type = Column(String)  # list, gantt, calendar, wall

class Field(Base):
    __tablename__ = "fields"
    id = Column(Integer, primary_key=True)
    name = Column(String)
    table_id = Column(Integer, ForeignKey("tables.id"))
    field_type = Column(String)  # int, text, date, etc
    is_primary = Column(Boolean)
    is_foreign = Column(Boolean)

class Permission(Base):
    __tablename__ = "permissions"
    id = Column(Integer, primary_key=True)
    user_id = Column(Integer, ForeignKey("users.id"))
    table_id = Column(Integer, ForeignKey("tables.id"))
    field_id = Column(Integer, ForeignKey("fields.id"), nullable=True)
    can_view = Column(Boolean, default=True)
    can_edit = Column(Boolean, default=False)
    can_delete = Column(Boolean, default=False)
    can_create = Column(Boolean, default=False)

class Mail(Base):
    __tablename__ = "mails"
    id = Column(Integer, primary_key=True)
    sender_id = Column(Integer, ForeignKey("users.id"))
    receiver_id = Column(Integer, ForeignKey("users.id"))
    subject = Column(String)
    content = Column(Text)
    created_at = Column(DateTime)

class History(Base):
    __tablename__ = "history"
    id = Column(Integer, primary_key=True)
    user_id = Column(Integer, ForeignKey("users.id"))
    action = Column(String)
    target = Column(String)
    details = Column(Text)
    timestamp = Column(DateTime)
