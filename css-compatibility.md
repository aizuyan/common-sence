## box-sizing 兼容性
- box-sizing 是 CSS3 的属性，IE >= 8 开始支持。
- 但在 IE8 中，值为 border-box 时，不能用 min-*、max-* 一起使用，因为 IE8 对 min-*、max-* 的解析，仍是作用于 content-box，不受 box-sizing 属性控制

## 盒模型
- IE5 采用IE盒模型
- IE6、7 标准模式采用 W3C 盒模型
- IE >= 8 借助 box-sizing，重新提供了对 IE 盒模型的支持
