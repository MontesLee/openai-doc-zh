# 快速入门
[查看原版文档](https://platform.openai.com/docs/quickstart)

OpenAI已经训练出了非常擅长理解和生成文本的尖端语言模型。我们的API提供了对这些模型的访问，可以用于解决几乎任何涉及处理语言的任务。

在本快速入门教程中，您将构建一个简单的示例应用程序。在此过程中，您将学习在各种任务中使用API的基本概念和技术，包括:

● 内容生成

● 摘要

● 分级，分类和情绪分析

● 数据提取

● 翻译

● 更多其他任务!

## 简介

[completions](https://platform.openai.com/docs/api-reference/completions)端点是我们API的核心，提供了一个非常灵活和强大的简单接口。您输入一些文本作为prompt，API将返回一个文本completion，试图匹配您给它的任何指令或上下文。

prompt： 为冰淇淋店写一句标语。

completion： 我们为每一勺冰淇淋提供微笑!

你可以认为这是一个非常高级的自动补全——模型处理你的文本提示，并尝试预测接下来最有可能发生的事情。

## 从指令开始

假设您想要创建一个宠物名生成器。从零开始起名字是很难的!

首先，你需要一个能明确你想要什么的prompt。让我们从一个指令开始。提交此prompt以生成你的第一个completion。


```js
帮我给马起一个名字。    [原文可以提交prompt]
```
很好！现在，试着让你的指令更具体。


```js
帮我给一匹黑马取一个名字。    [原文可以提交prompt]
```

如您所见，向prompt中添加一个简单的形容词会改变completion的结果。设计你的prompt本质上是你如何为模型“编程”。

## 再多一些例子

制定好的指令对于取得好的结果很重要，但有时还不够。让我们试着让你的指令更复杂。


```js
给一匹超级英雄马取三个名字。    [原文可以提交prompt]
```

这个completion并不是我们想要的。这些名称非常平庸，似乎模型并没有注意到我们指令中关于马的部分。让我们看看是否能让它提出一些更相关的建议。

在很多情况下，向模型展示并告诉模型你想要什么是很有帮助的。在prompt中添加示例有助于告知模型你需要的模式或细节。尝试提交这个括几个示例的prompt。


```js
给一位超级英雄动物起三个名字。    [原文可以提交prompt]

动物：猫

名字：Captain Sharpclaw，Agent Fluffball，The Incredible Feline

动物：狗狗

名字：Ruff the Protector，Wonder Canine，Sir Barks-a-Lot

动物：马

名字：

```

很好！为给定的输入添加我们期望的输出的示例，可以帮助模型为我们提供所需的那种名字。

## 调整你的设置

prompt设计并不是您可以使用的唯一工具。您还可以通过调整设置来控制completion。最重要的设置之一叫做temperature。

您可能已经注意到，如果在上面的示例中多次提交相同的prompt，模型将总是返回相同或非常相似的completion。这是因为temperature被设为0。

尝试在temperature设置为1的情况下重新提交相同的prompt几次。

```js
给一位超级英雄动物起三个名字。    [原文可以调整temperature并提交prompt]

动物：猫

名字：Captain Sharpclaw，Agent Fluffball，The Incredible Feline

动物：狗狗

名字：Ruff the Protector，Wonder Canine，Sir Barks-a-Lot

动物：马

名字：

```

看看发生了什么?当temperature高于0时，每次提交相同的prompt会产生不同的completion。

记住，该模型用来预测一段文本后面最有可能出现什么样的文本。temperature是一个介于0和1之间的值，它本质上用于让您控制模型在进行这些预测时的自信程度。降低temperature意味着风险更小，completion将更加准确和确定。提高temperature将产生更多样化的completion。


```js
深入了解

理解token和概率

我们的模型通过将文本分解为更小的单元(称为token)来处理文本。标记可以是单词、单词块或单个字符。
编辑下面的文本，看看它是如何被分解成token的。
```


![tokenizer.png](https://github.com/MontesLee/openai-doc-zh/blob/main/asset/tokenizer.png?raw=true)

```js
像“猫”这样的常见单词是一个token，而不太常见的单词通常被分解成多个token。例如，“Butterscotch” 
分解成四个token：“But”，“ters”，“cot”和“ch”。许多token以空白开始，例如“ hello”和“ bye”。

给定一些文本，模型确定下一个最有可能出现的token。例如，文本“马是我最喜欢的”后面最有可能跟着token“动物”。
```

![probabilities.png](https://github.com/MontesLee/openai-doc-zh/blob/main/asset/probabilities.png?raw=true)

```js
这就是temperature发挥作用的地方。如果你在temperature设置为0的情况下提交这个prompt 4次，模型下一次总是
返回“animal”，因为它有最高的概率。如果你提高temperature，它将承担更多的风险，并考虑概率较低的token。
```

![probabilities_temperature.png](https://github.com/MontesLee/openai-doc-zh/blob/main/asset/probabilities_temperature.png?raw=true)

```js
对于希望输出值非常明确的任务，通常最好设置较低的temperature。较高的temperature可能对需要多样性或创造性
的任务更有用，或者如果您想为最终用户或领域专家生成一些可供选择的变化。
```

对于宠物名生成器，您可能希望能够生成许多名字。将temperature设为适中的0.6就可以了。

## 构建应用程序

PYTHON(FLASK) 　　[原文有NODE.JS版本]

现在您已经找到了一个很好的prompt和设置，您已经准备好构建您的宠物名生成器了!我们已经写了一些代码让你开始——按照下面的说明下载代码并运行应用程序。

### 安装

如果你没有安装Python，请从这里[开始安装](https://www.python.org/downloads/)。然后通过克隆这个[存储库](https://github.com/openai/openai-quickstart-python)下载代码。

```
git clone https://github.com/openai/openai-quickstart-python.git
```

如果您不喜欢使用git，也可以使用这个[zip文件](https://github.com/openai/openai-quickstart-python/archive/refs/heads/master.zip)下载代码。

### 添加API key

要让应用程序工作，您需要一个API key。你可以通过[注册](https://platform.openai.com/signup)一个账户并返回到这个页面来获得API key。

### 运行应用程序

在项目目录中运行以下命令来安装依赖项并运行应用程序。运行这些命令时，根据您的设置，您可能需要键入python3/pip3而不是python/pip。

```
python -m venv venv
. venv/bin/activate
pip install -r requirements.txt
flask run
```

在浏览器中打开http://localhost:5000 ，您应该会看到宠物名生成器!

### 理解代码

打开openai-quickstart-python文件夹中的app.py。在底部，您将看到生成我们上面使用的prompt的函数。由于用户将输入他们的宠物的动物类型，它将动态地替换prompt中指定动物的部分。

```
def generate_prompt(animal):
    return """Suggest three names for an animal that is a superhero.

Animal: Cat
Names: Captain Sharpclaw, Agent Fluffball, The Incredible Feline
Animal: Dog
Names: Ruff the Protector, Wonder Canine, Sir Barks-a-Lot
Animal: {}
Names:""".format(animal.capitalize())
```

在app.py的第14行，您将看到发送实际API请求的代码。如上所述，它使用temperature为0.6的completion端点。

```
response = openai.Completion.create(
  model="text-davinci-003",
  prompt=generate_prompt(animal),
  temperature=0.6
)
```

就是这样!现在您应该已经完全理解了您的(超级英雄)宠物名生成器是如何使用OpenAI API的！

## 结尾

这些概念和技术将在很大程度上帮助您构建自己的应用程序。也就是说，这个简单的示例只演示了可能实现的功能的一小部分！completion端点非常灵活，几乎可以解决任何语言处理任务，包括内容生成、摘要、语义搜索、主题标记、情绪分析等等。

要记住的一个限制是，对于大多数模型，在prompt和completion之间，单个API请求最多只能处理2048个token(大约1500个单词)。

<table><tr><td>
深入了解

模型和价格

我们提供了一系列不同能力和[价位](https://openai.com/api/pricing/)的[模型](https://platform.openai.com/docs/models)。在本教程中，我们使用了我们能力最强的自然语言模型text-davinci-003。我们建议在实验时使用这个模型，因为它将产生最好的结果。一旦你让事情运转起来，你就可以看看其他模型是否能以更低的延迟和成本产生相同的结果。

在单个请求中处理的token总数(提示和完成)不能超过模型的最大上下文长度。对于大多数模型，这是2048个标记或大约1500个单词。从粗略的经验来看，对于英语文本来说，1个token大约是4个字符或0.75个单词。

价格是每1000个token现收现付，前3个月可使用5美元的无息借贷。[了解更多](https://openai.com/api/pricing/)。
</table></tr></td>


对于更高级的任务，你可能会发现自己希望能提供更多的例子或上下文，而不是一个简单的提示。对于像这样更高级的任务，微调API是一个很好的选择。微调允许您提供数百甚至数千个示例，以便为特定的用例定制模型。

## 下一步

为了获得灵感并进一步学习如何为不同任务设计prompt:

● 阅读我们的[completion指南](https://platform.openai.com/docs/guides/completion)。

● 探索我们的[prompt示例](https://platform.openai.com/examples)库。

● 开始在[游乐场](https://platform.openai.com/playground)上做实验。

● 在开始构建时，请牢记我们的[使用政策](https://platform.openai.com/docs/usage-policies)。