# Overview

	FreeBSD File System

```
	--------     ----------       --------
	|file  |  -> |dentry  |  ->   |vnode |
	--------  |  ----------  |    --------
	|flag  |  |  |refcnt  |  |    |ino   |
	|refcnt|  |  |path    |  | -- |mp    |
	|offset|  |  |vnode   | -- |  |vnops | --
	|dentry| --  |mp      | --_|  |refcnt|  |
	|data  |     |parent  |  |    |size  |  |
	| ...  |     |children|  |    |data  |  |
	--------     |...     |  |    | ...  |  |
	             ----------  |    --------  |
	                         |              |
	---------      --------  |     -------  |
	|vfsops | <-   |mount | <-     |vnops| <-
	|-------|  |   --------        -------
	|mount  |  --  |vfsops|        |open |
	|unmount|      |flag  |        |close|
	|vget   |      |refcnt|        |read |
	|sync   |      |path  |        |write|
	|statfs |      |dev   |        | ... |
	---------      |root  |        -------
	               |data  | --
	               | ...  |  |
	               --------  |
	                         |
	--------     ----------  |    -----------
	|Xfs_sb| <-  |Xfs_info| <- -> |Xfs_inode|
	--------  |  ----------    |  -----------
	|magic |  -- |sb      |    |  |   ...   |
	|bsize |     |inodes  |   --  -----------
	|inocnt|     | ....   |
	--------     ----------
```

## file

	Real File in OS
	
## dentry

	Directory Entry

## vnode

	FreeBSD Vnode including private data (inode, device, or others)

## mount

	Mount Point

## vfsops

	Specific FS Operations excluding Vnode Operations

| definition | description |
| :--------- | :---------- |
| int (\*vfs_mount)	(struct mount \*, const char \*, int, const void \*); | 挂载文件系统实例 |
| int (\*vfs_unmount)	(struct mount \*, int flags); | 卸载文件系统实例 |
| int (\*vfs_sync)		(struct mount \*); | 同步文件系统元数据到磁盘 |
| int (\*vfs_vget)		(struct mount \*, struct vnode \*); | <Unknown> |
| int (\*vfs_statfs)	(struct mount \*, struct statfs \*); | 获取文件系统统计信息 |

## vnops

	FreeBSD Vnode Operations (File Operations)

| definition | description |
| :--------- | :---------- |
| int (\*vnop_open_t)	(struct file \*); | 打开一个文件 |
| int (\*vnop_close_t)	(struct vnode \*, struct file \*); | 关闭一个文件 |
| int (\*vnop_read_t)	(struct vnode \*, struct file \*, struct uio \*, int); | 读取文件内容 |
| int (\*vnop_write_t)	(struct vnode \*, struct uio \*, int); | 写入文件内容 |
| int (\*vnop_seek_t)	(struct vnode \*, struct file \*, off_t, off_t); | 更新文件位置 |
| int (\*vnop_ioctl_t)	(struct vnode \*, file \*, u_long, void \*); | 将命令和参数发送给设备文件 |
| int (\*vnop_fsync_t)	(struct vnode \*, file \*); | 将文件缓存写入磁盘 |
| int (\*vnop_readdir_t)	(struct vnode \*, file \*, struct dirent \*); | 返回目录列表下一项 |
| int (\*vnop_lookup_t)	(struct vnode \*, struct vnode \*\*); | 查找dentry中指定文件名对应的vnode |
| int (\*vnop_create_t)	(struct vnode \*, mode_t); | 创建一个文件 |
| int (\*vnop_remove_t)	(struct vnode \*, node \*, char \*); | 删除一个文件 |
| int (\*vnop_rename_t)	(struct vnode \*, node \*, char \*, struct vnode \*, struct vnode \*, char \*); | 重命名一个文件 |
| int (\*vnop_mkdir_t)	(struct vnode \*, char \*, mode_t); | 创建一个目录 |
| int (\*vnop_rmdir_t)	(struct vnode \*, struct vnode \*, char \*); | 删除一个目录 |
| int (\*vnop_getattr_t)	(struct vnode \*, struct vattr \*); | 获取vnode属性 |
| int (\*vnop_setattr_t)	(struct vnode \*, struct vattr \*); | 设置vnode属性 |
| int (\*vnop_inactive_t)	(struct vnode \*); | <Unknown> |
| int (\*vnop_truncate_t)	(struct vnode \*, off_t); | 截断文件内容 |
| int (\*vnop_link_t)      (struct vnode \*, struct vnode \*, char \*); | 创建硬链接 |
| int (\*vnop_cache_t) (struct vnode \*, struct file \*, struct uio \*); | <Unknown> |
| int (\*vnop_fallocate_t) (struct vnode \*, int, loff_t, loff_t); | <Unknown> |
| int (\*vnop_readlink_t)  (struct vnode \*, struct uio \*); | 读/写链接? |
| int (\*vnop_symlink_t)   (struct vnode \*, char *, char \*); | 创建软链接 |

## Xfs_info

	Specific FS Information including SuperBlock, Inodes and etc

## Xfs_sb

	Specific FS SuperBlock

## Xfs_inode

	Specific FS Inode
