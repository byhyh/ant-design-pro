# 科研辅助系统 - 前端项目

## 📋 项目介绍

科研辅助系统的前端项目，提供用户界面和交互功能。采用原生HTML + CSS + JavaScript开发，通过Ajax调用后端API接口。

## 🚀 快速启动

### 方式一：Python HTTP服务器（推荐）

```bash
# 进入前端项目目录
cd frontend

# 启动HTTP服务器
python ../serve.py 8000

# 访问地址
# http://localhost:8000
```

### 方式二：Node.js服务器

```bash
# 安装http-server
npm install -g http-server

# 启动服务器
cd frontend
http-server -p 8000 -c-1

# 访问地址
# http://localhost:8000
```

### 方式三：VS Code Live Server

1. 安装Live Server扩展
2. 右键HTML文件 → "Open with Live Server"

## 📄 页面列表

### 用户端页面
- `chaxin.html` - 系统首页
- `novelty-search.html` - 科技查新服务
- `citation-report.html` - 收录引用检索服务  
- `pricing.html` - 收费标准
- `contact.html` - 联系我们
- `login.html` - 用户登录
- `user-center.html` - 用户中心
- `order-submit.html` - 科技查新订单提交（需要登录）
- `citation-search.html` - 查收查引订单提交（需要登录）
- `submitted-orders.html` - 已提交订单列表（用户只能查看自己的订单）
- `order-detail.html` - 订单详情页面（权限控制）
- `payment.html` - 支付页面

### 管理端页面
- `admin.html` - 后台管理系统（仅管理员可访问，需要ADMIN角色）

### 优化版页面
- `index-optimized.html` - 优化版首页

## 🔐 权限控制系统

### 前端权限实现
- 🔑 **JWT Token认证**：所有需要登录的页面都会验证JWT Token
- 👤 **角色区分**：普通用户(USER) 和 管理员(ADMIN) 两种角色
- 🛡️ **页面访问控制**：
  - 普通用户页面：`submitted-orders.html` - 只显示自己的订单
  - 管理员页面：`admin.html` - 显示所有订单，需要ADMIN角色
- 🚫 **权限拦截**：未登录用户访问受保护页面会跳转到登录页
- 🔄 **Token管理**：自动处理Token过期和刷新

### 权限验证流程
```javascript
// 获取Token并验证权限
function getAuthToken() {
    let token = localStorage.getItem('authToken') || sessionStorage.getItem('authToken');
    // 开发环境使用模拟Token
    return token || createMockToken();
}

// API调用带权限验证
async function makeAuthenticatedRequest(url, options = {}) {
    const token = getAuthToken();
    const response = await fetch(url, {
        ...options,
        headers: {
            'Authorization': `Bearer ${token}`,
            'Content-Type': 'application/json',
            ...options.headers
        }
    });
    
    // 处理权限错误
    if (response.status === 401) {
        handleUnauthorized(); // 跳转到登录页
    } else if (response.status === 403) {
        alert('权限不足'); // 显示权限不足提示
    }
    
    return response.json();
}
```

## 🔧 API配置

前端通过Ajax调用后端API，默认配置：

```javascript
// API基础地址
const API_BASE_URL = '/api';

// 权限控制的接口分类
const API_ENDPOINTS = {
    // 公开接口（无需认证）
    auth: {
        login: '/auth/login',
        register: '/auth/register',
        logout: '/auth/logout'
    },
    // 用户接口（需要USER或ADMIN角色）
    orders: {
        myOrders: '/order/my-orders',           // 查看个人订单
        detail: '/order/{orderId}',             // 订单详情（权限检查）
        noveltySubmit: '/order/novelty-search/submit',  // 提交科技查新
        citationSubmit: '/order/citation-search/submit' // 提交查收查引
    },
    // 管理员接口（仅ADMIN角色）
    admin: {
        allOrders: '/order/admin/all-orders',   // 查看所有订单
        approve: '/order/admin/{orderId}/approve',  // 批准订单
        complete: '/order/admin/{orderId}/complete', // 完成订单
        statistics: '/order/admin/statistics'   // 订单统计
    }
};
```

## 📁 目录结构

```
frontend/
├── *.html              # HTML页面文件
├── css/                 # 样式文件
│   ├── style.css        # 主样式
│   └── ...
├── js/                  # JavaScript文件
│   ├── main.js          # 主脚本
│   ├── api.js           # API调用
│   └── ...
└── README.md            # 项目文档
```

