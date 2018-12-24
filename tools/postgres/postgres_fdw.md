
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
docker tag registry.cn-hangzhou.aliyuncs.com/mengjieguo/postgres_extend:1.0.0 postgres_extend:1.0.0
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

## 连接

```python
    postgres_ip: 192.168.1.xxx (本机postgresip)
    postgres_port: 5432(本机postgres port）
    postgres_database: test_oracle_fdw （本机postgres数据库）
    postgres_admin_user: postgres （本机postgres 用户）
    postgres_admin_passwd: test_user（本机postgres  超级用户密码）
    postgres_user: test_use（本机postgres  用户）
    postgres_passwd: test_use （本机postgres  密码）
```

下面是测试用的sql命令

```sql
select * from pg_extension;
select * from pg_foreign_data_wrapper;
select * from pg_foreign_server;
select * from pg_foreign_table;

select * from oracle_mirror.user_test;

SELECT * FROM test2
WHERE EXTRACT(month FROM "text") = 2
AND EXTRACT(year FROM "text") = 2011;

select *  FROM
        oracle_mirror.VEHICLE  WHERE hpzl='02' and hphm='AAB008';

-- 首先使用在vehicle表上创建存储过程func_guo_select_vehicles_1，用户根据车牌查询机动车基本信息
create function func_guo_select_vehicles_1(
  hpzl varchar(2) = null,
  hphm char = null,
  sfzmhm char = null
) returns table(xh char, hpzl char, hphm char, sfzmhm char) as
$func$
  select a.xh, a.hpzl, a.hphm, a.sfzmhm
from oracle_mirror.vehicle a
where ($1 is null or a.hpzl = $1)
and  ($2 is null or a.hphm = $2)
and  ($3 is null or a.sfzmhm= $3)
$func$ LANGUAGE sql;

select * from func_guo_select_vehicles_1(); -- 查询所有车辆数据
select * from func_guo_select_vehicles_1(hpzl := '02'); --查询车辆 hpzl 是 02 的车辆信息
select * from func_guo_select_vehicles_1(hpzl := '02', hphm := 'AAB008'); --查询车辆 02AAB008 的车辆信息
select * from func_guo_select_vehicles_1(sfzmhm := '410126196905102558'); --某人的的车辆信息，如果没有找到会返回空

-- 加入对时间范围的查询
create function func_guo_select_vehicles_3(
  hpzl varchar(2) = null,
  hphm char = null,
  sfzmhm char = null,
  bxzzrq_start char = null,
  bxzzrq_end char = null
) returns table(xh char, hpzl char, hphm char, sfzmhm char, bxzzrq char) as
$func$
  select a.xh, a.hpzl, a.hphm, a.sfzmhm, to_char(bxzzrq, 'YYYY-MM-DD HH24:MI:SS') bxzzrq
from oracle_mirror.vehicle a
where ($1 is null or a.hpzl = $1)
and  ($2 is null or a.hphm = $2)
and  ($3 is null or a.sfzmhm= $3)
and   case when ($4 is null or $5 is null) then (to_timestamp($4, 'YYYY-MM-DD HH24:MI:SS') < a.bxzzrq) else
          (to_timestamp($4, 'YYYY-MM-DD HH24:MI:SS') < a.bxzzrq
        and to_timestamp($5, 'YYYY-MM-DD HH24:MI:SS') > a.bxzzrq) end
$func$ LANGUAGE sql;

select * from func_guo_select_vehicles_2(); -- 时间现在是必填的。。。

-- 多表连接
create function func_guo_select_vehicles_3(
  hpzl varchar(2) = null,
  hphm char = null,
  sfzmhm char = null
  --bxzzrq_start char = null,
  --bxzzrq_end char = null
) returns table(xh char, hpzl char, hphm char, sfzmhm char, bxzzrq char, xh_v char, wfdz char) as
$func$
select a.xh, a.hpzl, a.hphm, a.sfzmhm, to_char(a.bxzzrq, 'YYYY-MM-DD HH24:MI:SS') bxzzrq, b.xh xh_v, b.wfdz
from oracle_mirror.vehicle a
INNER join oracle_mirror.vio_surveil b
  on  (a.hpzl = b.hpzl and ('豫'|| a.hphm) = b.hphm)
  where ($1 is null or a.hpzl = $1)
  and ($2 is null or a.hphm = $2)
  and  ($3 is null or a.sfzmhm= $3)
$func$ LANGUAGE sql;

select * from func_guo_select_vehicles_3(hpzl := '02', hphm := 'AAB008'); -- 查询车辆的所有现场？违章

-- 多维分析函数
-- 分析机动车信息表
-- 按照地区显示车辆数据量
-- 按照每年的注册车辆
-- 按照机动车状态显示
-- 按照是否逾期2个检验周期期止
-- 按照是否逾期强制报废期止
-- 按照车辆用途
-- 按照用户属性
-- 按照环保达标
-- 按照是否抵押
-- 按照车辆是否转入过户
-- 按照机动车状态
-- 按照保险截止日期前3个月，6个月，9个月，12个月，1年又3个月，1年又6个月，1年又9个月，2年
-- 按照号牌种类
create function func_guo_select_vehicles_10(
  car_type char = NULL , -- hpzl
  car_status char = NULL, --

)



-- func
-- 使用user_test ,zcgl.user_test都无法访问表, 请使用 oracle_mirror.user_test 访问
create function func_guo_test_1(
  name text = null,
  sex text = null,
  id_card text = null,
  job_part text = null,
  phone text = null,
  address text = null
  --input_time date  = null
) returns table(name text, sex text, id_card text, job_part text, phone text, home_address text, input_time text) as
$func$
select a.name, a.sex, a.id_card, a.job_part, a.phone, a.home_address, a.input_time
from oracle_mirror.user_test a
where ($1 is null or a.name = $1)
  and ($2 is null or a.sex = $2)
  and ($3 is null or a.id_card = $3)
  and ($4 is null or a.job_part = $4)
  and ($5 is null or a.phone = $5)
  and ($6 is null or a.phone = $6)
  --and ($7 is null or a.phone = $7)
  $func$ LANGUAGE sql;
-- 使用刚才的func
select * from func_guo_test_1();
select * from func_guo_test_1(name := '于超');
select * from func_guo_test_1(name := '于超', job_part := '000000000009');

```