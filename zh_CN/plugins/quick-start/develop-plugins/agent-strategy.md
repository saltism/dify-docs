# Agent 策略插件

Agent 策略插件能够帮助 LLM 执行推理或决策逻辑，包括工具选择、调用和结果处理，以更加自动化的方式处理问题。

本文将演示如何创建一个具备工具调用（Function Calling）能力，自动获取当前准确时间的插件。

### 前置准备

* Dify 插件脚手架工具
* Python 环境，版本号 ≥ 3.12

关于如何准备插件开发的脚手架工具，详细说明请参考[初始化开发工具](initialize-development-tools.md)。

{% hint style="info" %}
**Tips**：在终端运行 `dify version` 命令，检查是否出现版本号以确认成功安装脚手架工具。
{% endhint %}

### ;1. 初始化插件模板

运行以下命令，初始化 Agent 插件开发模板。

```
dify plugin init
```

按照页面提示，填写对应信息。参考以下代码中的备注信息，进行设置。

```
➜  Dify Plugins Developing dify plugin init
Edit profile of the plugin
Plugin name (press Enter to next step): # 填写插件的名称
Author (press Enter to next step): Author name # 填写插件作者
Description (press Enter to next step): Description # 填写插件的描述
---
Select the language you want to use for plugin development, and press Enter to con
BTW, you need Python 3.12+ to develop the Plugin if you choose Python.
-> python # 选择 Python 环境
  go (not supported yet)
---
Based on the ability you want to extend, we have divided the Plugin into four type

- Tool: It's a tool provider, but not only limited to tools, you can implement an 
- Model: Just a model provider, extending others is not allowed.
- Extension: Other times, you may only need a simple http service to extend the fu
- Agent Strategy: Implement your own logics here, just by focusing on Agent itself

What's more, we have provided the template for you, you can choose one of them b
  tool
-> agent-strategy # 选择 Agent 策略模板
  llm
  text-embedding
---
Configure the permissions of the plugin, use up and down to navigate, tab to sel
Backwards Invocation:
Tools:
    Enabled: [✔]  You can invoke tools inside Dify if it's enabled # 默认开启
Models:
    Enabled: [✔]  You can invoke models inside Dify if it's enabled # 默认开启
    LLM: [✔]  You can invoke LLM models inside Dify if it's enabled # 默认开启
    Text Embedding: [✘]  You can invoke text embedding models inside Dify if it'
    Rerank: [✘]  You can invoke rerank models inside Dify if it's enabled
...
```

初始化插件模板后将生成一个代码文件夹，包含插件开发过程中所需的完整资源。熟悉 Agent 策略插件的整体代码结构有助于插件的开发过程。

```
├── GUIDE.md               # User guide and documentation
├── PRIVACY.md            # Privacy policy and data handling guidelines
├── README.md             # Project overview and setup instructions
├── _assets/             # Static assets directory
│   └── icon.svg         # Agent strategy provider icon/logo
├── main.py              # Main application entry point
├── manifest.yaml        # Basic plugin configuration
├── provider/           # Provider configurations directory
│   └── basic_agent.yaml # Your agent provider settings
├── requirements.txt    # Python dependencies list
└── strategies/         # Strategy implementation directory
    ├── basic_agent.py  # Basic agent strategy implementation
    └── basic_agent.yaml # Basic agent strategy configuration
```

插件的功能代码集中在 `strategies/` 目录内。

### 2. 开发插件功能

Agent 策略插件的开发主要围绕以下两个文件展开：

* 插件声明文件：`strategies/basic_agent.yaml`
* 插件功能代码：`strategies/basic_agent.py`

#### 2.1 定义参数

要创建一个 Agent 插件，首先需要在 `strategies/basic_agent.yaml` 文件中定义插件所需的参数。这些参数决定了插件的核心功能，例如调用 LLM 模型和使用工具的能力。

建议优先配置以下四个基础参数：

1\. **model**：指定要调用的大语言模型（LLM），如 GPT-4、GPT-4o-mini 等。

2\. **tools**：定义插件可以使用的工具列表，增强插件功能。

3\. **query**：设置与模型交互的提示词或输入内容。

4\. **maximum\_iterations**：限制插件执行的最大迭代次数，避免过度计算。

