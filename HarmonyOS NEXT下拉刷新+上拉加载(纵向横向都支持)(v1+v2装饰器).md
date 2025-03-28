
### 前言

在移动端下拉刷新和上拉加载属于高频使用的功能，目前官方提供的Refresh组件自定义效果有限，很难满足各家产品个性化定制的要求。下面我们从0到1打造一个无入侵性、可自定义扩展的下拉刷新组件

(源码地址和使用教程链接在文末)

[v1装饰器版本](https://ohpm.openharmony.cn/#/cn/detail/@zhongrui%2Fpull_to_refresh)

[v2装饰器版本](https://ohpm.openharmony.cn/#/cn/detail/@zhongrui%2Fpull_to_refresh_v2)
[404点这里](https://gitee.com/zhongrui_developer/PullToRefresh/tree/master_v2/)

### 目标特点

1.无入侵性，不需要传数据源
2.不限制组件，支持任意布局(List,Grid,Web,Scroll,Text,Row,Column等布局)
3.支持header和footer个性化视图扩展(支持Lottie动画)
4.支持垂直列表和横向列表的刷新和加载
5.支持下拉(或者上拉)打开其他页面

### 效果图

#### 垂直列表

| 垂直List列表刷新效果                                                                                                                                                                                                                                                                                                                                                                                                              | 垂直Grid列表刷新效果                                                                                                                                                                                                                                                                                                                                                                                                            | 下拉打开其他页面                                                                                                                                                                                                                                                                                                                                                                                                                  | 自动刷新                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/73b8726387134076bef755761bdf4d69~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436977&x-orig-sign=YZ7W0gQY8cIbBLy6MOk106%2Bfpzs%3D" width="200px"> | <img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/7890aaad012443849dbde27816b4e18b~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436977&x-orig-sign=nPAPmbH4U7dLERW1%2F5z2dqaFL5g%3D" width="200px"> | <img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/da9e949552c24ae08dbe926ce002f811~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436977&x-orig-sign=gPwhB8chSnbgRclOE03I1niRwrs%3D" width="200px"> | <img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/39a05359652b4380b3d46ecdcc740206~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436977&x-orig-sign=NdL1QO9eaeauQa5ve5L1f40xfEA%3D" width="200px"> |

| Web视图刷新效果                                                                                                                                                                                                                                                                                                                                                                                                               | 自定义动画刷新效果                                                                                                                                                                                                                                                                                                                                                                                                                 | Lottie动画刷新效果                                                                                                                                                                                                                                                                                                                                                                                                              | 横向列表刷新                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/04cbcc6429cb4f37834aa7c3736eb192~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436977&x-orig-sign=rMV8SEmM9Y%2FrN2Y8odMFo5t%2FUdA%3D" width="200px"> | <img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/fa5541bc978a472b95ebf99aa4c69cd0~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436977&x-orig-sign=TZjqsVr533azXvXNv21wtl0MLA0%3D" width="200px"> | <img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/556b9649ff664103a3869ffd648d1af0~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436977&x-orig-sign=mjPFZsTT8G7AYRWO2zohxOxsqzo%3D" width="200px"> | <img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/5ae6a074d6b746f392ffd6baac66cd93~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436977&x-orig-sign=3dzAdbSRKdesNG%2F3voQVj9Md3w4%3D" width="200px"> |

#### 三种横向模式header效果图(footer同理)

| header正常横向<br/>header宽度固定,高度撑满                                                                                                                                                                                                                                                                                                                                                                                          | header布局逆时针旋转90°<br/>header宽度撑满,高度固定<br/>和垂直列表布局方式一致                                                                                                                                                                                                                                                                                                                                                                    | header布局顺时针旋转90°<br/>header宽度撑满,高度固定<br/>和垂直列表布局方式一致                                                                                                                                                                                                                                                                                                                                                                        |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/b8c9bacd2d7d4b67a75512eaee7a978b~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436977&x-orig-sign=mmCe1EwQzEPtPSjo4MbFUcC9cz0%3D" width="200px"> | <img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/6f517e227aac4899ae97139b1358c173~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436977&x-orig-sign=bZRWMrCqxdlMmsR106fgmet1WY8%3D" width="200px"> | <img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/80fa72c854c74b98ad3797f0b2290907~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436977&x-orig-sign=VyV%2FLHmjTNgr4DWu6pOI1fFMhT8%3D" width="200px"> |

#### 缺省页设置(加载中，空数据，加载失败，无网络)

 <img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/e2d9fa53c54549a4b1a5619839a9d4eb~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436977&x-orig-sign=j9IOsdX56Wyvbri0K%2BZP4v2JNfI%3D" width="200px"> 

### 第1步通过自定义布局构建下拉刷新视图结构

<img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/dcabbb0aec1249aa9ab9dd1ab3dc5810~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743436977&x-orig-sign=IdcjtsYJ0WSlLY27G3wcLaAn9bw%3D" alt="微信图片_20240727181605.png" width="50%">

```typescript
/*这里的header，content，footer视图，全部由外部传入*/
//header视图
@BuilderParam headerView: () => void 
//内容视图
@BuilderParam contentView: () => void 
//footer视图
@BuilderParam loadView: () => void 

build() {
   this.headerAndContent()
}

/*在build函数内直接定义多个根视图编译会报错，这里使用@Builder绕过检查*/
@Builder
private headerAndContent() {
  //header视图(垂直列表时，设置宽度撑满，高度自适应)
  Stack(){
      this.headerView()
  }.width("100%")
  
  //内容视图
  Stack(){
      this.contentView()
  }.width("100%").height("100%")
  
  //footer视图(垂直列表时，设置宽度撑满，高度自适应)
  Stack(){
      this.loadView()
  }.width("100%")
}
```

现在这种布局会出现视图重叠，还无法实现上图的结构,这个时候借助onMeasureSize()和
onPlaceChildren()实现对三个布局的测量和摆放

主要处理header视图和footer视图的偏移，
header向上偏移，偏移量为自身高度的负数，footer向下偏移，偏移量为内容视图高度

```typescript
private sizeResult: SizeResult = { width: 0, height: 0 }
//header视图高度
private headerHeight=0
//视图测量
onMeasureSize(selfLayoutInfo: GeometryInfo, children: Measurable[], constraint: ConstraintSizeOptions): SizeResult {
    //selfLayoutInfo:父组件布局信息
    //children      :子组件布局信息
    //constraint    :父组件constraint信息
    
    //测量子组件
    const headerResult  = children[0].measure(constraint)
    const contentResult = children[1].measure(constraint)
    const footerResult  = children[2].measure(constraint)
    
    //记录header视图高度，触发刷新时，动画回弹需要
    this.headerHeight=headerResult.height;
    
    //将内容视图区域的宽高设置为当前组件宽高
    this.sizeResult.width = contentResult.width;
    this.sizeResult.height = contentResult.height;
        
    //返回组件尺寸信息
    return this.sizeResult
}

//视图布局
onPlaceChildren(selfLayoutInfo: GeometryInfo, children: Layoutable[], constraint: ConstraintSizeOptions): void {
    const childHeader = children[0]
    //设置header视图向上偏移自身高度
    childHeader.layout({ y: -childHeader.measureResult.height })

    const childContent = children[1]
    //content视图不需要做偏移
    childContent.layout({})

    const childFooter = children[2]
    //footer需要向下偏移，偏移量为内容视图高度
    childFooter.layout({ y: this.sizeResult.height })
}

```

此时已完成初始的布局逻辑(细心的你可能会发现一个问题，这里先挖个坑，后面细说)
接下来实现视图跟随手势实现下拉效果

### 第2步实现下拉

1：content视图通过parallelGesture属性绑定PanGesture拖动手势
2：记录每次拖动手势的偏移量，header和content视图通过offset属性设置Y轴偏移量达到下拉效果

    //header和content视图的偏移量(视图回弹动画需要)
    totalOffsetY: number = 0

    //header和content视图的偏移量
    @State currentOffsetY: number = 0

    //记录上一次拖动手势的偏移量
    preOffsetY = 0;

    //header和content视图设置offset属性
    .offset({ y: this.currentOffsetY })

    //content视图
    Stack() {
        this.contentView()
    }
    .offset({ y: this.currentOffsetY })
    .width("100%")
    .height("100%")
    .parallelGesture(PanGesture(new PanGestureOptions({ direction: PanDirection.Vertical }))
    .onActionStart((event: GestureEvent) => {
        //Pan手势识别成功回调
        //记录偏移量
        this.preOffsetY = event.offsetY
    }).onActionUpdate((event: GestureEvent) => {
        //Pan手势移动过程中回调
        //新增偏移量=(当前手势偏移量-上一次手势偏移量)*0.5阻尼系数
        //视图总偏移量=新增偏移量+当前视图偏移量
        this.currentOffsetY = this.currentOffsetY + (event.offsetY - this.preOffsetY)*0.5
        if(this.currentOffsetY<0){
           //上拉视图时，不能让内容超出组件范围
           this.currentOffsetY=0
        }
        this.totalOffsetY=this.currentOffsetY
        this.preOffsetY = event.offsetY
        
        if(this.currentOffsetY<100){
            //小于100vp时显示"下拉刷新"相关视图逻辑
        }else{
            //下拉距离超过100vp显示"释放刷新"相关视图逻辑
        }
        
    }).onActionEnd((event: GestureEvent)=>{
        //Pan手势识别成功，手指抬起后触发回调
    }).onActionCancel(()=>{
        //Pan手势识别成功，接收到触摸取消事件触发回调
        //在窗口失焦的时候会触发
    }))

此时已经实现了视图下拉的效果，接下来通过动画(animator)实现视图回弹

### 第3步实现视图回弹

1：给content视图设置onTouch事件监听,触发up和cancel事件时，执行回弹动画

看到这里是不是有个疑问，明明PanGesture有onActionEnd和onActionCancel事件，
在onActionEnd里面执行回弹动画不行吗？
为什么还需要监听onTouch事件？

首先触发PanGesture事件是有条件的，拖动手势必须有一小段偏移量，才能触发onActionStart事件
后续才有onActionUpdate、onActionEnd事件
也就是说触摸不一定有onActionStart事件，释放不一定有onActionEnd事件

**举个例子**：
当视图下拉过程松手，此时视图需要回弹，在视图回弹过程中，立刻触摸视图(只是按下，不拖动手势)
此时回弹动画需要停止，然后再松手(此时是不会触发onActionEnd事件)，但是视图需要继续回弹
那么只能通过监听onTouch的up和cancel事件来执行回弹动画了

    import animator from '@ohos.animator'
    private animOption:AnimatorOptions={
      duration: 250,
      easing: "fast-out-linear-in",
      delay: 0,
      fill: "forwards",
      direction: "normal",
      iterations: 1,
      begin: 0,
      end: 1
    }
    private anim: AnimatorResult = animator.create(this.animOption);
    private animPause=false;

    aboutToAppear(): void {
      this.anim.onFrame = (progress: number) => {
        if(this.animPause){
          //取消动画时，progress会变成1，如果不return this.currentOffsetY会立刻变成0
          return
        }
        //通过总偏移量和动画执行进度，计算出当前视图偏移量
        this.currentOffsetY = this.totalOffsetY * (1 - progress)
      }
      this.anim.onFinish=()=>{
        //动画完成时，总偏移量等于当前视图偏移量
        this.totalOffsetY=this.currentOffsetY
      }
      this.anim.onCancel=()=>{
        //动画取消时，总偏移量等于当前视图偏移量
        this.totalOffsetY=this.currentOffsetY
      }
    }

    //给content视图设置onTouch事件
    .onTouch((event: TouchEvent) => {
      const type = event.type
      if(type==TouchType.Down){
        //下拉距离大于0时，触摸content视图暂停回弹动画
        if(this.currentOffsetY>0){
          this.animPause=true
          this.anim.cancel()
        }
      }else if (type == TouchType.Up || type == TouchType.Cancel) {
        //下拉距离大于0时，触摸content视图暂停回弹动画
        if(this.currentOffsetY>0){
          this.animPause=false
          //执行回弹动画
          this.anim.play()
        }
      }
    })

看到这里，细心的你肯定又发现问题了
触摸视图时暂停动画为什么要用cancel()不用pause()?
如果触摸时用pause()，不拖动手势的情况下再次释放是没什么问题的
如果触摸时通过pause()暂停动画，再拖动视图(视图总偏移量发生变化)，
再次释放时就需要重新执行回弹动画，否则回弹就会出问题，
因为此时的视图总偏移量发生了变化，继续执行动画，onFrame监听中的progress进度还是延续上次的
这样就会导致动画回弹不连续

**举个例子：**
开始回弹时，视图的总偏移量为100px，回弹动画进度到0.5时(onFrame监听中的progress)，
触摸视图暂停动画，此时视图的偏移量为：50px
然后拖动视图，使总偏移量达到100px,此时释放触发up事件时，
如果继续上次动画，那么progress可能为0.51，
onFrame监听中计算的视图偏移量就为100px*0.51=51px,
此时视图会突然从偏移量100px变到51px,产生回弹动画不连续问题

此时已完成视图回弹至顶部的效果，
接下来实现触发刷新和修改视图回弹时保留header视图高度效果

### 第4步触发刷新，显示header刷新视图

下拉视图时，根据偏移量不同，显示的状态也不同
比如下拉距离<=100vp时，显示"下拉刷新"
下拉距离>=100vp时，显示"释放立即刷新"
达到刷新条件，释放手势，执行回弹动画时，保留header高度的视图，显示"正在刷新"

    外部设置开始刷新的回调
    public onRefresh: () => void = () => {
    }

    //释放触发刷新动作
    .onTouch((event: TouchEvent) => {
      const type = event.type
      if (type == TouchType.Down) {
        if (this.currentOffsetY > 0) {
          this.animPause = true
          this.anim.cancel()
        }
      } else if (type == TouchType.Up || type == TouchType.Cancel) {
        this.animPause = false
        if (this.currentOffsetY > 100) {
          //此时修改header视图，显示正在刷新中的视图
          
          //如果下拉高度达到刷新条件，释放时触发刷新操作
          this.onRefresh()
          
          /*计算回弹至header高度的progress进度*/
          this.animOption.end = (this.currentOffsetY - this.headerHeight) / this.currentOffsetY
          this.anim.reset(this.animOption)
          this.anim.play()
        } else if (this.currentOffsetY > 0) {
          //执行回弹动画
          this.animOption.end = 1
          this.anim.reset(this.animOption)
          this.anim.play()
        }
      }
    })

此时已完成释放手势触发刷新和显示刷新中的header视图

看到这里，细心的你又注意到了，如果外部刷新数据成功之后，如何通知组件内部并取消正在刷新中的header视图？

### 第5步构造controller,通知组件内部刷新完成

    PullToRefreshLayout({
        /*设置控制器*/
        controller: this.controller,
        /*内容视图*/
        contentView: () => {
           this.contentView()
        },
        /*触发刷新*/
        onRefresh: () => {
           setTimeout(() => {
               //通知刷新成功
              this.controller.refreshComplete(true)
          }, 1000)
        }
        }).width("100%").height("100%").clip(true)

    export class RefreshController {
      /*刷新完成，true:成功，false：失败*/
      refreshComplete: (isSuccess: boolean) => void = (isSuccess: boolean) => {
      }
    }

    public controller: RefreshController = new RefreshController()

    aboutToAppear(): void {
      /*通过参数通知刷新结果*/
      this.controller.refreshComplete = (isSuccess: boolean) => {
        if(isSuccess){
            //处理刷新成功的逻辑
        }else if(){
            //处理刷新失败的逻辑
        }
        //header视图提示"刷新成功"或"刷新失败"
        
        //随后通过动画回弹隐藏header视图
        this.animOption.end = 1
        this.anim.reset(this.animOption)
        this.anim.play()
      }
    }

### 第6步解决上滑再下拉,header会往下偏移问题

当内容视图是List或者其他可滑动列表时
将列表内容往上滑动，再下拉列表时，header视图会向下偏移
产生这个问题的原因是下滑过程没有判断列表内容是否到顶
所以导致拖动手势对header做了向下偏移的操作

解决办法：
让外部告诉组件内部，当前列表是否到顶

    PullToRefreshLayout({
        /*设置控制器*/
        controller: this.controller,
        /*内容视图*/
        contentView: () => {
           this.contentView()
        },
        /*触发刷新*/
        onRefresh: () => {
           setTimeout(() => {
           //通知刷新成功
          this.controller.refreshSuccess()
          }, 1000)
        },
        /*是否可以下拉*/
        onCanPullRefresh: () => {
            if (!this.scroller.currentOffset()) {
                /*处理无数据，为空的情况*/
               return true
            }
            //如果列表到顶，返回true，表示可以下拉，返回false，表示无法下拉
            return this.scroller.currentOffset().yOffset <= 0
        }
        }).width("100%").height("100%").clip(true)


    /*是否可以下拉，默认为true*/
    public onCanPullRefresh: () => boolean = () => true

    .onActionUpdate((event: GestureEvent) => {
        //如果不能下拉，则header视图不发生偏移
        if(!this.onCanPullRefresh()){
          return
        }
        //Pan手势移动过程中回调
        //新增偏移量=(当前手势偏移量-上一次手势偏移量)*0.5阻尼系数
        //视图总偏移量=新增偏移量+当前视图偏移量
        this.currentOffsetY = this.currentOffsetY + (event.offsetY - this.preOffsetY)*0.5
        if(this.currentOffsetY<0){
           //上拉视图时，不能让内容超出组件范围
           this.currentOffsetY=0
        }
        this.totalOffsetY=this.currentOffsetY
        this.preOffsetY = event.offsetY
        
        if(this.currentOffsetY<100){
            //小于100vp时显示"下拉刷新"相关视图逻辑
        }else{
            //下拉距离超过100vp显示"释放刷新"相关视图逻辑
        }
    })

### 第7步解决下拉再上拉,列表内容会滑动问题

当内容视图是List或者其他可滑动列表时
将组件往下拉显示header视图后，再上滑时，列表内容会往上滑动

解决办法：上滑隐藏header视图时，通过Scroller设置列表滑动距离为0

```
public scroller: Scroller | undefined = undefined

.onActionUpdate((event: GestureEvent) => {
if(!this.onCanPullRefresh()){
  return
}
//存在下拉距离的情况下，向上滑动视图
if (this.currentOffsetY > 0 && this.preOffsetY>event.offsetY) {
  /*如果下拉再上拉，不让列表滑动*/
  if (this.scroller) {
    this.scroller.scrollTo({ yOffset: 0xOffset: this.scroller.currentOffset()?.xOffset ?? 0 })
  }
}

```

    scroller: Scroller = new Scroller()
    PullToRefreshLayout({
        //设置内容列表的滚动控制器
        scroller: this.scroller,
        controller: this.controller,
        /*内容视图*/
        contentView: () => {
           this.contentView()
        }
        })
        .width("100%")
        .height("100%")
        .clip(true)
        
    @Builder
    contentView() {
      List({ scroller: this.scroller }) {
          
      }.width("100%").height("100%")
      .edgeEffect(EdgeEffect.None)  
    }

细心的你可能又发现了，为什么自定义组件用到了clip(true)属性？
这是为了解决在第1步自定义布局产生的一个问题
当header视图布局位置超出组件范围时(组件位置未超出屏幕时)，是可以看见的，所以需要设置clip(true)来解决这个问题

此时已完成下拉刷新的基础逻辑，此时的你可能还有不少疑问
比如为什么只有下拉刷新，说好的上拉加载呢？
其实上拉加载的逻辑和下拉刷新类似，一个下拉，一个上拉
能理解下拉的实现逻辑和原理，上拉加载的也就会了

***

完整案例的细节太多了，一篇很难讲到位，本文主要起到抛砖引玉的效果，有什么好的改进方案，可在评论区留言

### 项目源码

**[-->v1使用教程](https://ohpm.openharmony.cn/#/cn/detail/@zhongrui%2Fpull_to_refresh)**
<https://ohpm.openharmony.cn/#/cn/detail/@zhongrui%2Fpull_to_refresh>

**[-->v2使用教程](https://ohpm.openharmony.cn/#/cn/detail/@zhongrui%2Fpull_to_refresh_v2)**
<https://ohpm.openharmony.cn/#/cn/detail/@zhongrui%2Fpull_to_refresh_v2>

**[-->源码地址](https://gitee.com/zhongrui_developer/PullToRefresh)**
<https://gitee.com/zhongrui_developer/PullToRefresh>

(ps：大佬~点个关注吧，主页还有其他技术方案，后续会发布如何优雅的实现列表无感知加载下一页的文章)

### 历史文章

*   [HarmonyOS多环境+多渠道+自定义路径输出+自定义名称一键打app和hap包](https://juejin.cn/post/7427050728719368202#comment)

*   [HarmonyOS数据列表加载更多(无需监听列表滑到最底部)](https://juejin.cn/post/7397638569731358760)

*   [HarmonyOS一行代码实现任意处弹窗](https://juejin.cn/post/7401358349877444642)
