This file stores all the notes I took from the [Microsoft Autogen github portal](https://microsoft.github.io/autogen/0.2/docs/tutorial/) to develop this product.

1. While there are many definitions of agents, in AutoGen, an agent is an entity that can send messages, receive messages and generate a reply using models, tools, human inputs or a mixture of them.[1](https://www.gettingstarted.ai/autogen-agents-overview/)
2. An agent can be powered by models (such as a large language model like GPT-4), code executors (such as an IPython kernel), human, or a combination of these and other pluggable and customizable components.[2](https://www.gettingstarted.ai/autogen-agents-overview/)
3. An example of such agents is the built-in ConversableAgent which supports the following components: [3](https://www.gettingstarted.ai/autogen-agents-overview/)  
   A list of LLMs  
   A code executor  
   A function and tool executor  
   A component for keeping human-in-the-loop  
4. Terminate agent conversation: max_turns, max_consecutive_auto_reply, is_termination_msg parameters [4](https://microsoft.github.io/autogen/0.2/docs/tutorial/chat-termination)
5. Currently AutoGen supports three modes for human input. The mode is specified through the human_input_mode argument of the ConversableAgent. The three modes are: [5](https://microsoft.github.io/autogen/0.2/docs/tutorial/human-in-the-loop)  
   NEVER: human input is never requested.  
   TERMINATE (default): human input is requested only when a termination condition is met. Note that in this mode if the human chooses to intercept and reply, the conversation continues and the counter used by max_consecutive_auto_reply is reset.  
   ALWAYS: human input is always requested and the human can choose to skip and trigger an auto-reply, intercept and provide feedback, or terminate the conversation. Note that in this mode termination based on max_consecutive_auto_reply is ignored.  
6. AutoGen provides two types of built-in code executors, one is command line code executor, which runs code in a command line environment such as a UNIX shell, and the other is Jupyter executor, which runs code in an interactive Jupyter kernel.  
   The code can run in the local environment or in a docker. Autogen code executors: LocalCommandLineCodeExecutor, DockerCommandLineCodeExecutor, jupyter.JupyterCodeExecutor [6](https://microsoft.github.io/autogen/0.2/docs/tutorial/code-executors)  
7. The command line code executor does not keep any state in memory between executions of different code blocks it receives, as it writes each code block to a separate file and executes the code block in a new process.  
   Contrast to the command line code executor, the Jupyter code executor runs all code blocks in the same Jupyter kernel, which keeps the state in memory between executions. See the topic page for Jupyter Code Executor. [7](https://microsoft.github.io/autogen/0.2/docs/tutorial/code-executors)  
8. UserProxyAgent class is a subclass of ConversableAgent with human_input_mode=ALWAYS and llm_config=False – it always requests human input for every message and does not use LLM. It also comes with default description field for each of the human_input_mode setting.   This class is a convenient short-cut for creating an agent that is intended to be used as a code executor.[8](https://microsoft.github.io/autogen/0.2/docs/tutorial/code-executors)  
9. AssistantAgent class, which is a subclass of ConversableAgent with human_input_mode=NEVER and code_execution_config=False – it never requests human input and does not use code executor. It also comes with default system_message and description fields. This class is a convenient short-cut for creating an agent that is intended to be used as a code writer and does not execute code. [9](https://microsoft.github.io/autogen/0.2/docs/tutorial/code-executors)  
10. Tools are pre-defined functions that agents can use. Instead of writing arbitrary code, agents can call tools to perform actions, such as searching the web, performing calculations, reading files, or calling remote APIs. Because you can control what tools are available to an agent, you can control what actions an agent can perform. [10](https://microsoft.github.io/autogen/0.2/docs/tutorial/tool-use)  
11. Similar to code executors, a tool must be registered with at least two agents for it to be useful in conversation. The agent registered with the tool’s signature through register_for_llm can call the tool; the agent registered with the tool’s function object through register_for_execution can execute the tool’s function. Alternatively, you can use autogen.register_function function to register a tool with both agents at once.[11](https://microsoft.github.io/autogen/0.2/docs/tutorial/tool-use)  
12. Two-agent chat: the simplest form of conversation pattern where two agents chat with each other.[12](https://microsoft.github.io/autogen/0.2/docs/tutorial/conversation-patterns)  
    Sequential chat: a sequence of chats between two agents, chained together by a carryover mechanism, which brings the summary of the previous chat to the context of the next chat.  
    Group Chat: a single chat involving more than two agents. An important question in group chat is: What agent should be next to speak? To support different scenarios, we provide different ways to organize agents in a group chat:  
      Strategies to select the next agent: round_robin, random, manual (human selection), and auto (Default, using an LLM to decide).  
      It is allowed to add constraints to select the next speaker  
      It is also allowed to add a function to customize the selection of the next speaker.  
    Nested Chat: package a workflow into a single agent for reuse in a larger workflow.  
    
   

*Side notes*
- Adding http client in llm_config for proxy: In Autogen, a deepcopy is used on llm_config to ensure that the llm_config passed by user is not modified internally.  
  You may get an error if the llm_config contains objects of a class that do not support deepcopy, eg. http proxy clients connecting to llm apis. To fix this, you need to implement a __deepcopy__ method for the class [13](https://microsoft.github.io/autogen/0.2/docs/topics/llm_configuration/)

The below example shows how to implement a __deepcopy__ method for http client and add a proxy.
