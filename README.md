# 前言
相信微信大家是用得再多也不过了。那么，对于python+微信，又能玩出什么新的花样呢？下面小编就给大家带来一个好玩的东西。用python下载所有的微信好友的头像，然后拼接成一张大图。这样，大家就可以转载到朋友圈装XY了。哈哈。

还是先给大家看看效果图如何。毕竟无图言X。
![image](http://upload-images.jianshu.io/upload_images/10386940-2dadd13055d66579.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

PS：图侵删。这不是小编的微信哈……

好了废话不多说，看看我们是怎么一步一步做出来的吧。

# 下载微信好友头像

第一步当然是下载微信好友的头像，然后才能进行拼接。这里我们用到了itchat这个python的库。关于这个库，更多好玩的东西我们在后期会推出的，大家敬请关注and期待就好了。先看代码：

```python
itchat.auto_login()
for friend in itchat.get_friends(update=True)[0:]:
    #可以用此句print查看好友的微信名、备注名
    print(friend['NickName']+"("+friend['RemarkName']+")")
    img = itchat.get_head_img(userName=friend["UserName"])
    path = headImgPath+friend['NickName']+"("+friend['RemarkName']+").jpg"
    try:
        with open(path,'wb') as f:
            f.write(img)
    except Exception as e:
        print(repr(e))
```
代码灰常简单。首先我们调用itchat.auto_login()进行微信登陆。
![image](http://upload-images.jianshu.io/upload_images/10386940-dc2d8fab95153402.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

扫码登陆成功以后，我们再获取好友，然后获取头像，再配置一下文件保存路径，最后把头像写入文件里面。是不是简单到难以置信？
![image](http://upload-images.jianshu.io/upload_images/10386940-082478c376739adc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 拼接好友头像

拼接我们用了PIL库里面的Image模块。代码看下面：
```python
pathList = []
# os.listdir() 方法用于返回指定的文件夹包含的文件或文件夹的名字的列表。这个列表以字母顺序。 它不包括 '.' 和'..' 即使它在文件夹中。
for item in os.listdir(headImgPath):
    imgPath = os.path.join(headImgPath, item)
    pathList.append(imgPath)

total = len(pathList)#total是好友头像图片总数
line = int(sqrt(total))#line是拼接图片的行数（即每一行包含的图片数量）
NewImage = Image.new('RGB', (128*line,128*line))
x = y = 0
for item in pathList:
    try:
        img = Image.open(item)
        # Image.ANTIALIAS：平滑滤波。对所有可以影响输出像素的输入像素进行高质量的重采样滤波，以计算输出像素值。
        img = img.resize((128,128),Image.ANTIALIAS)
        NewImage.paste(img, (x * 128 , y * 128))
        x += 1
    except IOError:
        print("第%d行,%d列文件读取失败！IOError:%s" % (y,x,item))
        x -= 1
    if x == line:
        x = 0
        y += 1
    if (x+line*y) == line*line:
        break
NewImage.save("final.jpg")
```
我们先是用os.listdir获取了所有头像的路径保存到pathList里面。接着创建一个大图片，将大图片划分为n个小方格，再往小方格里面塞好友头像。需要注意的是，在塞好友头像前，需要对头像进行缩略变小才能塞进去。最后，生成最终文件。在当前目录下即可找到final.jpg。即为我们的成品。大家就可以拿着它去朋友圈装逼了~
![image](http://upload-images.jianshu.io/upload_images/10386940-82b895408a28e32a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

本来想弄一个现成的供大家下载使用，不过想想还是算了。大家直接下载python源码运行即可。

欲获取代码，请关注我们的微信公众号【程序猿声】，在后台回复：**txpj**。即可下载。

![微信公众号](http://upload-images.jianshu.io/upload_images/10386940-546ac15b9d7add56.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

推荐文章：[10分钟教你用Python做个打飞机小游戏超详细教程](https://www.jianshu.com/p/3bdea5b64269)