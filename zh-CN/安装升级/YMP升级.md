## 升级说明

升级功能提供YMP旧版本到YMP新版本的升级和正常使用，但是部分功能受限，建议用户首选YMP正常的[卸载](./安装与卸载/卸载)功能。

支持升级的版本为：（~为包含所有中间版本）

|  YMP旧版本| YMP新版本| 升级限制| 解决方案|
|-----------|-----------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| 23.1.1.0  | 23.2.1.2  | 升级后评估结果中schema的数据量总和为0                                                                                  | 全量刷新报告或者重新评估                           |
|           |           | 评估结果中衍生对象数、衍生兼容占比、衍生不兼容占比为0                                                                             | 重新评估                                   |
|           |           | 只更新新版本YMP中有的配置，文档未提及的配置不升级同步                                                                            | 升级后手动添加                                |
|           |           | 迁移报告无法体现评估时的转换规则                                                                                        | 重新评估                                   |
|           |           | 如果在升级前，对会产生衍生对象（序列、触发器）的表DDL做过修改保存，并且升级后，首次重新评估的手动DDL保留策略改为【全保留】，评估后除了多了衍生一个对象以外，表中衍生对象DDL还存在，可能会导致迁移失败 | 数据迁移时不迁移失败的衍生对象，并不影响迁移结果               |
| 23.2.1.2  | 23.2.1.3  | 升级后新版本才开始支持角色迁移，迁移配置中的角色初始化信息为空                                                                         | 需手动刷新角色列表                              |
| 23.2.1.3~ | ~23.2.1.5 | 无                                                                                                       |                                        |
| 23.2.1.5  | 23.2.1.6  | 升级后校验报告状态会变为生成失败                                                                                        | 重新生成校验报告                               |
| 23.2.1.6~ | ~23.3.1.2 | 无                                                                                                       |                                        |
| 23.3.1.2  | 23.3.2.3  | 无                                                                                                       |                                        |
| 23.3.2.5  | 23.3.2.7  | 无法查看和下载升级前生成的迁移日志和校验日志                                                                                  | 从YMP安装log的migration/、ydc_log/根据任务名查找使用 |
| 23.3.2.7  | 23.3.2.9  | 无法使用新特性：不支持失败的评估任务恢复评估、不支持单个对象的兼容率刷新                                                                    | 可重新评估或者全量刷新报告后使用                       |
| 23.3.2.9~ | ~23.4.1.3 | 无                                                                                                       |                                        |
| 23.4.1.3  | 23.4.3.2  | 升级前添加的mysql模式的崖山数据源暂不可用                                                                                 | 使用前重新测试连接且确认保存即可                       |
|           |           | 升级前不支持修改内置库的语法模式配置（conf/db.properties中的YASDB_MODE），默认升级为yashan模式                                        | 评估需要的话，使用mysql模式的外置库                   |
| 23.4.3.2~ | ~23.5.7.2 | 升级前后不支持切换内置库的语法模式                                                                                       |                                        |

> **Note**:
> 
> 1. 表格中支持的版本可跨版本升级，跨版本升级的功能限制为各个版本之间的叠加。
> 2. 23.2.1.4之前，对于[自定义内置库启动](./安装与卸载/安装部署（自定义内置库）)的旧版本YMP，升级前，需手动在自定义的数据库中创建一个YMP_DEFAULT_UPGRADE用户并赋权，密码和旧版本业务用户YMP_DEFAULT保持一致。
> 3. 23.2.1.4开始支持多次升级。例如从23.1.1.0升级到23.2.1.2使用一段时间之后再升级到23.2.1.4。
> 4. 23.1.1.0作为基线版本进行多次升级时，需在每次升级后手动更换OCI版本（参考[更换组件](./更换组件)）。
> 5. 从23.3.1.2之后版本，只支持release正式版本（xx.x.2.x为release正式版本）之间的升级，xx.x.1.x为beta联调版本，不支持升级。

## 环境条件

- YMP旧版本下的bin/operate.log日志不能清除。
- 升级前需保证新版本YMP路径下的db与bin/yasldr目录为空。
- ~/.yasboot目录下需存在升级前版本的ymp.env文件。

## 升级前准备

### 上传新的YMP安装包

```shell
# 创建新版本YMP安装地址
$ mkdir /home/ymp/upgrade
# 切换至YMP升级目录并解压
$ cd /home/ymp/upgrade
$ unzip yashan-migrate-platform-x86-64.zip
```

### 备份ymp.env文件

为避免因升级失败后误删备份文件导致旧版本YMP内置库无法拉起，建议在更新操作开始前手动备份~/.yasboot/ymp.env文件。

```shell
$ cp ~/.yasboot/ymp.env ~/.yasboot/ymp.env_bak
```

