# MAH Packs（awesome-mobile-agent-harness）

[mobile-agent-harness](https://github.com/Aelindra/mobile-agent-harness) 的知识包与插件社区目录。基座提供机制（多面观察 / 插件运行时 / 判别式求值），本目录提供业务层的两类可复用资产。

## 资产类型

| 目录 | 内容 | 消费方式 |
|---|---|---|
| `plugins/` | 某个使用场景的策略与编排：评分、比价、推荐、流程封装 | 拷入基座 `plugins/`，运行时热加载 |

插件**自包含**：所需的一切逻辑与定位信息都在插件内，不依赖外部知识库。

## 场景分类

```
packs/ 与 plugins/ 下的场景目录：
  电商/       购物、比价、优惠券、订单
  游戏/       日常任务、回合制/挂机类
  社交/       通讯、社区
  内容/       创作发布、评论整理
  生活服务/    外卖、打车、快递、票务
  效率工具/    签到聚合、备份、定时任务
  测试/       自有 app 的 QA/E2E
  AI对话/      助手类 app
```

各目录当前为空，等待首批贡献。新增场景 = 建目录并更新本文件。

## 贡献

### 知识包

知识包分两层：**业务语义**（设备无关）与**设备变体**（每个客户端一份映射）。同一 app 在手机和电脑上是同一份业务知识，只有像素与 IO 映射不同。

**知识来源与时效（重要）**：业务语义优先聚合既有社区知识源——官方教程、游戏 wiki、成熟攻略——而不是自行从零体验。每个包应在 `business.md` 头部维护来源清单（链接 + 最后核对日期），综述时标注相互矛盾或不确定的部分。适用筛选：**变动率低、敏感度低、存在社区维护的知识源、个人高频复用**。频繁改版或风控敏感的 app（电商大促页、账号型 app）不适合做知识包。

```
packs/<场景>/<app>/
  pack.json5     # 必需：name（"<场景slug>.<appslug>"）、version、game（包名）、
                 # risk（low|account|tos-grey）
                 # 可选：guide、flows、variants（["android","pc",...]）
  business.md    # 设备无关：实体、规则、流程图、词表（换设备不需要重写这层）
  guide.md       # 使用时机、流程、已知局限
  variants/      # 设备变体：variants/android|pc/{templates,states}
                 # templates/ = 图标图集（<game>/manifest.json + *.png）
```

要求：

- 附实测记录：目标 app 版本、dry-run 验证输出
- 选择器与模板定位，不使用坐标（基座 `lint` 会拒绝）
- `states` 表达式基于 `ui2.screen_evidence` 的证据字段（`overlay/motion/hud/color.*/filter.*`）
- 说明测试版本与已知局限；不更新或失效时在 guide 中标注

### 插件

```
plugins/<场景>/<名字>/
  plugin.py      # NAME / VERSION / requires / apply(ctx)
```

- `requires` 列出依赖的知识包，如 `["pack:ecom.taobao@>=1.2"]`
- handler 签名 `(bridge, engine, args) -> dict`，返回 ok/error 包络

### 规则

1. 在干净基座上可用；默认 dry-run 路径
2. **不收录**对抗检测、风控规避、指纹伪造、注入加固相关内容
3. **不收录**账号滥用类：群发、刷量、批量注册
4. 涉及登录账号的条目标注 `risk: "account"`，仅适用于个人粒度、低频、用户在场的使用方式
5. 评分与偏好等判断逻辑写在插件中；知识包只保留事实性内容

### 生命周期

```
知识包 → AI 借助基座工具直接执行任务
      → 实测结论沉淀到本地知识目录（私有）
      → 固化为插件或包 → diff 提交 PR 回流本目录
```

## 内容下架（Takedown）

本目录内容仅供个人私有设备自用研究。权利方或平台认为某条目侵犯权益或违反其条款时，提交 issue 即下架对应内容。相关数据与商标版权归各自厂商；本目录不含对抗检测、风控规避、提权内容，也不提供 root 获取指导。自动化产生的账号后果由使用者承担。

## License

MIT。

---

**EN**: Community catalog of plugins for
[mobile-agent-harness](https://github.com/Aelindra/mobile-agent-harness).
Plugins are self-contained strategy and orchestration packages for specific
use cases on Android apps. Scenario directories above form the taxonomy; all content is
contributed. Intended for low-frequency personal use on your own device. Risk
labels are mandatory (`low` / `account` / `tos-grey`). Takedown policy: report
an issue and the entry is removed. No anti-detection, no account abuse, no root
guidance.
