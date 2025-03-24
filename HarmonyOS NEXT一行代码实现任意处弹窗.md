---
theme: cyanosis
---

### 前言

从Api9开始开发鸿蒙的大佬应该被自定义弹窗折腾得够呛，到目前为止我能想到的自定义弹窗方案有以下几种

1.  promptAction.openCustomDialog(options: CustomDialogOptions)\
    (该方案@Builder装饰的视图(builder参数)必须定义在组件内部)

2.  CustomDialogController+CustomDialog\
    (该方案CustomDialogController仅在作为@CustomDialog和@Component struct的成员变量，\
    且在@Component struct内部定义时赋值才有效)

3.  windowStage.createSubWindow\
    (该方案需要对子窗口进行完美的控制,一旦逻辑控制不好容易出大问题)

4.  自定义一个@Component组件当做普通视图引入布局中(顶层位置)，动态控制隐藏显示，达到弹窗的效果

5.  使用Navigation时,将NavDestination的mode属性设置为NavDestinationMode.DIALOG\
    (优点：在使用Navigation做路由方案时，NavDestination弹窗方案不会覆盖即将要跳转的页面)**(推荐)**

6.  使用ComponentContent **(本文要介绍的方法)**  **(推荐)**\
    (源码地址在文末)

**除了3、5、6方案，其他方案都不方便在非组件中控制弹窗显示**

### 效果图

<img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/ca68bea84ebc483481a7ae9620904211~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743437232&x-orig-sign=u%2FmVv%2FWz07BPScS%2Fy%2BqP1kLZP3s%3D" alt="1.gif" width="50%">

<br/>  

#### **一行代码任意处显示Loading和对话框或者自定义弹窗**

    //显示loading弹窗
    Loading.show(UIContext)

    //loading弹窗消失
    Loading.dismiss()

    //显示自定义弹窗
    TestDialog.showDialog(UIContext)

#### **实现在普通类中也可以控制弹窗显示**

    import { Dialog } from './Dialog'
    import { Loading } from './Loading'

    export class TestDialog {
      static showLoading(ctx: UIContext) {
        Loading.show(ctx)
      }

      static showDialog(ctx: UIContext) {
        let param: DialogParams = {
          title: "提示",
          content: "是否确定要删除?",
          leftText: "取消",
          rightText: "确定",
          leftClick: () => {
            console.info("=========取消")
            dialog.dismiss()
          },
          rightClick: () => {
            console.info("=========确定")
            dialog.dismiss()
          }
        } as DialogParams

        let dialog = new BaseDialog(ctx)
        dialog.setContentView(wrapBuilder(buildAlertDialogView), param)
        dialog.show()
      }
    }

<!---->

    import { Loading } from './Loading';
    import { TestDialog } from './TestDialog';

    @Entry
    @Component
    struct Index {
      @State message: string = 'Hello World';

      build() {
        Column({ space: 10 }) {
          Button("显示Loading")
            .onClick(() => {
              TestDialog.showLoading(this.getUIContext())
              setTimeout(() => {
                Loading.dismiss()
              }, 2000)
            })
          Button("显示Dialog")
            .onClick(() => {
              TestDialog.showDialog(this.getUIContext())
            })
        }.justifyContent(FlexAlign.Center)

        .height('100%')
        .width('100%')
      }
    }

#### 封装后的使用方式

**有参数**

    let param: DialogParams = {
      title: "提示",
      content: "是否确定要删除?",
      leftText: "取消",
      rightText: "确定",
      leftClick: () => {
        dialog.dismiss()
      },
      rightClick: () => {
        dialog.dismiss()
      }
    } as DialogParams

    let dialog = new BaseDialog(ctx)
    dialog.setContentView(wrapBuilder(buildAlertDialogView), param)
    dialog.show()

**无参数**

      static showDialogNoData(ctx: UIContext) {
        let dialog = new BaseDialog(ctx)
        dialog.setContentView(wrapBuilder(buildAlertDialogViewNoData))
        dialog.show()
      }

