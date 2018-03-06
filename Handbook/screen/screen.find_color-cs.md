### 多点相似度模式找色 (**screen\.find\_color-cs**)


#### 声明
```lua
横坐标, 纵坐标 = screen.find_color({
    [find_all = 是否搜索多个结果],
    [max_results = 最大结果数],
    [max_miss = 允许最多未命中数],
    {起始点横坐标, 起始点纵坐标, 起始点颜色[, 起始点相似度]},
    {偏移点横坐标*, 偏移点纵坐标*, 偏移点颜色*[, 偏移点相似度*]},
    {偏移点横坐标*, 偏移点纵坐标*, 偏移点颜色*[, 偏移点相似度*]},
    ...
} [, 全局相似度, 左, 上, 右, 下 ])
```


#### 参数及返回值
> - 是否搜索多个结果
>   - 布尔型，可选参数，这个标签设置为 true 会返回范围内所有匹配位置的一个表，格式为 \{\{x1, y1\}, \{x2, y2\}, ...\}，默认 false
> - 最大结果数
>   - 整数型，可选参数，当 find\_all (是否搜索多个结果)  标签设置为 true 的时候，这个表示最多返回结果数，最多可以设为 1000，默认 100
> - 允许最多未命中数 **\*1\.2\-3 新增**
>   - 整数型，可选参数，可以允许最多的不匹配的点的数量，默认为 0，也就是全命中才算找到
> - 起始点横坐标, 起始点纵坐标
>   - 整数型，代表起始坐标，它并不是限制找色的范围为固定这一点，而仅仅是给偏移位置一个相对坐标，不理解就填 0, 0
> - 起始点颜色
>   - 整数型，代表需要搜索的那一点的颜色
> - 起始点相似度
>   - 整数型，可选参数，需要搜索的那一点颜色的相似度，取值范围 1~100，默认 100
> - 偏移点横坐标\*, 偏移点纵坐标\*
>   - 整数型，代表一个偏移位置坐标
> - 偏移点颜色\*
>   - 整数型，代表偏移位置需要匹配的颜色
> - 偏移点相似度\*
>   - 整数型，可选参数，偏移位置的颜色的相似度，取值范围 \-100~100，默认 100，负相似度意味着匹配小于该绝对值的相似度
> - 全局相似度
>   - 整数型，可选参数，如果没有给单个点设置相似度，那么每一点都会用这个相似度，取值范围 1~100，默认 100
> - 左, 上, 右, 下
>   - 整数型，可选参数，代表搜索区域，默认 全屏
> - 横坐标, 纵坐标
>   - 整数型，返回匹配色的第一个色的坐标，搜索失败返回 \-1, \-1


#### 说明
> 使用相似度模式查找，获取区域中第一个完全匹配的多点颜色结构的位置  


#### 示例  
```lua
x, y = screen.find_color({
	{  0,   0, 0xec1c23},
	{ 12,  -3, 0xffffff, 85},
	{  5, -18, 0x00adee},
	{ -1, -10, 0xffc823},
	{  2, -34, 0xa78217},
	{ 12, -55, 0xd0d2d2},
}, 90, 0, 0, 100, 100)
--
--[[
    在左上为 0, 0 右下为 100, 100 的区域找到第一点与 0xec1c23 相似度大于 90 
    且它的相对坐标 (12, -3) 的位置的颜色与 0xffffff 相似度大于 85
    且它的相对坐标 (5, -18) 的位置的颜色与 0x00adee 相似度大于 90 
    且…… (后面的同理) 都能匹配的那个点
--]]
--
-- 等效代码如下：
--
x, y = screen.find_color({
	{ 509, 488, 0xec1c23},
	{ 521, 485, 0xffffff, 85},
	{ 514, 470, 0x00adee},
	{ 508, 478, 0xffc823},
	{ 511, 454, 0xa78217},
	{ 521, 433, 0xd0d2d2},
}, 90, 0, 0, 100, 100)
--
--[[
    在左上为 0, 0 右下为 100, 100 的区域找到第一点与 0xec1c23 相似度大于 90 
    且它的相对坐标 (521-509, 485-488) 的位置的颜色与 0xffffff 相似度大于 85 
    且它的相对坐标 (514-509, 470-488) 的位置的颜色与 0x00adee 相似度大于 90 
    且…… (后面的同理) 都能匹配的那个点
--]]
--
-- 不换行无缩进就是这个效果：
x, y = screen.find_color({ {0,0,0xec1c23},{12,-3,0xffffff,85},{5,-18,0x00adee},{-1,-10,0xffc823},{2,-34,0xa78217},{12,-55,0xd0d2d2},},90,0,0,100,100)
--
x, y = screen.find_color({ -- 反匹配演示，在 5C 主屏幕运行可获得结果
	{ 516,  288, 0xffffff },
	{ 519,  286, 0xffffff },
	{ 521,  289, 0xffffff },
	{ 516,  296, 0xffffff },
	{ 522,  297, 0xffffff },
	{ 520,  295, 0xffffff, -10 }, -- 这一点颜色与 0xffffff 相似度小于 10 才匹配，下同
	{ 515,  291, 0xffffff, -10 },
	{ 518,  284, 0xffffff, -10 },
	{ 523,  298, 0xffffff, -10 },
	{ 514,  298, 0xffffff, -10 },
	{ 514,  296, 0xffffff, -10 },
}, 90) -- 不写区域参数表示全屏找
--
results = screen.find_color({ -- 范围匹配全输出演示
	{  527,  278, 0xde1d26 },
	{  524,  285, 0x007aff },
	{  555,  292, 0xe4ddc9 },
	{  536,  314, 0xffde02 },
	{  502,  291, 0xffde02 },
	{  502,  283, 0xe4ddc9 },
	find_all = true, -- 带这个标签将返回范围所有匹配的位置的一个表，格式为 { {x1, y1}, {x2, y2}, ...}
}, 90) -- 不写区域参数表示全屏找
```
