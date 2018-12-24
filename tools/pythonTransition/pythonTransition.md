## 状态机在python中的使用

<p class="tip">
  <strong>在程序中使用状态机，能够强制代码按照一定的格式进行交互，能使程序的状态管理更加清晰。</strong><br><br>
</p>

[python transition reference--github](https://github.com/pytransitions/transitions/blob/master/transitions/extensions/diagrams.py)


## 根据业务画出状态机图

    

## 编写代码生成状态机图

<p class="tip">
  <strong>python 代码参考</strong><br>
</p>

```python
# Draw pic.
from transitions.extensions import GraphMachine as Machine

# m = Model()
m = OrderModel()
# machine = Machine(model=m, show_auto_transitions=True, states=states, send_event=True,
# transitions=transitions, initial='Init')
machine = Machine(model=m, states=states, transitions=transitions, initial='Init')
# show_auto_transitions=True, show_conditions=True,

# in cases where auto transitions should be visible
# Machine(model=m, show_auto_transitions=True, ...)
# draw the whole graph ...
m.get_graph().draw('my_state_diagram.png', prog='dot')
# ... or just the region of interest
# (previous state, active state and all reachable states)
# m.get_graph(show_roi=True).draw('my_state_diagram.png', prog='dot')
m.get_graph().draw('my_state_diagram.png', prog='dot')
```
    代码生成的状态机图