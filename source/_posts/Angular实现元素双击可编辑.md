---
title: Angular实现元素双击可编辑
date: 2018-02-28 11:28:58
tags: Angular
---

#### 实现目标
>新建指令，将指令注册为表单控件，在元素上使用，可以通过ngModel实现双向绑定，双击可编辑元素内文字内容，失去焦点不可编辑

#### 使用
```html
<div appCanEdit [(ngModel)]="item.value"></div>
```

##### 效果
![](https://dknfeiov.github.io/images/files/editable.gif)  


#### 实现步骤
##### 1. 创建指令，实现 ControlValueAccessor接口
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

##### 2. 注册成为表单控件，将控件本身注册到DI框架成为一个可以让表单访问其值的控件

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

##### 3. 通过@HostListener 为组件添加事件绑定，通过@HostBinding实现元素innerText随value而变化
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


#### 完整代码
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

