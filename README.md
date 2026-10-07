# 商品后台上架缺项检查

把用户指定商品编辑页的可见资料整理成规格、图文、物流、售后四类的缺项、冲突和人工补充表。输入是商品编辑页、可选商家规则与用户指定的浏览器环境；结果保留可见值和来源。看不到的字段留空，没给规则就不猜，全程不保存、不提交、不上架。

![商品后台上架缺项检查推广图](assets/promo.png)

## 适用与交付

适用于上架前人工复核一个商品编辑页，尤其是图片、运费模板和售后承诺分散在不同区块时。单次最多输出 100 行；超出会提示分批。

输出 JSON 的每一行包含：分类、字段、状态（已见/缺项/冲突/待确认）、可见值、来源与建议的人工动作。页面没有读到字段时标为“待确认”；只有字段明确为空时才标为“缺项”。每个字段只保留一个状态，没有可见证据的字段不会写成“已完成”。

## 安装与自测

```bash
npx skills add xxjrq/shop-listing-missing-fields-free
npm run self_test
```

离线验证不访问浏览器：

```bash
node scripts/check-listing.mjs --offline-page fixtures/success-page.json --rules fixtures/rules.json
```

`fixtures/` 是明确标注的离线页面资料模拟，分别覆盖有资料和资料缺失两种情况，不是在线采集结果。

## 浏览器前置条件与安全边界

真实页面检查需要安装 [Easy WebBridge（GitHub）](https://github.com/xxjrq/easy-webbridge)，GitHub 不可用时可用 [Gitee 备用地址](https://gitee.com/xxjrq/easy-webbridge)。运行时只使用用户指定的 `browserId`，每次任务新建且只管理自身的 session/标签组；不会广播给其他账号，也不接管或关闭原有页面。

运行时需提供用户指定的 `browserId` 和编辑页 `--page-url`。脚本先读取 Easy WebBridge CLI help，再创建自有 session 和标签组、通过 `list_tabs` 确认页面归属。字段文字来自 DOM 逐节点可见性读取，不把整页快照文本当作证据；节点必须位于视口内且不能被隐藏或遮挡。控件需有明确标签关联；下拉框取已选项文案，单选/复选框只取已勾选项，不按序号或邻近文本猜配。来源只保留页面标题和移除 query/fragment 的 URL。未读字段与“未确认”“未填写”“请选择”等占位提示要求核实，不断言缺项；明确空字段的商家规则、action 和冲突说明会保留，重复动态字段合并成一行。结束时只关闭本次创建且已核对归属的 session。登录、验证码、风控、截断或无效 JSON 会停止且不输出缺项表。只读模式不填充、不保存、不发布。即使另获草稿填充授权，也不得点击最终发布或发送按钮。

## 参考与许可

业务用途参考 [ERPNext](https://github.com/frappe/erpnext) 与其 [项目说明](https://github.com/frappe/erpnext/blob/develop/README.md)，未复制外部代码或原文实现。仓库采用 [MIT License](LICENSE)。
