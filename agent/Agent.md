
- agent是智能体，能够以llm为基础完成用户个性化需求。
- 要将用户需求流程化，拆分成一个一个子任务，依靠LLM完成并执行。
- 我们通过提示LLM获得反馈，得到执行力。

- agent完成任务的能力：
	- 感知：把能用到的环境资源转化成提示
	- 记忆：将感知到的东西记忆下来
	- 思考：总结感知到的的东西
	- 动作：调用API，执行各种行为
- 与LLM的关系：
	- LLM是指令的实际执行者。
	- 是agent的核心。
- 多智能体：
	- 多个智能体分工合作，各自有自己的专门化任务
	- 不同智能体之间的交互。


## 四大模块

- Tools：链接外部工具
- Memory：管理对话记忆
- Planning：规划大模型的行动
- Action：管理行动的基本流程


## 开发框架

- 低代码框架：Coze,Dify,Langflow
- 基础框架：function calling,tools use
- 代码框架：langchain,langgraph,Llamaindex
- Multi-Agent框架：crewAI,Swarm,Assistant API

## 参数

- `message`:
```python
# 系统消息，开发者设定，作为模型设定
system_message = {
	"role":"system",
	"contene":"你是一位助人为乐的助手。"
}
# 用户消息
user_message = {
	"role":"user",
	"content":"你好"
}

# 调用模型
res = client.chat.completions.create(
	model="deepseek-chat",
	message=[
		system_message,
		user_message
	]
)
```
- `model`:调用的模型

## 多轮对话机器人

```python
def chat(client,messages):
    '''
    多轮对话
    参数：
    实例化客户端
    上下文消息列表
    返回：
    生成的回复文本
    '''
    res = client.chat.completions.create(
        model = "deepseek/deepseek-chat:free",
        messages = messages
    )
    return res.choices[0].message.content

  

def multi_chat(client):
    messages = [SYS_M]

    while 1:
        userinput = input("user：").strip()
        if userinput.lower() == "exit":
            print("对话结束")
            break
        # 构造用户消息列表
        messages.append({"role":"user","content":userinput});
        reply = chat(client,messages)
        # display(Markdown(f"AI:{reply}"))
        print(reply)
        # 更新消息列表
        messages.append({"role":"assistant","content":reply})

multi_chat(client)
```

## function calling