## 🔗 与后端集成

### 开发环境
- 后端API：http://localhost:8080/api
- 前端页面：http://localhost:8000

### 生产环境部署
建议使用Nginx反向代理：

```nginx
server {
    listen 80;
    server_name yourdomain.com;
    
    # 前端静态资源
    location / {
        root /path/to/frontend;
        index chaxin.html;
        try_files $uri $uri/ =404;
    }
    
    # 后端API
    location /api {
        proxy_pass http://localhost:8080/api;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

## 🎨 技术栈

- **HTML5** - 页面结构
- **CSS3** - 样式设计
- **JavaScript (ES6+)** - 交互逻辑
- **Bootstrap** - UI框架
- **jQuery** - DOM操作和Ajax请求

## 📱 响应式设计

项目采用响应式设计，支持：
- 🖥️ 桌面端（>=1200px）
- 💻 笔记本（768px-1199px）
- 📱 平板（576px-767px）
- 📱 手机（<576px）

## 🚀 性能优化

- CSS/JS文件压缩
- 图片懒加载
- 缓存策略优化
- CDN资源加速

## 🔧 开发指南

### 添加新页面
1. 创建HTML文件
2. 引入通用CSS和JS
3. 添加页面特定样式和脚本
4. 更新导航链接

### 权限控制API调用示例

```javascript
// 1. 用户登录（公开接口）
async function login(email, password) {
    try {
        const response = await fetch(`${API_BASE_URL}/auth/login`, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({ email, password })
        });
        
        const result = await response.json();
        if (result.success) {
            // 保存Token到本地存储
            localStorage.setItem('authToken', result.data.token);
            window.location.href = 'user-center.html';
        } else {
            alert(result.message);
        }
    } catch (error) {
        console.error('登录失败:', error);
        alert('网络错误，请稍后重试');
    }
}

// 2. 获取用户订单（需要USER权限）
async function getUserOrders() {
    try {
        const response = await makeAuthenticatedRequest('/api/order/my-orders');
        if (response.success) {
            return response.data.orders; // 只返回当前用户的订单
        }
    } catch (error) {
        console.error('获取订单失败:', error);
    }
}

// 3. 获取所有订单（需要ADMIN权限）
async function getAllOrders() {
    try {
        const response = await makeAuthenticatedRequest('/api/order/admin/all-orders');
        if (response.success) {
            return response.data.orders; // 返回所有用户的订单
        }
    } catch (error) {
        if (error.message === '权限不足') {
            alert('您没有管理员权限，无法查看所有订单');
        }
    }
}

// 4. 提交订单（需要USER权限）
async function submitOrder(orderData) {
    try {
        const response = await makeAuthenticatedRequest('/api/order/novelty-search/submit', {
            method: 'POST',
            body: JSON.stringify(orderData)
        });
        
        if (response.success) {
            alert('订单提交成功！');
            window.location.href = 'submitted-orders.html';
        }
    } catch (error) {
        console.error('提交订单失败:', error);
    }
}

// 5. 权限验证辅助函数
async function makeAuthenticatedRequest(url, options = {}) {
    const token = getAuthToken();
    
    const defaultOptions = {
        method: 'GET',
        headers: {
            'Content-Type': 'application/json',
            'Authorization': token ? `Bearer ${token}` : ''
        }
    };
    
    const finalOptions = { ...defaultOptions, ...options };
    
    try {
        const response = await fetch(url, finalOptions);
        
        // 处理权限相关错误
        if (response.status === 401) {
            handleUnauthorized();
            throw new Error('用户未登录或登录已过期');
        }
        
        if (response.status === 403) {
            throw new Error('权限不足');
        }
        
        return await response.json();
    } catch (error) {
        console.error('API请求失败:', error);
        throw error;
    }
}

// 6. 处理未授权访问
function handleUnauthorized() {
    // 清除本地Token
    localStorage.removeItem('authToken');
    sessionStorage.removeItem('authToken');
    
    // 跳转到登录页
    if (confirm('登录已过期，是否重新登录？')) {
        window.location.href = '/login.html';
    }
}
```

## 📞 技术支持

如有问题，请联系：
- 📧 邮箱：support@chaxin.com
- 📱 电话：400-900-1306 