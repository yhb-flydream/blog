# vue 项目路由权限管理实现

[TOC]

项目基于 [vue-element-admin](https://panjiachen.github.io/vue-element-admin) 基础之上进行开发，感谢 [花裤衩](https://github.com/PanJiaChen) 以及为此项目做出贡献的大佬 [社会社会]！

## 思路

路由数据由后端控制，前端调用接口返回数据；前端拿到数据处理成可使用的路由结构，然后渲染到页面。

1. 路由数据从哪来，路由数据不能在后端写死，那样每次增删都会很麻烦，所以需要有专门的系统去维护，这样可以方便的控制路由的增删；
2. 一个专门的系统只用来维护一个后台管理系统有点大材小用，所以这个专门的系统需要可以管理多个应用及对应的路由数据；
3. 用户登录后台管理系统会有自己单独的路由数据，这样才可以不同的人有不同的权限，所以对用户的管理划分了角色，不同的角色可以分配不同的路由权限，不同的角色可以绑定相应的用户，这样不同角色下的用户就可以分配不同的路由权限
4. 用户登录后台管理系统之后会去请求自己的路由数据，后端根据用户的账号绑定的角色返回给前端对应的路由数据
5. 前端拿到数据处理成可使用的路由结构，然后渲染到页面

## 路由数据结构

为了能提供更多的功能，后端返回的数据结构是在原来菜单结构基础之上有所改动的。

原路由数据结构：

```js
{
  path: '/example',
  component: Layout,
  redirect: '/example/list',
  name: 'Example',
  meta: {
    title: 'Example',
    icon: 'el-icon-s-help'
  },
  children: [
    {
      path: 'create',
      component: () => import('@/views/example/create'),
      name: 'CreateArticle',
      meta: { title: 'Create Article', icon: 'edit' }
    },
    {
      path: 'edit/:id(\\d+)',
      component: () => import('@/views/example/edit'),
      name: 'EditArticle',
      meta: { title: 'Edit Article', noCache: true, activeMenu: '/example/list' },
      hidden: true
    }
  ]
}
```

后端返回数据结构：

```json
{
  "id": "",
  "type": 0, // 用来标注当前层级的类型 0：头部菜单；1：侧边目录；2：侧边菜单；3：页面按钮
  "name": "商家管理", // 相对于 meta 里面的 title
  "icon": "", // 相对于 meta 里面的 icon
  "component": "", // 存放组件关键字，path、name 使用此字段
  "permissionFlag": "storeManagement:head", // 权限标识
  "sort": 1,
  "children": [
    {
      "id": "",
      "type": 1,
      "name": "商家管理",
      "icon": "",
      "component": "",
      "permissionFlag": "storeManage:menu",
      "sort": 1,
      "children": [
        {
          "id": "",
          "type": 2,
          "name": "商家管理",
          "icon": "",
          "component": "storeManage",
          "permissionFlag": "storeManage:page",
          "sort": 1,
          "children": [
            {
              "id": "",
              "type": 3,
              "name": "新增",
              "icon": "",
              "component": "storeManageDetail",
              "permissionFlag": "storeManageDetail:add",
              "sort": null,
              "children": null
            }
          ]
        }
      ]
    }
  ]
}
```

路由数据采用多级嵌套结构，基本可以满足需求。

## 处理路由数据

拿到接口返回的数据之后还不能直接使用，需要经过一些处理。

### 合并开发的临时路由数据

在本地的开发，新增页面时，为了方便开发，获取到后端返回的数据之后用了一个方法来合并数据：

```js
let temporaryMenuArray = [] // 临时路由数组
function getNewPermissionTree(temporaryMenuArray, userPermissionMenu) {
  if (temporaryMenuArray && temporaryMenuArray.length > 0) {
    temporaryMenuArray.forEach((tempItem) => {
      if (!userPermissionMenu.some((it) => it.permissionFlag === tempItem.permissionFlag)) {
        userPermissionMenu.push(tempItem)
      } else {
        userPermissionMenu = userPermissionMenu.map((item) => {
          if (item.permissionFlag === tempItem.permissionFlag) {
            item.children = getNewPermissionTree(tempItem.children || [], item.children || [])
          }
          return item
        })
      }
    })
  }
  return userPermissionMenu
}

export const setTemporaryMenu = function (userPermissionMenu) {
  return getNewPermissionTree(temporaryMenuArray, userPermissionMenu)
}
```

`temporaryMenuArray` 临时路由数组的结构需要跟后端返回的数据结构一致。

```json
temporaryMenuArray = [
  {
    "id": "",
    "type": 0, // 用来标注当前层级的类型
    "name": "商家管理", // 相对于 meta 里面的 title
    "icon": "", // 相对于 meta 里面的 icon
    "component": "", // 存放组件关键字，path、name 使用此字段
    "permissionFlag": "storeManagement:head", // 权限标识
    "sort": 1,
    "children": [
      {
        "id": "",
        "type": 1,
        "name": "商家管理",
        "icon": "",
        "component": "",
        "permissionFlag": "storeManage:menu",
        "sort": 1,
        "children": [
          {
            "id": "",
            "type": 2,
            "name": "商家管理",
            "icon": "",
            "component": "storeManage",
            "permissionFlag": "storeManage:page",
            "sort": 1,
            "children": [
+ --          {
+ --            "id": "",
+ --            "type": 3,
+ --            "name": "查看",
+ --            "icon": "",
+ --            "component": "storeManageDetail",
+ --            "permissionFlag": "storeManageDetail:view",
+ --            "sort": null,
+ --            "children": null
+ --          }
            ]
          }
        ]
      },
+ --  {
+ --    "id": "",
+ --    "type": 1,
+ --    "name": "商品管理",
+ --    "icon": "",
+ --    "component": "",
+ --    "permissionFlag": "goodsManage:menu",
+ --    "sort": 1,
+ --    "children": [
+ --      {
+ --        "id": "",
+ --        "type": 2,
+ --        "name": "商品管理",
+ --        "icon": "",
+ --        "component": "goodsManage",
+ --        "permissionFlag": "goodsManage:page",
+ --        "sort": 1,
+ --        "children": [
+ --          {
+ --            "id": "",
+ --            "type": 3,
+ --            "name": "新增",
+ --            "icon": "",
+ --            "component": "goodsManageDetail",
+ --            "permissionFlag": "goodsManageDetail:add",
+ --            "sort": null,
+ --            "children": null
+ --          }
+ --        ]
+ --      }
+ --    ]
+ --  }
    ]
  }
]
```

> `+ --` 部分为为新增数据。

使用 `setTemporaryMenu`:

```js
import { setTemporaryMenu } from '@/utils/set-temporary-menu'

// userPermissionMenu 为从接口获取权限菜单数据
// 把接口返回的菜单数据跟临时设置的菜单数据合并
userPermissionMenu = setTemporaryMenu(userPermissionMenu)
```

合并完临时的路由之后就开始进行路由的处理了。

### 处理路由模块

从上面后端返回的数据结构中可以看出，已经和需要处理成的路由很像了，但还是有一些数据没有必要在专门的系统那里维护，所以需要一些其他数据的处理：

```js
import { routesComponents } from '@/router/routes'

// 用于设置 headerMenu 字段，标注菜单属于哪一个头部一级菜单
let headerMenu = ''
// 用于设置 activeMenu 字段，可以使跳转子级页面时的父级页面高亮
let activeMenu = ''
// 用于设置 parentName 字段，设置子级页面 title
let parentName = ''
// 用于设置 parentPath 字段，设置嵌套目录的路径
let parentPath = ''
// 用于收集路由，防止设置重复路由
let routesComponentTemp = new Set()

/**
 * 收集当前用户可以使用的路由模块
 * @param {*} userPermissionMenu 接口返回的权限菜单数据
 */
function gatherUserCanUseRouteModules(userPermissionMenu) {
  let routersTemp = []
  userPermissionMenu.forEach((route) => {
    let routeTemp = {}
    let { children: userRouteChildren = [], name, icon, ...userRouteOtherData } = route
    let permissionFlagArr = route.permissionFlag.split(':')
    let flag = permissionFlagArr[0]
    // !routersTemp.some(it => it.name === flag) 防止 同一个父级下有重复路由
    // !routesComponentTemp.has(route.component) 防止 不同层级下有重复路由
    if (!routersTemp.some((it) => it.name === flag) && !routesComponentTemp.has(route.component)) {
      if (route.type === 0) {
        headerMenu = route.permissionFlag
        routeTemp.meta = { title: name }
        routeTemp.isHeaderMenu = true
      }
      if (route.type === 1) {
        routeTemp.alwaysShow = false
        // route.type 为 1 时，为目录层级，component 默认为 Layout。没有考录多层目录情况。
        // 多层目录情况下可以考虑在新建菜单时用 权限标识(permissionFlag) 字段区分。例如
        // 例如：权限标识(permissionFlag): "storeManage:menu:2"
        // :2 可以区分出此时的目录为嵌套目录，则 component 需要设置为 flag（此路由也需要在 @/router/routes 下新建过渡页面）
        let isNestedMenu = /\d/.test(permissionFlagArr[permissionFlagArr.length - 1])
        parentPath = isNestedMenu ? parentPath : flag
        routeTemp.component = isNestedMenu ? routesComponents[flag] : routesComponents['Layout']
        routeTemp.headerMenu = headerMenu
        routeTemp.meta = { icon: icon, title: name }
        routeTemp.path = isNestedMenu ? flag : `/${flag}`
        let firstPath = isNestedMenu ? `/${parentPath}` : ''
        routeTemp.redirect = `${firstPath}/${flag}/${userRouteChildren[0].permissionFlag.split(':')[0]}`
        activeMenu = routeTemp.redirect
      }
      if (route.type === 2) {
        routeTemp.path = flag
        routeTemp.component = routesComponents[route.component]
        // route.type 为 2 时，为列表页面 默认 noCache：false 缓存页面
        // 当 权限标识(permissionFlag) 包含 :keepAlive 时 noCache 会被设置为 true 不缓存页面
        let noCache = permissionFlagArr[permissionFlagArr.length - 1] === 'keepAlive' ? true : false
        routeTemp.meta = { title: name, isBack: false, isEdit: false, noCache }
        parentName = name
        routesComponentTemp.add(route.component)
      }
      if (route.type === 3 && route.component) {
        routeTemp.path = flag
        routeTemp.component = routesComponents[route.component]
        // route.type 为 3 时，为按钮跳转页面 默认 noCache：true 不缓存页面
        // 当 权限标识(permissionFlag) 包含 :keepAlive 时 noCache 会被设置为 false 缓存页面
        let noCache = permissionFlagArr[permissionFlagArr.length - 1] === 'keepAlive' ? false : true
        name = ['新建', '新增', '查看', '编辑'].includes(name) ? '详情' : name
        routeTemp.meta = { title: `${parentName} - ${name}`, noCache, activeMenu: activeMenu }
        routeTemp.hidden = true
        routesComponentTemp.add(route.component)
      }
      // 当前层级有子级，则去收集子级
      let userRouteChildrenTemp = []
      if (userRouteChildren && userRouteChildren.length) {
        userRouteChildrenTemp = gatherUserCanUseRouteModules(userRouteChildren)
      }
      routeTemp = { ...userRouteOtherData, ...routeTemp }
      // 当 route.type 为 0、1 时，name 设置为 permissionFlag 防止重复
      routeTemp.name = [0, 1].includes(route.type) ? route.permissionFlag : flag
      if (route.type === 0 || route.type === 1 || (route.type === 3 && route.component)) {
        routersTemp.push({ ...routeTemp, children: userRouteChildrenTemp })
      } else if (route.type === 2) {
        // 当 route.type 为 2 使其子级跟其平级，不用再嵌套页面
        routersTemp.push(routeTemp, ...userRouteChildrenTemp)
      }
    }
  })
  return routersTemp
}

// 收集当前用户可以使用的路由模块
const userCanUseRouteModules = gatherUserCanUseRouteModules(userPermissionMenu)
```

`routesComponents` 为一个手动维护的页面路由文件：

```js
// 写成 ...{} 格式是为了便于区分

/* Layout */
const Layout = {
  Layout: () => import('@/layout'),
}

/* 商家管理 storeManagement */
const storeManagement = {
  // 商家管理
  ...{
    storeManage: () => import('@/views/store/storeManage'),
    storeManageDetail: () => import('@/views/store/storeManageDetail'),
  },
  // 商品管理
  ...{
    goodsManage: () => import('@/views/goods/goodsManage'),
    goodsManageDetail: () => import('@/views/goods/goodsManageDetail'),
  },
}

export const routesComponents = {
  ...Layout,
  ...storeManagement,
}
```

得到的 `userCanUseRouteModules` 结构如下：

```json
[
  {
    "id": "",
    "type": 0,
    "component": "",
    "path": null,
    "permissionFlag": "storeManagement:head",
    "sort": 1,
    "meta": {
      "title": "商家管理"
    },
    "isHeaderMenu": true,
    "name": "storeManagement:head",
    "children": [
      {
        "id": "",
        "type": 1,
        "component": "",
        "path": "/storeManage",
        "permissionFlag": "storeManage:menu",
        "sort": 1,
        "alwaysShow": false,
        "headerMenu": "storeManagement:head",
        "meta": {
          "icon": "",
          "title": "商家管理"
        },
        "redirect": "/storeManage/storeManage",
        "name": "storeManage:menu",
        "children": [
          {
            "id": "",
            "type": 2,
            "component": "",
            "path": "storeManage",
            "permissionFlag": "storeManage:page",
            "sort": 1,
            "meta": {
              "title": "商家管理",
              "isBack": false,
              "isEdit": false,
              "noCache": false
            },
            "name": "storeManage"
          },
          {
            "id": "",
            "type": 3,
            "component": "",
            "path": "storeManageDetail",
            "permissionFlag": "storeManageDetail:view",
            "sort": null,
            "meta": {
              "title": "商家管理 - 详情",
              "noCache": true,
              "activeMenu": "/storeManage/storeManage"
            },
            "hidden": true,
            "name": "storeManageDetail",
            "children": []
          }
        ]
      }
    ]
  }
]
```

上面数据中的 `type` 为2、3层级的 `component` 已经被替换成对应页面的路由模块了。

### 获取需要添加的路由

得到 `userCanUseRouteModules` 之后，其中 name 为 `storeManagement:head` 的那一层数据是供头部菜单使用的，其 children 的数据才是需要添加的路由数据：

```js
import { asyncRoutes } from '@/router'
// 需要添加的的路由
const addRoutes = [...userCanUseRouteModules.reduce((prev, curr) => [...prev, ...curr.children], []), ...asyncRoutes]
```

此时的 `asyncRoutes` 只放了默认匹配的路由，这个是需要放在所有路由的最后的：

```js
export const asyncRoutes = [
  // 404 page must be placed at the end !!!
  { path: '*', redirect: '/404', hidden: true },
]
```

### 使用 `router.addRoutes` 添加路由

```js
import router, { resetRouter } from '@/router'

// reset router
resetRouter()

router.addRoutes(addRoutes)
```

至此对路由的处理结束。

处理路由这一步是放在 `router.beforeEach` 路由全局前置守卫里的，结合原有的逻辑进行了一些判断处理。

因为路由数据是由后端数据处理得到的，只包含和挂载了当前用户能有权限访问的部分，没有权限访问的路由就没有被 `router.addRoutes` 添加，所以并没有在路由守卫里对每次路由的跳转都进行校验。
