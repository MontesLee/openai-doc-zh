# 代码completion ```Limited beta```
[查看原版文档](https://platform.openai.com/docs/guides/code/introduction)

学习如何生成或操作代码

## 简介

Codex模型系列是我们[GPT-3系列](https://platform.openai.com/docs/models/base-series)的后代，它经过了自然语言和数十亿行代码的训练。它最擅长Python，同时精通十多种语言，包括JavaScript、Go、Perl、PHP、Ruby、Swift、TypeScript、SQL，甚至Shell。在最初的受限测试版期间，Codex的使用是免费的。[了解更多](https://platform.openai.com/docs/models/codex)。

你可以使用Codex完成各种任务，包括:

● 将注释转换为代码

● 在上下文中完成下一行或函数

● 为你带来知识，例如为应用程序找到有用的库或API调用

● 添加评论

● 重写代码以提高效率

要查看Codex的实际操作，请查看我们的[Codex JavaScript沙盒](https://platform.openai.com/codex-javascript-sandbox)或我们的其他[演示视频](https://www.youtube.com/playlist?list=PLOXw6I10VTv_FhQbbvYh1FvbiaPf43Ve2)。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9be300b868984466be7d94084a486b50~tplv-k3u1fbpfcp-zoom-1.image)

Codex JavaScript沙盒

这个示例应用程序使用Codex将自然语言指令转换为JavaScript。

[点击尝试](https://platform.openai.com/codex-javascript-sandbox) 　　[观看演示](https://www.youtube.com/watch?v=Zm9B-DvwOgw&list=PLOXw6I10VTv_FhQbbvYh1FvbiaPf43Ve2&index=2)

## 快速入门

这里有一些使用Codex的例子，可以在试验场上测试。

**说“Hello”(Python)**

```python
"""
询问用户的名字并说“Hello”
"""
```

**创建随机名字(Python)**

```python
"""
1. 制作一个名字列表
2. 创建一个姓氏列表
3.将它们随机组合成包含100个全名的列表
"""
```

**创建MySQL查询(Python)**

```python
"""
客户表，列= [客户Id，姓名，姓氏，公司，地址，城市，州，国家，邮政编码，电话，传真，电子邮件，SupportRepId]
为德克萨斯州所有名为Jane的客户创建一个MySQL查询
"""
查询=
```

**解释代码(JavaScript)**

```JavaScript
// Function 1
var fullNames = [];
for (var i = 0; i < 50; i++) {
  fullNames.push(names[Math.floor(Math.random() * names.length)]
    + " " + lastNames[Math.floor(Math.random() * lastNames.length)]);
}

// Function 1做了什么工作?
```

*更多的例子*

访问我们的[示例库](https://platform.openai.com/examples?category=code)，探索为Codex设计的更多提示。

## 最佳实践

从注释、数据或代码开始。你可以在我们的试验场上尝试使用Codex模型之一(在需要时将指令标记为注释)。

为了让Codex创造一个有用的completion，我们需要思考程序员在执行任务时需要哪些信息。这可能只是一个清晰的注释或编写有用函数所需的数据，如变量名或函数处理的类。

```
创建一个名为“nameImporter”的函数，将名字和姓氏添加到数据库中
```

这种方法甚至可以扩展到为Codex提供注释和数据库模式示例，让它为各种数据库编写有用的查询请求。

```
# Table albums, columns = [AlbumId, Title, ArtistId]
# Table artists, columns = [ArtistId, Name]
# Table media_types, columns = [MediaTypeId, Name]
# Table playlists, columns = [PlaylistId, Name]
# Table playlist_track, columns = [PlaylistId, TrackId]
# Table tracks, columns = [TrackId, Name, AlbumId, MediaTypeId, GenreId, Composer, Milliseconds, Bytes, UnitPrice]

# 创建一个查询，查询Adele的所有专辑
```

当你向Codex展示数据库模式时，它能够对如何格式化查询做出有根据的猜测。

**指定语言。** Codex能理解几十种不同的编程语言。其中很多都有类似的注释、函数和其他编程语法约定。通过在注释中指定语言和版本，Codex能够更好地为你提供所需的内容。也就是说，Codex在风格和语法上相当灵活。

```
# R language
# 计算一组点之间的平均距离
```

```
# Python 3
# 计算一组点之间的平均距离
```

提示Codex你想让它做什么。如果你想让Codex创建一个网页，在HTML文档中放置第一行代码(```<!DOCTYPE html>```)之后告诉Codex接下来应该做什么。同样的方法也适用于从注释创建函数(注释后面以```func```或```def```开头的新行)。

```
<!-- 创建一个网页，标题为'Kat Katman attorney at paw' -->
<!DOCTYPE html>
```

将```<!DOCTYPE html>```放在我们的注释之后，会让Codex很清楚我们想要它做什么。

```python
# 创建一个从1数到100的函数
def counter
```

如果我们开始编写函数，Codex就会明白接下来需要做什么。

**指定库可以帮助Codex理解你想要什么。** Codex了解大量的库、API和模块。通过告诉Codex使用哪些，无论是从注释中还是将它们导入到代码中，Codex将根据它们而不是替代方案提出建议。

```
<!-- Use A-Frame version 1.2.0 to create a 3D website -->
<!-- https://aframe.io/releases/1.2.0/aframe.min.js -->
```

通过指定版本，可以确保Codex使用最新的库。

注意:Codex可以推荐有用的库和API，但一定要自己做调研，以确保它们对你的应用程序是安全的。

**注释风格会影响代码质量。** 在某些语言中，注释的风格可以提高输出的质量。例如，在使用Python时，在某些情况下，使用文档字符串(用三引号括起来的注释)比使用磅符号(#)可以得到更高质量的结果。

```python
"""
创建一个包含用户和电子邮件地址的数组
"""
```

**在函数中添加注释可能会很有帮助。** 推荐的编码标准通常建议将函数的描述放在函数内部。使用这种格式可以帮助Codex更清楚地了解你希望函数做什么。

```python
def getUserBalance(id):
    """
    在数据库` UserData `中查找用户并返回他们的当前账户余额。
    """
```

**提供示例以获得更精确的结果。** 如果你需要Codex使用特定的风格或格式，在请求的第一部分提供示例或演示，将有助于Codex更准确地匹配你的需求。

```python
"""
    创建一个随机动物和物种的列表
"""
animals  = [ {"name": "Chomper", "species": "Hamster"}, {"name":
```

**temperature越低，结果越精确。** 在大多数情况下，将API的temperature设置为0或接近0(如0.1或0.2)往往会得到更好的结果。与GPT-3中较高的temperature可以提供有用的创造性和随机的结果不同，Codex的较高temperature可能会给你真正随机或不稳定的响应。

如果你需要Codex提供不同的潜在结果，可以从0开始，然后每次增加0.1，直到找到合适的变化。

**将任务组织成函数。** 我们可以通过在注释中尽可能精确地指定函数应该做什么来让Codex编写函数。通过编写下面的注释，Codex创建了一个Javascript定时器函数，当用户按下按钮时触发:

一个简单的JavaScript计时器

```
//创建一个定时器，它会在10秒内触发alert
```

我们可以使用Codex和知名库一起执行常见任务，例如使用Stripe API创建客户:

在Python中创建一个Stripe客户

```
#从电子邮件地址创建Stripe客户
```

**创建示例数据。** 测试应用程序通常需要使用示例数据。因为Codgen是一个可以理解和编写自然语言的语言模型，你可以让Codex创建由虚构的名称、产品和其他变量组成的数组之类的数据。

```
/*创建一个San Francisco的天气温度数组*/
```

让Codex执行此任务，将会生成如下表:

```javascript
var weather = [
  { month: 'January', high: 58, low: 48 },
  { month: 'February', high: 61, low: 50 },
  { month: 'March', high: 64, low: 53 },
  { month: 'April', high: 67, low: 55 },
  { month: 'May', high: 70, low: 58 },
  { month: 'June', high: 73, low: 61 },
  { month: 'July', high: 76, low: 63 },
  { month: 'August', high: 77, low: 64 },
  { month: 'September', high: 76, low: 63 },
  { month: 'October', high: 73, low: 61 },
  { month: 'November', high: 68, low: 57 },
  { month: 'December', high: 64, low: 54 }
];
```

**复合功能和小应用。** 我们可以为Codex提供一个包含复杂请求的注释，如创建随机名称生成器或执行用户输入的任务，Codex可以生成其余内容，只要有足够的token。

```
/ *
创建一个动物列表
创建一个城市列表
使用列表生成关于我在每个城市的动物园所看到的故事
* /
```

**限制completion大小以获得更精确的结果或更低的延迟。** 在Codex中要求较长的completion会导致不准确的答案和重复。通过减少```max_tokens```和设置```stop``` tokens来限制查询的大小。例如，添加```\n```作为```stop```序列，将completion限制为一行代码。较小的completion也会带来更少的延迟。

**使用streaming来减少延迟。** 大型Codex查询可能需要数十秒才能完成。为了构建较低延迟的应用程序，例如执行自动补全的编码助手，可以考虑使用streaming。响应将在模型生成整个completion之前返回。只需要部分completion的应用程序可以通过编程方式切断completion或使用自定义的```stop```参数来减少延迟。

用户可以将streaming与复制结合起来，通过从API请求多个解决方案并使用返回的第一个响应来减少延迟。通过设置```n > 1```。这种方法会消耗更多的token配额，所以要谨慎使用(例如，使用合理的```max_tokens```和```stop```设置)。

**使用Codex解释代码。** Codex创建和理解代码的能力允许我们使用它来执行任务，例如解释文件中的代码是做什么的。实现这一点的一种方法是在函数后面加上以“this function”或“this application is”开头的注释。Codex通常将此理解为解释的开始，并完成其余的文本。

```python
/* 解释一下前面的函数在做什么:它...
```

**解释SQL查询。** 在这个例子中，我们使用Codex以人类可读的格式解释SQL查询的作用。

```sql
SELECT DISTINCT department.name
FROM department
JOIN employee ON department.id = employee.department_id
JOIN salary_payments ON employee.id = salary_payments.employee_id
WHERE salary_payments.date BETWEEN '2020-06-01' AND '2020-06-30'
GROUP BY department.name
HAVING COUNT(employee.id) > 10;
-- 以人类可读的格式解释上述查询
--
```

**编写单元测试。** 在Python中创建单元测试只需添加注释“unit test”并启动一个函数即可。

```python
# Python 3
def sum_numbers(a, b):
  return a + b

# 单元测试
def
```

**检查代码是否有错误。** 通过举例，你可以向Codex展示如何识别代码中的错误。在某些情况下，不需要示例，但是展示级别和细节以提供描述可以帮助 Codex 了解寻找什么和如何解释它。(由Codex检查错误不应取代用户的仔细审查。)

```
/* 解释为什么前面的函数不能正常运行。 */
```

**使用源数据编写数据库函数。** 就像程序员会从理解数据库结构和列名中获益一样，Codex可以使用这些数据来帮助你编写准确的查询请求。在这个例子中，我们插入数据库的模式，并告诉Codex想要从数据库查询的内容。

```
# Table albums, columns = [AlbumId, Title, ArtistId]
# Table artists, columns = [ArtistId, Name]
# Table media_types, columns = [MediaTypeId, Name]
# Table playlists, columns = [PlaylistId, Name]
# Table playlist_track, columns = [PlaylistId, TrackId]
# Table tracks, columns = [TrackId, Name, AlbumId, MediaTypeId, GenreId, Composer, Milliseconds, Bytes, UnitPrice]

# 创建一个查询，查询Adele的所有专辑
```

**语言之间的转换。** 要让Codex从一种语言转换为另一种语言，可以遵循一种简单的格式，即在注释中列出要转换的代码的语言，然后是代码，最后是注释，注释中包含要翻译成的语言。

```
# Convert this from Python to R
# Python version

[ Python code ]

# End

# R version
```

**为库或框架重写代码。** 如果你希望Codex使一个函数更高效，可以为它提供要重写的代码，并提供使用什么格式的说明。

```javascript
// Rewrite this as a React component
var input = document.createElement('input');
input.setAttribute('type', 'text');
document.body.appendChild(input);
var button = document.createElement('button');
button.innerHTML = 'Say Hello';
document.body.appendChild(button);
button.onclick = function() {
  var name = input.value;
  var hello = document.createElement('div');
  hello.innerHTML = 'Hello ' + name;
  document.body.appendChild(hello);
};

// React version:
```

## 插入代码 ```Beta```

通过提供[前缀prompt](https://platform.openai.com/docs/api-reference/completions/create#completions/create-prompt)与后缀prompt，completions端点还支持在代码中插入代码。这可以用于在函数或文件的中间插入一个completion。

```python
def get_largest_prime_factor(n):

if n < 2:

return False

def i"""s_prime(n): > for i in range(2, n): > if n % i == 0: > return False > return True""" > largest = 1

for j in range(2, n + 1):

if n % j == 0 and is_prime(j):

return largest
```

通过为模型提供额外的上下文，它可以更加可控。然而，对于模型来说，这是一个更受限和更具挑战性的任务。

### 最佳实践

插入代码是测试版的新功能，为了获得更好的效果，你可能需要修改使用API的方式。以下是一些最佳实践。

**使用max_tokens > 256。** 该模型更擅长插入较长的completion。max_tokens太小，模型可能在能够连接到后缀之前被切断。请注意，即使使用较大的max_tokens，也只会对生成的token数量收费。

**建议finish_reason == "stop"。** 当模型到达自然停止点或用户提供的停止序列时，它将finish_reason设置为“stop”。这表明模型已经成功地连接到后缀，并且对completion质量是一个好的信号。这与使用n > 1或重新采样(参见下一点)时在几个completion之间进行选择尤其相关。

**重采样 3-5 次。** 虽然几乎所有的补全都能连接到前缀,但在更难的情况下,模型可能难以连接后缀。我们发现,在这种情况下,重采样 3 次或 5 次(或设置k=3,5的情况下使用 best_of 参数),并选择带有 "stop" 的样本作为它们的 finish_reason,可能是一种有效的方法。在重采样时,通常需要更高的temperature来增加多样性。

注意:如果所有返回的样本都有finish_reason == "length"，很可能是因为max_tokens太小，模型在设法自然地连接提示符和后缀之前就用完了token。考虑在重新采样之前增加max_tokens。

## 编辑code ```Beta```

[edits](https://platform.openai.com/docs/api-reference/edits)端点可用于编辑代码，而不仅仅是完成代码。你提供一些代码和如何修改它的说明，```code-davincian-edit-001```模型将尝试相应地编辑它。这是一个重构和调整代码的自然界面。在最初的测试期间，edits端点的使用是免费的。

### 例子

迭代地构建程序

编写代码通常是一个迭代的过程，需要在编写代码的过程中不断完善文本。Editing使它自然地不断细化模型的输出，直到最终结果变得优雅。在这个例子中，我们使用斐波那契数列作为一个例子来演示如何迭代地构建代码。

写一个函数

输入

```
No input
```

指令

```
用python编写一个计算斐波那契数列的函数
```

输出

```python
'def fibonacci(num):
    a = 0
    b = 1
    if num ==1:
       print(a)
    else:
        print(a)
        print(b)
        #the sequence starts with 0,1
        for i in range(2,num):
            c = a+b
            a = b
            b = c
            print(c)

fibonacci(10)'
```

重构函数

输入

```python
 if num ==1:
        print(a)
    else:
        print(a)
        print(b)
        #the sequence starts with 0,1
        for i in range(2,num):
            c = a+b
            a = b
            b = c
            print(c)

fibonacci(10)
```

指令

```
将这个函数重构为递归函数
```

输出

```python
'def fibonacci(num):
    if num <= 1:
        return num
    else:
        return fib(num-1) + fib(num-2)
print(fibonacci(10))'
```

重命名函数

输入

```python
def fibonacci(num):
    if num <= 1:
        return num
    else:
        return fib(num-1) + fib(num-2)
print(fibonacci(10))
```

指令

```
将函数重命名为fib
```

输出

```python
def 'fib'(num):
    if num <= 1:
          return num
      else:
        return 'fib'(num-1) + 'fib'(num-2)
print('fib'(10))
```

添加文档

输入

```python
def fib(num):
    if num <= 1:
        return num
    else:
        return fib(num-1) + fib(num-2)
print(fib(10))
```

指令

```
为函数添加文档
```

输出

```python
def fib(num):
    """ Print the nth number in the fibonacci sequence """
    if num <= 1:
          return num
      else:
          return fib(num-1) + fib(num-2)
  print(fib(10))
```

### 最佳实践

[edits](https://platform.openai.com/docs/api-reference/edits)端点仍处于alpha阶段，我们建议遵循这些最佳实践。

1.  考虑使用一个空prompt!在这种情况下，editing功能可以类似于completion功能。

2.  说明要尽可能具体。

3.  有时，模型无法找到解决方案，从而导致错误。我们建议重新措辞你的指令或输入。