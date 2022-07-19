# 1、浏览器访问网站提示缓存目录写入权限不足、**目录创建失败、**文件写入失败：

```
修改主机及nginx容器 www 目录权限： chmod -R 777 /nginx/data/www
```


# 2、访问数据库出错：
为php容器安装拓展（如果之前启用了gd，需要先在php.ini文件中注释掉extension=gd.so并重启容器后再执行以下步骤）：
```
docker exec -it myphp-fpm /bin/bash

cd /usr/local/bin

docker-php-ext-install pdo_mysql
```

执行完毕退出容器，并重启php容器。

# 3、缺少扩展gd库：
```
# 进入php容器
docker exec -it myphp-fpm /bin/bash

# 更新软件源
apt update

# 安装库
apt install -y libwebp-dev libjpeg-dev libpng-dev libfreetype6-dev

# 解压源码
docker-php-source extract

# 进入gd源码文件夹
cd /usr/src/php/ext/gd

# 准备编译
docker-php-ext-configure gd --with-webp-dir=/usr/include/webp --with-jpeg-dir=/usr/include --with-png-dir=/usr/include --with-freetype-dir=/usr/include/freetype2

# 编译安装
docker-php-ext-install gd

# 检查扩展是否安装成功
php -m | grep gd

# 退出php容器
exit

# 重启php容器
```


# 4、网站图形验证码显示不出来：
报错：Fatal error: Call to undefined function core\extend\code\imagettftext()
解决方案：
```
docker-php-ext-configure gd --enable-gd-native-ttf --with-freetype-dir=/usr/include/freetype2 --with-png-dir=/usr/include

# 重新编译：
docker-php-ext-install gd
```




报错：configure: error: freetype-config not found.
解决方案：
```
apt install libfreetype6-dev
 
docker-php-ext-configure gd --enable-gd-native-ttf --with-freetype-dir=/usr/include/freetype2 --with-png-dir=/usr/include
 
# 重新编译：
docker-php-ext-install gd

```

