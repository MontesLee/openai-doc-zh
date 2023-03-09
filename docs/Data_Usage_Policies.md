# API数据使用政策

2023年3月1日更新

```
从2023年3月1日起，我们将对数据使用和保留政策进行两项更改:

OpenAI不会使用客户通过我们的API提交的数据来训练或改进我们的模型，
除非您明确决定为此目的与我们共享您的数据。您可以选择加入共享数据。

通过API发送的任何数据将被保留最多30天，用于滥用和误用监测，
超过30天将被删除(除非法律另有规定)。
```

OpenAI API处理用户的prompts和completions，以及通过Files终端提交给微调模型的训练数据。我们把这些数据称为API数据。

默认情况下，OpenAI不会使用客户通过我们的API提交的数据来训练OpenAI模型或改进OpenAI的服务。用户提交用于微调的数据将仅用于微调客户的模型。然而，OpenAI将允许用户选择分享他们的数据，以[提高模型的性能](https://help.openai.com/en/articles/5722486-how-your-data-is-used-to-improve-model-performance)。[共享您的数据](https://docs.google.com/forms/d/e/1FAIpQLSevgtKyiSWIOj6CV6XWBHl1daPZSOcIWzcUYUXQ1xttjBgDpA/viewform)将确保模型的未来迭代可根据您的用例进行改进。如果客户之前没有选择不共享数据，那么在2023年3月1日(此更改的生效日期)之前提交给API的数据可能已用于改进。

OpenAI将API数据保留30天，用于监控滥用和误用。数量有限的授权OpenAI员工，以及受保密和安全义务约束的专业第三方承包商，可以访问这些数据，仅用于调查和核实涉嫌滥用。企业客户部署滥用风险较低的用例时，可以要求完全不存储 API 数据，包括安全监测和预防。当 OpenAI 怀疑数据包含滥用平台的内容时，可能仍然会使内容分类器标记。用户通过Files端点提交的数据(例如用于微调模型的数据)将被保留，直到用户删除该文件。

请注意，此数据政策不适用于OpenAI的Non-API消费者服务，如[ChatGPT](https://chat.openai.com/)或[DALL·E Labs](https://labs.openai.com/)。您可以在我们的[消费者服务数据使用常见问题解答](https://help.openai.com/en/articles/7039943-data-usage-for-consumer-services-faq)中了解更多有关这些政策的信息。

## 常见问题

**OpenAI对公共API有哪些技术保护和安全认证?**

OpenAI符合[SOC 2 Type 1](https://us.aicpa.org/interestareas/frc/assuranceadvisoryservices/serviceorganization-smanagement.html)标准，并已由独立第三方审计机构根据[2017年信任服务安全标准](https://us.aicpa.org/content/dam/aicpa/interestareas/frc/assuranceadvisoryservices/downloadabledocuments/trust-services-criteria.pdf)进行审计。

**API数据存储在哪里?**

内容存储在OpenAI系统和我们的子处理系统上。我们也可能会将部分脱敏内容发送给第三方承包商(受保密和安全义务的约束)以达到安全目的。我们的30天数据保留政策也适用于我们的子处理系统和承包商。您可以查看我们的[子处理系统列表及其位置的详细信息](https://platform.openai.com/subprocessors)。

**当我调用API时，数据在传输过程中是否加密?**

OpenAI API仅在Transport Layer Security(TLS)上可用，因此客户对OpenAI的请求和响应是加密的。

**你们在欧洲有数据中心吗?**

所有客户数据都在美国处理和存储。我们目前没有在欧洲或其他国家存储数据。

**我可以在 HIPAA 工作负载中使用这个 API 吗？**

我们可以签署商业伙伴协议，以支持客户遵守《健康保险可移植性与责任法案》（HIPAA）。要符合要求，您必须拥有与 OpenAI 的企业协议以及符合条件的使用案例。如果您感兴趣，请[联系我们的销售团队](https://openai.com/contact-sales)。

**我收到了一个数据删除请求，我如何让OpenAI删除数据?**

我们只将通过API发送的数据最多保留30天，用于滥用和监控目的。如果您想在此之前删除您的帐户，请遵循[这些步骤](https://help.openai.com/en/articles/6378407-how-can-i-delete-my-account)。

**OpenAI有数据处理附录(DPA)吗?**

是的。请填写我们的[DPA表格](https://ironcladapp.com/public-launch/63ffefa2bed6885f4536d0fe)以执行我们的数据处理附录。

**我们可以私有化部署吗?**

我们不提供内部托管服务。您可以通过与我们的[销售团队](https://openai.com/contact-sales)联系来购买专用容量。