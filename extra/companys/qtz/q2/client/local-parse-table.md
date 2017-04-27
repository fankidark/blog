# 本地导表工具使用说明

## 初始设置:

* 安装 `Python 2.7`

* 安装 `xlrd-0.9.3`

	- 打开cmd窗口
	- `cd xlrd-0.9.3`目录下
	- 确保目录下有 `setup.py` 文件, 输入 `setup.py install`

* 安装 `Django-1.4.3`

	- 解压 `Django-1.4.3`包
	- 打开 `cmd` 窗口
	- `cd` 到 `Django-1.4.3` 目录
	- 确保目录下有 `setup.py` 文件, 输入 `setup.py install`

* 确定好导表分支, 向服务端程序要一个最新的对应分支的 `parse_config.py` 文件, 覆盖在当前目录下的 `parse_config.py`

* 将当前目录复制到客户端所在根目录. (与scripts文件夹同级)

* 修改 `DOC_PATH`

	- 右键"parse.bat" 点击 "编辑"

	- 找到以下语句:

		`set DOC_PATH="D:\program\workplace\mobile_workplace\design"`

	- 将后面的路径修改为本地design文件夹所在路径.
      >路径不能包含中文.

		如: `set DOC_PATH="d:\design"`

## 导表操作:

双击 `parse.bat`, 输入相应导表指令. 如: `skill`, 则导技能表.

## 注意:
> 该工具生成的表数据仅对当前客户端生效. 本地改表调试完成后, 仍需走正常导表流程.

> 该工具不会同步更新服务端表, 故只能用于调试客户端表相关的功能模块.
