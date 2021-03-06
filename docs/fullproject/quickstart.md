## QuickStart
本文档演示一个极简Unity工程如何Step By Step转换到小游戏运行起来。

温馨提示：
* 本示例采用Unity2018.4.11.c1；
* 文档最后更新时间：2020.6.16；


### Unity 原始工程
本示例的逻辑非常简单，场景内有 Ground 和 Tank 两个Prefab，整个游戏有两个脚本，第一个为 Fllow，保证相机跟随坦克，第二个脚本为 TankMovement，用于监听事件并控制坦克移动。

![流程概览](./image/quickstart1.png)
```
// Follow
using UnityEngine;
using System.Collections;

  public class Fllow : MonoBehaviour
  {
    public Transform player; // 主角位置
    public float speed = 5f; // 相机速度   
    Vector3 distance; // 主角和摄像机之间的距离                   


    void Start()
    {
     
      distance = transform.position - player.position;
    }

    void Update()
    {
      Vector3 targetCamPos = player.position + distance;

      transform.position = targetCamPos;
    }
  }

```

```
// TankMovement
using UnityEngine;

public class TankMovement : MonoBehaviour {
  private ParticleSystem[] m_particleSystems; // References to all the particles systems used by the Tanks

  private bool flag = false;
  private UnityEngine.Vector3 origin;
  public const float sensitivity = 0.01f;

  private void OnEnable () {
    m_particleSystems = GetComponentsInChildren<ParticleSystem> ();
    for (int i = 0; i < m_particleSystems.Length; ++i) {
      m_particleSystems[i].Play ();
    }
  }

  private void Update () {
    if (UnityEngine.Input.GetMouseButtonDown (0)) {
      if (!flag) {
        flag = true;
        origin = UnityEngine.Input.mousePosition;
      }

    }
    if (flag) {
      var dir = new UnityEngine.Vector3 ((origin.x - UnityEngine.Input.mousePosition.x) * sensitivity,
        0.0f, (origin.y - UnityEngine.Input.mousePosition.y) * sensitivity);

      if (dir.magnitude < 0.00000001f) {
        if (UnityEngine.Input.GetMouseButtonUp (0)) {
          flag = false;
        }
        return;
      }

      if (dir.magnitude > 0.1f) {
        dir = dir.normalized * 0.1f;
      }
      var backup = this.gameObject.transform.position;

      this.gameObject.transform.position = this.gameObject.transform.position - dir;
      this.gameObject.transform.forward = -dir.normalized;
    }
    if (UnityEngine.Input.GetMouseButtonUp (0)) {
      flag = false;
    }
  }
}
```
可以直接 Unity 添加工程 examples/QuickStart 并运行游戏来查看效果。

### 2.下载导出插件

[点击下载](https://dldir1.qq.com/WechatWebDev/plugins/BeefBallEngine-unitytool/1.0.0/UnityTool.unitypackage)插件，下载完成之后双击即可安装插件。
![流程概览](./image/quickstart2.png)

当插件安装完成之后，会多出一个**微信小游戏**的菜单，因为这里会用到脚本导出，因此我们点击`微信小游戏->拓展模块管理`来安装核心模块和Script导出模块。
![流程概览](./image/quickstart3.png)

### 3.执行导出
插件安装完成后，在 All Materials 菜单将材质设置为 WXBBShader/BlinnPhong，其中 Dust 需要特殊设置为 WXBBShader/ShurikenParticle
 ![流程概览](./image/quickstart12.png)

Shader设置完成之后就可以执行资源导出操作了。
![流程概览](./image/quickstart4.png)
首先我们需要创建一个空的小游戏工程，点击"创建小游戏项目模板"即可开始执行导出操作，先执行**当前场景**导出
![流程概览](./image/quickstart5.png)
然后执行脚本导出，导出完成之后，会生成下列文件：
![流程概览](./image/quickstart6.png)

### 4.导入开发工具
接下来我们在微信开发者工具进行调试预览工作，首先[下载](https://developers.weixin.qq.com/miniprogram/dev/devtools/nightly.html)最新的微信开发者工具，下载完成之后导入刚才新建的小游戏工程。

小游戏工程默认为3D打飞机游戏示例，这里我们需要在**方案工具**里面将刚才导出的资源和脚本构建成新的小游戏。
![流程概览](./image/quickstart7.png)

点击进入方案工具，右键选中assets可以批量解包：
![流程概览](./image/quickstart8.png)

解包完成之后，执行构建，在红框出选中**构架工程**，选中待构建的场景执行构建：
![流程概览](./image/quickstart9.png)
构建完成后关系方案工具，在模拟器预览游戏。这里需要对minigame/game.json配置使用引擎插件，配置示例如下
```json
{
  "deviceOrientation":"landscape",
  "engine":true,
  "plugins":{
      "WXEngine":{
          "version":"0.7.2",
          "provider":"wx20afc706a711eefc",
          "contexts":[
              {
                  "type":"isolatedContext"
              }
          ]
      },
      "WXBridge":{
        "provider":"wxe92a56f105f5963d",
        "version":"0.0.2",
        "contexts":[
            {
                "type":"gameContext"
            }
        ]
    }
  }
}
```

### 6.真机预览
最后在工具上预览成功，点击**预览**就可真机预览游戏
![流程概览](./image/quickstart10.png)

真机预览结果如下：
![流程概览](./image/quickstart11.png)