# 项目结构

![image-20240321151154222](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240321151154222.png)

# 创建vite项目

```sh
npm init vite@latest
```

.....使用vite创建项目根据习惯，选择你习惯使用的插件。我这里因为只是研究一下导入***3D模型***所以没有加载很多东西

![image-20240321151023775](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240321151023775.png)

## 安装threejs

![image-20240321151104982](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240321151104982.png)

把不需要的东西删掉，保留干净的壳子

# 接下来开始整活

所需依赖

```js
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
import { ArcballControls } from "three/examples/jsm/controls/ArcballControls.js";
```

# threejs基本内容

```js
// 容器
const scene = new THREE.Scene();
// 相机
const camera = new THREE.PerspectiveCamera(
  30,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
);
// 渲染
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

camera.position.z = 5;

function animate() {
  requestAnimationFrame(animate);
  renderer.render(scene, camera);
}
animate();
```

下载3D模型的资源站

[sketchfab]: https://sketchfab.com/3d-models?features=downloadable&amp;sort_by=-likeCount



到这里都没有什么问题，接下里就开始报错

- 官网给的地址GLTFLoader地址不对
- gltf文件只能放在public文件夹下面放在assets文件夹下面读不到

错误的地址

```
// import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js"
```

正确的地址

```
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";
```

![image-20240321165646247](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240321165646247.png)

# 正确的代码

```
// 加载3D模型
const loader = new GLTFLoader();
loader.load(
  "../public/omgd/omgd.gltf",
  function (gltf) {
    scene.add(gltf.scene);
    console.log(scene);
    camera.lookAt(gltf.scene.position);
    gltf.animations; // Array<THREE.AnimationClip>
    gltf.scene; // THREE.Group
    gltf.scenes; // Array<THREE.Group>
    gltf.cameras; // Array<THREE.Camera>
    gltf.asset; // Object
  },
  undefined,
  function (error) {
    console.error(error);
  }
);
```

模型加载以后，看不到任何东西黑咕隆咚的，才想起来要加灯光

```
// 灯光
const light = new THREE.AmbientLight(0xffffff, 1, 100);
light.position.set(0, 0, 40);
scene.add(light);
```

加了灯光以后感觉位置也不对又调整了一下位置，让人物正对着。

又发现不能旋转，怎么说也得整个像qq飞车那种可以旋转的，然后再加个控制器

```
// 控制器
const controls = new ArcballControls(camera, renderer.domElement, scene);
// 控制器改变渲染页面
controls.addEventListener("change", function () {
  renderer.render(scene, camera);
});

//controls.update() must be called after any manual changes to the camera's transform
camera.position.set(0, 0, 40);
controls.update();
```

最后效果：

![image-20240321165023012](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240321165023012.png)

大功告成，嘿嘿嘿！