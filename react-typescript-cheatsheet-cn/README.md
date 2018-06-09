## 介绍

**原文地址：**[sw-yx](https://github.com/sw-yx) / [**react-typescript-cheatsheet**](https://github.com/sw-yx/react-typescript-cheatsheet)

**译文地址：**[[译] react-typescript-备忘录](https://github.com/fi3ework/blog/tree/master/react-typescript-cheatsheet-cn)

## 序

:wave:  本仓库由 [@swyx](https://twitter.com/swyx) 与 [@IslamAttrash ](https://twitter.com/IslamAttrash) 共同维护，我们很高兴看到你想配合 React 来使用 TypeScript， 本文是提供给那些熟悉 TypeScript 概念的 React 开发者，帮助他们编写他们的第一个 React + TypeScript 应用的教程，如果你在阅读的过程中发现任何错误和疏漏， 请[提出 issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new)! 👍

## 第一章：配置

### 准备工作

1. 对 [React](https://reactjs.org) 有一定的了解。
2. 熟悉 [Typescript Types](https://www.typescriptlang.org/docs/handbook/basic-types.html)。
3. 阅读过 [the Typescript section in the official React docs](https://reactjs.org/docs/static-type-checking.html#typescript)。
4. (可选) 阅读过微软的 [TypeScript-React-Starter](https://github.com/Microsoft/TypeScript-React-Starter#typescript-react-starter) 文档。

### React + Typescript 脚手架

1. <https://github.com/wmonk/create-react-app-typescript> 是官方推荐的 Typescript 的脚手架，fork 自`create-react-app`。

   > CodeSandbox 有一个基于 [React TypeScript template](https://codesandbox.io/s/react-ts) 的模版项目。 *Contributed by: [@antmdvs](https://github.com/sw-yx/react-typescript-cheatsheet/pull/11)*

2. <https://github.com/sw-yx/create-react-app-parcel-typescript> 使用 Parcel 来建立了一个 React + Typescript 应用 :)

3. https://github.com/basarat/typescript-react/tree/master/01%20bootstrap，手动配置 React + Typescript + Webpack + Babel。

要注意的是，请确保你安装了 `@types/react` 和 `@types/react-dom` 。 [如果你对有类型定义的项目不熟悉的话点击了解更多](https://definitelytyped.org/)。

### 引入 React

```tsx
import * as React from 'react';
import * as ReactDOM from 'react-dom';
```

在 [TypeScript 2.7+ ](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-7.html) 中，你可以将 Typescript 配置 `--allowSyntheticDefaultImports` （或者在 tsconfig 中添加 `"allowSyntheticDefaultImports": true ` ）来像普通的 jsx 一样来引入模块：

```tsx
import React from 'react';
import ReactDOM from 'react-dom';
```

<details>

<summary>解释</summary>

为什么不是添加 `esModuleInterop`？ [Daniel Rosenwasser](https://twitter.com/drosenwasser/status/1003097042653073408) 解释说这是为了更好的配合 webpack/parcel. 更多讨论请参见 <https://github.com/wmonk/create-react-app-typescript/issues/214>

Please PR or [File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new) with your suggestions!
</details>

## 第二章：开始

### 无状态组件

*Contributed by: [@jasanst](https://github.com/sw-yx/react-typescript-cheatsheet/pull/9) and [@tpetrina](https://github.com/sw-yx/react-typescript-cheatsheet/pull/21)*

你可以在将 props 解构的时候指定它们的类型：

```tsx
const App = ({ message }: { message: string }) => <div>{message}</div>;
```

或者你可以使用 `@types/react` 提供的无状态组件的通用类型：

```tsx
const App: React.SFC<{ message: string }> = ({ message }) => <div>{message}</div>;
```

<details>

<summary><b>讨论</b></summary>

前一种模式要简洁一点，那么为什么人们还会使用 `React.SFC` 呢？如果你想在函数体内正确的使用 `children` 的话，在第一种模式下你需要显示的声明它。`SFC<T>` 已经正确的包含了 `children` 属性，所以不需要你再声明它的类型了。

```tsx
const Title: React.SFC<{ title: string }> = ({ children, title }) => (
    <div title={title}>{children}</div>
);
```

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

</details>

### 基于 Class 的有状态组件

在使用 Typescript 时，`React.Component` 是一个通用类型 （也被写作 `React.Component<PropType, StateType>`），所以你实际上需要给它提供 prop 和 state（可选）的类型：

```tsx
class App extends React.Component<{
  message: string, // it takes one prop called 'message' which is a string type
}> {
  render() {
    return (
      <div>{this.props.message}</div>
    );
  }
}
```

如果组件有状态，我们如下来给状态添加类型：

```tsx
class App extends React.Component<{
  message: string, // this is the prop type
}, {
    count: number, // this is the state type
  }> {
  state = {
    count: 0
  }
  render() {
    return (
      <div>{this.props.message} {this.state.count}</div>
    );
  }
}
```

如果你想定义一个 clickhandler，那么就像原来一样即可，不过记住函数的参数都需要被定义类型：

```tsx
class App extends React.Component<{
  message: string,
}, {
    count: number,
  }> {
  state = {
    count: 0
  }
  render() {
    return (
      <div onClick={() => this.increment(1)}>{this.props.message} {this.state.count}</div>
    );
  }
  increment = (amt: number) => { // like this
    this.setState(state => ({
      count: state.count + amt
    }));
  }
}
```

如果你想先声明一个之后用到的变量，那么声明它的类型即可：

```tsx
class App extends React.Component<{
  message: string,
}> {
  pointer: number // like this
  componentDidMount() {
    this.pointer = 3;
  }
  render() {
    return (
      <div>{this.props.message} and {this.pointer}</div>
    );
  }
}
```

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

### 定义 DefaultProps

定义一个 React 组件的 defaultProps 静态成员变量不难，有很多种方法可以实现，不过我们想展示一种我们最终选择的最优雅的代码方式来实现。

```tsx
interface IMyComponentProps {
  firstProp: string;
  secondProp: IPerson[];
}

export class MyComponent extends React.Component<IMyComponentProps, {}> {
  static defaultProps: Partial<IMyComponentProps> = {
    firstProp: "default",
  };
}
```

<details>

<summary>解释</summary>

这种模式使用了TypeScript 的 `Partial type` 特性，这意味着当前的接口只会实现被包裹的接口的一部分，这样我们可以随意拓展 defaultProps  而不需要改其他任何地方。

另一种方案是定义一个全新的接口，如下：

```ts
interface IMyComponentProps {
  firstProp: string;
  secondProp: IPerson[];
}

interface IMyComponentDefaultProps {
    firstProp: string;
}

export class MyComponent extends React.Component<IMyComponentProps, {}> {
  static defaultProps: IMyComponentDefaultProps = {
    firstProp: "default",
  };
}
```

这种方式的问题是当我们需要给 defaultProps 添加新的属性的时候，需要给 `IMyComponentDefaultProps` 添加新的属性！
</details>

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

### 提取 Prop Types

相对于**内联式**的定义 prop types，我们也可以分离的进行声明（这样有更好的可复用性及代码组织）

```tsx
type AppProps = { message: string }
const App: React.SFC<AppProps> = ({ message }) => <div>{message}</div>;
```

你也可以在有状态组件中使用（真的，任何类型都可以）：

```tsx
type AppProps = { // like this
  message: string,
}
type AppState = { // and this
  count: number,
}
class App extends React.Component<AppProps, AppState> {
  state = {
    count: 0
  }
  render() {
    return (
      <div>{this.props.message} {this.state.count}</div>
    );
  }
}
```

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

### types 还是 interfaces？

`interface`s 和 `type`s 在 Typescript 中是不同的，不过就目前在 React 中使用到的方面来看，他们的作用用法和作用非常相似，这里提供一份何时使用它们的经验法则：

- 当允许库或第三方开发者定义类型时，要给这些公共的 API 定义使用 `interface`。
- 考虑为 React 组件的 Props 和 State 使用 `type` ，因为它有更多的限制。

[你可以在这里阅读更多的使用 types 和 interfaces 的边界用例](https://medium.com/@martin_hotell/interface-vs-type-alias-in-typescript-2-7-2a8f1777af4c)。注意 Typescript 2.1 之后已经有了很大的变化。

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

### 基本的 Prop Types 例子

```tsx
type AppProps = {
  message: string,
  count: number,
  disabled: boolean,
  names: string[], // array of a type!
  obj: object, // any object as long as you dont use it in your typescript code
  obj2: {}, // same
  object: {
   id: string,
   title: string
  }, // an object with defined properties
  objects: {
   id: string,
   title: string
  }[], // array of objects!
  onSomething: Function, // not recommended
  onClick: () => void, // function that doesn't return anything
  onChange: (id: number) => void, // function with named prop
  optional?: OptionalType, // an optional prop
}
```

### 有用的 React Type 例子

```tsx
export declare interface AppProps {
  children1: JSX.Element; // bad
  children2: JSX.Element | JSX.Element[]; // meh
  children3: React.ReactChild | React.ReactChildren; // better
  children: React.ReactNode; // best
  style?: React.CSSProperties; // for style
  onChange?: (e: React.FormEvent<HTMLInputElement>) => void; // form events!
  props: Props & React.HTMLProps<HTMLButtonElement> // to impersonate all the props of a HTML element
}
```

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

### 表单与事件

这里可以耍一下小聪明，因为有编辑器在这里帮了很大的忙，@type 给了我们很多预置的定义，在定义你想定义的类型时自动补全会帮助你完成，这里有一个表单的 `onChange` 类型定义：

```tsx
class App extends React.Component<{}, { // no props
    text: string,
  }> {
  state = {
    text: ''
  }
  render() {
    return (
      <div>
        <input
          type="text"
          value={this.state.text}
          onChange={this.onChange}
        />
      </div>
    );
  }
  onChange = (e: React.FormEvent<HTMLInputElement>): void => {
    this.setState({text: e.currentTarget.value})
  }
}
```

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

## 第三章：高级用法

### 高阶组件 / render props

有时你想写一个接受 React 元素或者字符串或者其他的类型的 prop，这种情况下最好的 Type 方式是使用 `React.ReactNode`，React Node 可以匹配任何合适的类型：

```tsx
import * as React from 'react';
export interface Props {
  label?: React.ReactNode;
  children: React.ReactNode;
}
export const Card = (props: Props) => {
  return (
    <div>
      {props.label && <div>{props.label}</div>}
      {props.children}
    </div>
  );
};
```

如果你使用函数作为渲染的 prop

```tsx
export interface Props {
  children: (foo: string) => React.ReactNode;
}
```

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

### Context

*Contributed by: [@jpavon](https://github.com/sw-yx/react-typescript-cheatsheet/pull/13)*

使用新的 context API `React.createContext`：

```tsx

class Provider extends React.Component<{}, ProviderState> {
  public readonly state = {
    themeColor: 'red'
  }

  private update = ({ key, value }: UpdateStateArg) => {
    this.setState({ [key]: value })
  }

  public render() {
    const store: ProviderStore = {
      state: this.state,
      update: this.update
    }

    return (
      <Context.Provider value={store}>
        {this.props.children}
      </Context.Provider>
    )
  }
}

const Consumer = Context.Consumer
```

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

### Forwarding References/createRef

使用 `React.RefObject`：

```tsx
class CssThemeProvider extends React.PureComponent<Props> {
  private rootRef: React.RefObject<HTMLDivElement> = React.createRef();
  render() {
    return <div ref={this.rootRef}>{this.props.children}</div>;
  }
}
```

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

### Portals

**Not written yet.**

[Want to contribute this section? Respond in this issue.](https://github.com/sw-yx/react-typescript-cheatsheet/issues/6)

## 错误边界

**Not written yet.**

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

## Timeout/Placeholder/createFetcher

*Not written yet.* watch <https://github.com/sw-yx/fresh-async-react> for more on React Suspense and Time Slicing.

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

## 第四章：其他注意事项

在写 React 的时候我们并不只是在写 React，现在我们还没有关注其他的库，比如 Redux（下面有关于它的内容），这里有一些在写 React + Typescript 应用需要经常注意的事项。

### 写 Typescript 库而不是应用

看起来 `propTypes` 对于 Typescript 并不是必要的，尤其是当我们写 React + Typescript 应用时。可是它们在写提供给使用 Javascript 语言的开发者的**库**时还是有用的。

```ts
interface IMyComponentProps {
  autoHeight: boolean;
  secondProp: number;
}

export class MyComponent extends React.Component<IMyComponentProps, {}> {
  static propTypes = {
    autoHeight: PropTypes.bool,
    secondProp: PropTypes.number.isRequired,
  };
}
```

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

### 组件/设计 系统开发

对于使用 Storybook 的开发者，请阅读我维护的文档：<https://storybook.js.org/configurations/typescript-config/>. 包括自动化的 proptype 文档生成，棒极了 :)

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

### 从Flow迁移

你也许想使用 <https://github.com/piotrwitek/utility-types>。如果你在这方面有好的建议，请提出 PR！

[Something to add? File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

### Prettier + TSLint

*Contributed by: @azdanov*

要想配合 Prettier 使用 TSLint，你需要 [`tslint-config-prettier`](https://github.com/alexjoverm/tslint-config-prettier) ，它禁止了所有冲突的规则，你也可以选择  [`tslint-plugin-prettier`](https://github.com/ikatyang/tslint-plugin-prettier)，它高亮了所有 TSLint 指出的不同之处。

配置样例：

<table>
    <tr>
        <th>
            <strong>tslint.json</strong>
        </th>
        <th>
            <strong>.prettierrc</strong>
        </th>
    </tr>
    <tr>
        <td>
            <pre>
{
  "rulesDirectory": ["tslint-plugin-prettier"],
  "extends": [
    "tslint:recommended",
    "tslint-config-prettier"
  ],
  "linterOptions": {
    "exclude": ["node_modules/**/*.ts"]
  },
  "rules": {
    "prettier": true
  }
}
            </pre>
        </td>
        <td>
            <pre>
{
  "printWidth": 89,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all",
  "bracketSpacing": true,
  "jsxBracketSameLine": false
}
            </pre>
        </td>
    </tr>
</table>

这是一个展示如何集成的项目的 github 仓库的地址： [prettier + tslint + create-react-app-ts](https://github.com/azdanov/tslint-eslint-crats).

### ESLint + TSLint

为什么？ESLint 有着非常丰富的生态，各种插件还有配置文件，这些都是 TSLint 所欠缺的。

[`eslint-typescript-parser`](https://github.com/eslint/typescript-eslint-parser)  可以补救这些缺陷，它试图连接 Javascript 和 Typescript 之前的不同，尽管它目前还有一些不完善的地方，但是在一些插件的帮配合下已经可以稳定地提供帮助了。

<table>
 <tr>
  <td>
   Usage
  </td>
  <td>
   .eslintrc
  </td>
 </tr>
 <tr>
  <td>
  <pre>
// Install:

npm i -D typescript-eslint-parser

// And in your ESLint configuration file:

"parser": "typescript-eslint-parser"
  </pre>
  </td>
  <td>
  <pre>
{
  "extends": [
    "airbnb",
    "prettier",
    "prettier/react",
    "plugin:prettier/recommended",
    "plugin:jest/recommended",
    "plugin:unicorn/recommended"
  ],
  "plugins": ["prettier", "jest", "unicorn"],
  "parserOptions": {
    "sourceType": "module",
    "ecmaFeatures": {
      "jsx": true
    }
  },
  "env": {
    "es6": true,
    "browser": true,
    "jest": true
  },
  "settings": {
    "import/resolver": {
      "node": {
        "extensions": [".js", ".jsx", ".ts", ".tsx"]
      }
    }
  },
  "overrides": [
    {
      "files": ["**/*.ts", "**/*.tsx"],
      "parser": "typescript-eslint-parser",
      "rules": {
        "no-undef": "off"
      }
    }
  ]
}
  </pre>
  </td>
 </tr>
</table>
这是一个展示如何集成的项目的 github 仓库的地址：[eslint + tslint + create-react-app-ts](https://github.com/azdanov/tslint-eslint-crats).

### 与非Typescript库配合使用 (编辑自己的 index.d.ts)

*Not written yet.*

Please contribute on this topic! [We have an ongoing issue here with some references](https://github.com/sw-yx/react-typescript-cheatsheet/issues/8).

## 故障排除手册：Types

你是否还在面对奇奇怪怪的错误提示？你不是一个人遇到这种事。这是 Typescript 配合 React 使用时最糟糕的地方，尽量避免使用 `any` 来尽可能多的获得来自 Typescript 的帮助。在这里，我们试着熟悉一些经常使用到的策略来解决这些问题。

### 联合类型

联合类型在解决这些类型问题时非常好用：

```tsx
class App extends React.Component<{}, {
    count: number | null, // like this
  }> {
  state = {
    count: null
  }
  render() {
    return (
      <div onClick={() => this.increment(1)}>{this.state.count}</div>
    );
  }
  increment = (amt: number) => {
    this.setState(state => ({
      count: (state.count || 0) + amt
    }));
  }
}
```

<details>

<summary>解释</summary>

This is not yet written. Please PR or [File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new) with your suggestions!
</details>

### 可选类型

如果一个组件有可选 prop，增加一个问号标记然后在解构的时候赋值（或者使用 defaultProps）。

```tsx
class MyComponent extends React.Component<{
  message?: string, // like this
}> {
  render() {
    const {message = 'default'} = this.props;
    return (
      <div>{message}</div>
    );
  }
}
```

你也可以使用 `!` 来断言某些变量没有被定义，不过并不推荐这样做。

<details>

<summary>解释</summary>

This is not yet written. Please PR or [File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new) with your suggestions!
</details>

### 枚举类型

Typescript 中的枚举默认是数字类型，不过你总会更倾向于使用字符串。

```tsx
export enum ButtonSizes {
  default = 'default',
  small = 'small',
  large = 'large'
}
```

用法：

```tsx
export const PrimaryButton = (
  props: Props & React.HTMLProps<HTMLButtonElement>
) => (
  <Button
    size={ButtonSizes.default}
    {...props}
  />
);
```

还有一种更简单的方案来做到枚举 —— 联合声明一串字符串，不过这样做不会有自动补全和语法的好处：

```tsx
export declare type Position = 'left' | 'right' | 'top' | 'bottom';
```

<details>

<summary>解释</summary>

枚举类型的好处是当你对 props 传入了错误的字符串那么 TypeScript 将会报错。

</details>

### 类型断言

有的时候 TypeScript 会判断错变量的类型，或者联合类型需要被断言为一个更加具体的类型才能配合其他 API 工作，这时候需要使用 `as` 进行类型断言。`as` 告诉编译器你比它更懂类型。

```tsx
class MyComponent extends React.Component<{
  message: string,
}> {
  render() {
    const {message} = this.props;
    return (
      <Component2 message={message as SpecialMessageType}>{message}</Component2>
    );
  }
}
```

<details>

<summary>解释</summary>

注意这 [与 casting 是不一样的](https://www.reddit.com/r/reactjs/comments/8o5owb/react_typescript_cheatsheet_for_react_users_using/e01d2md/?context=3).

Something to add? Please PR or [File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new) with your suggestions!
</details>

### 交叉类型

将两种类型融合在一起

```tsx
export interface Props {
  label: string;
}
export const PrimaryButton = (
  props: Props & React.HTMLProps<HTMLButtonElement> // adding my Props together with the @types/react button provided props
) => (
  <Button
    {...props}
  />
);
```

## 忽略类型的属性

当交叉类型的时候，有时我们想定义我们自己的属性。举个例子，我想让我的组件有一个我们自定义的 `label` prop，可是交叉后的类型后已经有了一个 `label` 属性，接下来展示如何将交叉类型中的 `label` 抽离出来：

```tsx
export interface Props {
  label: React.ReactNode // this will conflict with the InputElement's label
}

// here is the magic - omitting an attribute
type Diff<T extends string, U extends string> = ({ [P in T]: P } &
  { [P in U]: never } & { [x: string]: never })[T];
type Omit<T, K extends keyof T> = Pick<T, Diff<keyof T, K>>;
// end of magic

// usage
export const Checkbox = (
  props: Props & Omit<React.HTMLProps<HTMLInputElement>, 'label'>
) => {
  const { label } = props;
  return (
    <div className='Checkbox'>
      <label className='Checkbox-label'>
        <input
          type="checkbox"
          {...props}
        />
      </label>
      <span>{label}</span>
    </div>
  );
};
```

<details>

<summary>Explanation</summary>

This is not yet written. Please PR or [File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new) with your suggestions!
</details>

## Type Zoo

正如你上面看到的 Omit 的例子，你可以在你的代码中写出很炫酷的逻辑。 你也许想了解一下 [type-zoo](https://github.com/pelotom/type-zoo)，它是一个很棒的操作工具包（包括 Omit），[utility-types](https://github.com/piotrwitek/utility-types) 也是（尤其是给那些从 Flow 迁移过来的开发者）。

## 故障排除手册：TSLint

有时候 TSLint 会产生阻碍，明智的选择是将它们关闭。接下来是一些你可能禁用的 TSLint 规则：

- `/* tslint:disable */` 全部禁止
- `// tslint:disable-line` 某一行代码禁止规则检查
- `/* tslint:disable:semicolon */` 有的时候 Prettier 会添加分号但是 TSLint 并不认可
- `/* tslint:disable:no-any */` 当你想使用 `any` 的时候，禁止掉 TSLint 限制使用 `any` 的规则
- `/* tslint:disable:max-line-length */` 禁止掉折行规则

诸如以上这些等等，你可以随意禁止掉任何规则，通常你会根据  VSCode 或其他编辑器提示的错误信息的对应规则来决定。

<details>

<summary>解释</summary>

This is not yet written. Please PR or [File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new) with your suggestions!
</details>

## 故障排除手册：tsconfig.json

这是我的电脑上的配置：

```json
{
  "compilerOptions": {
    "outDir": "build/lib",
    "module": "commonjs",
    "target": "es5",
    "lib": ["es5", "es6", "es7", "es2017", "dom"],
    "sourceMap": true,
    "allowJs": false,
    "jsx": "react",
    "moduleResolution": "node",
    "rootDir": "src",
    "baseUrl": "src",
    "forceConsistentCasingInFileNames": true,
    "noImplicitReturns": true,
    "noImplicitThis": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "suppressImplicitAnyIndexErrors": true,
    "noUnusedLocals": true,
    "declaration": true,
    "allowSyntheticDefaultImports": true,
    "experimentalDecorators": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "build", "scripts"]
}
```

欢迎提出 issue 来和我讨论更好的配置选择，我喜欢使用 `noImplicitAny` 来强制我设定类型。

<details>

<summary>Explanation</summary>

This is not yet written. Please PR or [File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new) with your suggestions!
</details>

## 推荐的值得学习的 React+TypeScript 代码库

- https://github.com/jaredpalmer/formik
- https://github.com/jaredpalmer/react-fns
- https://github.com/palantir/blueprint
- https://github.com/Shopify/polaris

<details>

<summary>解释</summary>

This is not yet written. Please PR or [File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new) with your suggestions!
</details>

## 其他的 React + TypeScript 资源

- 我！ <https://twitter.com/swyx>
- <https://github.com/piotrwitek/react-redux-typescript-guide> - **强烈推荐**，在我写这篇文章之前我还不知道这个仓库，这个仓库涵盖了很多本文没有讲到的东西，包括 **Redux** 和 **Jest**。
- [Ultimate React Component Patterns with Typescript 2.8](https://levelup.gitconnected.com/ultimate-react-component-patterns-with-typescript-2-8-82990c516935)
- [Basarat's Typescript gitbook has a React section](https://basarat.gitbooks.io/typescript/content/docs/jsx/react.html) 来自 Egghead.io 的教程。
- [Charles Bryant's gitbook](https://charleslbryant.gitbooks.io/hello-react-and-typescript/content/) 维护了两年的项目，更加偏基础但是有简要的代码和 IDE 的建议。
- [You?](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).

## 我想问的问题在这里没有答案！

[File an issue](https://github.com/sw-yx/react-typescript-cheatsheet/issues/new).
