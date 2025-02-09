# pytorch | 加载数据集

1. dataset：构造数据集，使其支持索引
2. dataloader：目标拿出minibatch供给训练
3. 梯度下降可以用全部样本/一个样本

    随机梯度下降就是用一个样本，具有比较好的随机性，可以帮我们克服**鞍点**

    ​![image](assets/image-20231202142142-r24sqnp.png)​

    全部样本batch就是用向量计算充分利用gpu的并行能力，节省时间，但性能有问题
4. 所以用mini-batch平衡需求
5. 专用词汇epoch batch-size和iteration

    * epoch-**所有训练样本**都进行了一次前馈和反向传播

      ​![image](assets/image-20231202142336-x64x02r.png)​

      每个epoch循环（训练周期）里执行minibatch的循环
    * batch-size每次训练时候用的样本数量（一次前馈反馈更新）
    * iteration-number of passes,each pass using [batch size] number of examples

      ​![image](assets/image-20231202143834-bw8xp4r.png)​

      迭代的定义是指遍数，使用的样本数就是遍数乘以每个遍使用的样本数量。
6. ​![image](assets/image-20231202143940-5pxr5ip.png)​

    设置参数。"shuffle"（洗牌）

    ​​![image](assets/image-20231202144241-w3mw8sw.png)​​

    每一次迭代给一个batch
7. **代码实现**

    dataset.utils（工具）的数据工具（data）下面提供了两个类，注意dataset是一个抽象类，不能实例化，只能继承。

    ​![image](assets/image-20231202144342-oq4shtj.png)​

    dataloader是加载数据的
8. ​![image](assets/image-20231202144700-ypqtbup.png)​

    getitem调用索引用的

    魔法方法（Magic function）是一类特殊的方法，它们以双下划线（underscore）开头和结尾，例如`__init__`​​​、`__str__`​​​等。这些方法在对象的创建、操作和销毁等过程中自动被调用，用于定义对象的行为和操作。

    ​![image](assets/image-20231202145056-vq9omfv.png)​
9. ```python
            import torch
            from torch.utils.data import Dataset
            from torch.utils.data import DataLoader

            class gycdataset(Dataset):
                def __init__(self):  #括号里的self参数是指向当前对象实例的引用，它是必须的，用于访问和操作对象的属性和方法。
                    pass

                def __getitem__(self,index):#括号里的参数self是指向当前对象实例的引用，而index参数是在进行索引操作时传递的索引值。
                    pass

                def __len__(self):
                    pass

            dataset=gycdataset()
            train_loader=DataLoader(dataset=dataset,
                                   batch_size=32,
                                   shuffle=true,
                                   num_worker=2)
            #打完代码运行发现，py39的核是tensorflow，root才有pytorch
            ‍‍```
    ```
10. 一个报错：如果直接用train loader训练

     ​![image](assets/image-20231202150924-ngwtcz3.png)​

     所以要

     ​![image](assets/image-20231202150918-yzopaqw.png)​
11. 数据集代码

     ​![image](assets/image-20231202151110-9ithi6y.png)​

     * 数据格式上节课说了，是[n][9]的矩阵，所以shape[0]就可以把n取出来当len

       ​![image](assets/image-20231202151533-k8n2qsa.png)​
12. training cycle

     所有数据都跑上100遍

     ​​![image](assets/image-20231202153340-dxzwnij.png)​​

     enumerate可以获得当前迭代的次数

     准备数据、前向传播算损失、反向传播、更新
13. 完整代码、整个步骤

     ​​![image](assets/image-20231202153531-jdohvzu.png)​![image](assets/image-20231202153504-4tz3w5b.png)​
14. torchvision提供的数据集

     ​![image](assets/image-20231202153748-luztp4q.png)​

     ​`args.nThreads`​是一个变量，通常在命令行参数或配置文件中使用，用于指定并发线程数或进程数的值。

     举例：

     ```python
     import argparse

     # 创建命令行参数解析器
     parser = argparse.ArgumentParser()
     # 添加一个--nThreads参数
     parser.add_argument('--nThreads', type=int, default=4, help='Number of threads')

     # 解析命令行参数
     args = parser.parse_args()

     # 使用args.nThreads来获取并发线程数
     num_threads = args.nThreads

     # 在程序中使用num_threads来设置并发操作的线程数
     # ...
     ```

     '''
15. MINIST数据集 

     ​![1701502842478](assets/1701502842478-20231202154050-geezynu.png)​
16.  

     * ​`enumerate(train_loader)`​：`enumerate`​函数是Python内置的函数，它接受一个可迭代对象（这里是`train_loader`​），并返回一个迭代器，该迭代器会在每次迭代时返回一个元组，包含当前迭代的索引和对应的元素。在这里，`enumerate(train_loader)`​会返回一个迭代器，其每个元素是一个包含批次索引和对应数据的元组。
     * ​`(batch_idx, (inputs, target))`​：这是一个用于接收迭代器返回元组的语法结构。`batch_idx`​是当前迭代的索引，而`(inputs, target)`​是对应批次的数据。`inputs`​表示输入数据，`target`​表示目标数据（例如，标签）。
     * 在每次循环迭代中，`batch_idx`​会被赋值为当前批次的索引，`(inputs, target)`​会被赋值为当前批次的数据。循环体中的代码会针对当前批次的数据执行操作。

‍
