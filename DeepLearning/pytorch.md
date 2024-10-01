<!-- GFM-TOC -->
- [类之间的关系](#类之间的关系)
- [API](#api)
- [框架](#框架)
<!-- GFM-TOC -->
---

### 类之间的关系
    * 类LinearModel ---- 继承 ----> 类torch.nn.Module
    * 类LinearModel ---- 包含 ----> 类linear

### API
  * [torch.nn.Linear](https://pytorch.org/docs/stable/generated/torch.nn.Linear.html#torch.nn.Linear)
  * [torch.nn.MSELoss](https://pytorch.org/docs/stable/generated/torch.nn.MSELoss.html)
  * [torch.optim.SGD](https://pytorch.org/docs/stable/generated/torch.optim.SGD.html)

### 框架
```` python
    import torch

    x_data = torch.Tensor([[1.0], [2.0], [3.0]])
    y_data = torch.Tensor([[2.0], [4.0], [6.0]])

    class LinearModel(torch.nn.Module):
        def __init__(self):
            super(LinearModel, self).__init__()
            self.linear = torch.nn.Linear(1, 1)

        def forward(self, x):
            y_pred = self.linear(x)
            return y_pred
        
    model = LinearModel()
    criterion = torch.nn.MSELoss(size_average=False)
    optimizer = torch.optim.SGD(model.parameters(), lr=0.01)

    for epoch in range(1000):
        y_pred = model(x_data)
        loss = criterion(y_pred, y_data)
        print(epoch, loss.item())

        optimizer.zero_grad()   # grads not to be accumulated
        loss.backward()
        optimizer.step()        # update

    print('w = ', model.linear.weight.item())
    print('b = ', model.linear.bias.item())

    x_test = torch.Tensor([[4.0]])
    y_test = model(x_test)
    print('y_pred = ', y_test.data)
````