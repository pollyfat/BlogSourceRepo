---
title: Gitlab-runner-wechat-miniprogram
date: 2019-04-11 17:43:48
tags:
---
- [安装 Gitlab-runner ](https://docs.gitlab.com/runner/install/osx.html)

- 获取 token

  在项目页面中通过 settings -> CI/CD -> Runners -> Set up a specific Runner manually 中获取 token
  ![image](/uploads/2c659316686e8d652c566d3aae36be85/image.png)

- 注册 gitlab-runner
  ```
  # 运行 `gitlab-runner register`, 输入以下内容

  Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/):
  # https://gitlab.com/
  Please enter the gitlab-ci token for this runner:
  # 填入上述token
  Please enter the gitlab-ci description for this runner:
  # 可输入随意值
  Please enter the gitlab-ci tags for this runner (comma separated):
  # 可输入随意值, 在.gitlab-ci.yml中会用到, 此处示例为
  # mac
  Please enter the executor: docker-ssh, parallels, shell, virtualbox, docker+machine, docker, ssh, docker-ssh+machine,
   kubernetes:
  # shell
  Registering runner... succeeded
   ```
- 编写 .gitlab-ci.yml, 以下为示例
  ```
	image: node:alpine

	before_script:
	    - export APP_ENV=testing
	    - yarn config set registry 'https://registry.npm.taobao.org'
	stages:
	    - build
	    - deploy

	variables:
	    NPM_CONFIG_CACHE: "/cache/npm"
	    YARN_CACHE_FOLDER: "/cache/yarn"
	    DOCKER_DRIVER: overlay2
	build-package:
	    stage: build
	    dependencies: []
	    cache:
	      key: "$CI_COMMIT_REF_NAME"
	      policy: pull
	      paths:
	        - node_modules
	    script:
        #        本地构建
        #        - PORT=`cat ~/Library/Application\ Support/微信web开发者工具/Default/.ide`
        #        - curl http://127.0.0.1:$PORT/buildnpm?projectpath=$PWD\&compiletype=miniprogram
        #        远程构建
	        - if [ ! -d "node_modules" ]; then
	        - yarn install --cache-folder /cache/yarn
	        - fi
	        - yarn build
	        - cp ./project.config.json ./dist/project.config.json
	    artifacts:
	        name: "wxpkg-dlkhgl-$CI_COMMIT_TAG"
	        untracked: false
	        paths:
	            - dist
	    only:
	        - tags
            # 只在推送tag时触发runner

	    tags:
	        - docker
            # 该tag如为regist时使用的tag, 则在本地执行scirpt中的内容
	deploy:
	    stage: deploy
	    dependencies:
	        - build-package
	    variables:
	        GIT_STRATEGY: none
	    before_script: []
	    script:
	        # 获取HTTP服务的端口, 该端口是不固定的
	        - PORT=`cat ~/Library/Application\ Support/微信web开发者工具/Default/.ide`
	        # 调用上传的API
	        - curl http://127.0.0.1:$PORT/upload\?projectpath\=$PWD/dist\&version\=$CI_COMMIT_TAG\&desc\=audo-deploy
	        - echo success
	    only:
	        - tags
	    tags:
	        - mac
  ```

- 如开发工具中未开启服务端口, 需在工具中打开
![image](/uploads/6073caecada64c62b4dbe59dc0f4d380/image.png)

- 查看pipline

  构建时可在pipline中查看构建日志
![image](/uploads/d42f4045dafd09eb2013fd22a244f59b/image.png)
