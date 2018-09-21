---
title: Angular实现元素双击可编辑
date: 2018-02-28 11:28:58
tags: Angular , React
---

### 一.概述
    
    如果希望实现一个DOM元素，双击可编辑，失去焦点不可编辑，常用的方式，是绑定dbclick事件，当事件触发，将文本内容替换为表单元素，失去焦点，再替换为文本，这种做法固然简单，但如果要操作的dom元素太多，既要添加表单元素，还要监听dbclick、blur事件，代码不可避免会变得非常繁琐
    
    所以我采取的做法是，抽象出一个指令，将指令注册为表单控件，实现双向绑定，结合DOM的contentEditable属性，只要在DOM元素上添加该指令即可具备该能力

#### 使用
```html
<div appCanEdit [(ngModel)]="item.value"></div>
```

#### 效果
![](https://dknfeiov.github.io/images/files/editable.gif)  


### 二.实现步骤
#### 1. 创建指令，实现 ControlValueAccessor接口
>ControlValueAccessor是一个连接表单模型和视图（DOM元素）的接口，自定义的表单控件必须实现这个接口,它的作用是把 form 模型中值映射到视图中，当视图发生变化时，通知 form directives 或 form controls

```typescript
@Directive({
  selector: '[appCanEdit]'
})
export class ElementCanEditDirective implements ControlValueAccessor {

  _value;
  // callback function
  propagateChange = (value: any) => { };
  // Writes a new value to the element ，temporarily store the value in  '_value'
  writeValue(value: any) {
    if (value) {
      this._value = value;
    }
  }
  // when the control's value changes in the UI, call the callback function
  registerOnChange(fn: any) {
    this.propagateChange = fn;
  }

  registerOnTouched(fn: any) { }
  
  constructor(
  ) { }

}

```

#### 2. 注册成为表单控件，将控件本身注册到DI框架成为一个可以让表单访问其值的控件

```typescript
@Directive({
  selector: '[appCanEdit]',
  providers: [
    {
      provide: NG_VALUE_ACCESSOR,
      useExisting: forwardRef(() => ElementCanEditDirective),
      multi: true
    }
  ],
})
```

#### 3. 通过@HostListener 为组件添加事件绑定，通过@HostBinding实现元素innerText随value而变化
>当元素处于可编辑状态时，添加content-editable类样式，以便灵活控制样式

```javascript

  @HostBinding() get innerText() {
    return this._value;
  }

  @HostListener('blur', ['$event.target'])
  onBlur(ele: HTMLElement) {
    ele.contentEditable = 'false';
    this._value = ele.innerText;
    this.propagateChange(this._value);
    this.render.removeClass(ele, 'content-editable');
  }

  @HostListener('dblclick', ['$event.target'])
  dbClick(ele: HTMLElement) {
    // plaintext-only
    ele.contentEditable = 'true';
    ele.innerText = ele.innerText;
    ele.focus();
    this.render.addClass(ele, 'content-editable');
  }

```


### 三.完整代码
```html
<div appCanEdit [(ngModel)]="item.value"></div>
```
```typescript
import { Component, OnInit, Input, EventEmitter, Directive, HostListener, HostBinding, Renderer2, forwardRef } from '@angular/core';
import { ControlValueAccessor, NG_VALUE_ACCESSOR } from '@angular/forms';

/**
 * 让DOM元素可编辑
 * 可在元素上使用 ngModel
 * @export
 * @class ElementCanEditDirective
 * @implements {ControlValueAccessor}
 */
@Directive({
  selector: '[appCanEdit]',
  providers: [
    {
      provide: NG_VALUE_ACCESSOR,
      useExisting: forwardRef(() => ElementCanEditDirective),
      multi: true
    }
  ],
})
export class ElementCanEditDirective implements ControlValueAccessor {

  _value;
  // callback function
  propagateChange = (value: any) => { };
  // Writes a new value to the element ，temporarily store the value in  '_value'
  writeValue(value: any) {
    if (value) {
      this._value = value;
    }
  }
  // when the control's value changes in the UI, call the callback function
  registerOnChange(fn: any) {
    this.propagateChange = fn;
  }

  registerOnTouched(fn: any) { }

  @HostBinding() get innerText() {
    return this._value;
  }

  @HostListener('blur', ['$event.target'])
  onBlur(ele: HTMLElement) {
    ele.contentEditable = 'false';
    this._value = ele.innerText;
    this.propagateChange(this._value);
    this.render.removeClass(ele, 'content-editable');
  }

  @HostListener('dblclick', ['$event.target'])
  dbClick(ele: HTMLElement) {
    ele.contentEditable = 'true';
    ele.innerText = ele.innerText;
    ele.focus();
    this.render.addClass(ele, 'content-editable');
  }

  constructor(
    private render: Renderer2
  ) { }

}

```

