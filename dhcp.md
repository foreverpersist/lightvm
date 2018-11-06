# DHCP Protocol

# Package Format

|  Field  |       Purpose       |
| :------ | :------------------ |
| op      | 消息操作码            |
|         |                      |
| htype   | 硬件地址类型           |
|         |                      |
| hlen    | 硬件地址长度           |
|         |                      |
| hops    | 客户机设为0,可被中继使用 |                    |
|         |                      |
| xid     | 事务ID                |
|         |                      |
| secs    | 从获取IP或续约开始耗时  |
|         |                      |
| flags   | 标记                  |
|         |                      |
| ciaddr  | 客户机IP地址           |
|         |                      |
| yiaddr  | "你的"(客户机)IP地址   |
|         |                      |
| siaddr  | 下一服务器的IP地址      |
|         |                      |
| giaddr  | 中继IP地址            |
|         |                      |
| chaddr  | 客户机硬件地址         |
|         |                      |
| sname   | 可选的服务器主机名      |
|         |                      |
| file    | Boot文件名            |
|         |                      |
| options | 可选字段              |