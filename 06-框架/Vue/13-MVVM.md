# MVVM

## MVC

- Model：数据模型，用来存储数据
- View：视图界面，用来展示 UI 界面和响应用户交互
- Controller：控制器(大管家角色)，监听模型数据的改变和控制视图行为、处理用户交互

业务越来越复杂时，由于 Controller 主要用来处理各种逻辑和数据转化，复杂业务逻辑界面的 Controller 非常庞大，维护困难。

## MVVM

- Model：数据模型，用来存储数据
- View：视图界面，用来展示 UI 界面和响应用户交互
- ViewModel

ViewModel 负责把 Model 的数据同步到 View 显示出来，还负责把 View 的修改同步回 Model。

Model 层代表数据模型，View 代表 UI 组件，ViewModel 是 View 和 Model 层的桥梁，数据会绑定到 viewModel 层并自动将数据渲染到页面中，视图变化的时候会通知 viewModel 层更新数据。
