# 图像生成 ```Beta```
[查看原版文档](https://platform.openai.com/docs/guides/images)

了解如何使用我们的DALL·E模型生成或操作图像

## 介绍

Images API提供了三个与图像交互的方法:

1.  基于文本prompt从零开始创建图像

2.  基于新的文本prompt对现有的图像进行编辑

3.  创建现有图像的变体

本指南通过实用的代码示例介绍了使用这三个API的基础知识。要查看他们的行动，请查看我们的[DALL·E预览应用程序](https://labs.openai.com/)。

<pre>
```
Images API处于测试阶段。在此期间，API和模型将根据你的反馈进行改进。为了确保所有用户都可以轻松地创建原型，默认速率限制为每分钟50张图片。如果你想提高你的速率限制，请查看这篇帮助中心的文章（https://help.openai.com/en/articles/6696591）。当我们了解更多关于使用情况和容量需求时，我们将提高默认速率限制。
```
</pre>

## 使用

### 生成

图像生成端点允许你在给定文本prompt的情况下创建原始图像。生成的图像大小可以是256x256、512x512或1024x1024像素。较小的文件大小生成速度更快。你可以使用n参数一次请求1-10张图片。

```python
response = openai.Image.create(
  prompt="a white siamese cat",
  n=1,
  size="1024x1024"
)
image_url = response['data'][0]['url']
```

描述越详细，你就越有可能得到你或最终用户想要的结果。你可以在[DALL·E预览应用程序](https://labs.openai.com/)中探索示例以获得更多提示灵感。下面是一个简单的例子:

PROMPT                           | GENERATION                                                                                  |
| -------------------------------- | ------------------------------------------------------------------------------------------- |
| 一只白色的暹罗猫 | <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/62b156d09cb9420dafe2550735e29ef7~tplv-k3u1fbpfcp-zoom-1.image" width="240" height="240">|
| 一个特写，工作室摄影肖像风格的看起来很好奇的白色暹罗猫，背光耳朵 | <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/af63b1b9651a413bb8b12f922288afec~tplv-k3u1fbpfcp-zoom-1.image" width="240" height="240"> |

使用[response_format](https://platform.openai.com/docs/api-reference/images/create#images/create-response_format)参数，可以将每个图像作为URL或Base64数据返回。url将在一小时后过期。

### 编辑

[image edits](https://platform.openai.com/docs/api-reference/images/create-edit)端点允许你通过上传蒙版来编辑和扩展图像。蒙版的透明区域表示应该编辑图像的位置，prompt应该描述完整的新图像，**而不仅仅是擦除的区域**。这个端点可以使我们的[DALL·E预览应用程序中的编辑器](https://labs.openai.com/editor)等体验成为可能。

```python
response = openai.Image.create_edit(
  image=open("sunlit_lounge.png", "rb"),
  mask=open("mask.png", "rb"),
  prompt="A sunlit indoor lounge area with a pool containing a flamingo",
  n=1,
  size="1024x1024"
)
image_url = response['data'][0]['url']
```

图像                                                                                                                        | 蒙版                                                                                                                        | 输出                                                                                                                        |
| ------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bf3026e153244a1cb98a3b863ac8fe92~tplv-k3u1fbpfcp-zoom-1.image) | ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d53df6d26587486cb9bedd630c57650a~tplv-k3u1fbpfcp-zoom-1.image) | ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9719bb8da5334513931e7e27f7530a71~tplv-k3u1fbpfcp-zoom-1.image) |

Prompt: 阳光充足的室内休息区，有一个包含火烈鸟的游泳池

上传的图片和蒙版必须是小于4MB的正方形PNG图片，且大小相同。在生成输出时，不使用蒙版的非透明区域，因此它们不一定需要像上面的示例那样匹配原始图像。

### 变体

图像变体端点允许你生成给定图像的变体。

```python
response = openai.Image.create_variation(
  image=open("corgi_and_cat_paw.png", "rb"),
  n=1,
  size="1024x1024"
)
image_url = response['data'][0]['url']
```

IMAGE                                                                                                                     | OUTPUT                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3e2fb28cc49e437d959c92b08ef1ea88~tplv-k3u1fbpfcp-zoom-1.image) | ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6af13e831ec445d881791d041f5376a0~tplv-k3u1fbpfcp-zoom-1.image) |

与edits端点类似，输入图像必须是小于4MB的正方形PNG图像。

### 内容审核

上传的prompt和图像会根据我们的[内容策略](https://labs.openai.com/policies/content-policy)进行过滤，当prompt或图像被标记时返回错误。如果你有任何关于误报或相关问题的反馈，请通过我们的[帮助中心](https://help.openai.com/)联系我们。

## 特定于语言的技巧

PYTHON版本

**使用内存中的图像数据**

上面指南中的Python示例使用```open```函数从磁盘读取图像数据。在某些情况下，你可能会将图像数据保存在内存中。下面是一个API调用的例子，它使用了存储在```BytesIO```对象中的图像数据:

```python
from io import BytesIO

# This is the BytesIO object that contains your image data
byte_stream: BytesIO = [your image data]
byte_array = byte_stream.getvalue()
response = openai.Image.create_variation(
  image=byte_array,
  n=1,
  size="1024x1024"
)
```

**对图像数据进行操作**

在将图像传递给API之前，对它们执行一些操作可能会很有用。下面是一个使用```PIL```调整图像大小的例子:

```python
from io import BytesIO
from PIL import Image

# Read the image file from disk and resize it
image = Image.open("image.png")
width, height = 256, 256
image = image.resize((width, height))

# Convert the image to a BytesIO object
byte_stream = BytesIO()
image.save(byte_stream, format='PNG')
byte_array = byte_stream.getvalue()

response = openai.Image.create_variation(
  image=byte_array,
  n=1,
  size="1024x1024"
)
```

**错误处理**

由于输入无效、频率限制或其他问题，API请求可能会返回错误。这些错误可以用```try…Except```语句，错误的详细信息可以在```e.error```中找到:

```python
try:
  openai.Image.create_variation(
    open("image.png", "rb"),
    n=1,
    size="1024x1024"
  )
  print(response['data'][0]['url'])
except openai.error.OpenAIError as e:
  print(e.http_status)
  print(e.error)
```