## 单行溢出省略

```css
overflow: hidden;
text-overflow: ellipsis;
white-space: nowrap;
```

## 多行溢出省略

```css
display: -webkit-box;
-webkit-line-clamp: 3; 
-webkit-box-orient: vertical;
overflow : hidden;
text-overflow: ellipsis; 
```

## 弹性居中

```css
display: flex;
justify-content: center;
align-items: center;
```

## 计数器

```css
ul {
  counter-reset: count;
}
li::before {
  counter-increment: count;
  content: counter(count) ": ";
}
```

## 选择元素高亮

```csss
/*Webkit,Opera9.5+,Ie9+*/
::selection {
	color: #fff;
  background: #14B9C8;
}
/*Mozilla Firefox*/
::-moz-selection {
	color: #fff;
  background: #14B9C8;
}
```

## 模糊差值

```css
.wrapper{
  filter: contrast(30);
}
.wrapper>div{
  filter: blur(20px);
}
```

