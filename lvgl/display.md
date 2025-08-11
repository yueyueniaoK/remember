## DIsplay

### What is a Display?

在 LVGL 中，**lv_display_t**（不要与 Screen混淆）是**一个数据类型**，**表示一个单独的显示面板——即硬件设备**，用于在您的设备上显示 LVGL 渲染的像素。 在系统初始化期间，您必须为每个希望 LVGL 使用的显示面板执行以下操作：

- 为其创建 lv_display_t对象，
- 为其分配一个 flush_callback，
- 分配它的 draw_buffers。

### How Many Displays Can LVGL Use?

LVGL 可以使用**任意数量的显示器**，唯一的限制是**可用的 RAM 和 MCU 时间。**

为什么你需要支持多显示器？以下是一些示例：

- 拥有一个“正常的”TFT 显示器用于本地 UI，并根据需要在 VNC 上创建“虚拟”屏幕。（你需要添加 VNC 驱动程序。）
- 拥有一个大尺寸 TFT 显示器和一个小的单色显示器。
- 在一个大型仪器或技术设备中拥有一些较小且简单的显示器。
- 拥有两个大尺寸 TFT 显示器：一个给客户用，另一个给店员用。

如果你设置 LVGL 使用多个显示器，请注意某些函数在执行时使用 default_display，例如创建 [Screens](https://lvgl.100ask.net/master/details/base-widget/obj.html#screens)。

### Display Features

一旦创建，Display 对象会记住它所表示的显示硬件的特性，以及与其生命周期相关的其他信息：

- 分辨率（宽度和高度，单位为像素）
- 颜色深度（每像素位数）
- 颜色格式（像素中的颜色布局方式）
- 每个显示器自动创建的 4 个 screen_layers
- 与此显示器关联的所有 :ref:[`](https://lvgl.100ask.net/master/details/main-components/display.html#id6)screens`（且尚未删除——在任何给定时刻只显示一个）
- 分配给它的 draw_buffers
- 用于将像素从 draw_buffers 移动到显示硬件的 flush_callback 函数
- 显示器的哪些区域已更新（被标记为“脏”），以便渲染逻辑在 [display refresh](https://lvgl.100ask.net/master/intro/basics.html#basic-data-flow) 时计算需要渲染的内容
- 可选的自定义指针，作为 display_user_data

### Screen Layers

当一个lv_display_t对象被创建时，**4 个永久的 Screens会被创建并附加到它上面，这些屏幕有助于实现层次结构。**

1. 底层（位于活动屏幕下方，透明、不可滚动，但可以点击）
2. Active Screen
3. 顶层（位于活动屏幕上方，透明且不可滚动或点击）
4. 系统层（位于顶层上方，透明且不可滚动或点击）

**1、3 和 4 层是独立于 Active Screen 的，它们会被显示（如果它们包含任何可见内容），无论当前活动屏幕是什么。**

可以通过以下方式获取 default_display 上这些屏幕的指针（分别是）：

```C
lv_screen_active();
lv_layer_top();
lv_layer_sys();
lv_layer_bottom();
```

过以下方式获取指定显示器上的这些屏幕的指针（分别是）：

```C
lv_display_get_screen_active(disp)
lv_display_get_layer_top(disp)
lv_display_get_layer_sys(disp)
lv_display_get_layer_bottom(disp)
```

要将创建的屏幕设置为 Active Screen，调用 lv_screen_load()` 或 lv_screen_load_anim()

#### Top and System Layers

LVGL 使用**顶层和系统层**来确保某些 Widgets控件**始终位于其他层之上。**

可以自由地将“弹出窗口”添加到顶层。**顶层旨在用于创建在显示器上所有屏幕上都可见的 Widgets。** 而系统层则用于系统级别的内容（例如，鼠标光标将通过 lv_indev_set_cursor() 被放置在该层）。

![image-20250731103455868](D:\kaper\笔记\图片\image-20250731103455868.png)

![image-20250731103510453](D:\kaper\笔记\图片\image-20250731103510453.png)

#### Bottom Layer

与顶层和系统层类似，底层也与显示器的大小相同，但它位于Active Screen之下。 **只有当活动屏幕的背景不透明度小于 255 时，底层才会可见。**

***

### Display Events

***

### Display Setup

#### Creating a Display

```c
lv_display_t *display1 = lv_display_create(hor_res, ver_res)
```

#### Default Display

当第一个 显示器 对象被创建时，它会成为默认显示器。如果创建了其他显示器对象（用于服务其他显示面板），**默认显示器将保持为第一个创建的显示器。**

设置另一个 显示器 为默认显示器

```C
void lv_display_set_default(lv_display_t *disp)
```

对于许多 `lv_display_...()` 函数，传递 NULL 作为 `disp` 参数将使该函数作用于默认显示器。

### Draw Buffer(s)（绘图缓冲区）

绘图缓冲区是 LVGL 渲染系统的核心组件，**负责临时存储渲染数据**，**最终输出到显示设备**。其设计直接影响性能、内存占用和显示效果。在系统初始化期间，必须为 LVGL 设置绘图缓冲区，以供每个显示器使用。通过以下方式设置：

```C
void lv_display_set_buffers(lv_display_t *disp, void *buf1, void *buf2, uint32_t buf_size, lv_display_render_mode_t render_mode)
/*
	参数:disp -- pointer to a display
		buf1 -- first buffer
		buf2 -- second buffer (can be NULL)
		buf_size -- buffer size in byte
		render_mode --LV_DISPLAY_RENDER_MODE_PARTIAL/DIRECT/FULL
*/    
//LV_DISPLAY_RENDER_MODE_PARTIAL 使用小于显示器大小的缓冲区进行渲染。建议缓冲区至少为显示器大小的 1/10。在 flush_callback 中，需要将渲染的图像复制到显示器的指定区域。在此模式下，如果按下按钮，则仅按钮区域会被重绘。
//LV_DISPLAY_RENDER_MODE_DIRECT 缓冲区大小必须与显示器大小匹配。LVGL 会将图像渲染到缓冲区的正确位置。使用此方法，缓冲区始终包含整个显示图像。如果使用两个缓冲区，渲染区域会在刷新后自动复制到另一个缓冲区。因此，在 flush_callback 中，通常只需更改帧缓冲区地址。如果按下按钮，则仅按钮区域会被重绘。
//LV_DISPLAY_RENDER_MODE_FULL 缓冲区大小必须与显示器大小匹配。LVGL 会始终重绘整个屏幕，即使只有 1 个像素发生变化。如果提供了两个与显示器大小相同的绘图缓冲区，LVGL 的显示处理方式类似于传统的双缓冲。这意味着 flush_callback 回调只需更新帧缓冲区的地址到 px_map 参数。
```

#### One Buffer

如果只使用一个缓冲区，LVGL 会将**屏幕内容绘制到该绘图缓冲区**，并通过 **flush_callback** 将其发送到显示器。 然后，LVGL 会等待直到调用 :cpp:func: `lv_display_flush_ready`（**即缓冲区内容已发送到显示器）后，再向该缓冲区绘制新的内容。**

#### Two Buffers

如果使用两个缓冲区，**LVGL 可以在一个缓冲区绘制内容，同时将另一个缓冲区的内容在后台发送到显示器**。 应使用 DMA 或其他硬件将数据传输到显示器，这样 MCU 就可以继续绘制。这样做使**得 *渲染* 和 *刷新* 显示器成为并行操作。**

