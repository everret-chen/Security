# python

## python项目多文件模块化设计
1.出发思想:
- 关注点分离。一个文件只负责一件明确的事。
- 单一职责
- 高内聚低耦合

2.具体步骤：
- 先按功能分层（如数据访问，业务逻辑、用户界面等）
- 再按实体或特性划分

## 默认项目框架
```
my_project/
│
├── main.py           # 程序入口，只负责串联流程
├── models.py         # 定义数据类 (class User, class Product)
├── utils.py          # 通用工具函数
├── config.py         # 所有配置项和常量
│
├── handlers/         # 或者 services/，放核心业务逻辑
│   ├── auth.py       # 登录注册相关
│   └── order.py      # 订单处理相关
│
└── data/             # 如果需要，放数据文件 (如.csv, .json)
```

