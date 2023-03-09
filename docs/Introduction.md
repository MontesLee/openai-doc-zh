# 简介

[查看原版文档](https://platform.openai.com/docs/introduction)

## 概述

OpenAI API几乎可以应用于任何涉及理解或生成自然语言或代码的任务。我们提供了一系列适合不同任务的具有不同能力级别的模型，同样也提供了用于优化您自己定制的模型的能力。这些模型可以用于包括内容生成、语义搜索及分类的任何事情。

## 关键概念

我们建议首先完成我们的快速入门教程，您可以通过一个实际操作的交互式示例来熟悉关键概念。

**快速入门教程**

[通过构建一个快速示例应用程序来学习](Quickstart.md)

## Prompts and completions

[completion](https://platform.openai.com/docs/api-reference/completions)端点是我们API的核心。它为我们的模型提供了一个非常灵活且强大的简单的接口。您输入一些文本作为prompt，模型会生成一个尝试匹配您提供的上下文或模式的文本completion。例如，如果你给API如下的prompt，“为一家冰淇淋店写一句标语”，它将返回一个类似“我们为每一勺冰淇淋提供微笑!”的completion。

[设计你的prompt](https://platform.openai.com/docs/guides/completion/prompt-design)本质上是你如何为模型“编程”，这通常是通过提供一些说明或范例来完成。这与大多数其他为单一任务设计的，例如提供情绪分类或实体识别的NLP服务不同。相反，completion端点几乎可以用于任何任务，包括内容或代码生成、摘要、扩展、对话、创意写作、风格转换等等。

## Tokens

我们的模型通过将文本分解为一系列token来理解和处理文本。token可以是单词，也可以是字符块。例如，单词“hamburger”被分解为像“ham”、“bur”和“ger”这样的token，而像“pear”这样简短而常见的单词则被当做单一的token。许多token以空白开始，例如“ hello”和“ bye”。

某个API请求中处理的token数量取决于你的输入和输出的长度。从粗略的经验来看，对于英语文本来说，1个token大约是4个字符或0.75个单词。要记住的一个限制是，您的文本prompt和生成的completion组合不允许超过模型的最大上下文长度(对于大多数模型，这是2048个token，或大约1500个单词)。想了解更多关于文本如何转换为token的信息，查看我们的[tokenizer tool](https://platform.openai.com/tokenizer)。

## 模型

API由一组具有不同功能和价格的模型组成。我们的基础GPT-3模型被称为Davinci、Curie、Babbage和Ada。我们的上述代码生成器（codex）是基于自然语言和代码训练而产生的GPT-3的衍生品。要了解更多信息，请访问我们的[模型文档](Models.md)。

## 下一步

在开始构建应用程序时，请牢记我们的[使用政策](Usage_Policies.md)。

探索我们的[示例库](https://platform.openai.com/examples)以获取灵感。

访问我们的任意指南开始构建应用程序。

## 指南

[聊天](https://platform.openai.com/docs/guides/chat) ```Beta```

学习如何使用基于聊天的语言模型

[文本completion](https://platform.openai.com/docs/guides/completion)

学习如何使用我们的模型生成或编辑文本

[Embeddings](https://platform.openai.com/docs/guides/embeddings)

学习如何搜索、分类和比较文本

[语音转文本](https://platform.openai.com/docs/guides/speech-to-text) ```Beta```

学习如何将音频转换为文本

[图像生成](https://platform.openai.com/docs/guides/images) ```Beta```

学习如何生成或编辑图像

[代码completion](https://platform.openai.com/docs/guides/code) ```Beta```

学习如何生成、编辑或解释代码

[微调](https://platform.openai.com/docs/guides/fine-tuning)

学习如何为您的用例训练模型