# 聊天completion

[ChatGPT](https://chat.openai.com/chat)由OpenAI最先进的语言模型```gpt-3.5-turbo```提供支持。

使用OpenAI API，你可以使用```gpt-3.5-turbo```构建自己的应用程序，完成以下任务:

● 起草电子邮件或其他写作材料

● 编写Python代码

● 回答关于一组文档的问题

● 创建会话代理

● 给你的软件一个自然语言的界面

● 指导一系列学科

● 翻译语言

● 为电子游戏模拟角色等等

本指南解释了如何[对基于聊天的语言模型进行API调用](https://platform.openai.com/docs/api-reference/chat)，并分享了获得良好结果的技巧。你也可以在OpenAI的[Playground中试验新的聊天格式](https://platform.openai.com/playground?mode=chat)。

## 介绍

聊天模型接受一系列消息作为输入，并返回一个模型生成的消息作为输出。

虽然聊天格式的设计目的是简化多轮对话，但它对于没有对话的单轮任务同样有用(例如以前由```text- davinci003```等指令遵循模型提供的任务)。

一个API调用示例如下所示:

```python
# Note: you need to be using OpenAI Python v0.27.0 for the code below to work
import openai

openai.ChatCompletion.create(
  model="gpt-3.5-turbo",
  messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Who won the world series in 2020?"},
        {"role": "assistant", "content": "The Los Angeles Dodgers won the World Series in 2020."},
        {"role": "user", "content": "Where was it played?"}
    ]
)
```

主要的输入是messages参数。Messages必须是一个message对象数组，其中每个对象都有一个角色(“系统”、“用户”或“助手”)和内容(消息的内容)。对话可以短到一条消息，也可以填满许多页。

通常，一个对话首先由一条系统消息格式化，然后是交替的用户和助手消息。

系统消息帮助设置助手的行为。在上面的例子中，助手被告知“你是一个有用的助手。”

[gpt-3.5-turbo-0301](models.md) 并不总是非常关注系统消息。未来的模型将被训练成更关注系统消息。

用户消息有助于指导助手。它们可以由应用程序的最终用户生成，也可以由开发人员作为指令设置。

助手消息帮助存储先前的响应。它们也可以由开发人员编写，以帮助提供所需行为的示例。

当用户要求引用以前的消息时，包含对话历史记录会很有用。在上面的例子中，用户的最后一个问题“比赛在哪里举行?”只在之前关于2020年世界大赛的消息上下文中才有意义。因为模型没有存储过去请求的记忆，所以必须通过对话提供所有相关信息。如果对话不能满足模型的token限制，则需要以某种方式缩短对话。

### 响应格式

API响应示例如下所示:

```json
{
 'id': 'chatcmpl-6p9XYPYSTTRi0xEviKjjilqrWU2Ve',
 'object': 'chat.completion',
 'created': 1677649420,
 'model': 'gpt-3.5-turbo',
 'usage': {'prompt_tokens': 56, 'completion_tokens': 31, 'total_tokens': 87},
 'choices': [
   {
    'message': {
      'role': 'assistant',
      'content': 'The 2020 World Series was played in Arlington, Texas at the Globe Life Field, which was the new home stadium for the Texas Rangers.'},
    'finish_reason': 'stop',
    'index': 0
   }
  ]
}
```

在Python中，可以使用```response['choices'][0]['message']['content']```提取助手的回复。

每个响应都包含```finish_reason```。```finish_reason```的可能值如下。

● ```stop```: API返回完整的模型输出

● ```length```:由于max_tokens参数或令牌限制，模型输出不完整

● ```content_filter```:由于我们的内容过滤器的标志而忽略了内容

● ```null```: API响应仍在进行中或不完整

### 管理token

语言模型以称为标记(token)的块来读取文本。在英语中，token可以短到一个字符，也可以长到一个单词(例如，```a```或```apple```)。在某些语言中，token甚至可以短于一个字符，甚至可以长于一个单词。

例如，字符串```"ChatGPT is great!"```被编码为6个标记:```["Chat"， "G"， "PT"， " is"， " great"， "!"]```。

API调用中的token总数会影响:

● 你的API调用成本是多少，按每一个token支付成本

● 调用API需要多长时间，因为编写更多token需要更多时间

● 你的API调用是否正常工作，因为总token必须低于模型的最大限制(```gpt-3.5-turbo-0301```的令牌为4096个)

输入和输出token都计入这些数量。例如，如果你的API调用在消息输入中使用了10个token，而你在消息输出中收到了20个token，那么你将为30个token付费。

要查看API调用使用了多少token，请检查API响应中的```usage```字段(例如，```response['usage']['total_tokens']```)。

像gpt-3.5-turbo这样的聊天模型使用token的方式与其他模型相同，但由于它们是基于消息的格式，因此更难计算一个会话将使用多少token。

<br>

**深入了解**

#### 统计聊天API调用的令牌

下面是一个示例函数，用于计算传递给gpt-3.5-turbo-0301消息的token的数量。

消息转换为token的确切方式可能会因模型而异。因此，当新的模型版本发布时，这个函数返回的可能只是一个近似值。[ChatML文档](https://github.com/openai/openai-python/blob/main/chatml.md)解释了OpenAI API如何将消息转换为token，对于编写你自己的统计函数可能很有用。

```python
def num_tokens_from_messages(messages, model="gpt-3.5-turbo-0301"):
  """Returns the number of tokens used by a list of messages."""
  try:
      encoding = tiktoken.encoding_for_model(model)
  except KeyError:
      encoding = tiktoken.get_encoding("cl100k_base")
  if model == "gpt-3.5-turbo-0301":  # note: future models may deviate from this
      num_tokens = 0
      for message in messages:
          num_tokens += 4  # every message follows <im_start>{role/name}\n{content}<im_end>\n
          for key, value in message.items():
              num_tokens += len(encoding.encode(value))
              if key == "name":  # if there's a name, the role is omitted
                  num_tokens += -1  # role is always required and always 1 token
      num_tokens += 2  # every reply is primed with <im_start>assistant
      return num_tokens
  else:
      raise NotImplementedError(f"""num_tokens_from_messages() is not presently implemented for model {model}.
  See https://github.com/openai/openai-python/blob/main/chatml.md for information on how messages are converted to tokens.""")
```

接下来，创建一条消息并将其传递给上面定义的函数来查看token计数，这应该与API usage参数返回的值相匹配:

```python
messages = [
  {"role": "system", "content": "You are a helpful, pattern-following assistant that translates corporate jargon into plain English."},
  {"role": "system", "name":"example_user", "content": "New synergies will help drive top-line growth."},
  {"role": "system", "name": "example_assistant", "content": "Things working well together will increase revenue."},
  {"role": "system", "name":"example_user", "content": "Let's circle back when we have more bandwidth to touch base on opportunities for increased leverage."},
  {"role": "system", "name": "example_assistant", "content": "Let's talk later when we're less busy about how to do better."},
  {"role": "user", "content": "This late pivot means we don't have time to boil the ocean for the client deliverable."},
]

model = "gpt-3.5-turbo-0301"

print(f"{num_tokens_from_messages(messages, model)} prompt tokens counted.")
# Should show ~126 total_tokens
```

为了确认我们上面的函数生成的数字与API返回的数字相同，创建一个新的聊天Completion:

```python
# example token count from the OpenAI API
import openai


response = openai.ChatCompletion.create(
    model=model,
    messages=messages,
    temperature=0,
)

print(f'{response["usage"]["prompt_tokens"]} prompt tokens used.')
```
<br>

要想在不调用API的情况下查看文本字符串中有多少token，可以使用OpenAI的[tiktoken](https://github.com/openai/tiktoken) Python库。示例代码可以在OpenAI Cookbook的“如何[使用tiktoken为token计数](https://github.com/openai/openai-cookbook/blob/main/examples/How_to_count_tokens_with_tiktoken.ipynb)”指南中找到。

传递给API的每条消息都会消耗content、role和其他字段中的token数量，另外还有一些用于后台格式化的token。这在未来可能会略有改变。

如果对话中的token太多，超出模型的最大限制(例如，gpt-3.5-turbo的token超过4096)，则必须截断、省略或缩小文本，直到适合。请注意，如果从消息输入中删除某一条消息，模型将丢失有关它的所有知识。

还要注意，非常长的对话更有可能收到不完整的回复。例如，一个4090个token长的```gpt-3.5-turbo```会话在仅6个token后就会被切断。

## 指导聊天模型

指导模型的最佳实践可能会随着模型版本的不同而改变。以下建议适用于```gpt-3.5-turbo-0301```，可能不适用于未来的型号。

许多对话以系统消息开始，逐步地指示助手。例如，下面是一个用于ChatGPT的系统消息:

```
你是ChatGPT，由OpenAI训练的大型语言模型。尽可能简洁地回答。知识截止日期:{knowledge_cutoff}当前日期:{current_date}
```

一般来说，gpt-3.5-turbo-0301不太关注系统消息，因此重要的指令通常最好放在用户消息中。

如果模型没有生成你想要的输出，请随时迭代和试验潜在的改进。你可以尝试这样的方法:

● 让你的指令更明确

● 指定你希望答案采用的格式

● 让模型一步一步地思考，或者在确定答案之前讨论利弊

有关更多prompt engineering的想法，请阅读OpenAI Cookbook指南中有关[提高可靠性的技术的指导](https://github.com/openai/openai-cookbook/blob/main/techniques_to_improve_reliability.md)。

除了系统消息，temperature和max tokens是开发人员影响聊天模型输出的众多选项中的两个。对于temperature，较高的值(如0.8)将使输出更随机，而较低的值(如0.2)将使其更集中和确定性。对于max tokens，如果你想将响应限制在某个长度内，可以将max tokens设置为任意数字。这可能会导致问题，例如，如果你将最max tokens设置为5，由于输出将被切断，返回结果对用户没有意义。

## 聊天 vs Completion

因为```gpt-3.5-turbo```的性能与```text-davincinch-003```相似，但每个token的价格是它的10%，所以我们建议在大多数用例中使用```gpt-3.5-turbo```。

对于许多开发人员来说，转换非常简单，只需重写并重新测试prompt。

例如，如果你使用以下completion prompt将英语翻译为法语:

```
Translate the following English text to French: "{text}"
```

等价的聊天对话可能如下所示:

```json
[
  {"role": "system", "content": "You are a helpful assistant that translates English to French."},
  {"role": "user", "content": 'Translate the following English text to French: "{text}"'}
]
```

或者仅仅是用户消息:

```json
[
  {"role": "user", "content": 'Translate the following English text to French: "{text}"'}
]
```

## 常见问题解答

**gpt-3.5-turbo可进行微调吗?**

不。截至2023年3月1日，你只能基于GPT-3模型微调。有关如何对模型进行微调的更多详细信息，请参阅[微调指南](https://platform.openai.com/docs/guides/fine-tuning)。

**传递给API的数据存储了吗?**

从2023年3月1日起，我们将保留你的API数据30天，但不再使用通过API发送的数据来改进我们的模型。更多信息请参阅我们的[数据使用策略](Usage_Policies.md)。

**添加审核层**

如果你想在聊天API的输出中添加一个审核层，可以按照我们的[审核指南](https://platform.openai.com/docs/guides/moderation)来阻止违反OpenAI使用策略的内容显示。