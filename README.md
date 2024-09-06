# Reflection-4ALL
Reflection CoT thinking for ALL LLMs

<p align="left">
<img src="https://github.com/user-attachments/assets/abbeb62d-9508-4815-ada9-a689d54296be" height="300">
</p>

---

First, you need to download and install these:
- [Ollama](https://www.ollama.com/)
- [Open webui](https://docs.openwebui.com/getting-started/)
- [A LLM](https://www.ollama.com/library)

---

### Open `open webui`, `workspace`, `function`, `+` to create a function. which will help you to filter the output:

![](https://github.com/user-attachments/assets/f152eaab-014e-4076-a325-67571ff587bf)

### Enter the following script, then give your function a name, and click the save button at the bottom:

This script is not necessary but it will help you filter the models response (`after the response was completed`), so you only will only see the `output` part, `thinking` part will be automatically removed.

```
import re
from typing import Callable, Awaitable, Any, Optional, Literal, List
from pydantic import BaseModel, Field


def extract_output_content(message: str):
    # Regex to match content between <output> and </output>
    match = re.search(r"<output>(.*?)</output>", message, re.DOTALL)
    if match:
        # Return the content between the tags
        return match.group(1).strip()
    # Return original message if no match is found
    return message


class Filter:
    class Valves(BaseModel):
        start_removals: List[str] = Field(
            default=[":"],
            description="Words or terms to remove from the start of LLM replies.",
        )
        pass

    def __init__(self):
        self.valves = self.Valves()
        pass

    async def replace_message(self, message):
        await self.event_emitter({"type": "replace", "data": {"content": message}})

    async def outlet(
        self,
        body: dict,
        __user__: dict,
        __event_emitter__: Callable[[Any], Awaitable[None]],
        __model__: Optional[dict] = None,
    ) -> dict:
        """Remove words from the start and end of LLM replies."""
        self.event_emitter = __event_emitter__

        if len(body["messages"]) == 0:
            return body

        last_reply: dict = body["messages"][-1]
        last_reply = last_reply["content"].strip()

        # Extract content between <output> and </output>
        replaced_message = extract_output_content(last_reply)

        # If extracted content is different from original, replace the message
        if replaced_message != last_reply:
            body["messages"][-1]["content"] = replaced_message
            await self.replace_message(replaced_message)

        return body

```

### Enable your function:

![](https://github.com/user-attachments/assets/717dbccb-18bc-49c3-90b6-c89bee8a0b22)

### Go back to workspace, and create a model:

![](https://github.com/user-attachments/assets/aa6527d8-ade2-40f7-9183-c76f4727218a)

### Give your model a name, select the base model, then enter the following system prompt:

Source: https://www.reddit.com/r/LocalLLaMA/comments/1f9um6s/comment/llpc7ud

```
You are an AI assistant designed to provide detailed, step-by-step responses. Your outputs should follow this structure:

1. Begin with a <thinking> section.
2. Inside the thinking section:
   a. Briefly analyze the question and outline your approach.
   b. Present a clear plan of steps to solve the problem.
   c. Use a "Chain of Thought" reasoning process if necessary, breaking down your thought process into numbered steps.
3. Include a <reflection> section for each idea where you:
   a. Review your reasoning.
   b. Check for potential errors or oversights.
   c. Confirm or adjust your conclusion if necessary.
4. Be sure to close all reflection sections.
5. Close the thinking section with </thinking>.
6. Provide your final answer in an <output> section.

Always use these tags in your responses. Be thorough in your explanations, showing each step of your reasoning process. Aim to be precise and logical in your approach, and don't hesitate to break down complex problems into simpler components. Your tone should be analytical and slightly formal, focusing on clear communication of your thought process.

Remember: Both <thinking> and <reflection> MUST be tags and must be closed at their conclusion

Make sure all <tags> are on separate lines with no other text. Do not include other text on a line containing a tag.
```

![](https://github.com/user-attachments/assets/fa1d5c90-3ed5-42bb-9776-a8f64ef40be1)

### Scroll down and you will see `filters`, enable the function you just created, then click the save button

![](https://github.com/user-attachments/assets/fb5964a8-cc5f-4bbf-9446-8c7b323e266d)

### Now click `new chat` and select the model you just created and start chatting

![](https://github.com/user-attachments/assets/83cc7854-b904-46e2-8fa6-68d22db59a03)

![](https://github.com/user-attachments/assets/f699d076-de2e-487f-8578-c76ac2080c5e)

### If the prompt didn't work, or the filter didn't work, go back to workspace and check your model settings, or switch to a different base model, like Gemma 2 27B

---

### ✨ Check out my other project: [Waifu2x-Extension-GUI](https://github.com/AaronFeng753/Waifu2x-Extension-GUI)

#### Photo/Video/GIF enlargement and Video frame interpolation using machine learning

#### Supports AMD / Nvidia / Intel GPU

![](https://raw.githubusercontent.com/AaronFeng753/AaronFeng753/main/res/ReadMeCover.png)
