rpm

一、rpm打包工具安装

```
yum install rpm-build rpmdevtools
```

二、工具的使用方法

1. 创建文件夹 

```
#rpmdev-setuptree生成rpmbuild的工作目录
[root@localhost ~]# rpmdev-setuptree
[root@localhost ~]# cd /root
[root@localhost ~]# ls
anaconda-ks.cfg  rpmbuild
[root@localhost ~]#
[root@localhost ~]# cd rpmbuild/
[root@localhost rpmbuild]# ls
BUILD  BUILDROOT  RPMS  SOURCES  SPECS  SRPMS
[root@localhost rpmbuild]#
```

| Directory | Usage                                                        |
| --------- | ------------------------------------------------------------ |
| BUILD     | 源代码解压以后放的位置，只需提供BUILD目录，具体里面放什么，不用我们管，所以真正的制作车间是BUILD目录 |
| BUILDROOT | 假根，使用install临时安装到这个目录，把这个目录当作根来用的，所以在这个目录下的目录文件，才是真正的目录文件。当打包完成后，在清理阶段，这个目录将被删除 |
| RPMS      | 制作完成后的rpm包存放目录，为特定平台指定子目录（i386,i686,ppc） |
| SOURCES   | 收集的源文件，源材料，补丁文件等存放位置                     |
| SPECE     | 存放spec文件，作为制作rpm包的领岗文件，以 rpm名.spec         |
| SRPMS     | src格式的rpm包位置 ，既然是src格式的包，就没有平台的概念了   |

2. 创建SPEC文件

```
[root@localhost rpmbuild]# ls
BUILD  BUILDROOT  RPMS  SOURCES  SPECS  SRPMS
[root@localhost rpmbuild]# rpmdev-newspec -o test.1.1.spec
test.1.1.spec created; type minimal, rpm version >= 4.11.
[root@localhost rpmbuild]# ls
BUILD  BUILDROOT  RPMS  SOURCES  SPECS  SRPMS  test.1.1.spec
[root@localhost rpmbuild]# mv test.1.1.spec SPECS/
[root@localhost rpmbuild]# cd ./SPECS/
[root@localhost SPECS]# ls
dma100-1.spec  test.1.1.spec
[root@localhost SPECS]#
```

3. SPEC文件解析

```
Name:           test                                     #软件包名称
Version:        1.0                                      #版本号
Release:        1%{?dist}                                #release号，对应下面的changelog
Summary:        test.spec                                #简要描述信息

License:        
URL:            
Source0:    
    
BuildRequires:  
Requires:       

%description

%prep
%setup -q

%build
%configure
make %{?_smp_mflags}

%install
rm -rf $RPM_BUILD_ROOT
%make_install

%files
%doc

%changelog
```

4. 打包
   在SPEC目录下，执行 rpmbuild -xx xxx.spec

```
#rpmbuild
-bp 只作准备 （解压与打补丁）
-bc 准备并编译
-bi 编译并安装
-bl 检验文件是否齐全
-ba 编译后做成*.rpm和src.rpm
-bb 编译后做成*.rpm
-bs 只做成*.src.rpm
```

5.查询rpm 版本信息

```
rpm -qi 包名

rpm -qa | grep 包名
```





