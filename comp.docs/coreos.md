---
html:
  embed_local_images: true
  embed_svg: true
  offline: false
  toc: CoreOS Practice Guide

print_background: false
---
## CoreOS Practice Guide
CoreOS实践指南

日期：2014-12-29
摘要：CoreOS是一个采用了高度精简的系统内核及外围定制的操作系统。ThoughtWorks的软件工程师林帆将带来“漫步云端：CoreOS实践指南”系列文章，介绍CoreOS的精华和推荐的实践方法。

【编者按】Docker和CoreOS都是硅谷创业孵化器的优秀“毕业生”，据说两家老板的私交很好，Docker做容器引擎，CoreOS做容器管理，合作得非常愉快，只是随着Rocket的发布逐步“分道扬镳”。虽然Docker和CoreOS都在求“简”，但是Docker的“简”是力求用户能达到最简便地使用，CoreOS的“简”是追求极致的轻量化，究竟哪个将是Container技术的未来，其实也很难说。今天开始，来自ThoughtWorks的软件工程师林帆将带来“漫步云端：CoreOS实践指南”系列文章，带大家了解CoreOS的精华和推荐的实践方法。

作者简介：林帆，生在80后尾巴的IT攻城狮，ThoughtWorks成都办公室CloudOps小组成员，平时喜欢在业余时间研究DevOps相关的应用，目前在备考AWS认证和推广Docker相关技术。


##### Table of Contents
* [CoreOS实践指南（一）：CoreOS俯瞰](#coreos01)
* [CoreOS实践指南（二）：架设CoreOS集群](#coreos02)
* [CoreOS实践指南（三）：系统服务管家Systemd](#coreos03)
* [CoreOS实践指南（四）：集群的指挥所Fleet](#coreos04)
* [CoreOS实践指南（五）：分布式数据存储Etcd(上)](#coreos05)
* [CoreOS实践指南（六）：分布式数据存储Etcd(下)](#coreos06)
* [CoreOS实践指南（七）：Docker容器管理服务](#coreos07)
* [CoreOS实践指南（八）：Unit文件详解](#coreos08)
* [CoreOS实践指南（九）：在CoreOS上的应用服务实践（上）](#coreos09)
* [CoreOS实践指南（十）：在CoreOS上的应用服务实践（下）](#coreos10)

---
@import "coreos01.md"
@import "coreos02.md"
