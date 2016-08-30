##git  for windows
###一、软件准备
- git客户端 `Git-2.7.2-32-bit_setup.1457942412.exe`
- window 下搭建ssh服务 `Copssh_4.1.0_Installer.exe`
- git windows gui `TortoiseGit_1.8.14.0_32bit.1436149283.msi`

###二、软件安装

1.安装git,双击下载完毕的git客户端

![run git](http://192.168.23.106/doc_images/run_git.png "run git")

安装目录可以自己选择也可以默认。

![selectDirectory](http://192.168.23.106/doc_images/gitForWindow/selectDirectory.png "select Directory")

选择组件

![selectComponents](http://192.168.23.106/doc_images/gitForWindow/selectComponents.png "select Components")

是否创建开始菜单

![selectStartMenu](http://192.168.23.106/doc_images/gitForWindow/selectStartMenu.png "create select Start Menu")

选择环境(默认只有GIT Bash)

![selectEnvironment](http://192.168.23.106/doc_images/gitForWindow/selectEnvironment.png "select Environment")

此处选择默认选项

![choseSsh](http://192.168.23.106/doc_images/gitForWindow/choseSsh.png "chose SSH")

下面选择第三个，不去转换成unix的代码风格

![cofingureChose](http://192.168.23.106/doc_images/gitForWindow/cofingureChose.png "Configuring the line ending conversions")

![useGitBash](http://192.168.23.106/doc_images/gitForWindow/useGitBash.png "chose use git bash")

点击Next,安装完成，如下图：

![instarllComplete](http://192.168.23.106/doc_images/gitForWindow/instarllComplete.png "instarll Complete")

`git windows clients` 到此时的安装完成。

###二、安装SSH及配置用户

![runCopssh](http://192.168.23.106/doc_images/Copssh/runCopssh.png "run Copssh")

![agreeLicense](http://192.168.23.106/doc_images/Copssh/agreeLicense.png "agree License")

####`建议：（安装在根目录下，避免路径中有空格，造成不必要的麻烦）`

下面是设置SSH的帐号密码

![serviceAccount](http://192.168.23.106/doc_images/Copssh/serviceAccount.png "service Account")

![instarllCopssh](http://192.168.23.106/doc_images/Copssh/instarllCopssh.png "instarll Copssh")

到此时Copssh安装成功，安装完成后还有两个操作：

		1、将 Git安装目录\mingw32\libexec\git-core文件夹下的git-upload-pack.exe、git.exe、
		   git-receive-pack.exe和git-upload-archive.exe这4个文件复制到SSH的安装路径C:\Program Files (x86)\ICW\bin下。
		2、将 Git安装目录\mingw32\bin\libiconv-2.dll复制到C:\Program Files (x86)\ICW\bin下。
###三、连接测试
1.打开cmd, cd copssh安装目录\ICW\bin,输入ssh.exe git@192.168.23.106

![ssh](http://192.168.23.106/doc_images/connectTest/ssh.png "ssh test")

输入yes

![sshGit](http://192.168.23.106/doc_images/connectTest/sshGit.png "ssh Git")

出现以上界面，代表连接成功。此时你已经通过SSH协议连接上了Git。
###四、安装TortoiseGit

![runTortoiseGit](http://192.168.23.106/doc_images/TortoiseGit/runTortoiseGit.png "run TortoiseGit")

![agreeLicense](http://192.168.23.106/doc_images/TortoiseGit/agreeLicense.png "agree License")

![choseSshClient](http://192.168.23.106/doc_images/TortoiseGit/choseSshClient.png "chose SSh Client")

安装目录可更改

![choseDirectory](http://192.168.23.106/doc_images/TortoiseGit/choseDirectory.png "chose Directory")

![instarll](http://192.168.23.106/doc_images/TortoiseGit/instarll.png "instarll TortoiseGit")

![instarllFinish](http://192.168.23.106/doc_images/TortoiseGit/instarllFinish.png "instarll Finish")

![finishTest](http://192.168.23.106/doc_images/TortoiseGit/finishTest.png "Finish Test")

Git下运行git bash输入ssh-keygen.exe.不用设密码直接enter

![createSshkey](http://192.168.23.106/doc_images/TortoiseGit/createSshkey.png "create SSH key")

![sshSet](http://192.168.23.106/doc_images/TortoiseGit/sshSet.png "ssh Set")

生成公匙C:\Users\Administrator\\.ssh下id_rsa的pub文件。


###五、配置TortoiseGit

- 首先设置TortoiseGit>Settings>Network中SSH client的值为git安装的目录下.....\git\bin\ssh.exe.   
我的是在”D:\git\Git\bin\ssh.exe”

![sshSet](http://192.168.23.106/doc_images/setTortoiseGit/setSshPath.png "set Ssh Path")

- 配置TortoiseGit>Settings>Git设置name和email（建议格式如下图）

![setEmail](http://192.168.23.106/doc_images/setTortoiseGit/setEmail.png "set Email")

- 桌面上点击鼠标右键选择git clone

设置url为git@192.168.23.2:/home/git/test_proj.git（需要在 [git server](http://192.168.23.106:8000) 注册账号，并新建仓库）

Directory:自己想将文件放到的位置

![setEntrepot](http://192.168.23.106/doc_images/setTortoiseGit/setEntrepot.png "set Entrepot")

- 密码：password

![inputpwd](http://192.168.23.106/doc_images/setTortoiseGit/inputpwd.png "input pwd")

![success](http://192.168.23.106/doc_images/setTortoiseGit/success.png "success")

- 安装完成。

###六、git server 的使用

>1.打开浏览器，输入[http://192.168.23.106:8000](http://192.168.23.106:8000),点击注册


![register](http://192.168.23.106/doc_images/gitServer/register.png "login")

>2.注册完成后，登录

![sign_in](http://192.168.23.106/doc_images/gitServer/sign_in.png "sign in")


>3.创建新仓库

![createWarehouse](http://192.168.23.106/doc_images/gitServer/createWarehouse.png "create Warehouse")

>4.创建完成后，进入自己仓库，先添加ssh密匙（前面生成的公匙）

![addSshkey](http://192.168.23.106/doc_images/gitServer/addSshkey.png "add SSH key")

>5.与之关联。














