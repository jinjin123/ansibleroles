# Ansible Role: tengine

安装tengine

## 介绍
Tengine是由淘宝网发起的Web服务器项目。它在Nginx的基础上，针对大访问量网站的需求，添加了很多高级功能和特性。Tengine的性能和稳定性已经在大型的网站如淘宝网，天猫商城等得到了很好的检验。它的最终目标是打造一个高效、稳定、安全、易用的Web平台。

- 官方网站：http://tengine.taobao.org/
- 官方文档地址：http://tengine.taobao.org/documentation.html

## 要求

此角色仅在RHEL及其衍生产品上运行。

## 测试环境

ansible主机

    ansible: 2.9.1
    os: Centos 7.4 X64
    python: 2.7.5

ansible管理主机

    os: Centos 6, Centos 7, Debian 9

## 角色变量

```yaml
software_files_path: "/opt/software"
software_install_path: "/usr/local"

tengine_version: "2.3.2"

tengine_file: "tengine-{{ tengine_version }}.tar.gz"
tengine_file_path: "{{ software_files_path }}/{{ tengine_file }}"
tengine_file_url: "http://tengine.taobao.org/download/tengine-{{ tengine_version }}.tar.gz"


tengine_started: true
tengine_enabled: true
tengine_daemon: /usr/local/sbin/nginx
tengine_temp_path: "/var/tmp/tengine"
tengine_conf_path: "{{ software_install_path }}/tengine-{{ tengine_version }}/conf/"
tengine_conf_file: "{{ tengine_conf_path }}/nginx.conf"

tengine_user: 'tengine'
tengine_group: 'tengine'
tengine_build_options: ''
tengine_configure_command: >
  ./configure 
  --prefix={{ software_install_path }}/tengine-{{ tengine_version }}
  --user={{ tengine_user }}
  --group={{ tengine_group }}
  --with-select_module
  --without-select_module
  --with-poll_module
  --without-poll_module
  --without-procs
  --with-threads
  --with-file-aio
  --with-http_ssl_module
  --with-http_v2_module
  --with-http_realip_module
  --with-http_addition_module
  --with-http_xslt_module
  --with-http_image_filter_module
  --with-http_geoip_module
  --with-http_sub_module
  --with-http_dav_module
  --with-http_flv_module
  --with-http_mp4_module
  --with-http_gunzip_module
  --with-http_gzip_static_module
  --with-http_auth_request_module
  --with-http_random_index_module
  --with-http_secure_link_module
  --with-http_degradation_module
  --with-http_slice_module
  --with-http_stub_status_module
  --http-client-body-temp-path={{ tengine_temp_path }}/client
  --http-proxy-temp-path={{ tengine_temp_path }}/proxy
  --http-fastcgi-temp-path={{ tengine_temp_path }}/fcgi
  --http-uwsgi-temp-path={{ tengine_temp_path }}/uwsgi
  --http-scgi-temp-path={{ tengine_temp_path }}/scgi
  --with-stream
  --with-stream_ssl_module
  --with-stream_realip_module
  --with-stream_geoip_module
  --with-stream_ssl_preread_module
  --with-stream_sni
  --with-pcre
  --with-debug
  --without-http_upstream_keepalive_module
  --add-module=modules/ngx_http_footer_filter_module
  --add-module=modules/ngx_http_reqstat_module
  --add-module=modules/ngx_http_slice_module
  --add-module=modules/ngx_http_trim_filter_module
  --add-module=modules/ngx_http_upstream_check_module
  --add-module=modules/ngx_http_upstream_consistent_hash_module
  --add-module=modules/ngx_http_upstream_keepalive_module
  --add-module=modules/ngx_http_upstream_session_sticky_module
  --add-module=modules/ngx_http_upstream_dynamic_module
  --add-module=modules/ngx_http_upstream_dyups_module
  --add-module=modules/ngx_http_upstream_vnswrr_module
  --add-module=modules/ngx_http_user_agent_module
  --add-module=modules/ngx_multi_upstream_module
  {{ tengine_build_options }}

tengine_pid_file: '/var/run/tengine.pid'
tengine_worker_processes: "{{ ansible_processor_vcpus | default(ansible_processor_count) }}"
tengine_worker_connections: "10240"
tengine_multi_accept: "off"

tengine_log_path: "/var/log/tengine"
tengine_error_log: "{{ tengine_log_path }}/error.log"
tengine_access_log: "{{ tengine_log_path }}/access.log"
tengine_mime_file_path: "mime.types"

tengine_sendfile: "on"
tengine_tcp_nopush: "on"
tengine_tcp_nodelay: "off"

tengine_keepalive_timeout: "65"
tengine_keepalive_requests: "1000"

tengine_client_max_body_size: "64m"

tengine_server_names_hash_bucket_size: "64"

tengine_proxy_cache_path: ""

tengine_extra_conf_options: ""
#   tengine_extra_conf_options: |
#   env VARIABLE;

tengine_extra_http_options: ""
#  tengine_extra_http_options: |
#    proxy_buffering  off;
#    proxy_set_header   X-Real-IP $remote_addr;
#    proxy_set_header   X-Scheme $scheme;
#    proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
#    proxy_set_header   Host $http_host;

tengine_remove_default_vhost: false
tengine_vhosts: []
#- listen: 80
#  locations:
#  - name: /
#    server_name: test.com
#    proxy_pass: http://127.0.0.1:8080
#    proxy_set_headers:
#      Host: $host
#      X-Real-IP: $remote_addr
#      X-Forwarded-For: $proxy_add_x_forwarded_for

tengine_upstreams: ''
#- name: upstremtest
#  servers:
#  - 127.0.0.1:8000 weight=3 max_fails=2 fail_timeout=2
#  - 127.0.0.1:8001
#  - 127.0.0.1:8002
#  - 127.0.0.1:8003 backup
nginx_stream: {}
# upstreams:
# - name: streamtest
#   servers:
#    - 127.0.0.1:22
# servers:
# - listen: 2222
#   proxy_pass: streamtest

tengine_proxys: false
tengine_gzip: false
tengine_stub_status: false

```
    