```yaml
identity:
  name: basic_agent # the name of the agent_strategy
  author: novice # the author of the agent_strategy
  label:
    en_US: BasicAgent # the engilish label of the agent_strategy
description:
  en_US: BasicAgent # the english description of the agent_strategy
parameters:
  - name: model # the name of the model parameter
    type: model-selector # model-type
    scope: tool-call&llm # the scope of the parameter
    required: true
    label:
      en_US: Model
      zh_Hans: 模型
      pt_BR: Model
  - name: tools # the name of the tools parameter
    type: array[tools] # the type of tool parameter
    required: true
    label:
      en_US: Tools list
      zh_Hans: 工具列表
      pt_BR: Tools list
  - name: query # the name of the query parameter
    type: string # the type of query parameter
    required: true
    label:
      en_US: Query
      zh_Hans: 查询
      pt_BR: Query
  - name: maximum_iterations
    type: number
    required: false
    default: 5
    label:
      en_US: Maxium Iterations
      zh_Hans: 最大迭代次数
      pt_BR: Maxium Iterations
    max: 50 # if you set the max and min value, the display of the parameter will be a slider
    min: 1
extra:
  python:
    source: strategies/basic_agent.py

```

完成参数配置后，插件将在自动生成相应的设置的使用页面，方便你进行直观、便捷的调整和使用。