- 大模型对外部函数发送调用请求，外部函数请求外部工具，响应给外部函数，再将调用结果给大模型
- 当大模型确定要调用外部函数时，返回的内容为空，但是包含tool_call返回，依靠tool_call与外部函数库调用响应的外部函数，将返回的结果和响应的结果拼接到message中再次传递给LLM
```python
from openai import OpenAI
import os
from rich.markdown import Markdown
from rich.console import Console
import requests  # 用于发送HTTP请求获取天气数据
import json     # 用于处理JSON数据

API_KEY = "..."

# 实例化客户端
client = OpenAI(api_key=API_KEY,base_url="https://api.deepseek.com")

SYS_M = {"role":"system","content":"你是一位助人为乐的助手"}
USER_M =  {"role":"user","content":"帮我查询上海今日天气"}
message = [USER_M]
console = Console()
# 自定义函数
def get_weather(loc):
    url="https://api.openweathermap.org/data/2.5/weather"
    param = {
        "q":loc,
        "appid": "...",
        "units":"metric",
        "lang":"zh_cn"
    }
    response = requests.get(url,params=param)
    data = response.json()
    return json.dumps(data) # 返回json

# tools参数
tools = [
    {
        "type":"function",
        "function":{
            "name":"get_weather",
            "description":"查询天气",
            "parameters":{
                "type": "object",
                "properties": {
                    "loc" :{
                        "type":"string",
                        "description":"城市名，注意中国城市要用拼音代替，例如北京对应Beijing"
                    }
                },
                "required":["loc"]
            }
        }
    }
]
# 外部函数库
available_func = {
    "get_weather":get_weather,
}
# 调用外部工具第一次响应，返回空
response = client.chat.completions.create(
    model="deepseek-chat",
    messages=message,
    tools=tools
)
print(response.choices[0].finish_reason) # tool_calls
print(response.choices[0].message.tool_calls) # 模型调用tool的信息,是一个列表
'''
[ChatCompletionMessageToolCall(id='call_0_ce531e23-e3e4-477e-88d2-7d016814e336', function=Function(arguments='{"loc":"上海"}', name='get_weather'), type='function', index=0)]
'''
print(response.choices[0].message.tool_calls[0].id) # 调用函数的id
# call_0_15cf1eb9-e6f7-4c89-9b71-55b541e64e99
print(response.choices[0].message.tool_calls[0].function) #调用函数的情况（名称，参数）
# Function(arguments='{"loc":"上海"}', name='get_weather')

# 提取函数名,参数
func_name = response.choices[0].message.tool_calls[0].function.name
func_arg = json.loads(response.choices[0].message.tool_calls[0].function.arguments)
func = available_func[func_name] # 调用的函数
func_res = func(**func_arg)
print(func_res)

# 将第一轮响应结果转化为message格式并追加
print(response.choices[0].message.model_dump())
'''
{'content': '', 'refusal': None, 'role': 'assistant', 'annotations': None, 'audio': None, 'function_call': None, 'tool_calls': [{'id': 'call_0_2dd99f50-7464-47a3-8f29-3091c7ba005d', 'function': {'arguments': '{"loc":"上海"}', 'name': 'get_weather'}, 'type': 'function', 'index': 0}]}
'''
message.append(response.choices[0].message.model_dump())
# 追加外部工具返回消息
message.append({
    "role":"tool",
    "content": func_res,
    "tool_call_id": response.choices[0].message.tool_calls[0].id # 标明id确定对应关系
})
# 第二轮交互
second_response = client.chat.completions.create(
    model="deepseek-chat",
    messages=message,
    tools=tools
)
console.print(Markdown(second_response.choices[0].message.content))
print(second_response.choices[0].finish_reason) #stop
```
- 函数封装
```python
def run(messages,api_key,tools=None,func_list=None,model="deepseek-chat"):
    user_message = messages
    client = OpenAI(api_key=api_key,base_url="https://api.deepseek.com")
    # 无工具正常对话
    if tools == None:
        res = client.chat.completions.create(
            model=model,
            messages=user_message
        )    
        final_res = res.choices[0].message.content
    else:
        functb = {fun.__name__: fun for fun in func_list} # 建立外部函数库字典
        # first res
        res = client.chat.completions.create(
            model=model,
            messages=user_message,
            tools=tools
        )
        res_message = res.choices[0].message
        # 获取信息
        fun_name = res_message.tool_calls[0].function.name
        fun = functb[fun_name]
        fun_arg = json.loads(res_message.tool_calls[0].function.arguments)
        fun_res =fun(**fun_arg)
        # 拼接message
        user_message.append(res_message.model_dump())
        user_message.append(
            {
                "role":"tool",
                "content": fun_res,
                "tool_call_id": res_message.tool_calls[0].id
            }
        )

        # 第二次调用LLM
        second_res = client.chat.completions.create(
            model=model,
            messages=user_message,
            tools=tools
        )
        final_res = second_res.choices[0].message.content
    return final_res

res = run(message,API_KEY,tools,[get_weather])
console.print(Markdown(res))
```

## 多工具并联使用

```python

# 多工具并联
def create_func_res_message(message,response):
    function_call_messages = response.choices[0].message.tool_calls
    message.append(response.choices[0].message.model_dump())
    # 遍历工具调用列表
    for function_call_message in function_call_messages:
        tool_name = function_call_message.function.name
        tool_args = json.loads(function_call_message.function.arguments)
        function_to_call = available_func[tool_name]
        # 运行外部函数
        try:
            function_response = function_to_call(**tool_args)
        except Exception as e:
            function_response = "函数运行报错如下："+str(e)
        # 拼接消息队列
        message.append(
            {
                "role":"tool",
                "content": function_response,
                "tool_call_id": function_call_message.id
            }
        )
    return message

res = client.chat.completions.create(
    model="deepseek-chat",
    messages = message,
    tools= tools
)

message = create_func_res_message(message,res)
print(message)
res2 = client.chat.completions.create(
    model="deepseek-chat",
    messages=message,
    tools=tools
)

# res = run(message,API_KEY,tools,[get_weather])
console.print(Markdown(res2.choices[0].message.content))
```

## 多工具串联使用

- 若运用完一个工具后又要运行第二个工具，则会在第二轮回应中返回tool_call类型的响应

### minimanus

