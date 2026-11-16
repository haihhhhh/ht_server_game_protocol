# ht_server_game_protocol（共享协议源）

由服务端主导维护，客户端与服务端从同一提交生成各自代码。两层目录字段号生成消息编号，service/rpc 声明配对。模板接口采用各自的 XxxReq / XxxRep；普通业务仅返回本次相关数据，EnterRep.state 与显式查询 CurrentRep.state 才携带完整 PlayerState。框架仍允许复用响应类型，推送直接使用已登记消息类型。

本次由 Request/Response 改为 Req/Rep，并增加专用响应，属于协议变更。两端必须重新生成并配套部署；路由方法名不变，不提供旧响应格式兼容转换。失败继续使用原生错误通道。

账号 ID 和角色 ID 使用有符号的 64 位整数。此改动与旧字符串字段的二进制格式不兼容，客户端和服务端需要从同一版协议重新生成。JavaScript 客户端应使用生成器支持的 Long、BigInt 或十进制字符串表示，避免转为 Number 时丢失精度。

产物不入库。生成器用法见兄弟仓库 ht_server_game_template/scripts，客户端可对 game.proto 运行自身语言的 protoc 插件，并消费生成的 catalog.json。

业务增长时，可以把新功能的请求和响应放进根目录下的独立 `.proto` 文件，与现有文件使用相同的 package 和 go_package。需要使用这些消息的文件显式 import，再在消息目录登记字段并在对应 service 中声明 rpc。当前模板生成脚本读取根目录的全部 `.proto`；客户端生成脚本也应传入全部协议源。已有消息保留在原文件，避免改变客户端生成文件的归属。

编号允许随源定义调整；本版不交换协议版本，客户端与服务端协议不一致的后果由客户端承担。

本项目采用 [MIT 许可证](LICENSE)。
