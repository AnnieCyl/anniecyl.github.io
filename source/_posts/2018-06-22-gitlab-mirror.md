title: gitlab 镜像工程
date: 2018-06-22 20:58:46
tags:
- git
---

有时候需要在两个 gitlab 服务器之间做镜像文件，这样，在一个服务器上的更改可以同步到另一个服务器上。
假设有两个服务器，A 服务器和 B 服务器：
- pull 镜像：在 B 上开发的工程，在 A 上做此工程的 pull 镜像，每次 B 的这个工程有更新，A 会自动 pull 过来。
  步骤：
- push 镜像：在 A 上开发的工程，做 push 镜像到 B 上，每次 A 的这个工程有更新，A 会自动 push 到 B 上。操作步骤如下：
  - 在 B 上创建一个用户，例如 autopush
  - 如果 B 上还没有对应工程，创建一个对应工程，将 autopush 用户添加到该工程中。然后复制该工程的 http 地址。
  - 在 A 上进入工程的 Repository 配置 push 镜像    
    ![1](/img/2018-06-22-gitlab-mirror/1.png)
  - 将 B 上的工程地址和 autopush 用户组合起来，例如，http://autopush:password@github.com/annie/demo.git
    ![2](/img/2018-06-22-gitlab-mirror/2.png)
  - 如果 B 上这个工程是在某个 group 下，则这个 group 的成员都有对此工程的访问权限，此时需要在此工程的 Member 下把 group 中的每个成员添加为 reporter，因为 B 上这个工程为镜像工程，不能允许有人在镜像工程进行 push 操作。
