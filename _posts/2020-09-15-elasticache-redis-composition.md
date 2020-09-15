---
layout: post
title: Elasticache Redis 구성 (클러스트 구성 비활성화)
tags: [AWS, aws, elasticache, redis]
---

## 1. VPC 구성
    1.1 vpc 1개 생성
    1.2 subnet 3개 생성 -> multi AZ 구성(route 테이블 같게 구성, route table에 Internet Gateway attach)
    cf) internet gateway를 붙여서 외부에서 EC2와 ssh 통신 가능하게 해줌

## 2. elasticache 생성
    2.1 elasticache에서 VPC에서 만든 subnet으로 subnet group 구성
    2.2 subnet group을 이용하여 `1.1 VPC 내부`에 elastic-redis 생성

## 3. EC2 생성
    3.1 redis와 같은 VPC, subnet들 중 하나 내에 생성

## 4. **`1.1 VPC` 의 SG inbound 규칙에  6379 추가  or All traffic, source에 EC2가 쓰고 있는 SG 할당**
![4-inbound-rules](/assets/img/2020-09-15/4-inbound-rules.png)   
- `source에 EC2가 쓰고 있는 SG 할당!!!!!`
- `sg-0bd540c058d18a0d8 (launch-wizard-4)` 는 ec2가 사용하고있는 sg
  
## 5. EC2 내에 gcc 설치
```bash
sudo yum install -y gcc
```

## 6. redis-cli 설치 
```bash
wget http://download.redis.io/redis-stable.tar.gz && tar xvzf redis-stable.tar.gz && cd redis-stable && make

sudo cp src/redis-cli /usr/bin/
```

## 7. redis-cli 접속 및 확인
```bash
./redis-cli -c -h "primary endpoint" -p 6379
```

## 8. nvm 설치
```bash
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
nvm install 12.18.1
```
## 9. node.js redis client 작성
    9.1 primary endpoint를 이용해서 접속
    9.2 클러스터 구성 활성화 시, npm "redis" 모듈 사용불가("ioredis" 사용해야 한다)

# 10. 완성된 test-redis 구성도
![10-complete-architecture](/assets/img/2020-09-15/10-complete-architecture.png)