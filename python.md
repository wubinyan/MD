## @classmethod

1. **工厂方法**：类方法常常被用作工厂方法，用于创建类的实例。例如，如果你有一个日期类，你可能希望用户能够直接从字符串创建一个日期实例，而不是手动输入年、月、日。你可以创建一个类方法，接收一个字符串，然后解析这个字符串，最后返回一个类的实例
```python
class Date:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day

    @classmethod
    def from_string(cls, date_string):
        year, month, day = map(int, date_string.split('-'))
        return cls(year, month, day)

```

2. **修改/查询类状态**：类方法还可以用于修改或查询类的状态。例如，如果你有一个类，它有一个类级别的属性，你可能希望有一个方法能够修改或查询这个属性的值。类方法可以做到这一点
```python
class MyClass:
    _class_var = 0

    @classmethod
    def increment_class_var(cls):
        cls._class_var += 1
        return cls._class_var
```
3. **继承**：由于类方法总是接收类本身作为第一个参数，因此当你调用一个类方法时，Python会将调用该方法的类传递给该方法。这意味着，如果你在一个基类中定义了一个类方法，然后在一个子类中调用它，那么传递给该方法的类将是子类，而不是基类。这使得你可以在基类中定义一些行为，但是这些行为可以被子类覆盖。在以下例子中，get_species类方法允许我们通过方法调用来获取species属性，而不是直接访问属性(这样做我们提供了更大的灵活性，例如，如果我们决定改变属性的内部表示，我们只需要修改类方法，而不需要修改访问这些属性的所有地方)
```python
class Animal:
    species = "animal"

    @classmethod
    def get_species(cls):
        return cls.species

class Dog(Animal):
    species = "dog"
    sound = "bark"

# 使用基类
print(Animal.get_species())  # 输出 "animal"

# 使用子类 Dog
print(Dog.get_species())  # 输出 "dog"
```

## @property装饰器

在Python中，@property是一个内置的装饰器，用于将方法转换为属性。这使得你可以在不更改类的公共接口的情况下，将类的属性访问转换为方法调用。这是封装的一个很好的例子。

## 迭代器协议（Iterator Protocol）

**可迭代对象（Iterable）**：如果一个对象定义了 __iter__ 方法，并且返回一个迭代器，那么它就是一个可迭代对象。__iter__ 方法返回一个迭代器对象。Python 的内置数据类型如 list、tuple、str 等都是可迭代对象。你也可以定义自己的可迭代对象，只需在类中实现 __iter__ 方法即可。例如lit_llama/PackedDataset

**迭代器（Iterator）**：迭代器是一个更具体的概念。迭代器不仅实现了 __iter__ 方法，还实现了 __next__ 方法。通过反复调用 __next__ 方法，可以依次获取序列中的每一个元素。当序列中没有更多元素时，__next__ 方法会抛出 StopIteration 异常。例如lit_llama/PackedDatasetIterator

简单来说，可迭代对象提供了一种方式来获取一个迭代器，而迭代器则是用来进行实际的迭代操作的工具。在大部分情况下，__iter__ 方法会直接返回 self，这样的对象也被称为自迭代对象。但在某些情况下，为了支持多次迭代或者并发迭代，__iter__ 方法会返回一个新的迭代器对象。

1. 假设你有一个大的数据集，你希望同时有多个迭代器在这个数据集上进行独立的迭代。在这种情况下，每次调用 __iter__ 方法时都应该返回一个新的迭代器对象，这样每个迭代器都可以独立地维护它自己的状态（例如，当前的位置）。这样，多个迭代器就可以并发地在同一个数据集上进行迭代，而不会相互干扰。

2. 当你希望一个对象可以被多次迭代，且每次迭代都从头开始时，你也需要让 __iter__ 方法返回一个新的迭代器对象。如果 __iter__ 方法直接返回 self，那么一旦一个迭代完成，下一次迭代就无法从头开始了，因为迭代器的状态（当前的位置）已经被前一次的迭代改变了。


## 环境管理

