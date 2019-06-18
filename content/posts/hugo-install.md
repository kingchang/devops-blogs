---
title: "Hugo 安裝撞牆經驗"
date: 2019-06-17T09:06:44+08:00
#draft: true
---

* Install Ubuntu 18.04
* Install Dependency Software

```bash
cd /opt/
mkdir golang
cd golang
wget --no-check-certificate https://studygolang.com/dl/golang/go1.12.5.linux-amd64.tar.gz
tar -zxvf go1.12.5.linux-amd64.tar.gz -C /usr/lib
apt install git python3-pip wget software-properties-common apt-transport-https 
pip3 install Pygments
export VER="0.55.6"
wget https://github.com/gohugoio/hugo/releases/download/v${VER}/hugo_${VER}_Linux-64bit.deb
dpkg -i hugo_${VER}_Linux-64bit.deb
apt update
apt list --upgradable
apt upgrade
wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | sudo apt-key add -
add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"
apt update
apt install code
```

* Edit ~/.profile on latest line append

```bash
export PATH=$PATH:/usr/lib/go/bin
```

* Create blog directory

```bash
hugo new site devops_blog -f yaml
cd devops_blog
git init
git remote add origin git@github.com:<your-github-account>/devops_blog.git
```

* Install new Theme

 | [Hugo Themes repository](https://github.com/spf13/hugoThemes)

On the blog directory 

```shell
git submodule add https://github.com/spf13/hyde themes/hyde
git submodule add https://github.com/zwbetz-gh/vanilla-bootstrap-hugo-theme.git themes/vanilla-bootstrap-hugo-theme
cp themes/vanilla-bootstrap-hugo-theme/exampleSite/config.yaml .

```

* Create New Blog Document , Hugo new document  default on the content dirctory

```bash
hugo new posts/first-post.md
hugo new _index.md
```

* Edit _index.md 

```shell
---
title : Home
date : ..........
---
```

* Test Hugo 

```bash
hugo -D server
```

* Open the Browser 

```bash
firefox "http://localhost:1313/"
```
![hugo-demo](/hugo-demo.png)

* Publish to Github

```bash
git submodule add git@github.com:kingchang/kingchange.github.io.git public

hugo 
```

* into public directory and publish to Github