**有参数视图**

    ```
    export interface DialogParams {
      title: string,
      content: string,
      leftText: string,
      rightText: string,
      leftClick?: () => void,
      rightClick?: () => void,
    }
    ```
    @Builder
    function buildAlertDialogView(param: DialogParams) {
      Column() {
        if (param.title) {
          Text(param.title)
            .width('100%')
            .fontWeight(FontWeight.Medium)
            .fontColor("#333333")
            .fontSize(20)
            .margin({ top: 17 })
        }
        Text(param.content)
          .width('100%')
          .fontWeight(FontWeight.Medium)
          .fontColor("#666666")
          .fontSize(14)
          .margin({ top: 17, bottom: 8 })

        Row() {
          if (param.leftText) {
            Button(param.leftText)
              .height(40)
              .fontSize(16)
              .fontColor('#666666')
              .layoutWeight(1)
              .fontWeight(FontWeight.Medium)
              .margin({ right: 5 })
              .backgroundColor('#ffffff')
              .onClick(() => {
                param?.leftClick?.()
              })
          }

          Button(param.rightText)
            .height(40)
            .fontSize(16)
            .fontColor('#ff06bcef')
            .layoutWeight(1)
            .fontWeight(FontWeight.Medium)
            .backgroundColor('#ffffff')
            .onClick(() => {
              param?.rightClick?.()
            })
        }
      }
      .width("90%")
      .height('auto')
      .padding({ left: 25, right: 25, bottom: 15 })
      .backgroundColor(Color.White)
      .borderRadius(25)
    }

**无参数视图**

```

@Builder
function buildAlertDialogViewNoData() {
  Column() {
    Text('标题')
      .width('100%')
      .fontWeight(FontWeight.Medium)
      .fontColor("#333333")
      .fontSize(20)
      .margin({ top: 17 })
    Text('param.content')
      .width('100%')
      .fontWeight(FontWeight.Medium)
      .fontColor("#666666")
      .fontSize(14)
      .margin({ top: 17, bottom: 8 })

    Row() {
      Button('取消')
        .height(40)
        .fontSize(16)
        .fontColor('#666666')
        .layoutWeight(1)
        .fontWeight(FontWeight.Medium)
        .margin({ right: 5 })
        .backgroundColor('#ffffff')
        .onClick(() => {
          //param?.leftClick?.()
        })

      Button('确定')
        .height(40)
        .fontSize(16)
        .fontColor('#ff06bcef')
        .layoutWeight(1)
        .fontWeight(FontWeight.Medium)
        .backgroundColor('#ffffff')
        .onClick(() => {
          //param?.rightClick?.()
        })
    }
  }
  .width("90%")
  .height('auto')
  .padding({ left: 25, right: 25, bottom: 15 })
  .backgroundColor(Color.White)
  .borderRadius(25)
}
```

#### **Dialog完整封装代码**

    import { ComponentContent, promptAction, PromptAction } from '@kit.ArkUI'

    export class BaseDialog {
      private _isShowing: boolean = false
      private uiContext?: UIContext
      private promptAction?: PromptAction
      private contentNode?: ComponentContent<Object>

      constructor(uiContext: UIContext) {
        this.uiContext = uiContext
        this.promptAction = this.uiContext.getPromptAction();
      }

      public setContentView<T extends Object>(builder: WrappedBuilder<[T]>|WrappedBuilder<[]>, args?: T) {
        if (!this.uiContext) {
          return
        }
        if (args) {
          this.contentNode = new ComponentContent(this.uiContext, builder, args);
        } else {
          this.contentNode = new ComponentContent(this.uiContext, builder as WrappedBuilder<[]>);
        }
      }


      public setData<T extends Object>(args: T) {
        this.contentNode?.update(args)
      }

      public show(options?: promptAction.BaseDialogOptions) {
        if (this._isShowing) {
          return
        }
        this._isShowing = true
        if (options) {
          this.promptAction?.openCustomDialog(this.contentNode, options).then(() => {
          }).catch(() => {
            this._isShowing = false
          });
        } else {
          this.promptAction?.openCustomDialog(this.contentNode).then(() => {
          }).catch(() => {
            this._isShowing = false
          });
        }
      }

      public dismiss() {
        this._isShowing = false
        if (this.contentNode) {
          this.promptAction?.closeCustomDialog(this.contentNode).then(() => {
            this.contentNode?.dispose()
            this.uiContext = undefined
            this.promptAction = undefined
            this.contentNode = undefined
          })
        }
      }

      public isShowing(): boolean {
        return this._isShowing
      }

      public update<T extends Object>(args: T) {
        this.contentNode?.update(args)
      }

      public reuse<T extends Object>(args?: T) {
        this.contentNode?.reuse(args)
      }

      public recycle() {
        this.contentNode?.recycle()
      }

      public updateConfiguration() {
        this.contentNode?.updateConfiguration()
      }
    }

### 源码

\-->[源码地址](https://gitee.com/zhongrui_developer/dialog-demo))\
<https://gitee.com/zhongrui_developer/dialog-demo>

