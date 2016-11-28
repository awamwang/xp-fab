# getting started

> fab是个vue组件

安装
```
npm install --save xp-fab
```
引用
```
import FloatButtons from 'xp-fab'
```
使用
```
<float-buttons :links="fabLinks"></float-buttons>
fabLinks: [
          {
            'url': null,
            'bgcolor': 'red',
            'icon': '+',
            'width': '40px',
            'height': '40px',
            'iconSize': '30px',
            'right': 15,
            'bottom': 15
          },
          {
            'url': '#/moments/user/001228',
            'bgcolor': 'orange',
            'title': '很长的发生的结果卡机',
            'icon': '+'
          },
          {
            'url': '#/member',
            'bgcolor': 'yellow',
            'icon': '+'
          },
          {
            'url': '#/member',
            'bgcolor': 'yellow',
            'icon': '+'
          },
          {
            'url': '#/member',
            'bgcolor': 'yellow',
            'icon': '+'
          }
        ]
```

# caveat

+ right/bottom设定初始位置时，只能使用数字类型

# more

# remark

+ 图标（icon）使用了非安全的{{{}}}解析，当其用户可配时要考虑XSS攻击[](http://vuejs.org.cn/api/#v-html)

# release
+ 0.2.1 添加可拖动特性