title: Hexo 环境迁移
date: 2016-01-16 11:44:36
tags: "hexo"
---

# _config.yml

```markdown
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git
  repository: git@github.com:fanhengyuan/fanhengyuan.github.io.git
  branch: master
```

# .deploy_git/.git/config
```markdown
[branch "master"]
	remote = git@github.com:fanhengyuan/fanhengyuan.github.io.git
	merge = refs/heads/master
```