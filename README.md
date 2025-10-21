# popup-button-card
A homeassistant customizable card with a button &amp; a popup window
### 一个为Homeassistant Dashboard设计的自定义卡片

#### 提供了一个高度自定义的按钮 & 一个样式丰富的弹窗

#### 此项目全部功能实现代码由AI生成 Power By ChatGPT-5

---

### 预览：
![](https://github.com/gasment/popup-button-card/blob/main/preview.webp)
### 安装说明：

#### 方法一：

下载release中的popup_button_card.js文件，放入homeassistant 的 /config/www 下的任意文件夹内（给予执行权限），在HA设置->仪表盘中添加资源文件路径/local/xxxxx

#### 方法二：

复制本项目仓库地址：https://github.com/gasment/popup-button-card  ,在HACS添加Custom repositories，Repositories填写仓库地址，Type选择Dashboard；
搜索：popup-button-card，下载安装，按提示刷新页面

### 配置说明：

1、卡片调用

```yaml
type: custom:popup-button-card
```

2、variables & template，变量与模板，变量需要与模板一同使用，不使用模板不要配置变量，用法与button-card一致，可参考button-card相关文档和教程(https://github.com/custom-cards/button-card)
变量支持js模板写法

```yaml
variables:
  example1: 114514
  example2: |
    [[[
      if (states[`switch.entity`].state === "on"){
        return "已打开"
      } else {
        return "已关闭"
      }
    ]]]
template: my_template
```
3、 name、label、state，3个文本元素，3个写法一致，他们的布局受grid配置控制，如图
- 普通文本写法：

    ```yaml
    name: 我的按钮
    label: 我的标签
    state: 114514
    ```

- js模板写法：

    ```yaml
    name: |
        [[[
            if (states[`switch.entity`].state === "on"){
                return "已打开"
                } else {
                    return "已关闭"
                }
            ]]]
    label: #同上
    state: #同上
    ```

4、button-icon，按钮图标（图中灯泡），其布局位置受grid配置控制
- 内置图标写法：
    ```
    button_icon: mdi:lightbulb-group
    ```
- 图标文件写法：
    ```
    button_icon: /local/icon/your_icon.png
    ```
- js模板写法（可选择内置还是文件图标）：
    ```
    button_icon: |
        [[[
            if (states[`switch.entity`].state === "on"){
                return "mdi:alarm-light"  #或者文件路径return "/local/icon/your_icon_on.png"
            } else {
                return "mdi:alarm-light-off" #或者文件路径return "/local/icon/your_icon_off.png"
            }
         ]]]
    ```
5、expand_side，弹窗的展开方向(相对于按钮)
- 支持上下左右和全屏，其参数分别为：top/bottom/left/right/full_screen
    ```
    expand_side: bottom
    ```

6、button_effect & button_effect_color，弹窗展开时，按钮的凸显效果
- 开启& 关闭
    ```
    button_effect: true  #启用效果
    button_effect: false  #禁用效果
    ```
 - color支持颜色代码或js模板：
    ```
    button_effect_color: blue #或"#0254F8"或rgb(2,84,248)
    ```
- js模板写法：
    ```
    button_effect_color: |
            [[[
                var state = states[`sensor.entity`].state
                if (state === "off"){
                return "#D7DFED"
                } else if (state === "cool"){
                return "#2483FF"
                } else if (state === "heat"){
                return "#FF6B6B"
                } else if (state === "dry"){
                return "#54CEAE"
                } else if (state === "fan_only"){
                return "#4CCBA9"
                } else if (state === "auto"){
                return "#464BD8"
                } else {
                return "#D7DFED"
                }
            ]]]
    ```
7、any_ha_action_to_close_popup，是否启用弹窗内的单次ha action服务调用（实体操作）后，自动关闭弹窗
```
any_ha_action_to_close_popup: true  #开启
any_ha_action_to_close_popup: false #禁用
```
- 附加选项：filter_for_ha_action_to_close_popup，any_ha_action_to_close_popup为true时可过滤特定操作触发自动关闭弹窗
  ```
  filter_for_ha_action_to_close_popup:
    include_keyword:   #include关键词用于匹配触发自动关闭弹窗，可配置include_keyword：all，等同any_ha_action_to_close_popup: true,或者指定何种操作，哪个实体（如下）
      - switch.entity
      - light.entity
      - light.turn_off
      - switch.turn_off
      - more-info
      - navigation
    exclude_keyword:   #exclude关键词用于匹配【不】触发自动关闭弹窗，可配置exclude_keyword：all，等同any_ha_action_to_close_popup: false,或者指定何种操作，哪个实体（如下）
      - vacuum.start
      - sensor.entity
  ```
8、updown_slide_to_close_popup，是否启用上下滑动屏幕时，自动收起弹窗
```
updown_slide_to_close_popup: true  #开启
updown_slide_to_close_popup: false #禁用
```

9、multi_expand，是否启用单页面上的多个弹窗并存，最好同一页面的所有弹窗保持一致启用或禁用
```
multi_expand: true  #启用
multi_expand: false  #禁用
```

10、tap_action & hold_action，按钮点击&长按动作
 - 支持两种行为，popup & action，action使用官方卡片写法，也就是互动选项的yaml代码，两者写法一致
 - 点击打开弹窗
    ```
    tap_action: popup
    ```
- 长按打开弹窗
    ```
    hold_action: popup
    ```
- 点击操作实体
    ```
    tap_action:
        action: perform-action
        perform_action: switch.toggle
        target:
            entity_id: switch.entity
    ```
- 长按操作实体
    ```
    hold_action:
        action: perform-action
        perform_action: switch.toggle
        target:
            entity_id: switch.entity
    ```
 - ⚠️！tap和hold是互斥关系，只能取其一，比如点击打开弹窗必须配套长按操作电器，长按打开弹窗配套点击打开电器，两个效果如下：

 11、popup_outside_blur，用于开启或关闭弹窗外部的背景模糊效果，注意开启popup_outside_blur后，multi_expand会失效
 ```
popup_outside_blur: true #开启
popup_outside_blur: false  #禁用
 ```
12、styles，卡片内各元素的css样式定义，支持通用css样式插入，支持js模板返回值
styles目前支持的字段：content/button/name/label/state/icon/grid/overlay/popup_close_button/popup_outside_blur
如果不清楚通用css属性的用法，直接问ai是最快捷的方式

- 12、1 styles->content，设置弹窗外部包裹容器的样式，注意参考完整配置的缩进格式，这里可以设置弹窗的宽高，如果弹窗内卡片高度大于弹窗设定高度，将自动启用弹窗内的上下滑动
    ```
    styles:
        content:
            - box-shadow: none
            - background: lightgray
            - width: 95%
            - height: 300px
    ```
    * 一个全屏模式的示例写法，动态高度，上下预留：
        ```
        styles:
            content:
                - box-shadow: none
                - background: lightgray
                - height: calc(90vh - 130px)
                - width: 85%
                - margin-bottom: 100px  #底部边距
        ```
- 12、2 styles->button，设置按钮的样式
    ```
    styles:
        button:
            - height: 60px #按钮高度
            - width: 100%  #按钮宽度,height/width不配置时，按钮宽高由其内元素确定，可配置具体值，或使用100%以使其自动拉伸填充到外部容器尺寸
            - background: |    #按钮背景色，可使用js模板动态设置
                [[[
                if (states[`switch.entity`].state === "on"){
                    return "green"
                } else {
                    return "red"
                }
                ]]]
            - border-radius: 10px  #按钮圆角
    ```
 - 12、3 styles->name/label/state，设置3个文本的样式
    ```
    styles:
        name:
            - font-weight: bold  #加粗字体
            - font-size: 16px  #字体大小
            - color: white   #字体颜色
            - letter-spacing: 1px  #文件间距
            - margin-left: 2px  #左右像素位移
            - margin-top: 2px  #上下像素位移
        label: #同上
        state: #同上
    ```
- 12、4 styles->icon，设置图标的样式
    ```
    styles:
        icon:
            - width: 40px  #图标高度
            - height: 40px #图标宽度
            - color: white  #图标颜色
    ```
- 12、5 styles->grid，设置name/label/state/icon的布局位置，grid写法可直接参考button-card,此处不赘述了
    ```
    styles:
        grid:
            - display: grid
            - grid-template-areas: |
                "i n l"
                "i s s"
            - grid-template-columns: auto auto auto
            - grid-template-rows: 25px 25px
            - justify-items: center
    ```
- 12.6、 styles->overlay，仅全屏模式弹窗有效，设置弹窗卡片内容之外的背景样式
    - 不配置时为模糊效果，可以配置为纯色：
    ```
    styles:
        overlay:
            - background: white #纯色
            - backdrop-filter: none
    ```
- 12.7、 styles->popup_close_button，仅全屏模式有效，设置弹窗关闭按钮的样式
    ```
    styles:
        popup_close_button:
            - bottom: 20px
            - width: 56px
            - height: 56px
            - background: rgba(255, 64, 64, 0.9)
            - color: white
            - font-size: 26px
    ```
- 12、8 styles->popup_outside_blur，用于配置弹窗外的背景模糊量
    ```
    styles:
        popup_outside_blur:
            - backdrop-filter: blur(100px)  #默认10px,越大越模糊
    ```
    - 纯色写法（适用ios端m某些场景无法应用全屏模糊的情况），如图白色半透明：
    ```
    styles:
        popup_outside_blur:
            - background: rgba(255,255,255,0.5)
    ```
13、content，弹窗内容，可在此处接上其他卡片的yaml代码，理论上支持所有卡片，但兼容性不一，需要自行测试
```

content:
  card:
    type: vertical-stack
    cards:
      - type: button
        show_name: true
        show_icon: true
        entity: switch.ui_lovelace_minimalist_pre_release
      - type: light
        entity: light.xxxx_light
```

### 完整配置示例
```
type: custom:popup-button-card
name: 示例按钮
button_icon: mdi:lightbulb-group
expand_side: bottom
button_effect: true
button_effect_color: yellow
updown_slide_to_close_popup: true
any_ha_action_to_close_popup: fasle
tap_action: popup
hold_action: none
multi_expand: true
popup_outside_blur: true
styles:
  popup_outside_blur:
    - backdrop-filter: blur(100px)
  content:
    - box-shadow: none
    - background: rgba(0,0,0,0)
    - width: 200px
  button:
    - height: 60px
    - width: auto
    - background: orange
    - border-radius: 10px
  name:
    - font-weight: bold
    - font-size: 16px
    - color: white
    - letter-spacing: 1px
    - margin-left: 2px
  icon:
    - width: 40px
    - height: 40px
    - color: white
  state:
    - font-weight: bold
    - font-size: 16px
    - color: white
    - letter-spacing: 1px
    - margin-left: 2px
  label:
    - font-weight: bold
    - font-size: 16px
    - color: white
    - letter-spacing: 1px
    - margin-left: 2px
  overlay:
    - background: gray #纯色
    - backdrop-filter: none
  popup_close_button:
    - bottom: 20px
    - width: 56px
    - height: 56px
    - background: rgba(255, 64, 64, 0.9)
    - color: white
    - font-size: 26px
  grid:
    - display: grid
    - grid-template-areas: |
        "i n l"
        "i s s"
    - grid-template-columns: auto auto auto
    - grid-template-rows: 25px 25px
    - justify-items: center
content:
  card:
    type: custom:mushroom-number-card
```
