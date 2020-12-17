![Nginx Upsync Icon](https://github.com/VitoVan/nginx-upsync/raw/master/iconfile.png)

# nginx-upsync

[![Docker Image for Nginx Upsync](https://img.shields.io/docker/cloud/build/vitovan/nginx-upsync)](https://hub.docker.com/r/vitovan/nginx-upsync)

Docker Image for [Nginx Upsync](https://hub.docker.com/r/vitovan/nginx-upsync)

Current Version: 

- Nginx: [1.19.6](https://github.com/nginxinc/docker-nginx/blob/1.17.6/mainline/alpine/Dockerfile)
- nginx-upsync-module: [v2.1.3](https://github.com/weibocom/nginx-upsync-module/tree/v2.1.3)
- nginx-stream-upsync-module: [v1.2.2](https://github.com/xiaokai-wang/nginx-stream-upsync-module/tree/v1.2.2)


This image enabled two more [modules](https://www.nginx.com/resources/wiki/modules/) beyond [Nginx Offical Mainline Alpine Image](https://github.com/nginxinc/docker-nginx/blob/1.19.6/mainline/alpine/Dockerfile):

- [nginx-upsync-module](https://github.com/weibocom/nginx-upsync-module)

- [nginx-stream-upsync-module](https://github.com/xiaokai-wang/nginx-stream-upsync-module)

## How to use

> Demostration only, for more info please refer to the module repo(s).

1. Prepare nginx-upsync.conf

    File: `nginx-upsync.conf`

    ```nginx
    events { }

    http {
        upstream demo {
            least_conn;
            upsync 127.0.0.1:8500/v1/health/service/demo upsync_timeout=6m upsync_interval=500ms upsync_type=consul_health strong_dependency=off;
            upsync_dump_path /etc/nginx/conf.d/demo.conf;
            upsync_lb least_conn;
            include /etc/nginx/conf.d/demo.conf;
        }

        server {
            location = /demo {
                proxy_pass http://demo;
            }
            location = /upstream_show {
                upstream_show;
            }
        }
    }
    ```

2. Prepare nginx-demo.conf

    File: `nginx-demo.conf`

    > Bootstrap purpose only, you should replace it with your real server config
    
    ```nginx
    server 127.0.0.1:9527 weight=1 max_fails=1 fail_timeout=1s;
    ```

3. Run

