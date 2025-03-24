### 前言

做移动端开发时，不可避免的会遇到以下场景

*   测试：打个测试包给我，我要测个功能，比较急你赶紧弄一下

*   产品：给我打个正式环境的包，刚刚老板反馈一个问题我要看一下，事情很急优先安排一下

*   组长：这测试机里面安装的是测试包还是正式包？

*   产品：重新给我安装一个最新的xx环境包，我要拿去演示，比较急

*   运营：你给我打的这个包版本号是多少？什么时候打的？是不是最新的？事情比较急，帮忙确认一下

*   测试：这个bug在这个版本上为啥没修复？\
    (结果你排查半天，发现测试人员手里的版本没有包含修复的代码)\
    测试不知道什么时候打的包，你也不知道这个app当时打包的时间点

*   产品：业务需求原因，需要打xx个马甲包上架，核心业务不用改动\
    改下ui页面、app名称、app图标就行

*   你自己：为了应对上面的场景，每次打完包都需要手动改下名加个版本号和标记然后复制到某个目录下

**以上场景在中小型企业中很常见，大公司一般有流水线打包，基本不会出现打包事故**

### 先上效果图

*   app包文件命名规则：版本号+自定义名称+product+月日+时分秒.app
*   hap包文件命名规则：版本号+自定义名称+product+target+月日+时分秒.hap

**源码在文末**

![c.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/2d1cd264777242bab066e118baf1f61f~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436864&x-orig-sign=u8HVMyzf9NZTblVHirS48MObdG8%3D)

![e.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/619a95873aa04f07b7233ef058a77a87~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436864&x-orig-sign=QFiwemo7qurYkti%2BjpMESbTm2mU%3D)

项目初期，一般在项目中某个类中定义常量来区分正式环境还是测试环境\
每次打包之前，手动修改常量的值，最后编译出对应环境的包

正常情况下，这样操作问题也不大，简单省事

但实际情况大多是你打正式包时，正好手头事情又多，打包时总会遇到忘记把环境改回来的情况，导致打成测试包。

又或者其他同事改为测试环境调试，提交业务代码时没注意都给提交上去了，\
你本地是正式环境，然后你获取远程代码后直接打包，打完包之后没有明显标志区分是哪个环境的包

**打包出过问题的可以去评论区留个言，看看谁的经历更魔幻**

我先来，曾经不小心打了个测试包发布到线上去了\
当时手头事情比较多，同一时间有人要测试环境的包，有人要正式环境的包\
编译期间又在忙其他的，不同环境的配置又有好几处，忙着忙着就漏掉某些配置，\
导致编译的正式包里面包含一部分测试环境的配置

### 首先明确打包目标和规格

*   每次编译不同环境的包不用手动改配置，选择对应的product即可，规避手动修改配置容易出问题的场景
*   编译完成后，自动将app包(或hap包)重命名，带上版本号、可以通过名称区分对应环境、打包时间点
*   编译完成后，自动将包文件复制到某个目录下
*   项目的配置参数，统一在工程根目录的某个文件中配置(例如：local.properties)

### 通过hvigor实现构建过程动态修改参数

#### 第一步配置product

设置不同的product对应不同环境\
修改工程根目录的build-profile.json5\
在modules-->targets-->applyToProducts节点下添加product

| product            | 对应环境         | 对应的签名配置      |
| ------------------ | ------------ | ------------ |
| Agc                | 用于上架应用市场的正式包 | default      |
| AgcDebug\_Official | 调试包-正式环境     | agc\_debug   |
| AgcDebug\_Test     | 调试包-测试环境     | agc\_debug   |
| Company\_Official  | 企业分发-正式环境    | company      |
| Company\_Test      | 企业分发-测试环境    | company\_xxx |
| xxx                | xxx环境        | xxx签名配置      |
| xxx马甲包/xxx渠道       | xxx环境        | xxx签名配置      |

