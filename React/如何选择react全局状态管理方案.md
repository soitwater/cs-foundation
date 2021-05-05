# 如何选择react全局状态管理方案
- 答：
  * 当你的项目数据复杂度很低，用 react 自带的 component-state 就可以
  * 当你的项目数据复杂度一般，lift state 到 root component，然后通过 props 传递来管理
  * 当你的项目数据复杂度较高，mobx + react 是好的选择
  * 当你的项目数据复杂度很高，redux + react 可以帮助你维持可预测性和可维护性的下降曲线不那么陡。所有 state 变化都由 action 规范化。
  * 当你的项目数据复杂度很高且数据来源很杂，rxjs 可以帮助你把所有 input 规范化为 observable/stream，可以用统一的方式处理。
- 思路其实很简单：
  * 当 UI 变化很复杂时，用 component 归一化处理，即 View-Management
  * 当 state 变化很复杂时，用 action/state 归一化处理, 即 State-Management
  * 当 data-input 很复杂时，用 rxjs/observable 等概念归一化处理，即 Effect-Management。
- 任意问题，只要足够普遍和复杂，就值得抽象出专门化的机制。
  * Effect Management 在前端开源生态里，还不是很繁荣，是一个大家可以努力的方向。不仅 rxjs 可以做 effect 管理，vue/reactivity 和 react-hooks 乃至原生 JS 裸写都能做，有待大家的发掘。
  * 更具体地说，前端社区可以往下面几个方向努力：
    - 基于 react-hooks 和 State/Effect 分层理念重新梳理哪些应该暴露为 effect，哪些应该暴露为 state，提供一个性能更加良好的版本（只能用在 react 体系）。
    - 基于 vue/reactivity 和 State-Effect-Bindings 理念，实现的 state/effect 管理框架（脱离 vue 视图框架也能使用）。
    - 基于 rxjs 和 State-Effect-Observeable 模式，实现 state/effect 管理框架（可以配合任意视图框架使用）
