
# FDW

## PostgresFDW

[postgres_oracle_fdw github reference](https://github.com/laurenz/oracle_fdw)

## 使用选项

### 1、表映射

映射表时，可以映射所有字段也可以仅仅映射部分字段。

```sql
-- 在postgres中创建映射表test2，对应于oracle中表TEST2（这里注意使用表格名称的大写）
 CREATE FOREIGN TABLE test2 (
          text      character varying(200),
          text2      character varying(200),
          text3      character varying(200)
       ) SERVER appname_wrapper OPTIONS (table 'TEST2') ;

-- 仅仅映射某些字段，基于任意Oracle查询定义外部表。在这种情况下，请勿设置架构选项。
 CREATE FOREIGN TABLE test2 (
          text      character varying(200)
       ) SERVER appname_wrapper OPTIONS (table '(SELECT col FROM TEST2 WHERE val = ''string'')') ;
```

### 2、访问不属于连接时的用户表

schema

表的架构（或所有者）。用于访问不属于连接Oracle用户的表。此名称必须与Oracle系统目录中的名称完全相同，因此通常仅由大写字母组成。

### 3、只读

readonly

是否开启只读，默认为 false，可选值 yes/on/true，开启后只允许进行 select 操作。

```sql
-- 设置只读选项，让test2仅仅允许select操作
 CREATE FOREIGN TABLE test2 (
          text      character varying(200)
       ) SERVER appname_wrapper OPTIONS (table 'TEST2', readonly 'true') ;
```

### 4、oracle字段在postgres中字段长度限制

max_long (optional, defaults to "32767")

### 5、行预取

prefetch（可选，默认为“200”）

设置在外部表扫描期间使用PostgreSQL和Oracle之间的单次往返获取的行数。这是使用Oracle行预取实现的。该值必须介于0到10240之间，其中值为零会禁用预取。

较高的值可以提高性能，但会在PostgreSQL服务器上使用更多内存。

### 设置外部表某列为主键

key  default is false

外部Oracle表上的相应列将被视为主键列。

### IMPORT FOREIGN SCHEMA

批量导入外部表

[reference](http://www.postgresonline.com/journal/archives/359-Import-Foreign-Schema-for-ogr_fdw-for-PostgreSQL-9.5.html)
[postgres document reference](https://www.postgresql.org/docs/9.5/sql-importforeignschema.html)

```yaml
# 先使用docker创建postgres的容器
# bellow is fdw environment
      - FDW_NAME=appname_wrapper # whatever it is
      - FDW_FOREIGN_SCHEMA=ZCGL
      - FDW_FOREIGN_SERVER=//192.168.1.108:1521/wzzp  # dbserver string # jdbc:oracle:thin:@//192.168.1.108:1521/wzzp
      - FDW_USER=zcgl # oracle_name
      - FDW_PASS=zcgl # oracle_password
      - FDW_SCHEMA=oracle_mirror
```

```sql
-- 批量导入外部表
 import foreign schema "ZCGL"  from server appname_wrapper into oracle_mirror;

-- 如何查询oracle数据库中表数据
select * from oracle_mirror.user_test;
```

## 使用注意

1. 查询定义的表和视图定义的表，设置 sample_percent 会失败。
2. oracle 用户必须具有一定权限，create session权限及相关表和视图的查询权限。
3. 映射到postgres的表的实际字段可以比oracle中的表少或者多。
4. 必须使 oracle 中的数据类型可以自动映射到 postgres 的数据类型，当然对于select可以使用虚拟列，跳过不需要进行转换的列。
5. oracle 字段的值得字段超出 postgres 列的大小，会报错。
6. number 可以转换为 boolean。
7. 可以基于oracle中的视图映射为postgres中的表。
8. where 和 order by。 请使用  explain 是否正确转换为 oracle sql。
9. join。参考官方说明。

## 制作带扩展的postgres镜像

### 制作镜像参考

<p class='tip'>
<strong>制作的镜像默认导入oracle用户可以访问到的所有表和视图作为外部表。且没有任何限制。</strong><br>
<strong>所以如果需要对表做出限制，可以再次声明一个个的forign table，限制字段、限制readonly等</strong><br>
<strong>也可以修改dockerfile内容在制作镜像是做出限制。</strong>
</p>

[make a postgre96 images with FDW and GIS](https://github.com/bcgov/openshift-postgresql-oracle_fdw)

### 拉取制作好的镜像

```shell
# login remote repository, password is g.123456
docker login --username=slencecde registry.cn-hangzhou.aliyuncs.com
# pull a version
docker pull registry.cn-hangzhou.aliyuncs.com/mengjieguo/postgres_extend:1.0.0
```

### docker-compose.yml 配置

```yaml
version: '2.1'

services:

  postgres:
    image: postgres96-oracle-fdw:1.0.0
    hostname: postgreshost
    container_name: test_postgres96_oracle_fdw
    ports:
      - "5432:5432"
    volumes:
      - /tmp/fdw_db:/var/lib/pgsql/data  # 注意tmp目录在重启计算机后会丢失数据，如果想保留数据，可以挂载其他目录 /Users/{Username}/Downloads/postgres_fdw_test/
    # env_file:
      # - alipay_postgres.env
    environment:
      - POSTGRESQL_DATABASE=test_oracle_fdw
      - POSTGRESQL_USER=test_user
      - POSTGRESQL_PASSWORD=test_user
      - POSTGRESQL_ADMIN_PASSWORD=test_user
      # - POSTGRESQL_MAX_CONNECTIONS=2000  # default is 100
      # - POSTGRESQL_MAX_PREPARED_TRANSACTIONS=2000  # default is 0
      # - POSTGRESQL_SHARED_BUFFERS=650M  # default is 32M
      # - POSTGRESQL_EFFECTIVE_CACHE_SIZE=2560M  # default is 128M

      # bellow is fdw environment
      - FDW_NAME=appname_wrapper # whatever it is
      - FDW_FOREIGN_SCHEMA=ZCGL
      - FDW_FOREIGN_SERVER=//192.168.1.108:1521/wzzp  # dbserver string # jdbc:oracle:thin:@//192.168.1.108:1521/wzzp
      - FDW_USER=zcgl # oracle_name
      - FDW_PASS=zcgl # oracle_password
      - FDW_SCHEMA=oracle_mirror
```

### 使用docker-compose启动

```yaml
docker-compose up
```

## 清理映射关系
