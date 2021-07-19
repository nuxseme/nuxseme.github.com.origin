---
title: travis-ci-ssh
date: 2021-07-19 10:41:19
categories: ci
---



#### 安装Travis 

```bash
brew install ruby #如果遇到找不到依赖包 需要更新下brew [update|upgrade]
brew  install  gem
sudo gem install travis
```

#### 登录Travis

```bash
travis  login  --pro #加上--pro  travis login --auto会有报错
```

#### 生成ssh加密文件

1 在本地生成公私钥，公钥部署在应用服务器，私钥放在项目构建目录下

2 travis 对私钥加密，加密解密串存储在travis服务器，构建运行过程中解密私钥用于远程连接

```bash
travis encrypt-file deploy_rsa --add
```

#### .travis.yml

```bash
language: generic #一定要指定语言，不然会报gemfile错误
notifications:
  email:
  - xxx@gmail.com
  on_success: always
  on_failure: always
before_install:
   - echo -e "Host ${SERVER_HOST}\n\tStrictHostKeyChecking no\n" >> ~/.ssh/config #取消认证确认交互逻辑 addons:ssh-known-hosts 测试无效
   - openssl aes-256-cbc -K $encrypted_a6b36f5c33c8_key -iv $encrypted_a6b36f5c33c8_iv
     -in deploy_key.enc -out ~/.ssh/id_rsa -d #此处如果有\需要移除
   - chmod 600 ~/.ssh/id_rsa #如果命名为其他比如delpoy_rsa 有可能在ssh 阶段不会自动尝试该秘钥

after_success:
  - ssh ${USERNAME}@${SERVER_HOST} < deploy.sh #可以直接运行指令 也可以部署命令写入文件直接读取，通常就是进入项目目录Git pull
```

