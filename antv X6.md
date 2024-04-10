# antv官网

https://antv.antgroup.com/

# 功能

工位选择、工位重组



# 初始化

```js
import { Graph, Shape } from '@antv/x6'
import {
  custom,
  saveElement,
  template,
  templateList,
  testDelete, testSaveElement
} from '@/api/zhgw/spacePlanningManagement'

import { Clipboard } from '@antv/x6-plugin-clipboard'
import { History } from '@antv/x6-plugin-history'
import { Keyboard } from '@antv/x6-plugin-keyboard'
import {Message, MessageBox} from 'element-ui'
import { Selection } from '@antv/x6-plugin-selection'
import { Snapline } from '@antv/x6-plugin-snapline'
import { Stencil } from '@antv/x6-plugin-stencil'
import { Transform } from '@antv/x6-plugin-transform'
import app from '../../main'
import { computedImageSrc } from '@/utils/index'
import store from '@/store/index.js'

let graph
function destroyGraphInstance() {
  if (graph) {
    graph.dispose()
    graph = null
  }
}
var multiple = 2
const initX6 = (dom, bg, areaData, rate) => {
  multiple = rate
  destroyGraphInstance()
  // #region 初始化画布
  graph = new Graph({
    container: dom,
    grid: true,
    resizable: false, // 禁止缩放
    width: 1260,
    height: 740,
    // width: areaData.width*2,
    // height: areaData.height*2,
    background: {
      // width: areaData.width*2,
      // height: areaData.height*2,
      position: 'left left',
      image: bg.bgUrl,
      repeat: 'no-repeat', // 设置背景图片不重复
      size: bg.width * multiple + 'px ' + bg.height * multiple + 'px' // 设置背景图片大小为100%
    },
    mousewheel: {
      enabled: true,
      zoomAtMousePosition: true,
      modifiers: 'ctrl',
      minScale: 0.5,
      maxScale: 3
    },
    scroller: {
      enabled: false // 禁止缩放
    },
    connecting: {
      router: 'manhattan',
      connector: {
        name: 'rounded',
        args: {
          radius: 8
        }
      },
      anchor: 'center',
      connectionPoint: 'anchor',
      allowBlank: false,
      snap: {
        radius: 20
      },
      createEdge() {
        return new Shape.Edge({
          attrs: {
            line: {
              stroke: '#A2B1C3',
              strokeWidth: 2,
              targetMarker: {
                name: 'block',
                width: 12,
                height: 8
              }
            }
          },
          zIndex: 0
        })
      },
      validateConnection({ targetMagnet }) {
        return !!targetMagnet
      }
    },
    highlighting: {
      magnetAdsorbed: {
        name: 'stroke',
        args: {
          attrs: {
            fill: '#5F95FF',
            stroke: '#5F95FF'
          }
        }
      }
    }
  })
  // #endregion
  // graph.zoomTo(0.5)
  graph.setGridSize(3)
  // #region 使用插件
  graph
    .use(
      new Transform({
        resizing: true,
        rotating: true
      })
    )
    .use(
      new Selection({
        rubberband: true,
        showNodeSelectionBox: true
      })
    )
    .use(new Snapline())
    .use(new Keyboard())
    .use(new Clipboard())
    .use(new History())
  // #endregion

  // #region 初始化 stencil
  const stencil = new Stencil({
    title: '',
    target: graph,
    stencilGraphWidth: 200,
    stencilGraphHeight: 700,
    collapsable: false,
    groups: [
      {
        title: '系统设计图',
        name: 'group2',
        graphHeight: 700,
        collapsable: true,
        layoutOptions: {
          rowHeight: 70
        }
      }
    ],
    layoutOptions: {
      columns: 2,
      columnWidth: 80,
      rowHeight: 55
    }
  })
  document.getElementById('stencil').appendChild(stencil.container)
  // #endregion

  // 控制连接桩显示/隐藏
  const showPorts = (ports, show) => {
    for (let i = 0, len = ports.length; i < len; i += 1) {
      ports[i].style.visibility = show ? 'visible' : 'hidden'
    }
  }
  graph.on('node:mouseenter', () => {
    const container = document.getElementById('graph-container')
    const ports = container.querySelectorAll('.x6-port-body')
    showPorts(ports, true)
  })
  graph.on('node:mouseleave', () => {
    const container = document.getElementById('graph-container')
    const ports = container.querySelectorAll('.x6-port-body')
    showPorts(ports, false)
  })
  // #endregion

  // #region 初始化图形
  const ports = {
    groups: {
      top: {
        position: 'top',
        attrs: {
          circle: {
            r: 4,
            magnet: true,
            stroke: '#5F95FF',
            strokeWidth: 1,
            fill: '#fff',
            style: {
              visibility: 'hidden'
            }
          }
        }
      },
      right: {
        position: 'right',
        attrs: {
          circle: {
            r: 4,
            magnet: true,
            stroke: '#5F95FF',
            strokeWidth: 1,
            fill: '#fff',
            style: {
              visibility: 'hidden'
            }
          }
        }
      },
      bottom: {
        position: 'bottom',
        attrs: {
          circle: {
            r: 4,
            magnet: true,
            stroke: '#5F95FF',
            strokeWidth: 1,
            fill: '#fff',
            style: {
              visibility: 'hidden'
            }
          }
        }
      },
      left: {
        position: 'left',
        attrs: {
          circle: {
            r: 4,
            magnet: true,
            stroke: '#5F95FF',
            strokeWidth: 1,
            fill: '#fff',
            style: {
              visibility: 'hidden'
            }
          }
        }
      }
    },
    items: [
      {
        group: 'top'
      },
      {
        group: 'right'
      },
      {
        group: 'bottom'
      },
      {
        group: 'left'
      }
    ]
  }

  Graph.registerNode(
    'custom-rect',
    {
      inherit: 'rect',
      width: 66,
      height: 36,
      attrs: {
        body: {
          strokeWidth: 1,
          stroke: '#5F95FF',
          fill: '#EFF4FF'
        },
        text: {
          fontSize: 12,
          fill: '#262626'
        }
      },
      ports: { ...ports }
    },
    true
  )

  Graph.registerNode(
    'custom-polygon',
    {
      inherit: 'polygon',
      width: 66,
      height: 36,
      attrs: {
        body: {
          strokeWidth: 1,
          stroke: '#5F95FF',
          fill: '#EFF4FF'
        },
        text: {
          fontSize: 12,
          fill: '#262626'
        }
      },
      ports: {
        ...ports,
        items: [
          {
            group: 'top'
          },
          {
            group: 'bottom'
          }
        ]
      }
    },
    true
  )

  Graph.registerNode(
    'custom-circle',
    {
      inherit: 'circle',
      width: 45,
      height: 45,
      attrs: {
        body: {
          strokeWidth: 1,
          stroke: '#5F95FF',
          fill: '#EFF4FF'
        },
        text: {
          fontSize: 12,
          fill: '#262626'
        }
      },
      ports: { ...ports }
    },
    true
  )

  Graph.registerNode(
    'custom-image',
    {
      inherit: 'rect',
      width: 100,
      height: 100,
      markup: [
        {
          tagName: 'image'
        }
      ],
      attrs: {
        image: {
          width: 50,
          height: 50,
          refX: 13,
          refY: 16
        }
      },
      ports: { ...ports }
    },
    true
  )

  const imageNodes = store.state.room.element.map(item =>
    graph.createNode({
      shape: 'image',
      label: item.elementName,
      attrs: {
        image: {
          'xlink:href': computedImageSrc(item.elementPic)
        },
        text: {
          // fontSize: 28
        }
        // fill: 'none'
      },
      size: {
        width: 40,
        height: 40
      },
      data: {
        roomNo: null,
        stationId: null,
        stationNo: null,
        type: item.type,
        elementCode: item.elementCode,
        elementPic: item.elementPic
      }
    })
  )
  stencil.load(imageNodes, 'group2')
  // #endregion
  bindKey()
  toX6(areaData)
}
```

