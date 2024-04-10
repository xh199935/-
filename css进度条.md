# dom

```vue
<div class="progress-bar">
                  <div
                    class="schedule"
                    :style="{
                      width:
                        (videoMonitoring.online / videoMonitoring.total) * 100 +
                        '%'
                    }"
                  ></div>
                </div>
```

# style

```scss
.progress-bar {
          width: 100%;
          height: 15px;
          border-radius: 10px;
          background-color: #ddd;
          margin: 10px 0;
        }
        .schedule {
          height: 15px;
          background: #95c3d4;
          border-radius: 10px;
        }
```

