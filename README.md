# snbpy-assistant
snbpy-assistant

# snbpy 辅助类

1. 提供了一个简单的订单 ID 生成算法
2. 提供了一个订单状态监听器，可以监听订单状态的变化

# Usage
```python
import time

from snbpy.common.domain.response import HttpResponse
from snbpy.common.domain.snb_config import SnbConfig
from snbpy.snb_api_client import SnbHttpClient
from snbpy_assistant.snbpy_assistant import SnbAgent, SimpleOrderStatusListener, SnbAgentConfig


def on_order_status_refreshed(order_status: HttpResponse):
    print(order_status.data)


def on_order_removed(order_id: str):
    print("Order removed: " + order_id)


if __name__ == '__main__':
    config = SnbConfig()
    config.account = "123"
    config.key = "123"
    config.sign_type = 'None'
    config.snb_server = 'sandbox.snbsecurities.com'
    config.snb_port = '443'
    config.timeout = 1000
    config.schema = 'https'
    client = SnbHttpClient(config)
    client.login()

    agentConfig = SnbAgentConfig()
    agentConfig.remove_terminal_order = True
    agentConfig.auto_refresh_interval = 2
    
    agent = SnbAgent(client, config=agentConfig)
    listener = SimpleOrderStatusListener(on_order_status_refreshed, on_order_removed)
    agent.register_order_status_listener(listener)
    
    for i in range(0,100):
        agent.gen_order_id()

    agent.add_order_to_auto_refresh("123123")
    agent.start_order_refresh_thread()
    while True:
        time.sleep(1)
```
# Example
[example](https://gist.github.com/chnyangjie/f6462f22c700f21b941cfc104fc7f171)