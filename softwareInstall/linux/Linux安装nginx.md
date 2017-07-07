sudo ./configure --prefix=/home/developer/local/nginx && make && make install
--prefix=xxx来指定安装目录

1.下载 ngnix
2. tar -zxvf ngnix
3. make
4. make install
5. 编译以后的文件拷贝到指定的目录分派权限
6.配置文件启动

`
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" "$request_body"'
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

proxy_cache_path /home/work/local/nginx/proxy_cache levels=1:2 keys_zone=content:20m inactive=1d max_size=100m;
proxy_buffer_size 4k;
proxy_buffers 100 8k;
proxy_cache_valid any 1h;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    access_log  logs/access.log  main;
	#error_log logs/error.log main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;
    server {
    listen 8001;
    root html;
}
    server {
        listen       8000;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

    #        error_page 405 =200 /;
        #location ~* notmatch {
        #if ( $remote_addr ~ "101.231.127.13") {
        #    location ~* (a) {
        #        proxy_pass http://10.116.15.173:8001;
        #    }
        #}

        #外部ip过来，如果访问这几个地址，跳转到单独沙盒,保证银行过来通知，继续往下面匹配
        location ~* (1\_0\_0\_1|cashierBank|cashier|mcashier|ddpDirectBind) {
                proxy_pass http://10.116.15.72:8081;
							proxy_redirect off;
        }

				location ~* (\/info) {
                proxy_pass http://10.116.15.156:8080;
                proxy_redirect off;
        }

        location ~* (merchantgateway/interface) {
            if ( $remote_addr !~* "10.116.*") {
                proxy_pass http://10.116.15.144:8081;
            }
        }

			location ~* (teststub/testpage/merchantgateway02.jsp) {
            if ( $remote_addr !~* "10.116.*") {
                proxy_pass http://10.116.15.72:7890;
            }
        }

  	 location ~* (channelgateway/interface/chinapay/postrecv) {
                         proxy_pass http://10.116.15.73:8081;
                        proxy_redirect off;
            }

        location ~* (channelgateway/interface/bankcard_pay/postorder|channelgateway/interface/express_pay/sendSMS|channelgateway/interface/bankcard_refund|channelgateway/interface/dummybank/postrecv) {
            if ( $remote_addr !~* "10.116.*") {
                proxy_pass http://10.116.15.147:8081;
            }
        }

        location ~* (teststub) {
            proxy_pass http://10.116.15.181:7890;
            #proxy_pass http://myCluster;
            proxy_redirect off;
        }

        location ~* (testpage2) {
            proxy_pass http://10.116.15.182:8090;
            #proxy_pass http://myCluster;
            proxy_redirect off;
        }

        location ~* (chinapay|cib|icbc|boc\/) {
            proxy_pass http://10.116.15.173:8091;
            #proxy_pass http://myCluster;
            proxy_redirect off;
        }

        location / {
            #proxy_pass http://myCluster;
            proxy_pass http://10.116.15.73:8081;
            #proxy_pass http://10.116.15.171:8001;
            proxy_redirect off;
        }


        #error_page 405 =200 @405;

        #location / {
        #    root   html;
        #    index  index.html index.htm;
        #}

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }

	upstream myCluster {
	server 10.116.15.182:8000;
	server 10.116.15.173:8000;
	}

    upstream backend {
      ip_hash;
      server   10.116.15.182;
      server   10.116.15.173;
    }

    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
}
 `

	*
正则表达式匹配，其中：
* ~ 为区分大小写匹配
* ~* 为不区分大小写匹配
* !~和!~*分别为区分大小写不匹配及不区分大小写不匹配
文件及目录匹配，其中：
* -f和!-f用来判断是否存在文件
* -d和!-d用来判断是否存在目录
* -e和!-e用来判断是否存在文件或目录
* -x和!-x用来判断文件是否可执行
flag标记有：
* last 相当于Apache里的[L]标记，表示完成rewrite
* break 终止匹配, 不再匹配后面的规则
* redirect 返回302临时重定向 地址栏会显示跳转后的地址
* permanent 返回301永久重定向 地址栏会显示跳转后的地址
一些可用的全局变量有，可以用做条件判断
$args, 请求中的参数;
$content_length, HTTP请求信息里的"Content-Length";
$content_type, 请求信息里的"Content-Type";
$document_root, 针对当前请求的根路径设置值;
$document_uri, 与$uri相同;
$host, 请求信息中的"Host"，如果请求中没有Host行，则等于设置的服务器名;
$limit_rate, 对连接速率的限制;
$request_method, 请求的方法，比如"GET"、"POST"等;
$remote_addr, 客户端地址;
$remote_port, 客户端端口号;
$remote_user, 客户端用户名，认证用;
$request_filename, 当前请求的文件路径名
$request_body_file
$request_uri, 请求的URI，带查询字符串;
$query_string, 与$args相同;
$scheme, 所用的协议，比如http或者是https，比如rewrite  ^(.+)$  $scheme://example.com$1  redirect;
$server_protocol, 请求的协议版本，"HTTP/1.0"或"HTTP/1.1";
$server_addr, 服务器地址，如果没有用listen指明服务器地址，使用这个变量将发起一次系统调用以取得地址(造成资源浪费);
$server_name, 请求到达的服务器名;
$server_port, 请求到达的服务器端口号;
$uri, 请求的URI，可能和最初的值有不同，比如经过重定向之类的。
