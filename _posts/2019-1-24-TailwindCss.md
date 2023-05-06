## 一、简介
一个内置了多个基础css样式类，可以减少和规范css代码的库
官网：Tailwind CSS - Rapidly build modern websites without ever leaving your HTML.
中文：Tailwind CSS - 无需离开您的HTML，即可快速建立现代网站。

## 二、为什么你需要它
1. 项目中同样的css代码很多份，造成代码冗余，统一修改css代码时困难 
原因：
  1. 多个同学开发一个项目，没有很好沟通 
  2. 没有意识去进行复用，对于css不知道如何复用，没有标杆，不知道怎么设立标杆，而tailwindcss不需要你自己去设立标杆，直接把它作为标杆，在不同的项目中也能游刃有余的切换
  3. 使用css module，只负责自己的页面

2. 无需思考css类名：这些类名在当前文件下看上去合适，放在整体未必合适

3. 使用变量，样式更加规范，比如视觉规范只允许出现2/4/8/16px的边距，且能够快速实现自适应布局

4. 可以写组件么，但是组件的局限在哪，组件必须是比较确定的，就比如说你写一个title组件，自带了xx样式，别人可以用，但是如果这个时候需要加一些icon，背景颜色，可能这个title就不太好用了或者需要改api，这样会引起所有使用该组件的修改，组件引用比css成本大点
## 三、安装使用
官网安装流程：Installation: Using PostCSS - Tailwind CSS

推荐使用postcss的方式接入：可以对代码进行扫描，只添加使用到的css类，减少代码量

适用于eden 2.x，eden 2.x也是支持postcss.config的

## 四、详细介绍
```
@tailwind base; // 浏览器默认样式重置，和reset-css功能一致
@tailwind components; // 组件，默认是空的
@tailwind utilities; // 基础样式类
module.exports = {
  content: ["./src/**/*.{html,js}"], // 需要扫描的文件目录
  theme: {    // 自定义样式配置 https://tailwindcss.com/docs/configuration
    extend: {},
  },
  plugins: [], // 插件让您注册新的样式，让 Tailwind 使用 JavaScript 代替 CSS 注入用户的样式表。 https://tailwindcss.com/docs/plugins
}
```
### 自定义样式配置example
```
module.exports = {
  content: ['./src/**/*.{html,js}'],
  theme: {
    colors: {
      'blue': '#1fb6ff',
      'purple': '#7e5bef',
      'pink': '#ff49db',
      'orange': '#ff7849',
      'green': '#13ce66',
      'yellow': '#ffc82c',
      'gray-dark': '#273444',
      'gray': '#8492a6',
      'gray-light': '#d3dce6',
    },
    fontFamily: {
      sans: ['Graphik', 'sans-serif'],
      serif: ['Merriweather', 'serif'],
    },
    extend: {
      spacing: {
        '8xl': '96rem',
        '9xl': '128rem',
      },
      borderRadius: {
        '4xl': '2rem',
      }
    }
  },
}
```
接下来就能和官网演示的一样，愉快的写css了
### 自定义样式
由于 tailwind 包含的是基础样式类，现有的 utility，base，component 不足以满足所有的场景；
而使用 @layer 指令，Tailwind 将自动将这些样式移动到 @tailwind base， @tailwind utility，@tailwind component 的位置
比如我们要做一个按钮组件，提炼对应的样式：
```
@layer components {
  .btn-blue {
    @apply bg-blue-500 text-white font-bold py-2 px-4 rounded;
  }
  .btn-blue:hover {
    @apply bg-blue-700;
  }
}
```
### Tailwind 语法糖
先写个简单的例子：
```
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  h1 {
    @apply text-2xl;
  }
}

@layer components {
  .btn {
    @apply bg-gray-500 text-white font-bold;
    @apply py-2 px-4 rounded;
    font-family: Georgia, Cambria, "Times New Roman", Times, serif;
    background-color: theme("colors.blue-500");
  }
}
// hover:.filter-none 
@layer utilities {
  @variants hover, focus {
    .filter-none {
      filter: none;
    }
  }
  // bg-opacity-none:md bg-opacity-none:lg
  @responsive {
    .bg-opacity-none {
      @apply opacity-0;
    }
  }
}
```

- @tailwind：使用 @Tailwind 指令将 Tailwind 的 base, components, utilities 插入到 CSS 中。
- @apply：使用 @apply 将任何样式内联到您自己的自定义 CSS 中。
- @layer：使用 @layer 指令告诉 Tailwind 属于一组自定义样式的 “块”。在 base, components, utilities 有效。
- @variants：您可以通过在 @variant 指令中包装它们的定义来生成响应式、hover, focus, active 和其他伪类。
- @responsive：通过将 class 的定义包装在 @responsive 指令中，您可以生成自己 class 的响应式。
- theme()：使用 theme() 函数和 . 访问你的 Tailwind 配置值。

## 五、实践
1.  不要使用字符串连接来创建类名
在生产优化时，purgeCss 是通过正则匹配 html 类的属性，并不会解析 html，purgeCss 将不知道保存这些通过字符串连接的类。
 那么在项目中，一定不要字符串连接类名。
```
// 错误
<div class="text-{{ error ? 'red' : 'green' }}-600"></div>
// 正确
<div class="{{ error ? 'text-red-600' : 'text-green-600' }}"></div>
```
2. 一个元素上的类名建议小于四个，太多的话考虑自定义组件、类
3. Vscode添加Tailwind CSS IntelliSense插件，智能提示类名
4. Translate css to tailwind
我们在项目之初就应用 TailwindCss，会有比较丝滑的体验。而对于老项目迁移到 TailwindCss，这可能是一个比较难受的过程，这里可以介绍一个在线工具，可以根据你的 tailwind.config.js 文件，将 CSS 转化为 TailwindCss 类名。
```
.logo {
    margin-bottom: 1.6rem;
    background: url('logo.svg') no-repeat;
    display: flex;
    justify-content: center;
}

.footer {
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-direction: row-reverse;
    padding: 2.4rem 3rem;
    border-top: 1px solid #fff5f5;
}
```
转化后：
```
/* ℹ️ Base selector: .logo *//* ✨ TailwindCSS: "bg-no-repeat flex justify-center mb-6" *//* ⚠️ Some properties could not be matched with Tailwind classes. Use @apply to extend a CSS rule: */.logo {
@apply bg-no-repeat flex justify-center mb-6;
background-image: url(logo.svg);
}

/* ℹ️ Base selector: .footer *//* ✨ TailwindCSS: "border-red-100 border-solid border-t flex flex-row-reverse items-center justify-between py-10 px-12 w-full" */
```

5. 一些常用的组件
```
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
    .row-center-center {
      @apply flex items-center justify-center
    }
    .row-center-between {
      @apply flex items-center justify-between
    }
    .row-end-center {
      @apply flex items-end justify-center
    }
    // ... ...
}

// ... ...
```
