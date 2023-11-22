# Admin AWS 시스템 구성

> Type: `System`

# TOC

1. [Abstract](#1-abstract)
2. [AWS](#2-aws)
    - [EC2](#2-1-ec2)
    - [ELB](#2-2-elb)
    - [Route53](#2-3-route53)
    - [RDS](#2-4-rds)
3. [Server](#3-server)
    - [Nginx](#3-1-nginx)
    - [Uvicorn](#3-2-uvicorn)

# 1.Abstract

- 어드민-크롤링 시스템 환경

# 2. AWS

## 2-1. EC2

1. EC2 Specification
    - instance type: t2.micro
    - OS: Amazon Linux 2023

2. caution
    - t2.micro CPU BUST(높은 CPU 요구 작업 실행 시 CPU 버스트 발생으로 추가적인 요금이 발생할 우려가 있음)

## 2-2. ELB

- DNS: folder-admin-2086036178.ap-northeast-2.elb.amazonaws.com
- Region: ap-northeast-2 (서울)
- Target: folder-scrap-group

## 2-3. Route53

- hosting : foldering.io
- record : api.admin.foldering.io
- value: dualstack.folder-admin-2086036178.ap-northeast-2.elb.amazonaws.com.

## 2-4. RDS

version - MYSQL 8.0.34

1. InnoDB 설정:
    - innodb_buffer_pool_size: 268435456 (InnoDB 버퍼 풀 크기)

2. Connection settings:
    - max_connections: 60 (동시에 허용되는 최대 연결 수)
    - connect_timeout: 10 (연결 타임아웃 초)
    - wait_timeout: 28800 (비활성 연결 타임아웃 초)

# 3. Server

## 3-1. nginx

version - nginx/1.24.0

1. General settings:
    - worker_processes auto: 서버의 CPU 수에 맞춰 워커 프로세스의 수를 자동으로 설정
    - error logging: 에러 로그를 /var/log/nginx/error.log에 'notice' 레벨로 기록
    - access_log: 액세스 로그를 /var/log/nginx/access.log에 'main' 포맷으로 기록
    - process id: 프로세스 ID를 /run/nginx.pid에 저장
    - module: /usr/share/nginx/modules/*.conf
    - max worker_connections: 각 워커 프로세스가 처리할 수 있는 최대 연결 수 1024로 설정

2. HTTP settings:
    - keepalive_timeout: 연결 유지 시간을 65초 설정
    - types_hash_max_size: MIME 타입 해시 테이블의 최대 크기 4096으로 설정
    - MIME module: MIME 타입 설정을 위해 /etc/nginx/mime.types를 포함

3. Upstream and server settings:
    - Api server: 127.0.0.1:3301
    - Root directory: /usr/share/nginx/html
    - Listen port: 80

## 3-2. uvicorn

version - uvicorn/0.23.2

1. General settings:
    - project directory: /src- venv: /src/venv/bin/python3
    - host: 127.0.0.1
    - port: 3301
    - workers: 1
    