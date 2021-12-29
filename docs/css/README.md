## CSS reset

```css
html {
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  text-size-adjust: 100%;
}
body {
  margin: 0 auto;
  color: #333;
  line-height: 1.5;
  background: #ffffff;
  overflow-x: hidden;
  -webkit-overflow-scrolling: touch;
}
html,
body {
  position: relative;
  width: 100%;
  height: 100%;
  margin: 0 auto;
  -webkit-font-smoothing: antialiased;
  -webkit-user-select: none;
  font-size: 62.5%;
  font-family: "Helvetica";
}
html * {
  -webkit-tap-highlight-color: transparent;
}
.box-sizing {
  box-sizing: border-box;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
}
a {
  color: #333;
  text-decoration: none;
}
a:hover {
  text-decoration: none;
}
img,
textarea {
  border: none;
  vertical-align: middle;
}
body,
button,
dd,
dl,
dt,
form,
h1,
h2,
h3,
h4,
h5,
h6,
html,
input,
li,
ol,
p,
td,
th,
ul {
  margin: 0;
  padding: 0;
  font-family: Helvetica, Tahoma, Arial, "Hiragino Sans GB,STXihei", "Microsoft YaHei", "SimSun", "Heiti", sans-serif;
}
textarea,
input {
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}
textarea::-webkit-input-placeholder,
input::-webkit-input-placeholder {
  color: #999;
}
textarea:focus::-webkit-input-placeholder,
input:focus::-webkit-input-placeholder {
  color: #999;
}
input {
  border: 0;
  outline: 0;
  -webkit-appearance: none;
  background-color: transparent;
  color: inherit;
  text-align: right;
}
ul,
li,
ol {
  list-style: none;
}
a img {
  border: 0;
}
a {
  text-decoration: none;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}
/* ============================================================
   um-flex：定义布局为盒模型
   fl-v：盒模型垂直布局
   um-ver：子元素垂直居中
   um-center：子元素水平居中
   um-between：子元素两端对齐
   （以上都用于父元素）
   （以下用于子元素，父元素需dispay：flex；）
   um-flex-item:子元素占据剩余的空间
   um-grow:为1时等分剩余空间。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。
   兼容性：ios 4+、android 2.3+、winphone8+
   ============================================================ */
.um-flex {
  display: -webkit-box;
  display: -webkit-flex;
  display: flex;
}
.page-bd .um-flex {
  transition: all 0.3s ease 0s;
  display: -webkit-flex;
  display: -webkit-box;
  display: flex;
}
.um-flex-item {
  -webkit-box-flex: 1;
  -webkit-flex: 1;
  flex: 1;
}
.page-bd .um-flex-item {
  -webkit-flex: 1 1 0;
  flex: 1 1 0;
}
.um-ver {
  -webkit-box-align: center;
  -webkit-align-items: center;
  -ms-flex-align: center;
  align-items: center;
}
.fl-v {
  -webkit-box-orient: vertical;
  -webkit-flex-direction: column;
  -ms-flex-direction: column;
  flex-direction: column;
}
.um-center {
  -webkit-box-pack: center;
  -webkit-justify-content: center;
  -ms-flex-pack: center;
  justify-content: center;
}
.um-between {
  -webkit-box-pack: justify;
  -webkit-justify-content: space-between;
  -ms-flex-pack: justify;
  justify-content: space-between;
}
/*字体大小 生成22/24/26/28/30/32/40/60/80像素对应的rem字体 */
.r11 {
  font-size: .29rem!important;
  line-height: .29rem;
}
.r12 {
  font-size: .32rem!important;
  line-height: .32rem;
}
.r13 {
  font-size: .35rem!important;
  line-height: .35rem;
}
.r14 {
  font-size: .37rem!important;
  line-height: .37rem;
}
.r15 {
  font-size: .4rem!important;
  line-height: .4rem;
}
.r16 {
  font-size: .43rem!important;
  line-height: .43rem;
}
.r17 {
  font-size: .45rem!important;
  line-height: .45rem;
}
.r18 {
  font-size: .48rem!important;
  line-height: .48rem;
}
.r19 {
  font-size: .51rem!important;
  line-height: .51rem;
}
.r20 {
  font-size: .53rem!important;
  line-height: .53rem;
}
.cr:after {
  content: '';
  clear: both;
  float: none;
  zoom: 1;
  overflow: hidden;
  display: block;
}
.hide {
  display: none;
}
[v-cloak] {
  display: none;
}
```