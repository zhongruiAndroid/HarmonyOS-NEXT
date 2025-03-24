### 前言

有数据列表的页面，一般需要使用[*下拉刷新*](https://juejin.cn/post/7395866502716702755)和上拉加载的功能\
而数据列表实现加载下一页的功能，目前有以下几种方案\
(如果还有更优雅的方案,欢迎大佬们在评论区补充)

(源码地址在文末)

### 加载更多方案

1.  监听列表的滑动事件，在列表滑动到底部时触发加载更多的逻辑
2.  自定义布局，监听手势，上拉布局到某个高度时->释放手势->触发加载更多的逻辑\
    (实现原理[上篇文章](https://juejin.cn/post/7395866502716702755)有说)
3.  手动给数据列表加个ListItem，然后监听onVisibleAreaChange事件 ***(本文主要介绍的方案)***
4.  LazyForEach中通过index判断是否是最后一个item,然后触发加载更多逻辑

<!---->

    //在IDataSource中的totalCount方法中返回真实item数量+1
    totalCount(): number {
      return this.dataList.length + 1;
    }

<!---->

    LazyForEach(IDataSource, (item: Object, index) => {
      if(通过index判断是否是最后一个item){
        ListItem() {
          /*footer布局*/
        }
      }else{
        ListItem() {
          /*正常item布局*/
        }
      }
    })

### 各个方案缺点

**第1种方案**\
列表必须滑动到最底部才能触发事件，哪怕列表滑到99%也不能触发。\
如果列表有回弹效果，回弹时又会触发一次(小问题,可以通过逻辑判断规避或者禁用回弹效果)

**第2种方案**\
每次都需要一个上拉的操作，用户体验感略差(如果产品明确需要这种交互效果除外)

**第4种方案**\
该方案也是我一开始实现的方案，体验感也是最好的一种\
(真正的无感加载，正常滑动列表时，还没看到footer布局时，下一页可能就加载完成了)\
但是因为实现逻辑不够优雅，被我放弃了，然后改用第3种方案实现(对已有布局没有入侵性)

#### 效果图

##### 垂直列表

| 下拉刷新+上拉加载                                                                                                                                                                                                                                                                                                                                                                                                                          | List垂直列表                                                                                                                                                                                                                                                                                                                                                                                    | Grid垂直列表                                                                                                                                                                                                                                                                                                                                                                                  | 瀑布流垂直列表                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/0119843f36294906b84619ddc7a2834e~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743437186&x-orig-sign=e%2FB%2FEwIMqbYDJIw0EGqAqo6yhG0%3D" alt="a.gif" width="100%"> | ![2.gif](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/79efa8aa00e642bca82fb992d677c9dc~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743437186&x-orig-sign=js8x26Cn%2BIOn5BL202oMEKB%2BkGA%3D) | ![3.gif](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/01aeb47b08274a618abd6640456328e3~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743437186&x-orig-sign=12ssz57i%2Bf1%2BWSqNb1JQS4Qwxv8%3D) | <img src="https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/5aa3bbef43654252b4a2e345825e0a68~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743437186&x-orig-sign=Qi3r3e%2BoQHeG903zWSJrEo5Sv6U%3D" alt="4.gif" width="100%"> |

##### 横向列表

| List横向列表                                                                                                                                                                                                                                                                                                                                                                                 | Grid横向列表                                                                                                                                                                                                                                                                                                                                                                                 | 瀑布流横向列表                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| ![11.gif](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/48a88ef8bf034f09ac0e598b51889bd2~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743437186&x-orig-sign=ReiRYwGY89zXQw06dDOdv1iZ9jE%3D) | ![22.gif](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/d99ec6d110094ca9a4d7eed0b8b24346~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743437186&x-orig-sign=gPCoTrarBCCREGuBMa8fTscoKdE%3D) | ![33.gif](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/aa2e8b0ab14b4c95a64c56b648d87e67~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6ZKf552_:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNzAzMjg3MTI1NjI1OTAxIn0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1743437186&x-orig-sign=OMHc5zZCHACQjUco4GGrQxQlD4g%3D) |

**第3种方案**

#### 实现过程

1.  在list列表尾部添加一个item布局，然后监听item布局的onVisibleAreaChange事件
2.  onVisibleAreaChange()第一个参数传\[0],这样滑动列表时item布局在屏幕显示一丢丢时，就会触发该事件的回调,然后在该事件中执行加载更多的逻辑,\
    这样就规避第1种方案中必须滑动到最底部的问题

如果用户滑动速度不是很快，可以做到无感加载下一页\
如果产品需要footer视图出现一半及以上的面积，让用户感知明显，再触发加载更多\
只需要将第一个参数改成\[0.5]即可

    List() {
      LazyForEach(this.adapter, (item: Object, index) => {
        ListItem() {
           Text("正常item布局")
        }
      })
      
      /*手动添加一个footer布局*/
      ListItem() {
        LoadMoreView({
          /*控制器，用于通知LoadMoreView内部是否加载完成或加载错误或是否还有更多数据*/
          controller: this.loadMoreController,
          
          loadMore: () => {
            /*触发加载更多*/
          },
          
          /*非必传：是否是垂直列表*/
          vertical: this.isVertical,
          
          /*非必传：加载数据中的自定义视图*/
          loadingView:()=>{},
          
          /*非必传：加载错误的自定义视图*/
          errorView:()=>{},
          
          /*非必传：暂无更多数据的自定义视图*/
          noMoreView:()=>{}
        })
      }
    }

<!---->

    @Component
    export struct LoadMoreView {
        build() {
          Text("正在加载更多...")
          .width("100%")
          .height("50")
          .onVisibleAreaChange([0], (isVisible: boolean, currentRatio: number) => {
            if (isVisible) {
              /*触发加载更多逻辑*/
            }
          })
        }
    }

完整LoadMoreController代码

    export class LoadMoreController {
      /*加载结束*/
      loadEnd: (hasMore: boolean) => void = (hasMore: boolean) => {
        this.loadIsError = false
        //请求数据完成，hasMore true:还有更多数据，false:暂无更多数据
        this.hasMore = hasMore
      }
      /*加载错误*/
      loadError: () => void = () => {
        //请求数据失败
        this.loadIsError = true
      }
      /*隐藏加载提示*/
      setHiddenView: (hiddenView: boolean) => void = (hiddenView: boolean) => {
        this.hiddenView = hiddenView
      }
      /*是否还有更多数据*/
      public hasMore: boolean = false
      /*加载是否错误*/
      public loadIsError: boolean = false
      public hiddenView: boolean = false
      public isEndRequest: boolean = true
    }

完整LoadMoreView代码

    import { LoadMoreController } from './LoadMoreController';


    /*默认状态*/
    const state_def = 0;
    /*加载中*/
    const state_loading = 1;
    /*加载失败*/
    const state_error = 2;
    /*暂无更多*/
    const state_no_more = 3;

    @Component
    export struct LoadMoreView {
      controller: LoadMoreController = new LoadMoreController()
      loadMore: () => void = () => {
      }
      @State vertical: boolean = true
      @BuilderParam loadingView?: () => void
      @BuilderParam noMoreView?: () => void
      @BuilderParam errorView?: () => void
      @State state: number = state_def

      private setState() {
        if (this.controller.hasMore) {
          this.state = state_loading
        } else {
          this.state = state_no_more
        }
        if (this.controller.loadIsError) {
          this.state = state_error
        }
        if (this.controller.hiddenView) {
          this.state = state_def
        }
      }

      aboutToAppear() {
        this.setState()
        this.controller.loadEnd = (hasMore: boolean) => {
          this.controller.isEndRequest = true
          this.controller.loadIsError = false
          this.controller.hiddenView = false
          this.controller.hasMore = hasMore
          this.setState()
        }
        this.controller.loadError = () => {
          this.controller.isEndRequest = true
          this.controller.loadIsError = true
          this.controller.hiddenView = false
          this.setState()
        }
        this.controller.setHiddenView = (hiddenView: boolean) => {
          this.controller.hiddenView = hiddenView
          this.setState()
        }


        this.startLoadMore();
      }

      private startLoadMore() {
        if (this.controller.isEndRequest && this.state == state_loading) {
          this.controller.isEndRequest = false
          this.loadMore()
        }
      }

      build() {
        if (state_loading == this.state) {
          if (this.loadingView) {
            Stack() {
              this.loadingView()
            }
            .width(this.vertical ? "100%" : "auto")
            .height(!this.vertical ? "100%" : "auto")
            .onVisibleAreaChange([0], (isVisible: boolean, currentRatio: number) => {
              if (isVisible) {
                this.startLoadMore();
              }
            })
          } else {
            Text(this.vertical ? "正在加载更多..." : "正\n在\n加\n载\n更\n多\n...")
              .width(this.vertical ? "100%" : "50")
              .height(this.vertical ? "50" : "100%")
              .textAlign(TextAlign.Center)
              .onVisibleAreaChange([0], (isVisible: boolean, currentRatio: number) => {
                if (isVisible) {
                  this.startLoadMore();
                }
              })
          }

        } else if (state_no_more == this.state) {
          if (this.noMoreView) {
            this.noMoreView()
          } else {
            Text(this.vertical ? "暂无更多数据" : "暂\n无\n更\n多\n数\n据")
              .width(this.vertical ? "100%" : "50")
              .height(this.vertical ? "50" : "100%")
              .textAlign(TextAlign.Center)
          }
        } else if (state_error == this.state) {
          if (this.errorView) {
            Stack() {
              this.errorView()
            }
            .width(this.vertical ? "100%" : "auto")
            .height(!this.vertical ? "100%" : "auto")
            .onClick(() => {
              this.clickLoadMore();
            })

          } else {
            Text(this.vertical ? "加载失败,点击重试" : "加\n载\n失\n败\n,\n点\n击\n重\n试")
              .width(this.vertical ? "100%" : "50")
              .height(this.vertical ? "50" : "100%")
              .textAlign(TextAlign.Center)
              .onClick(() => {
                this.clickLoadMore();
              })
          }
        }
      }

      private clickLoadMore() {
        if (this.controller.isEndRequest && this.state == state_error) {
          this.state = state_loading;
          this.controller.isEndRequest = false;
          this.loadMore();
        }
      }
    }

### 项目源码

**[-->源码地址](https://gitee.com/zhongrui_developer/LoadMoreProject)**\
<https://gitee.com/zhongrui_developer/LoadMoreProject>

