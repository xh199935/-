dom

```vue
<div
                  class="circle"
                  :style="{
                    'color': `rgb(${item.color})`,
                    'box-shadow': `0 0 20px 10px rgba(${item.color}, 0.7)`
                  }"
                >
                  {{ item.total }}
                </div>
```

style

```scss
.circle {
          width: 100px;
          height: 100px;
          border-radius: 50%;
          backdrop-filter: blur(10px);
          text-align: center;
          line-height: 100px;
          margin: 23px auto 0;
          font-size: 40px;
        }
```

![image-20240321145126540](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240321145126540.png)