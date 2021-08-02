# aligo
🚀🔥使用Python连接阿里云盘, 实现了官方大部分功能 👍👍



![python version](https://img.shields.io/pypi/pyversions/aligo) 

> 为了完善代码提示, 方便大家代码书写, **aligo** 引入了一些 **python 3.8** 的新特性, 所以要求 **python >= 3.8.***

```bash
pip install aligo
```

或

```bash
pip install aligo -i https://pypi.org/simple
```

必要时可以加个 `--upgrade` 参数



## 快速入门

```python
from aligo import Aligo

ali = Aligo()

# 获取用户信息
user = ali.get_user()

# 获取网盘根目录文件列表
ll = ali.get_file_list()

```



## 基本功能

1. 登录
   - [x] 扫描二维码登录
   
     ```python
     # 导包, 这里为了方便演示, 导入全部
     from aligo import *
     
     # 创建身份认证对象
     # 所有参数可选, 默认二维码扫码登录, 推荐
     auth = Auth()
     
     # 如需自定义二维码显示方式, 可指定一个函数, 详情参考源码
     # auth = Auth(show=show_qrcode)
     ```
   
   - [x] **refresh_token** 登录
   
     ```python
     # 也可使用 refresh_token 验证登录
     auth = Auth(refresh_token='<refresh_token>')
     
     # refresh_token 参数可在 Chrome -> F12 -> Application -> Local Storage -> token 中寻找
     ```
   
   - [x] 持久化登录
   
     ```python
     # aligo 支持自动自动刷新 access_token
     # 所以不是每次使用都需要登录的
     # aligo 会将 token 信息保存到 <home>/.aligo 目录下的配置文件中
     # 配置文件名为 <name>.json, name 通过一下方式提供, 默认为 'aligo'
     
     auth = Auth(name='aligo')
     ```
   
2. 获取信息
   - [x] 获取用户信息

     ```python
     # 创建 Auth 对象之后, 再创建 Aligo 对象, 剩下的就是方法调用了
     # 需要提供一个 Auth 对象, 可选, 默认 Auth()
     ali = Aligo()
     user = ali.get_user()
     
     # user 是一个 BaseUser 对象, 详情参考源码, 非常简单😁
     ```

   - [x] 获取网盘信息

     ```python
     # 比如获取 云盘容量 信息
     drive_info = ali.get_personal_info()
     ```

     ![image-20210731202528336](images/image-20210731202528336.png)

3. 功能太多, 就不一一列举, 大致如下

   - [x] 扫码登录
   - [x] refresh_token登录
   - [x] 持久化登录
   - [x] 获取用户信息
   - [x] 获取云盘信息
   - [x] 获取文件信息
   - [x] 批量获取文件下载地址
   - [x] 根据路径获取文件
   - [x] 获取文件列表
   - [x] 批量下载/上传文件(夹)
   - [x] 秒传文件
   - [x] 批量重命名/移动/复制文件(夹)
   - [x] 批量收藏/取消收藏文件(夹)
   - [x] 批量移动文件到回收站
   - [x] 批量恢复回收站文件
   - [x] 获取回收站文件列表
   - [x] 搜索文件/标签
   - [x] 创建官方分享，支持设置密码，有效期
   - [x] 更新分享(官方)
   - [x] 批量取消分享(官方)
   - [x] 批量保存他人分享文件
   - [x] 自定义分享，突破官方限制
   - [x] 自定义分享保存
   - [x] 支持自定义功能
   
   一般说来, 能用官方客户端实现的基本操作, 你都可以用 **aligo** 试试. 无常用功能? [反馈](https://github.com/foyoux/aligo/issues/new)



## 用法示例

```python
from aligo import *

ali = Aligo()
```

> 1. 如果是第一次使用, 则会弹出二维码, 使用移动端 阿里云盘 扫描授权后即可. 在Windows中默认调用图片查看器打开二维码, ;类Unix中, 直接打印到终端. 
> 2. 如果已经登录过, 则会直接加载保存的配置文件, 如果过期, 则自动刷新, 如果 refresh_token 过期, 则重新弹出二维码供扫描登录



### 1. 获取文件(列表)

```python
# 所有参数可选, 默认 获取网盘根目录文件列表, 即 parent_file_id='root'
root_list = ali.get_file_list()
# 等价于 root_list = ali.get_file_list(parent_file_id='root')

# 获取指定目录列表
file_list = ali.get_file_list(parent_file_id='<file_id>')

# 获取指定文件
file = ali.get_file('<file_id>')

# 通过路径获取文件, 默认以 网盘根目录为基础目录
all_folder = '61076daaa84228b3b3f643a6a32829c30a23785f'
file = ali.get_file_by_path('aligo/All_Test', parent_file_id='root')
assert file.file_id == all_folder
```

![image-20210801220111374](images/image-20210801220111374.png)

- 阿里云盘和百度不同, 百度网盘使用的是 **路径** 方式定位文件, 而阿里云盘使用的是 **drive_id** / **share_id** + **file_id** 定位文件
- 在 **aligo** 中, 所有默认 **drive_id** 都可省略, 所以一般只需提供 **file_id** 参数即可
- 以前阿里云盘时允许同名文件的, 但现在已更改了此策略, 文件名区分大小写



### 2. 保存他人分享文件

在阿里云盘分享中, 链接末尾那一段, 代表 **share_id**, 即代表一个分享的唯一识别码, 例如: **https://www.aliyundrive.com/s/nDtTamX9vTP**, 此分享密码 **share_pwd='w652'**

其中 **nDtTamX9vTP** 即为 **share_id**

```python
share_id = 'nDtTamX9vTP'
# 如果一个分享是公开分享, 那么 share_pwd = '', 默认就是此值, 所以没有密码时, 直接忽略此参数即可.
# 具体情况你可以在开发工具中查看源码
share_pwd = 'w652'

# 1.如果想获取 此 share_id 对应分享信息, 可以这样做
info = ali.get_share_info(share_id)

# 2.现在你想访问 此分享, 首先你需要获取 share_token
share_token = ali.get_share_token(share_id, share_pwd)

# 3.现在你可以获取分享文件列表了
share_file_list = ali.get_share_file_list(share_id, share_token.share_token)

# 4.这里还有一个 get_share_file 方法
file = ali.get_share_file(share_id, file_id=share_file_list[0].file_id, share_token=share_token.share_token)

# 5.现在我们可以进行保存了, 比如我们保存到网盘根目录, 此时 to_parent_file_id 可以省略
save_file = ali.share_file_saveto_drive(share_id, file_id=share_file_list[0].file_id, share_token=share_token.share_token, to_parent_file_id='root')

# 6.批量保存
batch_save_file = ali.batch_share_file_saveto_drive(share_id, [i.file_id for i in share_file_list], share_token.share_token, 'root')

```



### 3. 重名文件

```python
new_file = ali.rename_file('新名字.jpg', '<file_id>', check_name_mode='refuse', drive_id=ali.default_drive_id)
```



### 4. 移动文件

```python
# 移动默认 drive_id 下的 file_id 文件到 默认 drive_id 的 'root' 下
move_file = ali.move_file('<file_id>', 'root')

# 批量可使用 batch_move_files 方法

# 复制文件
# ali.copy_file()
# ali.batch_copy_files()
```



### 5. 移动文件到回收站

```python
trash_file = ali.move_file_to_trash('<file_id>')

# 批量 batch_move_to_trash
```



### 6. 获取回收站文件列表

```python
recyclebin_list = ali.get_recyclebin_list()
```



### 7. 从回收站恢复文件

```python
restore_file = ali.restore_file('<file_id>')

# 批量 batch_restore_files
```



### 8. 收藏/取消收藏

```python
ali.starred_file('<file_id>', starred=True)

# starred=True 表示收藏
# starred=False 表示取消收藏

# 获取收藏列表, 具体参数用法, 请查看 代码提示 或 源码
starred_list = ali.ali.get_starred_list()
```



### 9. 秒传文件

```python
# 具体参数看源码
# 必须参数, 取个name, 随意

# content_hash, size 这两个就是唯一确定一个文件的参数, 即秒传所需参数
ali.create_by_hash(...)
```



### 10. 下载文件(夹)

```python
# ali.download_file()
# ali.download_files()
# ali.download_folder()
```



### 11. 上传文件(夹)

```python
# ali.upload_file()
# ali.upload_files()
# ali.upload_folder()
```



### 12. 分享文件, 可设置密码, 有效期

```python
# ali.share_file()
```



### 13. 自定义分享, 突破官方限制

```python
# ali.share_files_by_aligo()
# ali.share_folder_by_aligo()
```



### 14. 保存自定义分享

```python
# ali.save_files_by_aligo()
```



### 15. 搜索文件

```python
# ali.search_file()
```



### 16. 搜索目标/标签

```python
# ali.searech_aims()
```



## 自定义功能

这里以删除文件为例, 自定义功能

> 其他例子我想不出来了, 因为基本上都实现了

```python
"""..."""
from aligo import Aligo


class CustomAligo(Aligo):
    """自定义 aligo """

    V2_FILE_DELETE = '/v2/file/delete'

    def delete_file(self, file_id: str):
        """删除文件"""
        response = self._post(self.V2_FILE_DELETE, body={'file_id': file_id})
        return response.json()


cali = CustomAligo()

cali.delete_file('<file_id>')

```



## FAQ

1. 为什么代码中注释这么少(没有)

   因为模块太多了, 同时也是没必要的, apis接口简单, 稍许使用, 便可完全掌握, 所以我也就偷个懒了. 

   如果有问题可以随时 [反馈](https://github.com/foyoux/aligo/issues/new)

   

2. 有(彻底)删除文件(夹)的功能吗?

   没有, 因为太过危险, 但是有移动文件(夹)到回收站的功能. 如果万一要实现的话, 请参考自定义功能

   

3. 可以操作保险箱中的文件吗?

   不可以, 非必要, 也不安全

   

4. 为什么获取文件列表(10000个)这么慢?

   这是没办法的事情, 官方限制最多一次性获取 **200** 个, 10000 个的话, 要发几十个请求, 并且还不能并发或者批量, 因为后一个请求依赖前一个请求的内容.

   

5. 没有你想要的功能?

   请尽管 [反馈](https://github.com/foyoux/aligo/issues/new), 如果提议不错, 我会好好考虑的. 如果不能附加进去, 我也会回复, 给一些建议或替代方案.

   

6. 你这文档也太简单了吧?

   是很简单, 后续慢慢完善. 这个花了我不少心思, 也拖了好久, 今天赶鸭子上架. 🦆🦆🦆

   

7. **BUG在所难免**

   如果 **出现BUG** 或 **使用问题**, 或 **不合逻辑的设计**, 或者 **建议**, 请 [issue](https://github.com/foyoux/aligo/issues/new)




![aligo反馈交流群](images/wechat.png)
