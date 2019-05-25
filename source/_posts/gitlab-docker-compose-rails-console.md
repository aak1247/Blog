title: docker 安装的 gitlab 修改配置/重置密码等

category: 配置

tags: 
 - gitlab
 - 配置

date: 2019/05/21
---

简单记录一下

<!--more-->

## rails console 进入方法

```shell
docker exec -it GITLAB_CONTAINER_ID /bin/bash
su - git
cd gitlab
bundle exec rails c production
```

或
```shell
docker-compose exec --user git gitlab bundle exec rails c production
# docker exec --user git -it GITLAB_CONTAINER_ID bundle exec rails c production
```


## 重置密码

先进入rails console，然后
```ruby on rails
user = User.where(id: 1).first
user.password = 'NEW_PASS'
user.password_confirmation = 'NEW_PASS'
user.save
exit
```

## 其他配置（如备份等） 

```shell
docker-compose exec --user git gitlab bundle exec rake RAKE_TASK
# eg: docker-compose exec --user git gitlab bundle exec rake gitlab:backup:create RAILS_ENV=production
```


# 参考

[docker-gitLab](https://github.com/sameersbn/docker-gitlab)
[](https://docs.gitlab.com/ee/raketasks/README.html#rake-tasks)
