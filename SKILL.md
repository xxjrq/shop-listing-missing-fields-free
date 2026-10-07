---
name: shop-listing-missing-fields-free
description: 使用用户指定的商品编辑页、商家规则和浏览器环境，按规格、图文、物流、售后整理可见字段的缺项、冲突与人工补充表。不可见字段明确留空，未提供规则不虚构要求；全程默认只读，不保存、不提交、不上架，交付结果保留每条资料来源与处理边界。
---

# 商品后台上架缺项检查

把本人指定商品编辑页中的可见资料，变成能直接人工补齐的缺项与冲突表；不会擅自补全规则，更不会保存或上架。

输入为用户指定的 `browserId`、商品编辑页 URL 及可选商家规则。输出为最多 100 行的规格、图文、物流、售后检查表，每行包含状态、可见值、来源和人工动作。超过 100 行时先分批；页面未读到的字段标“待确认”，明确空值才标“缺项”，每个字段只有一个状态。

## 运行边界

默认只读。先确认目标 `browserId` 及商品页面属于用户授权范围；不接管已有页面、不关闭用户页面。登录、验证码、风控或账号安全验证出现时暂停，不绕过。即使用户授权草稿填充，也绝不点击最终发布、发送或上架。

本 Skill 依赖可安装的 [Easy WebBridge](https://github.com/xxjrq/easy-webbridge)；GitHub 无法访问时使用 [Gitee 备用地址](https://gitee.com/xxjrq/easy-webbridge)。每次任务只选择一个用户指定的 `browserId`，创建 `shop-listing-missing-fields-free-<run-id>` session 与一个任务标签组，复用其中标签；结束时仅关闭本任务创建的 session。不得广播至所有账号或环境。

## 执行方式

1. 将已安装 Easy WebBridge CLI 的路径以 `EASY_WEBBRIDGE_CLI` 传入；脚本会先运行 CLI help，再用用户传入的 `--page-url` 创建专属 session，使用 `list_tabs` 确认 `navigate` 返回的 tab 属于本任务，再以 `snapshot` 获取页面身份和加载状态。字段文字来自浏览器 DOM 的逐节点可见性读取；不会把整页 `result.text` 当字段证据。
2. 登录、验证、快照截断或 JSON 包装无效时停止且不生成缺项表；`finally` 仅在 `list_tabs` 核对到本次导航标签后关闭自己的 session。每条浏览器结果保留快照标题及已移除 query/fragment 的 URL，不以关闭后失效的 tab 编号充当来源；扫描每个视口前后都复核当前 URL，页面身份发生变化时丢弃整份结果。文字节点、字段标签、字段值节点都必须有视口内文本区域，且不能被透明、隐藏或遮挡；控件还须有 `label[for]`、包裹标签或 `aria-labelledby` 明确关联。文本框、contenteditable 和自定义下拉框读取当前可见值，原生下拉框读取所选项的可见文案，复选框/单选框只读取已勾选项，并用其明确的字段组标题映射到颜色、退货承诺等字段；相邻文本和无关联控件绝不按顺序猜配。同名多 SKU 值分别保留；只要某项为空或仍是提示状态，该字段就标为待确认。规格、图文、物流、售后分区中其他明确标注的字段也进入检查表，不限于内置字段和商家规则。未读到的字段标“待确认”，只把明确空值标“缺项”；“未确认”“未填写”“请选择”“请填写”“请上传”“暂无运费模板”等提示仍为待确认，人工动作要求核实而不称其缺失。明确空字段的商家规则和 action 会保留；规则冲突行也保留商家要求。重复动态规则按字段合并为一行和一个状态。来源 URL 的查询参数与片段会被移除。商家规则可选；未给定时不产生臆测规则。
3. 运行检查器或使用离线资料验证：

```bash
EASY_WEBBRIDGE_CLI=/path/to/easy-webbridge/cli/easy-webbridge.mjs node scripts/check-listing.mjs --browser-id "$BROWSER_ID" --page-url "$PRODUCT_EDIT_URL"
node scripts/check-listing.mjs --offline-page fixtures/success-page.json --rules fixtures/rules.json
npm run self_test
```

离线 fixtures 仅为页面资料模拟，绝不是在线采集或平台测试结果。

## 来源与许可证

本 Skill 的业务场景仅参考 [ERPNext](https://github.com/frappe/erpnext) 及其 [README](https://github.com/frappe/erpnext/blob/develop/README.md)，没有复制其代码或文字实现；本仓库内容以 MIT 许可证发布。
