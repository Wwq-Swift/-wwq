# -wwq
一些 书籍 资料 阅读随笔

Kingfisher 相关 1.  PNG 以及 JPEG 等格式的图片对原图进行了压缩，必须要将其图片数据解码成位图之后才能使用  2. 使用 Core Graphics 绘制图片时，图片会倒立显示，Kingfisher 中使用了这个特性创建了一个工具函数来确保图片的正立(思考： 为什么图片会倒立) extension UIImage{
 	public func kf_normalizedImage() -> UIImage {
		if imageOrientation == .Up { return self }

		UIGraphicsBeginImageContextWithOptions(size, false, scale) 
		drawInRect(CGRect(origin: CGPointZero, size: size)) 
		let normalizedImage = UIGraphicsGetImageFromCurrentImageContext()
		UIGraphicsEndImageContext()
		return normalizedImage;
	}
}
如果该图片方向为正立，返回自身；否则将其用 Core Graphics 绘制，返回正立后的图片。  3. UIImage 并不能直接保存，需要先将其转化为 NSData 才能写入硬盘以及内存中缓存起来，UIKit 提供了两个 C 语言函数：UIImageJPEGRepresentation 和 UIImagePNGRepresentation，以便于将 JPG 及 PNG 格式的图片转化为 NSData 数据，但却并没有提供相应的 UIImageGIFRepresentation，可以自己编写这个函数以完成对Gif数据的保存。 （gif 图数据保存函数可以网上寻找）
 4. 图片存储文件名采用md5 加密   二： 缓存模块？ 1.缓存功能主要分为连个模块： a。 内存缓存。  B。 磁盘缓存 2. 主要功能： 缓存路径管理， 缓存的添加与删除。缓存的读取 缓存的清理。 缓存的状态监控   磁盘管理有专门的io队列进行对硬盘操作，硬盘操作极其耗时间，使其与主线程并行以避免造成阻塞。
processAQueue 用于执行图片解码操作，图片的decode 也是耗时的操作。 耗时的操作有专门的线程进行处理。 
三。缓存路径管理 为了方便硬盘的存取操作， 我们需要有几种工具来帮助我们实现通过key 获得特定的缓存：
A。对应 UIimage 图片， b。 对应NSData 数据。c。硬盘存储路径。 D。加密后的文件名  
四，字典按值排序 在缓存的管理当中，有时候我们需要依照缓存的修改时间进行排序，以确定缓存是否过期，而缓存时间往往位于字典键值对中值的位置，通常情况下对其排序并不是太容易，这里提供一个工具函数，代码如下  extension Dictionary { 	func keysSortedByValue(isOrderedBefore:(Value, Value) -> Bool) -> [Key] {
		var array = Array(self) 		array.sortInPlace {
            		let (_, lv) = $0
            		let (_, rv) = $1
            		return isOrderedBefore(lv, rv)
        		} 		return array.map {
            		let (k, _) = $0
            		return k
        		}
	}
}


通过一个数组来存储每个key 和 value， 然后通过 value 的比较来存储对应的排序key 数组。 Array（dictionary） 直接将字典转化为一个元组数组。   第一个为 key。第二个 为 value  四。缓存的添加与删除
缓存的添加分为三步： 写入内存， 写入硬盘，执行 comletionHandler 操作。 写入磁盘比较复杂可以参考源码（简单就是先判断图片类型， 得到对应的数据，然后判断文件磁盘中是否已经存在了 存储path， 不存在就进行创建 path将数据进行存储）  写入内存简单 直接调用 NSCache 的setObject 就可以了。  uiimage 中gif 图片的展示 并不能直接展示， 需要对数据进行操作转成。【uiimage】数组。  
五。缓存的删除 删除操作十分简单， 删除内存缓存， 删除硬盘缓存，   六。 缓存的读取 缓存读取就是从内存和硬盘中读取缓存。 若缓存在硬盘中，读取缓存后将其缓存一份到内存当中

七。 缓存清理
我们在缓存清理方面的需求一般有两个：清理所有硬盘内存缓存、后台自动删除过期超量硬盘缓存。

八。 后台自动删除过期超量的硬盘缓存
步骤： 1. 遍历所有缓存文件。 2. 判断缓存文件是否过期。3. 将缓存文件按日期排序， 逐步清理直到所占的空间小于预定大小。  4. 后台自动清理缓存

九。 GCD 的使用。比如使用调度组。来做 一些列必包完成之后 执行某个操作

十。缓存状态监控。（用于读取某些文件的缓存状态）
1. 查询某个图片是否存在缓存中，  查询某个图片的缓存名称。 读取当前缓存所占硬盘空间大小（子线程计算（遍历文件，累加） 回归到子线程调用completion）

十一。  用一个 rawvalue。转换为 二进制数字。 每一位都代表一种状态。比如。1010。  第四位表示是否在后台解码。  第二位表示 是否只有内存缓存。 等等。 用一个数字 进行来判断各种状态
