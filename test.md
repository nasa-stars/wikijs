---
title: test
description: 
published: true
date: 2024-10-19T13:29:33.060Z
tags: 
editor: markdown
dateCreated: 2024-10-16T03:14:05.882Z
---

https://wiki.zhoumx.net/project-3/doc-65/
一些部署的项目配置
1. 修复 SSH 密钥的权限
私钥文件的权限必须非常严格，其他用户不应该有读、写或执行该文件的权限。你需要将私钥文件的权限改为 600：
2. 设置正确的 .ssh 目录
错误中提到的 /nonexistent/.ssh 目录表明当前用户的 SSH 配置不正确。你需要确保用户的主目录中存在 .ssh 目录，并且有正确的权限。执行以下步骤：
/nonexistent/.ssh 说明当前用户的主目录可能设置不正确。你可以通过以下命令检查当前用户的主目录：

双向同步 
极客教程 https://geek-docs.com/