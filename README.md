

SSPanel-Malio 主题安装指南

## 1. 环境安装

确保安装以下软件版本：

- ☑️ Nginx 1.17
- ☑️ MySQL 5.6
- ☑️ PHP 7.2~7.4

## 2. 配置 PHP

删除禁用的函数：

- `system`
- `proc_open`
- `proc_get_status`
- `putenv`
- `pcntl_signal`

## 3. 使用 Composer 安装依赖

```bash
wget https://getcomposer.org/installer -O composer.phar
php composer.phar
php composer.phar install
```

## 4. 复制配置文件

```bash
cp config/.config.example.php config/.config.php
cp config/.metron_setting.example.php config/.metron_setting.php
cp config/appprofile.example.php config/appprofile.php
```

## 5. 配置站点目录及伪静态

1. 添加站点目录并选择 `/public` 保存。
2. 取消勾选“防跨站攻击”。
3. 编辑伪静态信息：

```nginx
location / {
    try_files $uri /index.php$is_args$args;
}
```

## 6. 设置网站文件权限

在网站目录下，将网站文件的权限设置为 `755`：

```bash
chmod -R 755 /www/wwwroot/你的网站目录
```

## 7. 修改根目录配置文件

编辑 `config.php` 文件。

## 8. 配置数据库信息

将数据库名称、用户名和密码填入 `.config.php` 文件中：

```php
$_ENV['baseUrl'] = 'https://www.xxxx.com'; // 站点地址
$_ENV['db_database'] = 'sspanel'; // 数据库名
$_ENV['db_username'] = 'sspanel'; // 数据库用户名
$_ENV['db_password'] = 'sspanel_password'; // 用户名对应的密码
```

## 9. 创建管理员账号及初始化工作

```bash
cd /www/wwwroot/域名/
php xcat User createAdmin
php xcat User resetTraffic
php xcat SyncRadius syncusers
php xcat Tool initQQWry
php xcat Tool initdownload
```

## 10. 使用宝塔面板的计划任务配置

### 每日任务 (必须)

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每天 0 小时 0 分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat Job DailyJob
  ```

### 检测任务 (必须)

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat Job CheckJob
  ```

### 用户账户相关任务 (必须)

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每小时
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat Job UserJob
  ```

### 检查用户会员等级过期任务 (必须)

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat Job CheckUserClassExpire
  ```

### 检查账号过期任务 (必须)

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每小时
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat Job CheckUserExpire
  ```

### 定时检测邮件队列 (必须)

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat Job SendMail
  ```

### 每日流量报告 (给开启每日邮件的用户发送邮件)

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每天 0 小时 0 分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat SendDiaryMail
  ```

### 审计封禁 (建议设置)

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat DetectBan
  ```

### 检测节点被墙 (可选)

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat DetectGFW
  ```

### 检测中转服务器 (可选)

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每5分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat DetectTransfer
  ```

### Radius (可选)

#### synclogin

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat SyncRadius synclogin
  ```

#### syncvpn

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat SyncRadius syncvpn
  ```

#### syncnas

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat SyncRadius syncnas
  ```

### 自动备份 (可选)

#### 整体备份

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每30分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat Backup full
  ```

#### 只备份核心数据

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每30分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat Backup simple
  ```

### 财务报表 (可选)

#### 日报

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每天 0 小时 0 分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat FinanceMail day
  ```

#### 周报

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每星期 周日 0 小时 0 分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat FinanceMail week
  ```

#### 月报

- **任务类型**: Shell 脚本
- **任务名称**: 自行填写
- **执行周期**: 每月 1 日 0 小时 0 分钟
- **脚本内容**: 
  ```bash
  php /www/wwwroot/你的网站目录/xcat FinanceMail month
  ```
```
