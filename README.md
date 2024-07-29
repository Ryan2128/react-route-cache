## 安装

```js
// npm
npm i react-route-cache -S
// pnpm
pnpm i react-route-cache -S
// yarn
yarn add react-route-cache
```

## 使用

1. 给 Layout 组件的 outlet 加上 keep-alive

```js
import { KeepAlive, KeepAliveScope, RouterTabs } from "react-route-cache"
import { useOutlet } from "react-router-dom"

const Layout = () => {
  // 需要使用useOutlet
  const outlet = useOutlet()

  return (
    <KeepAliveScope>
      <RouterTabs />
      <KeepAlive>{outlet}</KeepAlive>
    </KeepAliveScope>
  )
}

export default Layout
```

2. 路由定义需要增加 name 属性

```js
// 也可以是createHashRouter
createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    loader: rootLoader,
    // 增加name属性，否则标签没有title，展示出现问题
    handle: { name: "首页" },
    children: [
      {
        path: "events/:id",
        element: <Event />,
        loader: eventLoader,
        handle: { name: "事件" },
      },
    ],
  },
])
```

3. 生命周期函数
   - useActivated 返回的方法会在 Deactivated 的时候执行。
   - 第二个可选参数是一个依赖项数组，为了更新回调函数里的依赖，一般不会用到，功能类似 useCallback，依赖变化不会执行函数。

```js
import { useActivated, useDeactivated } from "react-route-cache"

export const Demo = () => {
  useActivated(() => {
    console.log("激活")
    return () => {
      console.log("activated返回的方法会在Deactivated的时候执行")
    }
  })

  useDeactivated(() => {
    console.log("离开组件")
  })

  return <div>123</div>
}
```

## 其他 API

1. mode
   - 默认匹配路由 path，path 变化则会新增一个 tab，也就是如果查询参数变化不会新增一个 tab 缓存组件
   - 如果希望查询参数变化也会新增一个 tab 需要将 mode 改为 search
2. nameKey：如果路由 name 已被占用，可以通过该字段获取 handle 下其他字段的信息作为 tab 的 title

```js
interface KeepAliveScopeProps {
  mode?: "path" | "search";
  nameKey?: string;
}
;<KeepAliveScope mode="search" nameKey="tabName" />
```

3. close、closeAll、closeNavigator
   - close 方法用于关闭当前标签页
   - closeAll 用于关闭除了当前激活的 tab 所有的标签页
   - closeNavigator 是为了解决比如表单创建页，创建完之后需要跳转到其他路由。closeNavigator 会关闭当前创建页标签，然后跳转到指定路由。是 close()和 navigator(url)的语法糖。

```js
import { useKeepAlive } from '../hooks/use-keep-alive';
...
  const { close, closeAll, closeNavigator } = useKeepAlive();
  close()
  closeAll()
  // 是close()和navigator方法跳转到其他路由
  closeNavigator(url)
...
```