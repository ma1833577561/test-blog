# Blog

*  Extra's blog


## 服务器
    ECS
        按量收费
    分类选型 
    筛选: 2vcpu + 4GB
    架构：x86计算
    分类：通用型
    规格：计算增强型（性价比最高的）
    镜像：Ubuntu v20.04 64位
    next
    公网IP: true
    宽带计费模式： 按使用流量
    带宽峰值：Max
    安全组：all(勾选所有)
    next
    登陆凭证：自定义密码
    登录名：root(需记录)
    登陆密码：xxxx(需记录)
    实例名称：自定义
    主机名：自定义
    实例释放保护：设置有效时间
    next
    下单（账户余额>100）

## 域名
    * 阿里云购买
    * 域名解析
        主机记录 [www,shop,top,自定义] .xxx.com(自己购买的域名)
        记录值 服务器公网IP地址

## 登录服务器
    ssh [服务器管理员名称]@[服务器公网IP] 
    ## ssh root@39.168.23.56
    ## passworld: 默认是看不到输入的盲数
    
    apt update //更新软件源
    apt install git // 安装git,用于拉取代码 --version
    cur -sL https://deb.nodesource.com/setup_14.x | sudo -E bash - //添加node14安装源
    apt install nodejs // 安装nodejs -V
    apt install nginx // 安装nginx -V
    npm install yarn -g // 安装yarn

    cd /var/www/   //项目目录
    cd  /etc/nginx/sites-available/ nginx目录

## ngix 配置

```txt
   ### 目录 vim /etc/nginx/sites-available/default
   ## i-- 输入模式
   ## 编辑完成按`ESC`键，
   ## 输入 [:] + [wq] --保存&&退出
   ## 或 输入 [:] + [qa] --取消&&退出
    server {
        ...
        root  /var/www/project目录
        ...
        server_name www.xxx.com(你的域名)

        location / {
            try_files $uri $uri/ /index.html; # 项目的入口文件（刷新） 
        }

        location ^~/api/ {
            proxy_pass https://***.com; # 后端接口地址
            # rewrite ^/api/(.*)$ /$1 break;
        }
    }

    ## nginx -t // 检查配置
    ## nginx -s reload // 重启服务
```
## https

scp xxx.pem [服务器管理员名称]@[服务器公网IP]:/tmp
scp xxx.pem root@39.168.23.56:/tmp
scp xxx.key root@39.168.23.56:/tmp  // 以scp方式上传到服务器tmp目录下

cd /etc/nginx 
mkdir ssl // 新建文件夹
mv /tmp/private.key /etc/nginx/ssl 把/tmp/private.key文件移动到 /etc/nginx/ssl下

http->https

添加以下配置
server {
    ...
    listen 443 ssl default_server;
    ssl_certificate /etc/nginx/ssl/full_chain.pem;
    ssl_certificate_key /etc/nginx/ssl/private.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

    root /var/www/test-blog;
    ...
}

nginx全部配置：
server {
    listen 80;
    server_name blog.extra9705.xyz;
    rewrite ^(.*) https://$server_name$1 permanent;
}

server {
    listen 443 ssl default_server;
    ssl_certificate /etc/nginx/ssl/full_chain.pem;
    ssl_certificate_key /etc/nginx/ssl/private.key;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

    root /var/www/test-blog;

    index index.html index.htm index.nginx-debian.html;

    location / {
        # First attempt to serve request as file, then
        # as directory, then fall back to displaying a 404.
        try_files $uri $uri/ =404;
    }

}