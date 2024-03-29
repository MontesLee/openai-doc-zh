# 文本 completion

[查看原版文档](https://platform.openai.com/docs/guides/completion/text-completion)

了解如何生成或处理文本

## 简介

[completion](https://platform.openai.com/docs/api-reference/completions)端点可以用于各种各样的任务。它为我们的各种[模型](models.md)提供了一个简单但强大的接口。你输入一些文本作为prompt，模型将生成一个文本completion，尝试匹配你提供的上下文或模式。例如，如果你给API这样一个prompt，“正如笛卡尔所说，我思，因此”，它将大概率返回completion：“故我在”。

开始探索completion的最佳方式是通过我们的试验场。它只是一个让你输入prompt并返回completion的文本框。你可以从下面的例子开始:

为一家冰淇淋店写一句标语。

```
为一家冰淇淋店写一句标语。
```

提交后，你会看到如下内容:

```python
为一家冰淇淋店写一句标语。
'我们为每一勺冰淇淋提供微笑!'
```

你实际看到的completion可能有所不同，因为默认情况下API是非确定性的。这意味着每次调用它时，即使prompt保持不变，也可能得到略微不同的completion。将[temperature](https://platform.openai.com/docs/api-reference/completions/create#completions/create-temperature)设置为0，将使输出大概率是确定的，但可能会保留少量的可变性。

这个简单的文本输入，文本输出界面意味着你可以通过提供指令或仅仅是你想要它做的一些示例来“编程”模型。它的成功通常取决于任务的复杂性和prompt的质量。一个很好的经验法则是思考如何写一个供中学生来解决的应用题。一个精心编写的prompt为模型提供了足够的信息，让模型知道你想要什么以及它应该如何响应。

本指南涵盖一般prompt设计、最佳实践和示例。要了解使用Codex模型处理代码的更多信息，请访问我们的[代码指南](https://platform.openai.com/docs/guides/code)。

```
请记住，默认模型的训练数据在2021年中断，因此它们可能不了解当前事件。我们计划
在未来增加更多的持续训练。
```

## prompt设计

### 基础知识

我们的模型可以做任何事情，从生成原创故事到执行复杂的文本分析。因为他们可以做很多事情，你必须明确地描述你想要什么。展示，而不仅仅是讲述，往往是创造优质prompt的秘密。

创建提prompt有三个基本原则：

展示和讲述。通过说明、例子或两者结合来明确你想要什么。如果你想让模型按字母顺序对项目列表进行排序，或者根据情绪对段落进行分类，请向它展示你想要的效果。

提供高质量的数据。如果你试图构建一个分类器或让模型遵循一个模式运行，请确保有足够的示例。一定要校对你的例子——这个模型通常足够聪明，可以看穿基本的拼写错误并给你回复，但它也可能假设这是故意的，这可能会影响回复的质量。

检查你的设置。temperature和top_p参数控制模型在生成响应时的确定性。如果你要求它给出一个只有一个正确答案的答案，那么你就需要把这些值设得更低。如果你在寻找更多样化的回答，那么你可能想把它们设置得更高。人们使用这些设置时犯的第一个错误是，假设它们是用来控制“聪明度”或“创造力”的。

### 故障排除

如果你在让API按预期执行方面遇到困难，请遵循以下检查表:

● 它是否清楚预期的生成结果应该是什么?

● 有足够的例子吗?

● 你是否检查过例子有没有错误?(API不会直接告诉你)

● 你是否正确地使用了temperature和top_p?

### 分类

为了使用API创建文本分类器，我们提供了任务描述和一些示例。在这个例子中，我们展示了如何对推文的情绪进行分类。

```
判断一条推文的情绪是积极的、中性的还是消极的。

推特:我喜欢新的蝙蝠侠电影!
情绪:
```

在这个例子中，有几个特性值得注意:

● 使用简单的语言描述你的输入和输出。我们使用简单的语言来输入“推文”和预期的输出“情绪”。作为最佳实践，你应从简单的语言描述开始。虽然你通常可以使用简写或关键点来指示输入和输出，但最好从尽可能描述性的开始，然后向后删除多余的单词，看看性能是否保持一致。

● 向API展示如何响应各种情况。在这个例子中，我们在指令中包含了可能的情感标签。一个中性的标签很重要，因为在很多情况下，即使是人类也很难确定某物是积极的还是消极的，而在一些情况下，它两者都不是。

● 对于熟悉的任务，你不需要太多的例子。对于这个分类器，我们不提供任何示例。这是因为API已经理解了情感和推文的概念。如果你正在为API可能不熟悉的内容构建分类器，则可能有必要提供较多示例。

### 提高分类器的效率

现在我们已经掌握了如何构建分类器，让我们以这个例子为例，使其更加高效，以便我们可以使用它从一个API调用中获得多个结果。

```
对这些推文中的情绪进行分类:

1. “我受不了家庭作业”
2. “这很糟糕。我很无聊😠”
3.“我等不及万圣节了!!”
4. “我的猫很可爱❤️❤️”
5. “我讨厌巧克力”

推文情绪评级:
```

我们提供了一个有编号的推文列表，这样API就可以在一个API调用中对五个(甚至更多)推文进行评级。

需要注意的是，当你要求API创建列表或评估文本时，你需要额外注意你的概率设置(Top P或Temperature)，以避免漂移。

1.  通过运行多个测试，确保正确校准了概率设置。

2.  不要让列表太长，否则API可能会漂移。

### 生成

API 最强大且最简单的任务之一是生成输入的新想法或版本。你可以要求任何事情，从故事想法、商业计划、角色描述到营销口号。在本例中，我们将使用 API 为在健身中使用虚拟现实的想法生成创意。

```
头脑风暴一些结合VR和健身的想法:
```

如果需要，你可以通过在prompt中加入一些例子来提高回答的质量。

### 对话

API 在与人类甚至自身进行对话方面非常擅长。只需几行指令，我们就可以看到 API 表现为一个可以智能的回答问题而不会感到困惑的客户服务聊天机器人，或者是一个诙谐的对话伴侣，会说笑话和双关语。关键是告诉 API 它应该如何行动，然后提供一些例子。以下是 API 扮演 AI 回答问题的示例：

```
以下是与人工智能助手的对话。这位助手乐于助人、富有创造力、聪明而且非常友好。

人类:你好，你是谁?
AI:我是OpenAI创造的AI。今天我能为你效劳吗?
人类:
```

这就是创建一个能够进行对话的聊天机器人所需要的一切。在它的简单性之下，有几件事情值得关注:

1.  我们告诉 API 我们的意图，但我们也告诉它如何行动。就像其他提示一样，我们引导 API 理解这个例子代表的含义，但我们还添加了另一个关键细节：我们明确告诉它如何与短语“The assistant is helpful, creative, clever, and very friendly.”进行交互。

如果缺少这个指令，API 可能会偏离轨道，模仿它正在交互的人类并变得讽刺或其 他我们想避免的行为。

2.  我们为 API 赋予了一个身份。一开始，我们让 API 以 AI 助手的身份进行回复。虽然 API 没有固有的身份，但这有助于它以尽可能接近事实的方式进行回复。你可以以其他方式使用身份来创建其他类型的聊天机器人。如果你告诉 API 以一位在生物学领域工作的研究科学家的身份进行回复，你将从 API 中获得智能而周到的评论，类似于你从具有该背景的人那里期望的内容。

在这个例子中，我们创建了一个聊天机器人，它有点讽刺，不情愿地回答问题:

```
Marv是一个聊天机器人，不情愿地用讽刺的回答回答问题:

你:一公斤有几磅?
马夫:又是这个?一公斤有2.2磅。请记下来。
HTML代表什么?
马夫:谷歌太忙了吗?超文本标记语言。T代表将来努力提出更好的问题。
第一架飞机是什么时候飞起来的?
马夫:1903年12月17日，威尔伯和奥维尔·赖特进行了第一次飞行。我希望他们来把我带走。
生命的意义是什么?
马夫:我不确定。我会问我的朋友谷歌。
为什么天是蓝色的?
```

为了创建一个有趣且有点帮助的聊天机器人，我们提供了一些问题和答案示例，向API展示如何回答。它所需要的只是一些讽刺的回答，而API能够捕捉到这种模式并提供无数的讽刺回答。

### 转换

API 是一种语言模型，它熟悉各种表达信息的单词和字符的方式。其中包括自然语言文本、代码以及英语以外的语言。API还能够在一定程度上理解内容，从而允许它以不同的方式总结、转换和表达内容。

#### 翻译

在这个例子中，我们向API展示了如何从英语转换为法语、西班牙语和日语:

```
翻译成法语、西班牙语和日语:

你们有哪些空房可用?
```

这个例子可以运行，因为API已经掌握了这些语言，所以没有必要尝试教授它们。

如果你希望将英语翻译成API不熟悉的语言，则需要为其提供更多示例，甚至需要[对模型进行微调](https://platform.openai.com/docs/guides/fine-tuning)以使其流畅地完成翻译。

#### 转换

在本例中，我们将电影名称转换为表情符号。这显示了API在拾取模式和使用其他字符方面的适应性。

```
将电影标题转换为表情符号。

回到未来:👨👴🚗🕒
蝙蝠侠:🤵🦇
变形金刚:🚗🤖
星球大战:
```

API能够掌握文本的上下文，并以不同的方式重新措辞。在这个例子中，我们从一个更长、更复杂的文本段落中创建一个孩子可以理解的解释。这说明API对语言有很深的理解。

```
给一个二年级的学生总结如下文字:

木星是离太阳第五远的行星，也是太阳系中最大的行星。它是一颗气体巨星，
质量是太阳的千分之一，但却是太阳系中所有其他行星总和的2.5倍。
木星是夜空中肉眼可见的最明亮的天体之一，自古以来就为古代文明所知。
它是以罗马神朱庇特命名的。[19]从地球上看，木星足够亮，其反射光可以投射出
可见的阴影，[20]，平均而言，它是夜空中仅次于月球和金星的第三亮的自然天体。
```

###

### completions

虽然所有prompt都会产生completion，当你希望 API 从你停下来的地方继续的时候，可以把文本completion看作是一个独立的任务。例如，如果给出这个prompt，API将继续关于垂直农业的思路。你可以降低temperature设置，使API更专注于prompt的意图，或者增加它，让它偏离正题。

```
垂直农业为当地生产食品提供了一种新的解决方案，降低了运输成本和成本
```

下面的提示说明了如何使用补全来帮助编写React组件。我们发送一些代码到API，它能够继续剩下的，因为它理解React库。我们建议在涉及理解或生成代码的任务中使用我们的[Codex模型](https://platform.openai.com/docs/models/codex)。要了解更多信息，请访问我们的[代码指南](https://platform.openai.com/docs/guides/code)。

```
import React from 'react';
const HeaderComponent = () => (
```

### 真实的响应

API有很多知识都是从训练过的数据中学来的。它还能够提供听起来非常真实但实际上是编造的回答。有两种方法可以限制API编造答案的可能性。

1.  为API提供一个基本真理。如果你为API提供一段文本来回答有关的问题(如维基百科条目)，则不太可能虚构一个响应。

2.  使用低概率，并向API展示如何说“我不知道”。如果API理解在某些情况下，它在不太确定一个回答时，说“我不知道”或类似的话是合适的，它就不太倾向于编造答案。

在这个例子中，我们给API提供了它知道的问题和答案的例子，然后是它不知道的事情的例子，并提供了“？”。我们还将概率设置为零，以便API在有任何疑问时更有可能用“?”来响应。

```
问:蝙蝠侠是谁?
A:蝙蝠侠是一个虚构的漫画人物。

问:什么是torsalplexity?
答:?

问:什么是Devz9?
答:?

问:乔治·卢卡斯是谁?
A:乔治·卢卡斯是美国电影导演和制片人，因创作《星球大战》而闻名。

问:加州的首府是哪里?
萨克拉门托。

问:什么绕地球运行?
A:月亮。

问:弗雷德·瑞克森是谁?
答:?

问:原子是什么?
A:原子是构成万物的微小粒子。

问:Alvan Muntz是谁?
答:?

问:什么是Kozar-09?
答:?

问:火星有多少颗卫星?
A:两个，火卫一和火卫二。

问:
```

## 插入文本 ```Beta```

completion端点还支持在文本中插入文本，方法是提供[前缀提示](https://platform.openai.com/docs/api-reference/completions/create#completions/create-prompt)以及[后缀提示](https://platform.openai.com/docs/api-reference/completions/create#completions/create-suffix)。在编写长篇文本、段落之间的过渡、遵循大纲或引导模型走向结尾时，这种需求自然会出现。这也适用于代码，可用于在函数或文件中间插入。访问我们的[代码指南](https://platform.openai.com/docs/guides/code/inserting-text)了解更多信息。

为了说明后缀上下文对我们预测能力的重要性，注意下面的prompt，“今天我决定做一个大的改变。”人们可以想象有很多方法来完成这个句子。但是如果我们现在提供故事的结尾:“我的新头发得到了很多赞美!，预期的completion变得清晰。

```python
我在波士顿大学上的大学。拿到学位后，我决定做出改变。'一个很大的变化!

我收拾好行李，搬到了美国西海岸。'

现在，我怎么也看不够太平洋!
```

通过为模型提供更多的上下文，它可以被更好的控制。然而，这对模型来说是一项更有约束和挑战性的任务。

### 最佳实践

插入文本是beta版的一个新功能，为了获得更好的结果，你可能需要修改使用API的方式。以下是一些最佳实践:

使用max_tokens > 256。该模型更擅长插入较长的completion。如果max_tokens太小，模型可能会在连接到后缀之前被切断。请注意，即使使用更大的max_tokens，你也只会按生成的token数量收费。

建议finish_reason == "stop"。当模型到达自然停止点或用户提供的停止序列时，它将finish_reason设置为“stop”。这表明模型已经成功地连接到后缀，并且对completion质量是一个好的信号。这与使用n > 1或重新采样(参见下一点)时在几个completion之间进行选择尤其相关。

重采样 3-5 次。虽然几乎所有的补全都能连接到前缀,但在更难的情况下,模型可能难以连接后缀。我们发现,在这种情况下,重采样 3 次或 5 次(或设置k=3,5的情况下使用 best_of 参数),并选择带有 "stop" 的样本作为它们的 finish_reason,可能是一种有效的方法。在重采样时,通常需要更高的temperature来增加多样性。

注意:如果所有返回的样本都有finish_reason == "length"，很可能是因为max_tokens太小，模型在设法自然地连接提示符和后缀之前就用完了token。考虑在重新采样之前增加max_tokens。

试着多给点线索。在某些情况下，为了更好地帮助模型生成内容，你可以通过提供一些模型可以遵循的模式示例来为模型提供线索，帮它确定一个自然的停止位置。

如何制作美味的热巧克力:

```python
1. '煮水'

2. '把热巧克力放进杯子里'

3. '向杯中加入开水'

4. 享受热巧克力
```


```python
1. 狗是忠诚的动物。

2. 狮子是凶猛的动物。

3. 海豚'是爱玩的动物。'

4. 马是威严的动物。
```

## 编辑文本 ```Alpha```

[编辑](https://platform.openai.com/docs/api-reference/edits)端点可以用来编辑文本，而不仅仅是补全文本。你提供一些文本和如何修改它的指令，```text-davinci-edit-001```模型将尝试按要求编辑它。这是一个用于翻译、编辑和调整文本的自然界面。这对于重构和处理代码也很有用。访问我们的代[码指南](https://platform.openai.com/docs/guides/code/editing-text)了解更多信息。在初始测试期间，使用编辑端点是免费的。

### 示例

输入

```
GPT-3是一个非常好的AI
他很擅长写回复
当它被问到一个问题时
它给出了建议
这是一它写的首押韵的诗
```

指令

```
用GPT-3的口吻说
```

输出

```python
'我'是一个非常好的人工智能

'我'`很擅长写回复

当'我'被问到一个问题

'我'给出我的建议

这是'我'写的一首押韵诗
```

