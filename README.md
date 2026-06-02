# 食品过敏原检测 Web 应用

基于 OpenFoodFacts API 的过敏原检测应用，单文件 HTML 实现，无需服务器。

## 功能特性

✅ **条码扫描**：调用手机摄像头实时扫描商品条码（QuaggaJS）
✅ **商品查询**：支持条形码和商品名称两种查询方式
✅ **模糊搜索**：实时搜索建议，输入时自动提示匹配商品
✅ **条码验证**：EAN-8/EAN-13 校验位验证，防止扫描错误
✅ **过敏原管理**：预设 20 种常见过敏原 + 自定义添加
✅ **智能检测**：一键识别食品中是否含有过敏原
✅ **JSONP 跨域**：无需代理配置，直接调用 OpenFoodFacts API
✅ **本地存储**：所有数据存储在浏览器 localStorage

## 快速开始

### 方式一：直接打开（推荐）

双击 `index.html` 文件，在浏览器中打开即可使用。

### 方式二：本地服务器

```bash
# 使用 Python
python -m http.server 8080

# 或使用 Node.js
npx http-server -p 8080
```

访问 http://localhost:8080

## 使用说明

### 1. 添加过敏原

**快速添加**：点击预设标签（荞麦、花生、乳糖等）
**自定义添加**：输入框输入过敏原名称，点击"添加"或按回车键

### 2. 查询食品

**方式一：条码查询**
- 输入条形码数字（如：3017620422003）
- 系统自动验证条码合法性
- 点击"查询"按钮

**方式二：扫码查询**
- 点击"开启扫码"
- 授权摄像头权限
- 将商品条码对准扫描框
- 自动识别并查询（自动验证条码）

**方式三：商品名查询**
- 输入商品名称（如：Nutella）
- 支持模糊搜索，输入时自动显示建议
- 点击建议项或按回车查询

### 3. 查看结果

- ✅ 绿色：未检测到过敏原，可以放心食用
- ⚠️ 红色：含有过敏原，高亮显示具体成分

## 技术特点

### JSONP 跨域解决方案

无需配置代理服务器，使用 JSONP 方式直接调用 OpenFoodFacts API：

```javascript
// JSONP 请求示例
const callbackName = 'jsonp_callback_' + Date.now();
const url = `${API_BASE}/product/${barcode}.json?callback=${callbackName}`;

window[callbackName] = function(data) {
    // 处理返回数据
    delete window[callbackName];
};

const script = document.createElement('script');
script.src = url;
document.body.appendChild(script);
```

### 条码验证算法

支持 EAN-8、EAN-13、UPC-A、UPC-E 等主流条码格式：

```javascript
// EAN-13 校验位计算
const checkDigit = (10 - (digits.reduce((sum, d, i) => {
    return sum + d * (i % 2 === 0 ? 1 : 3);
}, 0) % 10)) % 10;
```

### 模糊搜索实现

- 本地热门商品匹配（Nutella、Coca Cola 等）
- 远程 API 实时搜索建议
- 防抖优化（300ms 延迟）

## OpenFoodFacts API

- 官网：https://world.openfoodfacts.org
- 文档：https://wiki.openfoodfacts.org/API
- 免费：无需密钥，无调用限制
- 数据：全球千万级食品数据库
- 跨域：支持 JSONP 调用

### API 示例

```bash
# 条码查询（JSONP）
GET https://world.openfoodfacts.org/api/v2/product/3017620422003.json?callback=callback

# 商品名搜索（JSONP）
GET https://world.openfoodfacts.org/api/v2/search?search_terms=Nutella&page_size=10&json=1&callback=callback
```

## 测试示例

### 测试条码

- `3017620422003` - Nutella 榛子酱
- `7622210449283` - Milka 巧克力
- `5000159484691` - Coca Cola
- `7613035023577` - Lindt 巧克力
- `4008403004215` - Ritter Sport

### 测试商品名

- Nutella
- Coca Cola
- KitKat
- Milka
- Oreo

## 浏览器兼容性

- ✅ Chrome 60+
- ✅ Firefox 55+
- ✅ Safari 11+
- ✅ Edge 79+

移动端完全支持，可在手机浏览器中使用摄像头扫描功能。

## 项目结构

```
allergen-detector/
└── index.html    # 单文件 HTML（包含 CSS + JS）
```

## 常见问题

**Q: 扫码无法启动摄像头？**
A: 请使用 HTTPS 或 localhost 访问，并在浏览器设置中允许摄像头权限。

**Q: 查询提示"暂无该商品信息"？**
A: 该商品未收录在 OpenFoodFacts 数据库中，建议尝试其他关键词或条码。

**Q: 条码验证失败？**
A: 检查条码是否清晰，扫描时保持稳定。系统会自动校验 EAN 码的校验位。

**Q: 如何部署到生产环境？**
A: 直接上传 index.html 到任意静态服务器即可，无需配置代理或后端。

**Q: JSONP 和 CORS 有什么区别？**
A: JSONP 不受浏览器同源策略限制，无需配置代理，但只支持 GET 请求。本应用使用 JSONP 解决跨域问题。

## 技术栈

- **前端**：原生 HTML/CSS/JavaScript（单文件）
- **条码扫描**：QuaggaJS
- **数据源**：OpenFoodFacts API（JSONP）
- **存储**：localStorage
- **跨域方案**：JSONP

## License

MIT
