## Setup Database

`docker run -d --name db -p 3306:3306 -v D:/Database:/var/lib/mysql --network="host" -e MYSQL_ROOT_PASSWORD=siddhant mysql`

## Create PHP 8.0 Alpine latest image
`docker build  -t php80-alpine:latest -f Dockerfile_php80_alpine .
`

## Create PHP 8.1 Alpine latest image
`docker build --no-cache -t php81-alpine:latest -f Dockerfile_php81_alpine .
`

## Create NGINX latest image
`docker build --no-cache -t nginx-local:latest -f Dockerfile_nginx .
`


`docker build --no-cache -t node/local:latest -f Dockerfile_node .
`

### Copy Files from inside the docker container on to windows
`docker cp 814346de4ac7:/app/node_modules ./"Web Frontend"/`



## Docker Compose Sample

    services:
        db:
            container_name: db
            image: mysql
            environment:
                MYSQL_ROOT_PASSWORD: siddhant
            ports:
                - 3306:3306
            volumes:
                - ./db-data:/var/lib/mysql
        
        testpro:
            container_name: testing
            build:
                context: ./TestDocker
                dockerfile: build/Dockerfile.local
            ports:
                - 9001:80
            volumes:
                - ./TestDocker:/var/www
            depends_on:
                - db
            
        aggregation-worker:
            image: oakv2-aggregation
            volumes:
                - ./Aggregation:/var/www/html
            command: php artisan queue:work --tries=3
            depends_on:
                - aggregation
            networks:
                - oak-network
            deploy:
                resources:
                    limits:
                        cpus: '0.5'
                        memory: '256m'