- **pip**
    * `pip install <package_name>（不带.）`会首先尝试从PyPI下载与您的平台匹配的预编译轮子文件（如果存在）。如果没有预编译的轮子文件，pip会从源代码构建该包。pip会尝试解决包的依赖关系，并确保安装正确版本的包。然而，pip只能管理Python级别的依赖，不能管理系统级别的依赖。
    * 如果项目包含纯Python代码（没有C/C++扩展），`pip wheel .`只会打包Python源代码为轮子文件。
    * 如果项目包含C/C++扩展（例如XGBoost），`pip wheel .`会触发这些扩展的编译过程，然后将编译后的二进制文件和Python源代码一起打包为轮子文件。

- **conda**：
  * 创建和管理独立的环境，同时也会尝试解决所有的依赖关系，并确保安装正确版本的包。
  * 不仅可以管理Python包，还可以管理非Python的包，甚至可以管理操作系统级别的库。
  * conda并不总是能够管理所有的系统级别的依赖。有些依赖可能需要手动安装，或者使用系统的包管理器（如apt或yum）来安装
    当你使用conda安装一个包时，conda通常是在下载和安装已经为主机操作系统`预编译`的二进制文件。这使得conda非常易于使用，但也限制了它的跨不同操作系统能力。

- **docker&编译**：
  * 对于包含C扩展的Python包，需要进行编译步骤。编译过程中，C编译器会将C代码转换为机器代码，并链接到`系统级别`的库。这个过程确保了Python包可以正确地使用这些系统级别的库。如果系统级别的库没有被正确地安装，或者安装的版本不正确，编译可能会失败，或者编译出的程序可能无法正常运行。
  * 使用Docker，你可以创建一个完全定制的环境，包括操作系统、系统库、编译工具、`运行时环境`、应用程序和其依赖等。你可以控制每一个细节，包括如何编译你的应用程序。并允许开发者将应用和其环境打包成一个轻量级、可移植的容器，然后发布到任何支持Docker的系统上

#### pip

1. **安装包依赖**
    如果一个Python项目遵循了标准的项目结构，并且包含了一个setup.py文件，那么它就可以被打包成.whl文件。以lightning为例
    ```shell
    git clone https://github.com/Lightning-AI/lightning
    cd lightning
    pip wheel .
    ```

2. **拷贝python环境**
    ```shell
    #命令来创建一个包含所有已安装包及其版本的列表
    pip freeze > requirements.txt        
    pip download -r requirements.txt -d /path/to/wheels
    pip install --no-index --find-links=/path/to/wheels -r requirements.txt
    #为xgboost生成 .whl 文件并将其保存在当前目录下 
    pip wheel xgboost --wheel-dir=.      
    ```

#### conda

在不同的Linux系统之间离线打包环境，可以使用`conda pack`命令。以下是具体步骤：

1. 在源系统中，首先确定你的conda环境名称，假设为`myenv`。

2. 在源系统中，运行`conda pack -n myenv`命令。这将创建一个名为myenv.tar.gz的文件，其中包含了环境myenv的所有内容。

3. 将myenv.tar.gz文件复制到目标系统。你可以使用任何你喜欢的方法（例如USB驱动器，SCP，rsync等）。

4. 在目标系统中，解压myenv.tar.gz文件。例如，你可以运行`mkdir myenv && tar -xzf myenv.tar.gz -C myenv`命令。

5. 在目标系统中，激活环境。你可以运行`source myenv/bin/activate`命令


## 编码
让我们从这三个角度来深入探讨汉字"你好"。

Unicode:

Unicode是一个字符集，为世界上的每个字符、标点符号和许多其他符号都指定了一个唯一的数字，称为“代码点”。
对于汉字"你好"：
"你" 的 Unicode 代码点是 U+4F60。
"好" 的 Unicode 代码点是 U+597D。
这些代码点为每个字符提供了一个唯一的标识，而不考虑它们如何在计算机中存储或表示。
UTF-8:

UTF-8是一种字符编码，用于将Unicode字符表示为字节序列。
它是一种变长编码，意味着不同的字符可能使用不同数量的字节来表示。
对于汉字"你好"：
"你" 在UTF-8中表示为3个字节：E4 BD A0。
"好" 在UTF-8中表示为3个字节：E5 A5 BD。
所以，当你将汉字"你好"保存为UTF-8编码的文件时，它将占用6个字节。
字节字符串:

