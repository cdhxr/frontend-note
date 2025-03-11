
Description:
	尝试用CSS来导入字体文件，最终由于对于文件链接写法不正确，导致失败

目录结构：
```
LIFE SYSTEM
├── assets
│   ├── fonts
│   │   ├── ADOBECLEAN-BOLDIT.ttf
│   │   ├── LAMORERegular.woff2
│   │   └── SubGalacticaRegular.otf
│   └── others
├── images
├── scripts
├── styles
│   └── typography.css
└── index.html
```

Before:`'src: url('/assets/fonts/ADOBECLEAN-BOLDIT.ttf') format('opentype');'

After:`'src: url('../assets/fonts/ADOBECLEAN-BOLDIT.ttf') format('opentype');'

Before:`<link rel="stylesheet" href="styles/typography.css">

After:`<link rel="stylesheet" href="styles/typography.css">