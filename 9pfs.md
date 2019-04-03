# 9pfs

	9pfs in OSv implemented base on Linux 9p client API

## File System Operations

| Name | Used API |
| :--- | :------- |
| mount   | create, attach |
| unmount | begin_disconnect, disconnect, destroy |
| sync    | fsync |
| vget    |       |
| statfs  | statfs |

## File Operations

| Name | legacy | 2000u | 2000L |
| :--- | :----- | :---- | :---- |
| lookup    | walk |  |  |
| open      | 
| read      | read |  |  |
| write     | write |  |  |
| seek      |   |  |  |
| ioctl     |   |  |  |
| cache     | - | - | - |
| sync      | write, wstat | write, wstat | fsync |
| readdir   | read | read | readdir |
| create    | fcreate |  |  |
| remove    | remove |  |  |
| rename    | rename |  |  |
| mkdir     | fcreate | fcreate | mkdir_dotl |
| rmdir     | remove |  |  |
| getattr   | stat | stat | getattr_dotl |
| setattr   | wstat | wstat | setattr |
| truncate  |   |  |  |
| link      | fcreate | fcreate | link |
| fallocate |   |  |  |
| readlink  | stat | stat | readlink |
| symlink   | fcreate | fcreate | symlink |
