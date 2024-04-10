# if else 代码优化

### 优化前的代码

```vue
 if (type == '3') {
          getDksDev(id).then(response => {
            this.form1 = response.data
            this.open1 = true
            this.title = '查看戴克森动力设备'
          })
        } else if (type == '4') {
          getDksDev(id).then(response => {
            this.form2 = response.data.instant
            this.open2 = true
            this.title = '查看戴克森动力设备'
          })
        } else if (type == '5') {
          getDksDev(id).then(response => {
            var list = response.data.instant_data
            for (let i = 0; i < list.length; i++) {
              if (list[i].status == '7') {
                //7 正常 8 告警 15 正常 16 告警 5 网络离线 6 设备离线
                list[i].status = '正常'
              } else if (list[i].status == '8') {
                list[i].status = '告警'
              } else if (list[i].status == '15') {
                list[i].status = '正常'
              } else if (list[i].status == '16') {
                list[i].status = '告警'
              } else if (list[i].status == '5') {
                list[i].status = '网络离线'
              } else if (list[i].status == '6') {
                list[i].status = '设备离线'
              }
            }
            this.devList1 = list
            this.open3 = true
            this.title = '查看戴克森动力设备'
          })
        } else if (type == '6') {
          getDksDev(id).then(response => {
            var list = response.data.instant_data
            for (let i = 0; i < list.length; i++) {
              if (list[i].status == '15') {
                list[i].status = '正常'
              } else if (list[i].status == '16') {
                list[i].status = '告警'
              }
            }
            this.devList2 = list
            this.open4 = true
            this.title = '查看戴克森动力设备'
          })
        }
```

### 优化后的代码

```vue
getDksDev(id)
          .then(response => {
            this.form1 = response.data
            // 7 正常 8 告警 15 正常 16 告警 5 网络离线 6 设备离线
            const obj = {
              3: () => {
                this.form1 = response.data
                this.open1 = true
              },
              4: () => {
                this.form2 = response.data.instant
                this.open2 = true
              },
              5: () => {
                var list = response.data.instant_data
                for (let i = 0; i < list.length; i++) {
                  const status = {
                    7: '正常',
                    8: '告警',
                    15: '正常',
                    16: '告警',
                    5: '网络离线',
                    6: '设备离线'
                  }
                  list[i].status = status[list[i].status]
                }
                this.devList1 = list
                this.open3 = true
              },
              6: () => {
                var list = response.data.instant_data
                for (let i = 0; i < list.length; i++) {
                  if (list[i].status == '15') {
                    list[i].status = '正常'
                  } else if (list[i].status == '16') {
                    list[i].status = '告警'
                  }
                }
                this.devList2 = list
                this.open4 = true
              }
            }
            obj[type]()
            this.$nextTick(() => {
              this.title = '查看戴克森动力设备'
            })
          })
          .catch(err => {
            this['open' + (type - 2)] = true
          })
```

按照之钱的逻辑写，每个请求后面都要写重复的.catch 。代码应该是七八十行优化后的代码只有50多行