字节字符串是Python中的数据类型，用于表示原始字节数据。
对于汉字"你好"的UTF-8编码表示：
它可以表示为字节字符串 b'\xE4\xBD\xA0\xE5\xA5\xBD'。
这个字节字符串直接表示上述的6个字节，而不关心它们代表的字符或字符的含义。
总之，从Unicode角度看，"你好"由两个唯一的代码点组成。从UTF-8角度看，它由6个字节组成。从字节字符串的角度看，它是这6个字节的原始表示。



## 上下文管理器

1. **临时修改与自动恢复**
  上下文管理器：在 lora 的 with 语句作用域内，  CausalSelfAttention 类的引用被临时替换为使用LoRA的版本。当我们退出该作用域时，这个修改会自动撤销，即使我们不显式地这样做。这意味着开发者不需要记住在使用LoRA后进行任何清理操作。
  函数调用：如果我们使用一个函数实现这个功能，那么在该函数返回后，开发者需要记住手动恢复原始的 CausalSelfAttention 类引用。

2. **异常处理与资源清理**
   上下文管理器：如果在 with lora(...): 作用域内的代码发生异常，上下文管理器仍会确保在退出时执行清理操作，恢复原始的类引用。
   函数调用：我们需要使用 try...except...finally 结构来确保，在函数执行过程中发生异常时，资源被正确清理。

3. **语义清晰性**
   上下文管理器：当开发者看到 with lora(...):，他们可以立即理解该代码块是在LoRA的上下文中执行的。
   函数调用：我们可能需要更多的注释或文档来说明函数内部的行为，特别是当函数有副作用时。


## pandas 中使用 `groupby.apply`

1. **检查第一个组的输出**:
   - `groupby.apply` 会首先应用函数到第一个组。基于这个输出，pandas 会判断返回的对象类型。

2. **根据返回类型组合结果**:
   - 如果每个组的函数返回一个 **标量**，最终的输出将是一个 Series，其索引是 group keys。
   - 如果每个组的函数返回一个 **Series**，pandas 会尝试将这些 Series 组合成一个 DataFrame。这里，DataFrame 的行索引是 group keys，列索引是每个返回的 Series 的索引。
   - 如果每个组的函数返回一个 **DataFrame**，pandas 将创建一个 MultiIndex DataFrame，其中第一级索引是 group keys，第二级索引是每个返回的 DataFrame 的索引。

3. **处理Series索引**:
   - 如果返回的 Series 具有相同的索引，那么这些 Series 将被垂直堆叠，结果 DataFrame 将具有与输入 DataFrame 相同的长度。
   - 如果返回的 Series 有不同的索引，pandas 将按列合并这些 Series，结果 DataFrame 的列是所有返回的 Series 索引的联合。

## 类型提示

#### 静态类型提示

这里的**静态**是指这种检查是基于代码中的注解和声明进行的，而不是基于运行时数据的实际值。
在 PySpark 的 `pandas UDFs` 或其他涉及 `Arrow` 转换的场景中使用类型提示，PySpark 会利用这些类型提示来指导 Arrow 转换。这个过程可以简化为以下几个步骤：

1. **提取函数的类型提示**：PySpark 使用 Python 的内置模块，如`inspect`和`typing`，来检查和解析函数的类型提示。

2. **映射到 PySpark 类型**：PySpark 有一个内部的映射表，用于将 Python 的类型和 typing 模块的类型映射到 PySpark 的数据类型。

3. **指导 Arrow 转换**：一旦 PySpark 了解了预期的数据类型（从类型提示中），它就可以指导 Arrow 转换过程，确保数据正确地从 PySpark 内部格式转换为 Arrow 格式，反之亦然。例如，如果类型提示指定了某列应该是 str，那么 PySpark 会确保 Arrow 使用 String 数据类型来表示该列。

4. **验证**：基于类型提示，PySpark 还可以在转换后验证数据的一致性，确保数据与类型提示匹配。如果不匹配，PySpark 可以提前捕获这个问题并抛出一个更具描述性的错误。











