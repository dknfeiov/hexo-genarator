




#### CSS 变量 ： 使用自定义属性来设置变量名，并使用特定的 var() 来访问
> 声明一个 全局 CSS 变量：

```css
    :root {
        --global-color: #666;
        --pane-padding: 5px 42px;
    }
```


#### var() 函数 ：可以代替元素中任何属性中的值的任何部分。不能作为属性名、选择器或者其他除了属性值之外的值

> 方法的第一个参数是要替换的自定义属性的名称。函数的可选第二个参数用作回退值。如果第一个参数引用的自定义属性无效，则该函数将使用第二个值
> 可以指定多个回退值，通过逗号分隔  eg: 
    ```css
        div {
            color: var(--global-color, red, blue);
        }
    ```



#### :host （选择shadow DOM 根节点，只在shadow DOM中起作用，定义在shadow DOM之外不生效）


#### :host(selector)  根据选择器找到对应的 shadow DOM 根节点