### 停止旧版本YMP

```shell
# 切换至旧版本YMP目录并停止
$ cd /home/ymp/yashan-migrate-platform
```

#### 旧版本YMP使用默认内置库

```shell
# 停止YMP
$ sh bin/ymp.sh stop
# 记录旧版本路径，升级时需要用到
$ pwd
```

#### 旧版本YMP使用自定义内置库

```shell
# 停止YMP
$ sh bin/ymp.sh stopnodb
# 记录旧版本路径，升级时需要用到
$ pwd
```

> **Note**：
> 
> 仅停止YMP，不可卸载。不能执行uninstall操作，不变动旧版本YMP依赖的安装包。

## 升级YMP

### 切换至新版本YMP目录

```shell
# 切换至新版本YMP升级目录
$ cd /home/ymp/upgrade/yashan-migrate-platform
```

### 使用升级指令对YMP进行升级

```shell
# 升级前需保证新版本YMP路径下的db与bin/yasldr目录为空
$ sh bin/ymp.sh upgrade --from /home/ymp/yashan-migrate-platform --db /home/ymp/yashandb-23.2.1.0-linux-x86_64.tar.gz

# --db参数为可选项，若不指定，则会通过旧版本YMP的operate.log查找旧版本使用的路径自行补全
```

等待升级完成，升级成功后会自动拉起新版本YMP，无需手动启动。

### 升级成功

升级完成后，业务数据会保留在（默认/自定义）内置库用户YMP_DEFAULT_{版本}_{升级时间}下（例如：YMP_DEFAULT_V23214_20240531175950），密码和升级前用户保持一致。  

可重启浏览器后正常使用新版本YMP功能。

升级成功后，如需回退至旧版本，可按照下述操作切换至旧版本使用。

1. 停止新版本YMP
   
   ```shell
   $ sh bin/ymp.sh stop
   ```

2. 备份新版本ymp.env文件
   
   ```shell
   $ cp ~/.yasboot/ymp.env cp ~/.yasboot/new_ymp_bak.env
   ```

3. 删除新版本ymp.env文件
   
   ```shell
   $ rm -rf ~/.yasboot/ymp.env
   ```

4. 若存在ymp_yasdb_home，直接删除
   
   ```shell
   $ rm -rf ~/.yasboot/ymp_yasdb_home
   ```

5. 手动恢复旧版本ymp.env文件（以旧版本为23.1.1.0为例）：
   a.若tmp/upgrade下备份文件还存在：
   
   ```shell
   $ cp tmp/upgrade/ymp-v23.1.1.0.env ~/.yasboot/ymp.env
   ```

   b.若tmp/upgrade下备份文件已被删除，则使用手动备份文件进行恢复。
   
   ```shell
   $ cp ~/.yasboot/ymp.env_bak ~/.yasboot/ymp.env
   ```

6. 若旧版本YMP所使用的内置库需要ymp_yasdb_home，需要重新建立软链接（以23.4.1.100版本数据库为例）：
   
   1. 查看/home/ymp/yashan-migrate-platform/db/是否存在数据库版本号的文件，例如：/home/ymp/yashan-migrate-platform/db/23.4.1.100，如果存在文件夹，则说明需要软链接，请继续执行下一步。
   
   2. 重新建立软链接：
      
      ```shell
      $ ln -s /home/ymp/yashan-migrate-platform/db/23.4.1.100 ~/.yasboot/ymp_yasdb_home
      ```

7. 切换至旧版本YMP目录并启动

```shell
$ cd /home/ymp/yashan-migrate-platform
$ sh bin/ymp.sh start或者sh bin/ymp.sh startnodb
```

### 升级失败

若升级失败，可切换至旧版本YMP路径下，拉起旧版本YMP继续使用。

1. 手动恢复旧版本ymp.env文件，以旧版本为23.1.1.0为例：

```shell
$ cp tmp/upgrade/ymp-v23.1.1.0.env ~/.yasboot/ymp.env
```

2. 若tmp/upgrade下备份文件已被删除，则使用手动备份文件进行恢复

```shell
$ cp ~/.yasboot/ymp.env_bak ~/.yasboot/ymp.env
```

3. 若存在ymp_yasdb_home，则删除后重建，以23.4.1.100版本数据库为例：
   
   ```shell
   $ rm -rf ~/.yasboot/ymp_yasdb_home
   $ ln -s /home/ymp/yashan-migrate-platform/db/23.4.1.100 ~/.yasboot/ymp_yasdb_home
   ```

4. 切换至旧版本YMP目录并启动
   
   ```shell
   $ cd /home/ymp/yashan-migrate-platform
   $ sh bin/ymp.sh start或者sh bin/ymp.sh startnodb   
   ```