## 依赖

- epel

## github地址
https://github.com/jimmy/Ansible-roles/tree/master/tengine

## Example Playbook
```yaml
# 默认安装
- hosts: node1
  roles:
    - tengine

# 定义虚拟主机
- hosts: node1
  vars:
  - tengine_vhosts:
    - listen: 80
      locations:
      - name: /
        proxy_pass: http://192.168.77.135:8080
        proxy_set_headers:
          Host: $host
          X-Real-IP: $remote_addr
          X-Forwarded-For: $proxy_add_x_forwarded_for
  roles:
   - tengine
   
# 定义虚拟主机并设置代理缓存
- hosts: node1
  vars:
  - tengine_proxy_cache_path: /tmp levels=1:2 keys_zone=STATIC:10m inactive=24h max_size=5g
  - tengine_vhosts:
    - listen: 80
      extra_parameters: |
        location / {
            proxy_pass   http://192.168.77.135:8080;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_cache STATIC;
            proxy_cache_valid 200 1d;
            proxy_cache_use_stale error timeout invalid_header updating http_500 http_502 http_503 http_504;
        }
  roles:
   - tengine
   
# 设置upstream
- hosts: node1
  vars:
  - tengine_upstreams:
    - name: upstremtest
      servers:
      - 127.0.0.1:8000 weight=3 max_fails=2 fail_timeout=2
      - 127.0.0.1:8001
      - 127.0.0.1:8002
      - 127.0.0.1:8003 backup
  - tengine_vhosts:
    - listen: 80
      server_name: test.com
      locations:
      - name: /
        proxy_pass: http://upstremtest
    - listen: 80 
      server_name: test2.com
      locations:
      - name: /
        proxy_pass: http://upstremtest
  roles:
  - tengine
  
# 定义stream
- hosts: 192.168.77.131
  vars:
  - nginx_stream:
     upstreams:
     - name: streamtest
       servers:
        - 127.0.0.1:22
     servers:
     - listen: 2222
       proxy_pass: streamtest

  roles:
  - tengine
```
## 使用

```
# non systemd
/etc/init.d/tengine 
Usage: /etc/init.d/tengine {start|stop|reload|configtest|status|force-reload|upgrade|restart|reopen_logs}

# systemd
systemctl status tengine
systemctl start tengine
systemctl stop tengine
```