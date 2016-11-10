# <div align="center">模糊测试工具调研</div>
## [BrundleFuzz](https://github.com/carlosgprado/BrundleFuzz/wiki)
BrundleFuzz是一个windows平台的模糊测试工具，基于AFL，由Python编写。它分为客户端与服务器两部分：
* Clients: **核心是PIN Tool**
  * Windows clients (Python, C++)
  * Linux clients (Python, C++)
* Server:
  * Linux server (Python, third party stuff)

**客户端和服务器通过RabbitMQ进行RPC通讯**
