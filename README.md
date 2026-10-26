# tgbot — C++20 异步 Telegram Bot 框架

基于 Boost.Asio 协程（`boost::asio::awaitable`）的轻量 Telegram Bot 框架，
无额外运行时依赖，可产出纯静态单文件二进制。

## 组件

| 头文件 | 功能 |
| --- | --- |
| `tgbot/http_client.hpp` | HTTPS 客户端（Beast + OpenSSL，TLS 校验） |
| `tgbot/bot_client.hpp` | Telegram Bot API 封装（sendMessage / editMessageText / deleteMessage / getChatMember / answerCallbackQuery ...） |
| `tgbot/dispatcher.hpp` | 命令 / 回调 / 任意消息路由，per-chat strand 保证同群顺序 |
| `tgbot/long_poll.hpp` | getUpdates 长轮询循环，offset 持久化、429/409 处理 |
| `tgbot/rate_limiter.hpp` | per-chat 发送限流 |
| `tgbot/state_store.hpp` | 键值状态存储抽象（`get` / `set` / `erase`） |
| `tgbot/sqlite_state_store.hpp` | SQLite 持久化实现（WAL） |

## 依赖

由 vcpkg manifest（`vcpkg.json`）锁定：boost-asio、boost-beast、openssl、
nlohmann-json、spdlog、sqlite3。

## 预编译依赖

本仓库 CI（`.github/workflows/deps.yml`）每日检查 microsoft/vcpkg 上游，
把编译好的 vcpkg binary cache 发布到 [`deps` release](https://github.com/Ellenp2p/tgbot/releases/tag/deps)：

- `vcpkg-bincache-<triplet>.tar.gz` —— 预编译依赖包
- `vcpkg-commit.txt` —— 编译时使用的 vcpkg commit（下游须检出同一 commit 才能命中缓存）

下游项目构建时拉取并解压 bincache、检出对应 vcpkg commit 后，
`vcpkg install` 变为纯恢复，秒级完成；拉取失败则回退全量编译。

## 用法

```cmake
find_package(unofficial-sqlite3 CONFIG REQUIRED)
target_link_libraries(your_bot PRIVATE tgbot)
```

示例见 Ellenp2p/cpp-tg-bot 仓库的 `examples/`（echo_bot、group_admin）。
