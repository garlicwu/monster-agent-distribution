# 怪兽Agent Distribution

怪兽Agent的公开分发仓库，用于提供Codex插件和经过验证的桌面安装包。
桌面端源码、Agent运行时、构建系统、测试代码和开发历史不在本仓库中。

## 安装Codex插件

```bash
codex plugin marketplace add garlicwu/monster-agent-distribution
codex plugin add monster-agent@monster-agent
```

安装后请重启Codex。插件只负责将明确授权的学术任务交接到本机怪兽Agent，
不会复制Codex凭据、配置文件、会话历史、Skills或MCP配置。

## 安装怪兽Agent

桌面安装包发布在[Releases](https://github.com/garlicwu/monster-agent-distribution/releases)。
只使用标记为正式发布且带有校验值的安装包：

- Windows 10/11 x64：NSIS安装程序
- macOS 13或更高版本：Apple Silicon使用`aarch64.dmg`，Intel使用`x64.dmg`

如果Releases中尚无正式签名版本，请等待发布，不要从第三方渠道下载安装包。

## 仓库边界

本仓库仅包含Codex识别插件所需的manifest、Skill和本地Host启动器。
插件启动器连接已安装桌面应用中的原生`monster-agent-plugin-host`；
核心Agent能力在桌面应用中运行。

## 许可

插件分发文件适用本仓库中的[MIT License](LICENSE)。桌面应用及第三方组件
可能包含各自的许可和声明。