不同的product有时需要对应不同的签名配置\
[build-profile.json5](https://gitee.com/zhongrui_developer/CompileBuildDemo/blob/master/build-profile.json5)

![2.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/70d96eec28054da3a727b62533b8fa83~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436864&x-orig-sign=1QRGq1tx6lFhXEs3x1a9DT4%2BZGc%3D)

![3.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/e8f2ab73934a4c6bbaf391178942dec2~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436864&x-orig-sign=JoXJFuLsKhTICJ7eS%2BMXAJCPXkI%3D)

![4.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/48396c4955b542d686789d6ac3e2fb79~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436864&x-orig-sign=FJPjRCsowObjwyOESASojk94IH0%3D)

![5.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/8e35c9282bbf48099e1b413e82aeb84e~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436864&x-orig-sign=%2FYvubXDhBOSwkaWG9iE%2BywCX2xw%3D)

![6.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/429679130cf947c5ae1567c550945e3d~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436864&x-orig-sign=bUoo3wN5WRscA26l68mqhA1QfOk%3D)

![7.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/4e0a74b989014b8e964591f8fb5aafeb~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436864&x-orig-sign=W8umOvJxPx7Act6BlbD9%2BMVPyEE%3D)

![8.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/cc03ac0efbcc423b86f5a388416926de~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436864&x-orig-sign=yW5A6yNqM%2FtYh5mSFDFC7KwWrLw%3D)

![9.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/2019ec6d756743a59c53e625bd2b1ecf~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436864&x-orig-sign=ybvkQLm1dSrcXbNtXNGpS8m9%2B6E%3D)

#### 第二步配置工程根目录的hvigorfile.ts

新建一个hvigorfileConfig.ts文件
该文件主要实现以下两个功能，然后在hvigorfile.ts内导入使用

*   获取当前时间并格式化返回
*   获取工程根目录的local.properties文件内容

hvigorfile.ts配置中主要实现以下几点功能

*   输出local.properties文件配置的相关内容
*   根据product配置对应环境的接口地址
*   自定义配置app包的文件名
*   配置打包时间
*   编译完成后输出当前打包信息，如：打包时间、包名、版本号、product
*   编译完成后自动复制app文件到自定义目录下

以下为hvigorfile.ts完整配置

    /*导入OhosAppContext和OhosPluginId，用于动态修改app.json5配置中的版本号等信息*/
    import { appTasks, OhosAppContext, OhosPluginId } from '@ohos/hvigor-ohos-plugin';

    /*导入FileUtil，用于复制打包后的hap包和app包文件和判断文件路径是否存在*/
    import { hvigor, FileUtil } from '@ohos/hvigor'

    /*导入hvigorfileConfig.ts中的getLocalFileContent方法*/
    import { getLocalFileContent } from './hvigorfileConfig.ts';

    /*获取配置参数的全局对象*/
    let localData = getLocalFileContent()
    console.info("===========================config===========================")
    /*输出local.properties配置参数*/
    console.info(JSON.stringify(localData,null,1))

    /*记录打包编译时，当前的product*/
    let _productName = "def"
    /*记录打包编译时，当前的包名*/
    let _bundleName = "def"
    /*记录当前打包编译时的版本号*/
    let _versionCode = "def"
    /*记录当前打包编译时的版本名称*/
    let _versionName = "def"
    /*记录当前打包编译时的app名称*/
    let appName = ""

    /*获取根项目的节点*/
    let rootNode = hvigor.getRootNode()

    /*为根节点添加一个afterNodeEvaluate hook 在hook中修改app.json5的内容并生效*/
    hvigor.getRootNode().afterNodeEvaluate(rootNode => {
      /*获取app插件的上下文对象*/
      const appContext = rootNode.getContext(OhosPluginId.OHOS_APP_PLUGIN) as OhosAppContext;

      /*获取当前product*/
      const productName = appContext.getCurrentProduct().getProductName() ?? ""

      /*通过上下文对象获取从app.json5文件中读出来的obj对象*/
      const appJsonOpt = appContext.getAppJsonOpt();
      
      /*通过上下文对象获取从根目录build-profile.json5文件中读出来的obj对象,可用于修改app中的signingConfigs*/
      const buildProfileOpt = appContext.getBuildProfileOpt();

      /*修改AppScope/app.json5中的版本号-数据来源于local.properties中的参数配置*/
      if (productName.toLowerCase().indexOf("agc") >= 0) {
        /*上架应用市场的版本号*/
        appJsonOpt['app']['versionCode'] = localData["versionCode"]
        appJsonOpt['app']['versionName'] = localData["versionName"] + ""
      } else {
        /*企业分发的版本号或者各个马甲包的版本号*/
        appJsonOpt['app']['versionCode'] = localData["companyVersionCode"]
        appJsonOpt['app']['versionName'] = localData["companyVersionName"] + ""
      }

      /*将appJsonOpt对象设置回上下文对象以使能到构建的过程与结果中*/
      appContext.setAppJsonOpt(appJsonOpt);

      /*保存打包的相关信息，用于自定义app包名称*/
      _productName = productName
      _versionCode = appJsonOpt['app']['versionCode']
      _versionName = appJsonOpt['app']['versionName']

      /*获取当前编译时生成的时间*/
      /*getLocalFileContent()方法被调用时生成的时间*/
      let timeStr = localData["buildTime"]

      /*遍历工程目录下的build-profile.json5文件中app-->products节点的数据*/
      const products = buildProfileOpt['app']['products']
      for (let i = 0; i < products.length; i++) {
        const item = products[i]
        /*设置打包时间*/
        /*buildOption,arkOptions,buildProfileFields,buildTime需要在build-profile.json5提前定义出来*/
        item?.["buildOption"]?.["arkOptions"]?.["buildProfileFields"]?.["buildTime"] = timeStr
        
        let output=item["output"]
        if (output && output["artifactName"]) {
          let tempProductName = item["name"]
          if (tempProductName) {
            /*设置正式环境或者测试环境地址*/
            if(tempProductName.toLowerCase().indexOf("test")){
              /*如果product含有test，则配置为测试环境地址*/
              item?.["buildOption"]?.["arkOptions"]?.["buildProfileFields"]?.["url"] = localData["devUrl"]
            }else{
              item?.["buildOption"]?.["arkOptions"]?.["buildProfileFields"]?.["url"] = localData["OfficialUrl"]
            }

            /*设置打包输出文件的自定义名称*/
            /*格式为：版本号+自定义名字+product+时间.app*/
            const resultName = output["artifactName"] = _versionCode + "Harmony_" + tempProductName + "_" + timeStr
            
            if (item["name"] == productName) {
              /*如果是当前product，记录打包时的app文件名和bundleName*/
              appName = resultName
              _bundleName = item["bundleName"]
              if(!_bundleName){
                /*如果没有在build-profile.json5设置bundleName,直接获取app.json5中的bundleName*/
                _bundleName=appJsonOpt['app']['bundleName']
              }
            }
          }
        }
      }
      /*将buildProfileOpt对象设置回上下文对象以使能到构建的过程与结果中*/
      appContext.setBuildProfileOpt(buildProfileOpt);

    })

    /*添加一个构建结束的回调函数(打包完成后，复制app文件到新目录+输出相关信息)*/
    hvigor.buildFinished(buildResult => {
      const path = require('path');
      const appContext = rootNode.getContext(OhosPluginId.OHOS_APP_PLUGIN) as OhosAppContext;
      /*获取.app文件所在目录*/
      let dirPath = appContext.getBuildProductOutputPath()
      
      /*获取.app文件完整路径*/
      let appPath = path.join(dirPath, appName + ".app")
      // console.info("======appPath=====" + appPath)

      /*如果.app文件存在，就复制到新目录下*/
      if (FileUtil.exist(appPath)) {
        const parentPath = path.join(path.dirname(__filename), "_app")
        
        /*如果工程的根目录没有_app目录，则创建*/
        FileUtil.ensureDirSync(parentPath)
        
        /*目标文件路径*/
        const destPath = path.join(parentPath, appName + ".app")
        console.info("======destPath=====" + destPath)
        
        /*将打包完成的.app文件复制到项目根目录下的_app目录中*/
        FileUtil.copyFileSync(appPath, destPath)
      }
      /*复制完成后，打印当前编译出的包信息*/
      console.info("================================================")
      console.info("buildTime  :" + localData["buildTime"])
      console.info("productName:" + _productName)
      console.info("bundleName :" + _bundleName)
      console.info("versionCode:" + _versionCode)
      console.info("versionName:" + _versionName)
    })


    export default {
      system: appTasks, /* Built-in plugin of Hvigor. It cannot be modified. */
      plugins: []         /* Custom plugin to extend the functionality of Hvigor. */
    }

#### 第三步配置entry模块

如果选择不同的product打出的app包，我们希望实现app图标、源码集以及其他资源样式不同，就需要配置不同的Module Target(类似Android的多渠道配置)，分别对应不同的product

首先定义两个目录main\_company和main\_def，在这两个目录中分别配置源码集目录ets和资源目录resources

![d.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/d563059a5f374f95aa2a7c7f3119fcd7~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436864&x-orig-sign=fQgmzu%2Fx%2FegtOLXI3tvHwowZAsg%3D)

**配置entry模块的hvigorfile.ts**

entry模块中的hvigorfile.ts配置中主要实现以下几点功能

*   自定义配置hap包的文件名
*   配置hap打包时间
*   编译完成后自动复制hap文件到自定义目录下

以下为hvigorfile.ts完整配置

    import { hapTasks, OhosHapContext, OhosPluginId } from '@ohos/hvigor-ohos-plugin';
    import { hvigor, FileUtil, getNode } from '@ohos/hvigor'
    import { readFileSync } from 'fs';
    import { appTasks, OhosAppContext } from '@ohos/hvigor-ohos-plugin';
    /*导入变量为config的对象，获取打包配置参数*/
    import { config } from '../hvigorfileConfig'

    /*通过当前目录的hvigorfile.ts获取节点*/
    const rootNode = getNode(__filename);

    /*通过key-value形式记录hap包名称，key=Module Target,value=hap的文件名*/
    let hapNameMap = {}

    /*记录product，打包输出时用于自定义hap包名称*/
    let _productName = "def"

    /*为节点添加一个afterNodeEvaluate hook 在hook中修改该目录下的build-profile.json5的内容并使能*/
    rootNode.afterNodeEvaluate(node => {
      const path = require('path');
      const parentDir = path.dirname(path.dirname(__filename));
      
      /*通过项目根目录的hvigorfile.ts脚本文件路径获取对应的节点对象*/
      const appNode = getNode(path.join(parentDir, "hvigorfile.ts"))

      const appContext = appNode.getContext(OhosPluginId.OHOS_APP_PLUGIN) as OhosAppContext;
      const appJsonOpt = appContext.getAppJsonOpt();
      
      /*获取当前product，用于自定义hap文件名*/
      _productName = appContext.getCurrentProduct().getProductName() ?? ""

      /*获取此节点使用插件的上下文对象 此时为hap插件 获取hap插件上下文对象*/
      const hapContext = node.getContext(OhosPluginId.OHOS_HAP_PLUGIN) as OhosHapContext;
      
      /*通过上下文对象从entry目录build-profile.json5文件中读出来的obj对象*/
      const buildProfileOpt = hapContext.getBuildProfileOpt();
      const targets = buildProfileOpt['targets']
      
      /*获取编译时生成的时间，[在hvigorfileConfig.ts文件中的getLocalFileContent()方法中生成]*/
      /*因为工程根目录下hvigor先于module目录下hvigor执行，所以这里的buildTime直接通过变量config获取*/
      /*记得导入import { config } from '../hvigorfileConfig'*/
      let timeStr = config["buildTime"]
      
      /*因为工程根目录下hvigor先于module目录下hvigor执行，同理可得：此时获取的versionCode是被动态修改之后的值*/
      let versionCode = appJsonOpt["app"]["versionCode"]
      
      /*遍历build-profile.json5中targets节点下的内容*/
      for (let i = 0; i < targets.length; i++) {
        let output = targets[i]["output"]
        if (output && output["artifactName"]) {
          /*获取target name，自定义hap名称需要*/
          let tempName = targets[i]["name"]
          if (tempName) {
            
            /*设置自定义hap名称，版本号+自定义名+product+target+打包时间.hap*/
            const resultName =output["artifactName"] = versionCode + "Harmony" + "_"+ _productName + "_" + tempName + "_" + timeStr
            
            /*key-value形式保存hap文件名，key=target name*/
            hapNameMap[tempName] = resultName + ".hap"
            // console.info("===========" + resultName)
          }
        }
      }
      //console.info("===hapNameMap========" + JSON.stringify(hapNameMap))
      /*将buildProfileOpt对象设置回上下文对象以使能到构建的过程与结果中*/
      hapContext.setBuildProfileOpt(buildProfileOpt);
    })


    /*添加一个构建结束的回调函数(打包完成后，复制hap文件到新目录)*/
    hvigor.buildFinished(buildResult => {

      const hapContext = rootNode.getContext(OhosPluginId.OHOS_HAP_PLUGIN) as OhosHapContext;
      hapContext?.targets((target: Target) => {
      
        /*通过target name获取对应的hap文件*/
        let hapName = hapNameMap[target.getTargetName()]
        
        //console.info(target.getTargetName()+"======target=====" +hapName)
        if (!hapName) {
          return
        }
        const path = require('path');
        
        /*获取编译完成后的hap包所在路径*/
        const dirPath = target.getBuildTargetOutputPath();
        
        /*得到hap包完整路径*/
        let hapPath = path.join(dirPath, hapName)
        
        //console.info("======hapPath=====" +hapPath)
        if (FileUtil.exist(hapPath)) {
          /*复制到目标目录*/
          const parentPath = path.join(path.dirname(path.dirname(__filename)), "_hap")
          
          /*如果目标目录不存在就创建*/
          FileUtil.ensureDirSync(parentPath)
          
          /*定义目标文件路径*/
          const destPath = path.join(parentPath, hapName)
          
          /*复制hap文件到目标路径*/
          FileUtil.copyFileSync(hapPath, destPath)
        }

      })
    })


    export default {
      system: hapTasks, /* Built-in plugin of Hvigor. It cannot be modified. */
      plugins: []         /* Custom plugin to extend the functionality of Hvigor. */
    }

**以上就是完整配置，配置完就不用每次手动修改配置打包了**\
看上去挺复杂的，其实只要熟读官方文档之后，再实操几遍，就变得很简单了

#### 实际运行效果

![build3.gif](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/e64513e4fd0047c0a00871e1a8424417~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436864&x-orig-sign=P6Tw4aO5vkXJh%2BJiLp4H5XCsn3g%3D)

**可能遇到的问题**\
有开发者可能是通过配置一个product对应多个Module Target(deviceType或distributionFilter/distroFilter都一致的情况下)来实现多渠道或者多环境打hap包，\
如果是直接运行，没什么问题，但是一旦打app包就会报错

如下图所示：
![1.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/5f9b2215b8524bb4840e6cba101ae126~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436864&x-orig-sign=wbEkmpsaVUnw9FfPJxDqomY0hJo%3D)

官方解释：

![2.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/57d5b617b1834b1ba63bb7ff59a1afde~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436864&x-orig-sign=P45%2BGC1ynUTr9QrlaruoFSISHo8%3D)

所以这种情况建议是通过多个product去配置，或者同一个Module下配置不同的deviceType

[**源码地址**](https://gitee.com/zhongrui_developer/CompileBuildDemo)

### 历史文章

[HarmonyOS一行代码实现任意处弹窗](https://juejin.cn/post/7401358349877444642)

[HarmonyOS数据列表加载更多(无需监听列表滑到最底部)](https://juejin.cn/post/7397638569731358760)

[HarmonyOS下拉刷新+上拉加载(纵向横向都支持)(v1+v2装饰器)](https://juejin.cn/post/7395866502716702755)
