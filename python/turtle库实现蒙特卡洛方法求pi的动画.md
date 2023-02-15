# turtle库实现蒙特卡洛方法求pi的动画

## 动画效果如图

![2](C:\Users\14815\Desktop\2.png)



## 代码如下

```python
import turtle
from random import random
from math import sqrt

turtle.setup(410, 410, 100, 100)
r = 200
darts = 5000
for i in range(1):
    turtle.penup()
    turtle.goto(0, -200)
    turtle.pendown()
    turtle.circle(r)
turtle.goto(0, 200)
turtle.penup()
turtle.goto(-200, 0)
turtle.pendown()
turtle.goto(200, 0)
turtle.hideturtle()

turtle.tracer(False)
hits = 0
for i in range(1, darts + 1):
    x = random()
    y = random()
    dist = sqrt(x ** 2 + y ** 2)
    turtle.goto(x * 300, y * 300)
    turtle.pendown()
    if dist <= 0.67:
        hits += 1
        turtle.dot(3, 'red')
    else:
        turtle.dot(3, 'blue')
    turtle.penup()
turtle.tracer(True)

turtle.done()
```