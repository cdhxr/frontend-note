---
date: 2025-07-20
tags:
  - nextjs
---
# 从Google font中引入字体

1. 创建fonts.ts文件
2. 在该文件中导入字体，并导出字体

```ts
import { Inter, Lusitana } from 'next/font/google';
 
export const inter = Inter({ subsets: ['latin'] });
 
export const lusitana = Lusitana({
  weight: ['400', '700'],
  subsets: ['latin'],
});
```

`next/font/google`是集成在NextJS框架中的模块，可以方便的使用GoogleFonts中的字体

从 `next/font/google` 模块中导入 `Inter` 字体——这将是你主要的字体。然后，指定你想加载的子集。

也可以导入字重`weight`等等属性选项

3. 在标签中应用字体 

- 先从fonts文件导入字体，
- 在classname内加上`${字体名.className}`

```html
<p className={`${lusitana.className} text-xl text-gray-800 md:text-3xl md:leading-normal`} >
```

# 使用内置的Image组件图片优化

和普通的image相比：

- 在图像加载时自动防止布局偏移。
- 调整图像大小，避免向视口较小的设备发送大型图像。
- 默认情况下图片懒加载（图片在进入视口时加载）。
- 在浏览器支持的情况下，以 WebP 和 AVIF 等现代格式提供图片。

```tsx
...
import Image from 'next/image';
 
export default function Page() {
  return (
    // ...
      <Image
        src="/hero-desktop.png"
        width={1000}
        height={760}
        className="hidden md:block"
        alt="Screenshots of the dashboard project showing desktop version"
      />
    //...
  );
}
```

为了避免布局偏移，建议设置 `width` 和 `height` ，并是宽高比与原图相同

没有尺寸的图片和网页字体是导致布局偏移的常见原因，因为浏览器需要下载额外的资源。

# 补充资料

关于这些主题还有更多要学习的内容，包括优化远程图像和使用本地字体文件。如果您想深入了解字体和图像，请参阅：

- [Image Optimization Docs  图片优化文档](https://nextjs.org/docs/app/building-your-application/optimizing/images)
- [Font Optimization Docs  字体优化文档](https://nextjs.org/docs/app/building-your-application/optimizing/fonts)
- [Improving Web Performance with Images (MDN)  
    使用图像提升网页性能（MDN）](https://developer.mozilla.org/en-US/docs/Learn/Performance/Multimedia)
- [Web Fonts (MDN)  网络字体（MDN）](https://developer.mozilla.org/en-US/docs/Learn/CSS/Styling_text/Web_fonts)
- [How Core Web Vitals Affect SEO  
    核心网页指标如何影响 SEO](https://vercel.com/blog/how-core-web-vitals-affect-seo)
- [How Google handles JavaScript throughout the indexing process  
    谷歌如何在索引过程中处理 JavaScript](https://vercel.com/blog/how-google-handles-javascript-throughout-the-indexing-process)