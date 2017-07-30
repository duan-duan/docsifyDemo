以前用github的ssh key，后来工作原因多了一个gitlab的账号，在绑定gitlab的ssh key时，发现将github的ssh key覆盖了。怎么同时绑定github和gitlab的ssh key，并不产生冲突呢？
今天找到了个小技巧，在.ssh目录下新建一个config文件配置一下，就能解决gitlab与github的ssh key的冲突



生成并添加第一个ssh key
```
cd ~/.ssh
ssh  ssh-keygen -t ras -C "wangruirui@le.com"
```
这时可以一路回车，不输入任何字符，将自动生成id_rsa和id_rsa.pub文件。
生成并添加第二个ssh key
```
$ ssh-keygen -t rsa -C "972065033@qq.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/wangruirui/.ssh/id_rsa): id_rsa_github
Enter passphrase (empty for no passphrase):

```
注意，这时不能一路回车，否则邮箱将覆盖上一次生成的ssh key，给这个文件起一个名字， 比如叫 id_rsa_github, 所以相应的也会生成一个 id_rsa_github.pub 文件。

此时查看.ssh下的目录文件，发现多了id_rsa_github和id_rsa_github.pub两个文件。

添加私钥
$ ssh-add ~/.ssh/id_rsa
$ ssh-add ~/.ssh/id_rsa_github
修改配置文件
在 ~/.ssh 目录下新建一个config文件
```
touch config
```
# gitlab
Host gitlab.com
    HostName gitlab.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa
# github
Host github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_github
```  
给github/gitlab上添加ssh key

测试
```
$ ssh -T git@github.com
```
如果输出Hi xuyuan923! You've successfully authenticated, but GitHub does not provide shell access.，说明成功的连上github了。

设置提交用户名和邮箱

1.取消global
git config --global --unset user.name
git config --global --unset user.email

2.设置每个项目repo的自己的user.email  这样就行了，只不过不能统一了
git config  user.email "xxxx@xx.com"
git config  user.name "xxx"