# 右键菜单

```js
const onContextmenu = event => {
  const cells = graph.getSelectedCells()
  const isRedo = graph.canRedo
  const isUndo = graph.canUndo

  app.$contextmenu({
    items: [
      {
        label: '复制(Ctrl+Z)',
        disabled: cells.length === 0 ? true : false,
        onClick: () => {
          if (cells.length) {
            graph.copy(cells)
          }
        }
      },
      {
        label: '粘贴(Ctrl+V)',
        disabled: graph.isClipboardEmpty(),
        onClick: () => {
          if (!graph.isClipboardEmpty()) {
            const cells = graph.paste({ offset: 32 })
            graph.cleanSelection()
            graph.select(cells)
          }
        }
      },
      {
        label: '剪切(Ctrl+X)',
        divided: true,
        disabled: cells.length === 0 ? true : false,
        onClick: () => {
          if (cells.length) {
            graph.copy(cells)
            graph.removeCells(cells)
          }
        }
      },
      {
        label: '撤销(Ctrl+Z)',
        disabled: !isUndo,
        onClick: () => {
          graph.undo()
        }
      },
      {
        label: '恢复(Ctrl+Y)',
        disabled: !isRedo,
        divided: true,
        onClick: () => {
          graph.redo()
        }
      },
      {
        label: '删除(Backspace)',
        disabled: cells.length === 0 ? true : false,
        onClick: () => {
          if (cells.length) {
            testDelete({
              floorId: store.state.room.areaInfo.floorId,
              stationNo: cells[0].data.stationNo
            }).then(res => {
              if (res.data.flag == 1) {
                graph.removeCells(cells)
              } else {
                app.$message.error(res.data.message)
              }
            })
          }
        }
      }
    ],
    event, // 鼠标事件信息
    customClass: 'custom-class', // 自定义菜单 class
    zIndex: 100, // 菜单样式 z-index
    minWidth: 150 // 主菜单最小宽度
  })

  return false
}
```