![Agent 策略插件的使用页面](https://assets-docs.dify.ai/2025/01/d011e2eba4c37f07a9564067ba787df8.png)

#### 2.2 获取参数并执行

当使用者在插件的使用页面完成基础的信息填写后，插件需要处理已填写的传入参数。因此需要先在 `strategies/basic_agent.py` 文件内定义 Agent 参数类供后续使用。

校验传入参数：

```python
from dify_plugin.entities.agent import AgentInvokeMessage
from dify_plugin.interfaces.agent import AgentModelConfig, AgentStrategy, ToolEntity
from pydantic import BaseModel

class BasicParams(BaseModel):
    maximum_iterations: int
    model: AgentModelConfig
    tools: list[ToolEntity]
    query: str

```

获取参数后，执行具体的业务逻辑：

```python
class BasicAgentAgentStrategy(AgentStrategy):
    def _invoke(self, parameters: dict[str, Any]) -> Generator[AgentInvokeMessage]:
        params = BasicParams(**parameters)
```

#### 3. 调用模型

在 Agent 策略插件中，**调用模型**是核心执行逻辑之一。可以通过 SDK 提供的 `session.model.llm.invoke()` 方法高效地调用 LLM 模型，实现文本生成、对话处理等功能。

如果希望模型具备**调用工具**的能力，首先需要确保模型能够输出符合工具调用格式的输入参数。也就是说，模型需要根据用户指令生成符合工具接口要求的参数。

构造以下参数：

* model：模型信息
* prompt\_messages：提示词
* tools：工具信息（Function Calling 相关）
* stop：停止符
* stream：是否支持流式输出

方法定义示例代码：

```python
def invoke(
        self,
        model_config: LLMModelConfig,
        prompt_messages: list[PromptMessage],
        tools: list[PromptMessageTool] | None = None,
        stop: list[str] | None = None,
        stream: bool = True,
    ) -> Generator[LLMResultChunk, None, None] | LLMResult:...
```

要查看完整的功能实现，请参考模型调用[示例代码](agent-strategy.md#diao-yong-gong-ju-1)。

该代码实现了以下功能：用户输入指令后，Agent 策略插件会自动调用 LLM，根据生成结果构建并传递工具调用所需的参数，使模型能够灵活调度已接入的工具，高效完成复杂任务。

![生成工具的请求参数](https://assets-docs.dify.ai/2025/01/01e32c2d77150213c7c929b3cceb4dae.png)

#### 4. 调用工具

填写工具参数后，需赋予 Agent 策略插件实际调用工具的能力。可以通过 SDK 中的`session.tool.invoke()` 函数进行工具调用。

构造以下参数：

* provider：工具提供商
* tool\_name：工具名称
* parameters：输入参数

方法定义示例代码：

```python
 def invoke(
        self,
        provider_type: ToolProviderType,
        provider: str,
        tool_name: str,
        parameters: dict[str, Any],
    ) -> Generator[ToolInvokeMessage, None, None]:...
```

若希望通过 LLM 直接生成参数完成工具调用，请参考以下工具调用的示例代码：

```python
tool_instances = (
    {tool.identity.name: tool for tool in params.tools} if params.tools else {}
)
for tool_call_id, tool_call_name, tool_call_args in tool_calls:
    tool_instance = tool_instances[tool_call_name]
    self.session.tool.invoke(
        provider_type=ToolProviderType.BUILT_IN,
        provider=tool_instance.identity.provider,
        tool_name=tool_instance.identity.name,
        parameters={**tool_instance.runtime_parameters, **tool_call_args},
    )
```

如需查看完整的功能代码，请阅读调用工具[示例代码](agent-strategy.md#diao-yong-gong-ju-1)。

实现这部分的功能代码后，Agent 策略插件将具备自动 Function Calling 的能力，例如自动获取当前时间：

![工具调用](https://assets-docs.dify.ai/2025/01/80e5de8acc2b0ed00524e490fd611ff5.png)

#### 5. 日志创建

在 **Agent 策略插件**中，通常需要执行多轮操作才能完成复杂任务。记录每轮操作的执行结果对于开发者来说非常重要，有助于追踪 Agent 的执行过程、分析每一步的决策依据，从而更好地评估和优化策略效果。

为了实现这一功能，可以利用 SDK 中的 `create_log_message` 和 `finish_log_message` 方法记录日志。这种方式不仅可以在模型调用前后实时记录操作状态，还能帮助开发者快速定位问题。

场景示例：

* 在模型调用之前，记录一条“开始调用模型”的日志，帮助开发者明确任务执行进度。
* 在模型调用成功后，记录一条“调用成功”的日志，方便追踪模型响应的完整性。

```python
model_log = self.create_log_message(
            label=f"{params.model.model} Thought",
            data={},
            metadata={"start_at": model_started_at, "provider": params.model.provider},
            status=ToolInvokeMessage.LogMessage.LogStatus.START,
        )
yield model_log
self.session.model.llm.invoke(...)
yield self.finish_log_message(
    log=model_log,
    data={
        "output": response,
        "tool_name": tool_call_names,
        "tool_input": tool_call_inputs,
    },
    metadata={
        "started_at": model_started_at,
        "finished_at": time.perf_counter(),
        "elapsed_time": time.perf_counter() - model_started_at,
        "provider": params.model.provider,
    },
)
```

设置完成后，工作流日志将输出执行结果：

![Agent 输出执行结果](https://assets-docs.dify.ai/2025/01/96516388a4fb1da9cea85fc1804ff377.png)

在 Agent 执行的过程中，有可能会产生多轮日志。若日志能具备层级结构将有助于开发者查看。通过在日志记录时传入 parent 参数，不同轮次的日志可以形成上下级关系，使日志展示更加清晰、易于追踪。

**引用方法：**

```python
function_call_round_log = self.create_log_message(
    label="Function Call Round1 ",
    data={},
    metadata={},
)
yield function_call_round_log

model_log = self.create_log_message(
    label=f"{params.model.model} Thought",
    data={},
    metadata={"start_at": model_started_at, "provider": params.model.provider},
    status=ToolInvokeMessage.LogMessage.LogStatus.START,
    # add parent log
    parent=function_call_round_log,
)
yield model_log
```

插件功能示例代码：

{% tabs %}
{% tab title="调用模型" %}
#### 调用模型

以下代码将演示如何赋予 Agent 策略插件调用模型的能力：

```python
import json
from collections.abc import Generator
from typing import Any, cast

from dify_plugin.entities.agent import AgentInvokeMessage
from dify_plugin.entities.model.llm import LLMModelConfig, LLMResult, LLMResultChunk
from dify_plugin.entities.model.message import (
    PromptMessageTool,
    UserPromptMessage,
)
from dify_plugin.entities.tool import ToolInvokeMessage, ToolParameter, ToolProviderType
from dify_plugin.interfaces.agent import AgentModelConfig, AgentStrategy, ToolEntity
from pydantic import BaseModel

class BasicParams(BaseModel):
    maximum_iterations: int
    model: AgentModelConfig
    tools: list[ToolEntity]
    query: str

class BasicAgentAgentStrategy(AgentStrategy):
    def _invoke(self, parameters: dict[str, Any]) -> Generator[AgentInvokeMessage]:
        params = BasicParams(**parameters)
        chunks: Generator[LLMResultChunk, None, None] | LLMResult = (
            self.session.model.llm.invoke(
                model_config=LLMModelConfig(**params.model.model_dump(mode="json")),
                prompt_messages=[UserPromptMessage(content=params.query)],
                tools=[
                    self._convert_tool_to_prompt_message_tool(tool)
                    for tool in params.tools
                ],
                stop=params.model.completion_params.get("stop", [])
                if params.model.completion_params
                else [],
                stream=True,
            )
        )
        response = ""
        tool_calls = []
        tool_instances = (
            {tool.identity.name: tool for tool in params.tools} if params.tools else {}
        )

        for chunk in chunks:
            # check if there is any tool call
            if self.check_tool_calls(chunk):
                tool_calls = self.extract_tool_calls(chunk)
                tool_call_names = ";".join([tool_call[1] for tool_call in tool_calls])
                try:
                    tool_call_inputs = json.dumps(
                        {tool_call[1]: tool_call[2] for tool_call in tool_calls},
                        ensure_ascii=False,
                    )
                except json.JSONDecodeError:
                    # ensure ascii to avoid encoding error
                    tool_call_inputs = json.dumps(
                        {tool_call[1]: tool_call[2] for tool_call in tool_calls}
                    )
                print(tool_call_names, tool_call_inputs)
            if chunk.delta.message and chunk.delta.message.content:
                if isinstance(chunk.delta.message.content, list):
                    for content in chunk.delta.message.content:
                        response += content.data
                        print(content.data, end="", flush=True)
                else:
                    response += str(chunk.delta.message.content)
                    print(str(chunk.delta.message.content), end="", flush=True)

            if chunk.delta.usage:
                # usage of the model
                usage = chunk.delta.usage

        yield self.create_text_message(
            text=f"{response or json.dumps(tool_calls, ensure_ascii=False)}\n"
        )
        result = ""
        for tool_call_id, tool_call_name, tool_call_args in tool_calls:
            tool_instance = tool_instances[tool_call_name]
            tool_invoke_responses = self.session.tool.invoke(
                provider_type=ToolProviderType.BUILT_IN,
                provider=tool_instance.identity.provider,
                tool_name=tool_instance.identity.name,
                parameters={**tool_instance.runtime_parameters, **tool_call_args},
            )
            if not tool_instance:
                tool_invoke_responses = {
                    "tool_call_id": tool_call_id,
                    "tool_call_name": tool_call_name,
                    "tool_response": f"there is not a tool named {tool_call_name}",
                }
            else:
                # invoke tool
                tool_invoke_responses = self.session.tool.invoke(
                    provider_type=ToolProviderType.BUILT_IN,
                    provider=tool_instance.identity.provider,
                    tool_name=tool_instance.identity.name,
                    parameters={**tool_instance.runtime_parameters, **tool_call_args},
                )
                result = ""
                for tool_invoke_response in tool_invoke_responses:
                    if tool_invoke_response.type == ToolInvokeMessage.MessageType.TEXT:
                        result += cast(
                            ToolInvokeMessage.TextMessage, tool_invoke_response.message
                        ).text
                    elif (
                        tool_invoke_response.type == ToolInvokeMessage.MessageType.LINK
                    ):
                        result += (
                            f"result link: {cast(ToolInvokeMessage.TextMessage, tool_invoke_response.message).text}."
                            + " please tell user to check it."
                        )
                    elif tool_invoke_response.type in {
                        ToolInvokeMessage.MessageType.IMAGE_LINK,
                        ToolInvokeMessage.MessageType.IMAGE,
                    }:
                        result += (
                            "image has been created and sent to user already, "
                            + "you do not need to create it, just tell the user to check it now."
                        )
                    elif (
                        tool_invoke_response.type == ToolInvokeMessage.MessageType.JSON
                    ):
                        text = json.dumps(
                            cast(
                                ToolInvokeMessage.JsonMessage,
                                tool_invoke_response.message,
                            ).json_object,
                            ensure_ascii=False,
                        )
                        result += f"tool response: {text}."
                    else:
                        result += f"tool response: {tool_invoke_response.message!r}."

                tool_response = {
                    "tool_call_id": tool_call_id,
                    "tool_call_name": tool_call_name,
                    "tool_response": result,
                }
        yield self.create_text_message(result)

    def _convert_tool_to_prompt_message_tool(
        self, tool: ToolEntity
    ) -> PromptMessageTool:
        """
        convert tool to prompt message tool
        """
        message_tool = PromptMessageTool(
            name=tool.identity.name,
            description=tool.description.llm if tool.description else "",
            parameters={
                "type": "object",
                "properties": {},
                "required": [],
            },
        )

        parameters = tool.parameters
        for parameter in parameters:
            if parameter.form != ToolParameter.ToolParameterForm.LLM:
                continue

            parameter_type = parameter.type
            if parameter.type in {
                ToolParameter.ToolParameterType.FILE,
                ToolParameter.ToolParameterType.FILES,
            }:
                continue
            enum = []
            if parameter.type == ToolParameter.ToolParameterType.SELECT:
                enum = (
                    [option.value for option in parameter.options]
                    if parameter.options
                    else []
                )

            message_tool.parameters["properties"][parameter.name] = {
                "type": parameter_type,
                "description": parameter.llm_description or "",
            }

            if len(enum) > 0:
                message_tool.parameters["properties"][parameter.name]["enum"] = enum

            if parameter.required:
                message_tool.parameters["required"].append(parameter.name)

        return message_tool

    def check_tool_calls(self, llm_result_chunk: LLMResultChunk) -> bool:
        """
        Check if there is any tool call in llm result chunk
        """
        return bool(llm_result_chunk.delta.message.tool_calls)

    def extract_tool_calls(
        self, llm_result_chunk: LLMResultChunk
    ) -> list[tuple[str, str, dict[str, Any]]]:
        """
        Extract tool calls from llm result chunk

        Returns:
            List[Tuple[str, str, Dict[str, Any]]]: [(tool_call_id, tool_call_name, tool_call_args)]
        """
        tool_calls = []
        for prompt_message in llm_result_chunk.delta.message.tool_calls:
            args = {}
            if prompt_message.function.arguments != "":
                args = json.loads(prompt_message.function.arguments)

            tool_calls.append(
                (
                    prompt_message.id,
                    prompt_message.function.name,
                    args,
                )
            )

        return tool_calls
```
{% endtab %}

{% tab title="调用工具" %}
#### 调用工具

以下代码展示了如何为 Agent 策略插件实现模型调用并向工具发送规范化请求。

```python
import json
from collections.abc import Generator
from typing import Any, cast

from dify_plugin.entities.agent import AgentInvokeMessage
from dify_plugin.entities.model.llm import LLMModelConfig, LLMResult, LLMResultChunk
from dify_plugin.entities.model.message import (
    PromptMessageTool,
    UserPromptMessage,
)
from dify_plugin.entities.tool import ToolInvokeMessage, ToolParameter, ToolProviderType
from dify_plugin.interfaces.agent import AgentModelConfig, AgentStrategy, ToolEntity
from pydantic import BaseModel

class BasicParams(BaseModel):
    maximum_iterations: int
    model: AgentModelConfig
    tools: list[ToolEntity]
    query: str

class BasicAgentAgentStrategy(AgentStrategy):
    def _invoke(self, parameters: dict[str, Any]) -> Generator[AgentInvokeMessage]:
        params = BasicParams(**parameters)
        chunks: Generator[LLMResultChunk, None, None] | LLMResult = (
            self.session.model.llm.invoke(
                model_config=LLMModelConfig(**params.model.model_dump(mode="json")),
                prompt_messages=[UserPromptMessage(content=params.query)],
                tools=[
                    self._convert_tool_to_prompt_message_tool(tool)
                    for tool in params.tools
                ],
                stop=params.model.completion_params.get("stop", [])
                if params.model.completion_params
                else [],
                stream=True,
            )
        )
        response = ""
        tool_calls = []
        tool_instances = (
            {tool.identity.name: tool for tool in params.tools} if params.tools else {}
        )

        for chunk in chunks:
            # check if there is any tool call
            if self.check_tool_calls(chunk):
                tool_calls = self.extract_tool_calls(chunk)
                tool_call_names = ";".join([tool_call[1] for tool_call in tool_calls])
                try:
                    tool_call_inputs = json.dumps(
                        {tool_call[1]: tool_call[2] for tool_call in tool_calls},
                        ensure_ascii=False,
                    )
                except json.JSONDecodeError:
                    # ensure ascii to avoid encoding error
                    tool_call_inputs = json.dumps(
                        {tool_call[1]: tool_call[2] for tool_call in tool_calls}
                    )
                print(tool_call_names, tool_call_inputs)
            if chunk.delta.message and chunk.delta.message.content:
                if isinstance(chunk.delta.message.content, list):
                    for content in chunk.delta.message.content:
                        response += content.data
                        print(content.data, end="", flush=True)
                else:
                    response += str(chunk.delta.message.content)
                    print(str(chunk.delta.message.content), end="", flush=True)

            if chunk.delta.usage:
                # usage of the model
                usage = chunk.delta.usage

        yield self.create_text_message(
            text=f"{response or json.dumps(tool_calls, ensure_ascii=False)}\n"
        )
        result = ""
        for tool_call_id, tool_call_name, tool_call_args in tool_calls:
            tool_instance = tool_instances[tool_call_name]
            tool_invoke_responses = self.session.tool.invoke(
                provider_type=ToolProviderType.BUILT_IN,
                provider=tool_instance.identity.provider,
                tool_name=tool_instance.identity.name,
                parameters={**tool_instance.runtime_parameters, **tool_call_args},
            )
            if not tool_instance:
                tool_invoke_responses = {
                    "tool_call_id": tool_call_id,
                    "tool_call_name": tool_call_name,
                    "tool_response": f"there is not a tool named {tool_call_name}",
                }
            else:
                # invoke tool
                tool_invoke_responses = self.session.tool.invoke(
                    provider_type=ToolProviderType.BUILT_IN,
                    provider=tool_instance.identity.provider,
                    tool_name=tool_instance.identity.name,
                    parameters={**tool_instance.runtime_parameters, **tool_call_args},
                )
                result = ""
                for tool_invoke_response in tool_invoke_responses:
                    if tool_invoke_response.type == ToolInvokeMessage.MessageType.TEXT:
                        result += cast(
                            ToolInvokeMessage.TextMessage, tool_invoke_response.message
                        ).text
                    elif (
                        tool_invoke_response.type == ToolInvokeMessage.MessageType.LINK
                    ):
                        result += (
                            f"result link: {cast(ToolInvokeMessage.TextMessage, tool_invoke_response.message).text}."
                            + " please tell user to check it."
                        )
                    elif tool_invoke_response.type in {
                        ToolInvokeMessage.MessageType.IMAGE_LINK,
                        ToolInvokeMessage.MessageType.IMAGE,
                    }:
                        result += (
                            "image has been created and sent to user already, "
                            + "you do not need to create it, just tell the user to check it now."
                        )
                    elif (
                        tool_invoke_response.type == ToolInvokeMessage.MessageType.JSON
                    ):
                        text = json.dumps(
                            cast(
                                ToolInvokeMessage.JsonMessage,
                                tool_invoke_response.message,
                            ).json_object,
                            ensure_ascii=False,
                        )
                        result += f"tool response: {text}."
                    else:
                        result += f"tool response: {tool_invoke_response.message!r}."

                tool_response = {
                    "tool_call_id": tool_call_id,
                    "tool_call_name": tool_call_name,
                    "tool_response": result,
                }
        yield self.create_text_message(result)

    def _convert_tool_to_prompt_message_tool(
        self, tool: ToolEntity
    ) -> PromptMessageTool:
        """
        convert tool to prompt message tool
        """
        message_tool = PromptMessageTool(
            name=tool.identity.name,
            description=tool.description.llm if tool.description else "",
            parameters={
                "type": "object",
                "properties": {},
                "required": [],
            },
        )

        parameters = tool.parameters
        for parameter in parameters:
            if parameter.form != ToolParameter.ToolParameterForm.LLM:
                continue

            parameter_type = parameter.type
            if parameter.type in {
                ToolParameter.ToolParameterType.FILE,
                ToolParameter.ToolParameterType.FILES,
            }:
                continue
            enum = []
            if parameter.type == ToolParameter.ToolParameterType.SELECT:
                enum = (
                    [option.value for option in parameter.options]
                    if parameter.options
                    else []
                )

            message_tool.parameters["properties"][parameter.name] = {
                "type": parameter_type,
                "description": parameter.llm_description or "",
            }

            if len(enum) > 0:
                message_tool.parameters["properties"][parameter.name]["enum"] = enum

            if parameter.required:
                message_tool.parameters["required"].append(parameter.name)

        return message_tool

    def check_tool_calls(self, llm_result_chunk: LLMResultChunk) -> bool:
        """
        Check if there is any tool call in llm result chunk
        """
        return bool(llm_result_chunk.delta.message.tool_calls)

    def extract_tool_calls(
        self, llm_result_chunk: LLMResultChunk
    ) -> list[tuple[str, str, dict[str, Any]]]:
        """
        Extract tool calls from llm result chunk

        Returns:
            List[Tuple[str, str, Dict[str, Any]]]: [(tool_call_id, tool_call_name, tool_call_args)]
        """
        tool_calls = []
        for prompt_message in llm_result_chunk.delta.message.tool_calls:
            args = {}
            if prompt_message.function.arguments != "":
                args = json.loads(prompt_message.function.arguments)

            tool_calls.append(
                (
                    prompt_message.id,
                    prompt_message.function.name,
                    args,
                )
            )

        return tool_calls
```
{% endtab %}

{% tab title="完整功能代码示例" %}
#### 完整功能代码示例

包含**调用模型、调用工具**以及**输出多轮日志功能**的完整插件代码示例：

```python
import json
import time
from collections.abc import Generator
from typing import Any, cast

from dify_plugin.entities.agent import AgentInvokeMessage
from dify_plugin.entities.model.llm import LLMModelConfig, LLMResult, LLMResultChunk
from dify_plugin.entities.model.message import (
    PromptMessageTool,
    UserPromptMessage,
)
from dify_plugin.entities.tool import ToolInvokeMessage, ToolParameter, ToolProviderType
from dify_plugin.interfaces.agent import AgentModelConfig, AgentStrategy, ToolEntity
from pydantic import BaseModel

class BasicParams(BaseModel):
    maximum_iterations: int
    model: AgentModelConfig
    tools: list[ToolEntity]
    query: str

class BasicAgentAgentStrategy(AgentStrategy):
    def _invoke(self, parameters: dict[str, Any]) -> Generator[AgentInvokeMessage]:
        params = BasicParams(**parameters)
        function_call_round_log = self.create_log_message(
            label="Function Call Round1 ",
            data={},
            metadata={},
        )
        yield function_call_round_log
        model_started_at = time.perf_counter()
        model_log = self.create_log_message(
            label=f"{params.model.model} Thought",
            data={},
            metadata={"start_at": model_started_at, "provider": params.model.provider},
            status=ToolInvokeMessage.LogMessage.LogStatus.START,
            parent=function_call_round_log,
        )
        yield model_log
        chunks: Generator[LLMResultChunk, None, None] | LLMResult = (
            self.session.model.llm.invoke(
                model_config=LLMModelConfig(**params.model.model_dump(mode="json")),
                prompt_messages=[UserPromptMessage(content=params.query)],
                tools=[
                    self._convert_tool_to_prompt_message_tool(tool)
                    for tool in params.tools
                ],
                stop=params.model.completion_params.get("stop", [])
                if params.model.completion_params
                else [],
                stream=True,
            )
        )
        response = ""
        tool_calls = []
        tool_instances = (
            {tool.identity.name: tool for tool in params.tools} if params.tools else {}
        )
        tool_call_names = ""
        tool_call_inputs = ""
        for chunk in chunks:
            # check if there is any tool call
            if self.check_tool_calls(chunk):
                tool_calls = self.extract_tool_calls(chunk)
                tool_call_names = ";".join([tool_call[1] for tool_call in tool_calls])
                try:
                    tool_call_inputs = json.dumps(
                        {tool_call[1]: tool_call[2] for tool_call in tool_calls},
                        ensure_ascii=False,
                    )
                except json.JSONDecodeError:
                    # ensure ascii to avoid encoding error
                    tool_call_inputs = json.dumps(
                        {tool_call[1]: tool_call[2] for tool_call in tool_calls}
                    )
                print(tool_call_names, tool_call_inputs)
            if chunk.delta.message and chunk.delta.message.content:
                if isinstance(chunk.delta.message.content, list):
                    for content in chunk.delta.message.content:
                        response += content.data
                        print(content.data, end="", flush=True)
                else:
                    response += str(chunk.delta.message.content)
                    print(str(chunk.delta.message.content), end="", flush=True)

            if chunk.delta.usage:
                # usage of the model
                usage = chunk.delta.usage

        yield self.finish_log_message(
            log=model_log,
            data={
                "output": response,
                "tool_name": tool_call_names,
                "tool_input": tool_call_inputs,
            },
            metadata={
                "started_at": model_started_at,
                "finished_at": time.perf_counter(),
                "elapsed_time": time.perf_counter() - model_started_at,
                "provider": params.model.provider,
            },
        )
        yield self.create_text_message(
            text=f"{response or json.dumps(tool_calls, ensure_ascii=False)}\n"
        )
        result = ""
        for tool_call_id, tool_call_name, tool_call_args in tool_calls:
            tool_instance = tool_instances[tool_call_name]
            tool_invoke_responses = self.session.tool.invoke(
                provider_type=ToolProviderType.BUILT_IN,
                provider=tool_instance.identity.provider,
                tool_name=tool_instance.identity.name,
                parameters={**tool_instance.runtime_parameters, **tool_call_args},
            )
            if not tool_instance:
                tool_invoke_responses = {
                    "tool_call_id": tool_call_id,
                    "tool_call_name": tool_call_name,
                    "tool_response": f"there is not a tool named {tool_call_name}",
                }
            else:
                # invoke tool
                tool_invoke_responses = self.session.tool.invoke(
                    provider_type=ToolProviderType.BUILT_IN,
                    provider=tool_instance.identity.provider,
                    tool_name=tool_instance.identity.name,
                    parameters={**tool_instance.runtime_parameters, **tool_call_args},
                )
                result = ""
                for tool_invoke_response in tool_invoke_responses:
                    if tool_invoke_response.type == ToolInvokeMessage.MessageType.TEXT:
                        result += cast(
                            ToolInvokeMessage.TextMessage, tool_invoke_response.message
                        ).text
                    elif (
                        tool_invoke_response.type == ToolInvokeMessage.MessageType.LINK
                    ):
                        result += (
                            f"result link: {cast(ToolInvokeMessage.TextMessage, tool_invoke_response.message).text}."
                            + " please tell user to check it."
                        )
                    elif tool_invoke_response.type in {
                        ToolInvokeMessage.MessageType.IMAGE_LINK,
                        ToolInvokeMessage.MessageType.IMAGE,
                    }:
                        result += (
                            "image has been created and sent to user already, "
                            + "you do not need to create it, just tell the user to check it now."
                        )
                    elif (
                        tool_invoke_response.type == ToolInvokeMessage.MessageType.JSON
                    ):
                        text = json.dumps(
                            cast(
                                ToolInvokeMessage.JsonMessage,
                                tool_invoke_response.message,
                            ).json_object,
                            ensure_ascii=False,
                        )
                        result += f"tool response: {text}."
                    else:
                        result += f"tool response: {tool_invoke_response.message!r}."

                tool_response = {
                    "tool_call_id": tool_call_id,
                    "tool_call_name": tool_call_name,
                    "tool_response": result,
                }
        yield self.create_text_message(result)

    def _convert_tool_to_prompt_message_tool(
        self, tool: ToolEntity
    ) -> PromptMessageTool:
        """
        convert tool to prompt message tool
        """
        message_tool = PromptMessageTool(
            name=tool.identity.name,
            description=tool.description.llm if tool.description else "",
            parameters={
                "type": "object",
                "properties": {},
                "required": [],
            },
        )

        parameters = tool.parameters
        for parameter in parameters:
            if parameter.form != ToolParameter.ToolParameterForm.LLM:
                continue

            parameter_type = parameter.type
            if parameter.type in {
                ToolParameter.ToolParameterType.FILE,
                ToolParameter.ToolParameterType.FILES,
            }:
                continue
            enum = []
            if parameter.type == ToolParameter.ToolParameterType.SELECT:
                enum = (
                    [option.value for option in parameter.options]
                    if parameter.options
                    else []
                )

            message_tool.parameters["properties"][parameter.name] = {
                "type": parameter_type,
                "description": parameter.llm_description or "",
            }

            if len(enum) > 0:
                message_tool.parameters["properties"][parameter.name]["enum"] = enum

            if parameter.required:
                message_tool.parameters["required"].append(parameter.name)

        return message_tool

    def check_tool_calls(self, llm_result_chunk: LLMResultChunk) -> bool:
        """
        Check if there is any tool call in llm result chunk
        """
        return bool(llm_result_chunk.delta.message.tool_calls)

    def extract_tool_calls(
        self, llm_result_chunk: LLMResultChunk
    ) -> list[tuple[str, str, dict[str, Any]]]:
        """
        Extract tool calls from llm result chunk

        Returns:
            List[Tuple[str, str, Dict[str, Any]]]: [(tool_call_id, tool_call_name, tool_call_args)]
        """
        tool_calls = []
        for prompt_message in llm_result_chunk.delta.message.tool_calls:
            args = {}
            if prompt_message.function.arguments != "":
                args = json.loads(prompt_message.function.arguments)

            tool_calls.append(
                (
                    prompt_message.id,
                    prompt_message.function.name,
                    args,
                )
            )

        return tool_calls
```
{% endtab %}
{% endtabs %}

### 3. 调试插件

配置插件的声明文件与功能代码后，在插件的目录内运行 `python -m main` 命令重启插件。接下来需测试插件是否可以正常运行。Dify 提供远程调试方式，前往[“插件管理”](https://cloud.dify.aij/plugins)获取调试 Key 和远程服务器地址。

<figure><img src="https://assets-docs.dify.ai/2024/12/053415ef127f1f4d6dd85dd3ae79626a.png" alt=""><figcaption></figcaption></figure>

回到插件项目，拷贝 `.env.example` 文件并重命名为 `.env`，将获取的远程服务器地址和调试 Key 等信息填入至 `REMOTE_INSTALL_HOST` 与 `REMOTE_INSTALL_KEY` 参数内。

```bash
INSTALL_METHOD=remote
REMOTE_INSTALL_HOST=localhost
REMOTE_INSTALL_PORT=5003
REMOTE_INSTALL_KEY=****-****-****-****-****
```

运行 `python -m main` 命令启动插件。在插件页即可看到该插件已被安装至 Workspace 内。其他团队成员也可以访问该插件。

<figure><img src="https://assets-docs.dify.ai/2025/01/c82ec0202e5bf914b36e06c796398dd6.png" alt=""><figcaption><p>访问插件</p></figcaption></figure>

### 打包插件（可选）

确认插件能够正常运行后，可以通过以下命令行工具打包并命名插件。运行以后你可以在当前文件夹发现 `google.difypkg` 文件，该文件为最终的插件包。

```
dify plugin package ./basic_agent/
```

恭喜，你已完成一个工具类型插件的完整开发、调试与打包过程！

### 发布插件（可选）

现在可以将它上传至 [Dify Plugins 代码仓库](https://github.com/langgenius/dify-plugins)来发布你的插件了！上传前，请确保插件已遵循[插件发布规范](https://docs.dify.ai/zh-hans/plugins/publish-plugins/publish-to-dify-marketplace)。审核通过后，代码将合并至主分支并自动上线至 [Dify Marketplace](https://marketplace.dify.ai/)。

### 探索更多

复杂任务往往需要多轮思考和多次工具调用。为了实现更智能的任务处理，通常采用循环执行的策略：**模型调用 → 工具调用**，直到任务完成或达到设定的最大迭代次数。

在这个过程中，提示词（Prompt）管理变得尤为重要。为了高效地组织和动态调整模型输入，建议参考插件内 Function Calling 功能的[完整实现代码](https://github.com/langgenius/dify-official-plugins/blob/main/agent-strategies/cot_agent/strategies/function_calling.py)，了解如何通过标准化的方式来让模型调用外部工具并处理返回结果。



