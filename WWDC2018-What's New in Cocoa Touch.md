# -wwq
1. 预处理 cell 中耗时的内容 （并将预处理做了同步操作，在cell 滚动结束后 执行完当前页面要展示的内容再做 预加载）避免CPU 爆炸
2.  2.请求一个内容时候，如果内存足够就会直接取对应的内存，如果内存不足，就会从 Other Apps and System 中获取，这样的操作会影响那些在后台中的进程，会对其他地方产生影响
3. 图片 Automatic Backing Store 在IOS 12 中默认会在 三种方法中进行 a.	UIView.draw()
	b.UIGraphicsImageRenderer
	c.UIGraphicsImageRenderFormat.Range
	所有使用图片渲染的 API 都可以使用 Automatic Backing Store
 4.ios 12 对 autolayout 进行了优化， 让复杂的自动布局约束指数增长变成线性增长，大大提高列表自动布局性能