# 绑定快捷键

```js
const bindKey = () => {
  // 监听 contextmenu 事件
  graph.on('cell:contextmenu', ({ e, x, y, cell, view }) => {
    onContextmenu(e)
  })
  // #region 快捷键与事件
  graph.bindKey(['meta+c', 'ctrl+c'], () => {
    const cells = graph.getSelectedCells()
    if (cells.length) {
      graph.copy(cells)
    }
    return false
  })
  graph.bindKey(['meta+x', 'ctrl+x'], () => {
    const cells = graph.getSelectedCells()
    if (cells.length) {
      graph.cut(cells)
    }
    return false
  })
  graph.bindKey(['meta+v', 'ctrl+v'], () => {
    if (!graph.isClipboardEmpty()) {
      const cells = graph.paste({ offset: 32 })
      graph.cleanSelection()
      graph.select(cells)
    }
    return false
  })

  // undo redo
  graph.bindKey(['meta+z', 'ctrl+z'], () => {
    if (graph.canUndo()) {
      graph.undo()
    }
    return false
  })
  graph.bindKey(['meta+shift+z', 'ctrl+shift+z'], () => {
    if (graph.canRedo()) {
      graph.redo()
    }
    return false
  })

  // select all
  graph.bindKey(['meta+a', 'ctrl+a'], () => {
    const nodes = graph.getNodes()
    if (nodes) {
      graph.select(nodes)
    }
  })

  // delete
  graph.bindKey('backspace', () => {
    const cells = graph.getSelectedCells()
    if (cells.length) {
      graph.removeCells(cells)
    }
  })

  // zoom
  graph.bindKey(['ctrl+1', 'meta+1'], () => {
    const zoom = graph.zoom()
    if (zoom < 1.5) {
      graph.zoom(0.1)
    }
  })
  graph.bindKey(['ctrl+2', 'meta+2'], () => {
    const zoom = graph.zoom()
    if (zoom > 0.5) {
      graph.zoom(-0.1)
    }
  })
}
```

# 组件转化为数据

```js
const toJson = () => {
  const { cells } = graph.toJSON()
  console.log(cells)
  const result = cells.map(item => {
    return {
      leftSide: item.position.x / multiple,
      topSide: item.position.y / multiple,
      width: item.size.width / multiple,
      height: item.size.height / multiple,
      roomNo: item.data.roomNo,
      stationId: item.data.stationId,
      stationNo: item.data.stationNo,
      type: item.data.type,
      elementCode: item.data.elementCode,
      elementPic: item.data.elementPic,
      id: item.attrs.text.id,
      floorId: store.state.room.floorId,
      areaCode: store.state.room.areaCode,
      angle: item.angle
    }
  })
  const data = {
    floorId: store.state.room.floorId,
    areaCode: store.state.room.areaCode,
    areaId: store.state.room.areaId,
    vos: result
  }
  // save
  testSaveElement(data).then(response => {
    if (response.data.flag != 1) {
      MessageBox.confirm(response.data.message, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        saveElement(data).then(response => {
          Message.success('保存成功')
        })
      })
    } else {
      saveElement(data).then(response => {
        Message.success('保存成功')
      })
    }
  })
}
```

# 数据转换为图

```js
const toX6 = data => {
  graph.clearCells()
  const arr = data.map(item => {
    return {
      position: {
        x: item.leftSide * multiple,
        y: item.topSide * multiple
      },
      angle: item.angle,
      attrs: {
        text: {
          id: item.id
        },
        image: {
          'xlink:href': computedImageSrc(item.elementPic)
        }
      },
      size: {
        width: item.width == null ? 40 : item.width * multiple,
        height: item.height == null ? 40 : item.height * multiple
      },
      data: {
        roomNo: item.roomNo,
        stationId: item.stationId,
        stationNo: item.stationNo,
        type: item.type,
        elementCode: item.elementCode,
        elementPic: item.elementPic
      },
      id: item.id,
      shape: 'image',
      visible: true,
      zIndex: 1
    }
  })

  console.log('toX6', arr)

  // 将 JSON 数据转换为结构化数据
  graph.fromJSON(arr)
  // graph.zoomToFit()
}
```

# 导出方法

```javascript
export { initX6, toJson, toX6, toCustom }
```

