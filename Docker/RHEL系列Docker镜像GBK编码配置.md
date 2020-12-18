# RHEL系列Docker镜像中GBK字符编码配置

## 一 背景

在某些线上系统环境中，字符编码使用的是GBK。在向容器迁移时，如果不正确配置，那么显示在屏幕上的将是乱码，为此我们需要针对这一问题的Docker镜像进行正确配置才能避免该情况的出现。

## 二 配置步骤

### 2.1 在Docker中加入以下内容：

```Dockerfile
RUN yum install -y kde-l10n-Chinese \
                   glibc-common && \
    localedef -c -f GBK -i zh_CN zh_CN.GBK && \
    echo 'LANG="zh_CN.GBK"' > /etc/locale.conf && \
    yum clean all

ENV LANG="zh_CN.GBK" \
    LC_ALL="zh_CN.GBK"
```

### 2.2 测试验证

完整Dockerfile：

```dockerfile
FROM scratch

LABEL author="ivan du" email="mrivandu@hotmail.com"

ADD centos-7.8.2003-x86_64-docker.tar.xz /

RUN yum install -y kde-l10n-Chinese \
                   glibc-common && \
    localedef -c -f GBK -i zh_CN zh_CN.GBK && \
    echo 'LANG="zh_CN.GBK"' > /etc/locale.conf && \
    yum clean all

ENV LANG="zh_CN.GBK" \
    LC_ALL="zh_CN.GBK"

CMD [ "/bin/bash" ]
```

构建镜像并测试：

```bash
docker run -it --rm test:1.0
[root@fb699d83bdac /]# locale
LANG=zh_CN.GBK
LC_CTYPE="zh_CN.GBK"
LC_NUMERIC="zh_CN.GBK"
LC_TIME="zh_CN.GBK"
LC_COLLATE="zh_CN.GBK"
LC_MONETARY="zh_CN.GBK"
LC_MESSAGES="zh_CN.GBK"
LC_PAPER="zh_CN.GBK"
LC_NAME="zh_CN.GBK"
LC_ADDRESS="zh_CN.GBK"
LC_TELEPHONE="zh_CN.GBK"
LC_MEASUREMENT="zh_CN.GBK"
LC_IDENTIFICATION="zh_CN.GBK"
LC_ALL=zh_CN.GBK
```

## 三 总结

3.1 其他中文编码操作方式与上文一致。
3.2 以上操作适用于RHEL系列操作系统的字符集编码调整。