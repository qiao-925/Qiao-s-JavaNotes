# Design Patterns

https://draveness.me/holy-grail-design-pattern/

```markdown
# 策略模式
将一组算法或者行为封装成独立的策略类，然后在运行时动态的选择合适的策略。
应用：扩展腾讯云能力的时候，合规的一些功能基于策略类去实现使用阿里云还是腾讯云

# 模版模式
用抽象类定义一个处理流程，然后在子类中进行具体细节的扩展
应用：合规检测的MQ送检通道，对于合规来讲，它只需要做检测一件事情，对于不同的业务来源，可能有一些自己特殊的处理。所以思路就是，通用处理放在抽象类中，然后具体不同的细节放在子类中扩展

```