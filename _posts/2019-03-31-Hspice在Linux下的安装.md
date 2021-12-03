---
title: "Install Hspice in Linux OS"
date: 2019-03-31
categories:
  - blog
tags:
  - Hspice
  - Install
toc: true
--- 
 
# 需要的安装文件
## 1.安装

找到文件夹中的installer，里面的文件是SynopsysInstaller_v3.2.run,使用命令
`chmod +x SynopsysInstaller_v3.2.run`
从而改变文件的权限，之后则可以正常的安装，采用命令

```
./SynopsysInstaller_v3.2.run
```
![解压得到的文件](https://img-blog.csdnimg.cn/20181110213222131.png)

这样子就可以安装好installer。

**接下来安装HSPICE**,切换到之前解压到的目录，使用命令

```
./setup.sh
```
结果发生错误，说解释器错误：**无相关文件或目录**

这时，查找了一些方法，网上几乎说的全是由于文件在Windows下编辑过，对文件的行结束进行了修改，因此要用到正则表达式来删掉多余的格式，我试了没有什么用，我猜测可能是csh的问题，因此重新安装了`apt-get install csh`
解决，并用下列命令运行shell脚本

```
./setup.sh -install_as_root
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181110214027958.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p3cHJvbWlzZQ==,size_16,color_FFFFFF,t_70)

之后一直确定就可以进行安装了。site administrator填Ubuntu（我用的这个，其他的类似吧）的用户名

  **现在来安装SCL** ，安装方法与安装Hspice类似，安装好后，在其安装目录中，我的是`synopsys/11.9/linux/bin`使用`./lmgrd` `./lmhostid`等命令，如果提示没有此命令，则表示缺少相应的库，则要使用命令`sudo apt-get install lsb-core`安装库，之后再执行命令即可正常运行。
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/2018111111180260.png)
  到现在，安装就大致完成了，当然也可以安装explorer查看波形图，我不需要看图就不进行安装了。
  
## 2.破解

Windows下双击scl_keygen.exe，填入主机号和mac地址（Ubuntu系统的，不是Windows），其余的不用修改，点击generate，即可在scl_keygen.exe目录下看到Synopsys.dat文件，之后，将此文件复制到Linux下hspice的安装目录，这样既可破解成功。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181125222624889.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p3cHJvbWlzZQ==,size_16,color_FFFFFF,t_70)

## 3.修改环境变量

 打开根目录/etc的文件bash.bashrc，在文件的最后添加如下内容：
 

```
export SNPSLMD_LICENSE_FILE=27000@ubuntu
export LM_LICENSE_FILE=/usr/synopsys/L-2016.06-SP1/Synopsys.dat

export PATH="/usr/synopsys/L-2016.06-SP1/hspice/bin:"$PATH
export PATH="/usr/synopsys/11.9/linux/bin:"$PATH

alias hsplmd='/usr/synopsys/11.9/linux/bin/lmgrd -c /usr/synopsys/L-2016.06-SP1/Synopsys.dat'
```
上述指明hspice，scl的路径，并定义lmgrd的别名hsplmd，用于每次的服务器破解。

## 4.验证破解

**(1)方法1**

输入指令hsplmd，若结果如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181125231226809.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p3cHJvbWlzZQ==,size_16,color_FFFFFF,t_70)

 则表明破解成功
 
 **（2）方法2**
 
 输入指令hspice -I
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2018112523145789.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p3cHJvbWlzZQ==,size_16,color_FFFFFF,t_70)
 
 出现上图表明也是破解成功，若没有成功，则看下主机名和端口号是否设置正确。
 
 ## 5.跑例子测试
 
 找到安装目录，进行测试，选择bjt文件夹中的mextram_dc.sp，可得如下结果![](https://img-blog.csdnimg.cn/2018112523235083.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p3cHJvbWlzZQ==,size_16,color_FFFFFF,t_70)
 
 ![常用指令](https://img-blog.csdnimg.cn/20181125232602433.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p3cHJvbWlzZQ==,size_16,color_FFFFFF,t_70)
