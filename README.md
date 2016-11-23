## getting started

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
            'right': '5%',
            'bottom': '10%'
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

## more