所有 Widget 都使用一个 lv_ibj_t 指针作为句柄进行引用。 此指针稍后可用于读取或更改 Widget 的属性。

## Attributes

### Basic attributes

![image-20250730162245207](D:\kaper\笔记\图片\image-20250730162245207.png)

![image-20250730162312625](D:\kaper\笔记\图片\image-20250730162312625.png)

### parent and children

**子级和父级之间的操作**

获取当前widget的父级

```C
lv_obj_t *lv_obj_get_parent(const lv_obj_t *obj)
/*
	参数：obj--指向当前的对象
	返回：该对象的父级（如果该对象是screen，返回null）
*/
```

将widget移动到一个新的父级

```C
void lv_obj_set_parent(lv_obj_t *obj, lv_obj_t *parent)
/*
	参数：obj--只想需要改变父级的对象
		 parent--指向新的父级
*/
```

获取父级的特定子 Widget

```C
lv_obj_t *lv_obj_get_child(const lv_obj_t *obj, int32_t idx)
/*
	参数：obj--指向想要获取子级的父级
		 idx--子级的序号
	返回：想要获取的子级
	0 获取最先创建的子 Widget
	1 获取第二个创建的子 Widget
	count-1 获取最后创建的子 Widget	
*/
//迭代一个父级widget的所有子级widget
uint32_t i;
for(i = 0; i < lv_obj_get_child_count(parent); i++) {
    lv_obj_t * child = lv_obj_get_child(parent, i);
    /* 对子 Widget 执行某些操作。*/
}
```

返回 Widget 在其父级中的索引

```C
int32_t lv_obj_get_index(const lv_obj_t *obj)
/*
	参数：obj--指向当前需要查询index的子级
	返回：该子级widget在当前父级的索引，也相当于在父级中比它早创建的子 	       Widget 数量。
*/
```

将 Widget 移动到前景或发送到背景

```C
static inline void lv_obj_move_foreground(lv_obj_t *obj)
/*
	参数：obj--指向当前需要移动的子级widget
*/  
static inline void lv_obj_move_background(lv_obj_t *obj)
/*
	参数：obj--指向当前需要移动的子级widget
*/  
```

 改变 Widget 在其父级中的索引。

```C++
void lv_obj_move_to_index(lv_obj_t *obj, int32_t index)
/*
	参数：obj--指向当前需要移动的子级widget
		 index--移动的位置
*/  
```

![image-20250731090547977](D:\kaper\笔记\图片\image-20250731090547977.png)

 交换两个 Widget 的位置

```C
void lv_obj_swap(lv_obj_t *obj1, lv_obj_t *obj2)
/*
	参数：obj1--pointer to the first object
		 obj2--pointer to the second object
*/  
```

获取 Widget 的 Screen（最高级别的父级）

```C
lv_obj_t *lv_obj_get_screen(const lv_obj_t *obj)
/*
	参数：obj1--该widget
	返回：该widget的screen
*/  
```

## Working Mechanisms

### Parent-child structure

一个父级 Widget 可以被视为其子 Widget 的容器。**每个 Widget 恰好有一个父级 Widget（除了 Screens 之外）， 但是一个父级 Widget 可以拥有任意数量的子 Widget。** 对于父级的类型没有限制，但有一些通常是作为父级的 Widgets（例如按钮 button）或作为子级的 Widgets（例如标签 label）。

### Moving together

如果父级的位置发生变化，子 Widget 将会随之移动。因此，所有位置都是相对于父级的。

![image-20250731092324600](D:\kaper\笔记\图片\image-20250731092324600.png)

![image-20250731092332433](D:\kaper\笔记\图片\image-20250731092332433.png)

![image-20250731092348700](D:\kaper\笔记\图片\image-20250731092348700.png)

![image-20250731092403235](D:\kaper\笔记\图片\image-20250731092403235.png)

### Visibility only on the parent

如果子级部分或完全位于其父级之外，则外部的部分将不会被显示。

![image-20250731092441061](D:\kaper\笔记\图片\image-20250731092441061.png)

![image-20250731094234787](D:\kaper\笔记\图片\image-20250731094234787.png)

回调函数

![image-20250731094256115](D:\kaper\笔记\图片\image-20250731094256115.png)

示例：

![image-20250731094329913](D:\kaper\笔记\图片\image-20250731094329913.png)

### Create and delete Widgets

创建一个widget

```C
lv_obj_t * lv_<widget>_create(lv_obj_t * parent, <other parameters if any>);
```

通常，创建函数仅有一个 **parent 参数**，用于告知在哪个 Widget 上创建新的 Widget。返回值是**指向创建的 Widget 的指针，类型为 lv_obj_t***。

删除一个widget

```C
void lv_obj_delete(lv_obj_t * widget);
```

所有 Widget 类型都有一个通用的 **delete** 函数。它会删除 **Widget 及其所有子级。**

![image-20250731095217932](D:\kaper\笔记\图片\image-20250731095217932.png)

移除 Widget 的所有子级（但不包括 Widget 本身）

```C
void lv_obj_clean(lv_obj_t *obj)
```

 在一段时间后删除 Widget。延迟是以毫秒为单位表示的。

```C
void lv_obj_delete_delayed(lv_obj_t *obj, uint32_t delay_ms)
```

在 LVGL 中，`**lv_obj_null_on_delete(&widget)**` 是一个非常有用的安全机制，用于 **自动将已删除的 Widget 指针置为 NULL**，避免出现悬垂指针（Dangling Pointer）问题

![image-20250731100351308](D:\kaper\笔记\图片\image-20250731100351308.png)

```C
lv_obj_t *btn1 = lv_btn_create(lv_scr_act());
lv_obj_t *btn2 = lv_btn_create(lv_scr_act());

// 启用自动置空
lv_obj_null_on_delete(&btn1);
lv_obj_null_on_delete(&btn2);

// 删除 btn1
lv_obj_delete(btn1);

// 检查指针状态
printf("btn1: %s\n", btn1 ? "存活" : "已删除");  // 输出 "已删除"
printf("btn2: %s\n", btn2 ? "存活" : "已删除");  // 输出 "存活"
```

## Screens

### What are Screens?
